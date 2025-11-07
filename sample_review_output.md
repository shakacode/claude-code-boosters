## Claude Code Review - PR #347: CH23126: scraping fixes

### Summary
This PR makes three focused changes to address scraping issues: adjusts production configuration for rate limiting and data expiration, fixes the rate limiter implementation to work correctly with lower request rates, and improves error handling in the Booking parser with a corrected regex pattern for room ID detection.

### Key Findings

#### Strengths
- Good error handling improvement in booking/details.rs - explicitly handling the None case for capla_json with a descriptive error message
- Rate limiting fix is mathematically sound - changing window_length calculation to divide duration by rate and using count=1
- Configuration changes are clear and focused on production stability

#### Issues
**Medium Severity:**
- **Rate Limiter Logic Change** (parser/http-client/src/shakaproxy.rs:47-52): The new implementation changes from "N requests per 1 second" to "1 request per (1/N) seconds". While mathematically equivalent for steady state, this could affect burst handling behavior. The original allowed N concurrent requests in a 1-second window, while the new version strictly limits to 1 request every window_length. Verify this matches the intended behavior for rate limiting with the proxy.

**Low Severity:**
- **Regex Pattern Escape** (parser/parser-provider/src/booking/details.rs:76): The new pattern `r#"\?"roomId\?":(\d+)"#` uses `\?` to match literal question marks. This is unusual JSON structure. Verify this is the actual format in the scraped HTML and not a workaround for a different parsing issue.

#### Suggestions
- Consider adding a comment explaining why the rate limiter implementation changed from (1sec, N requests) to (1/N sec, 1 request) to help future maintainers understand the design decision
- The error message "Failed to parse capla json" could include the actual error from the parser if available, to aid debugging in production
- Consider whether the database expiration_days change from 1 to 30 days needs corresponding changes in storage capacity or cleanup jobs

### Code Quality
Code is well-structured and follows Rust best practices. The error handling improvement is idiomatic Rust. Variable naming is clear.

### Testing
No tests are included in this PR. Consider adding:
- Unit test for the rate limiter with max_requests_per_second=40 to verify timing behavior
- Test case for the new room ID regex pattern
- Test for the error path when capla_json parsing fails

### Security
The rate limiting reduction from 300 to 40 requests/second is a good defensive measure against overloading the proxy or getting blocked. No other security concerns identified.

### Performance
**Positive impact:** Reducing request rate and increasing data retention should reduce load on the scraping infrastructure and decrease redundant scraping operations.

### Documentation
The configuration changes lack context in the PR description. Consider documenting:
- Why the rate was reduced from 300 to 40 (were you getting blocked/throttled?)
- Why expiration increased from 1 to 30 days (cache strategy change?)
- What issue the regex pattern fix addresses

### Overall Assessment
This is a solid bug fix PR with good error handling improvements. The main concern is ensuring the rate limiter behavior change is intentional and well-understood. Would benefit from tests and more detailed PR description.

**Recommendation:** Approve with minor suggestions for additional testing and documentation.

---
*Automated review by Claude Code*
