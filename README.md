# User-name-availability-intelligence-
cat > report.md << 'EOF'
# Task 2 Report: Username Availability Intelligence

**Author:** Alisha Rokka
**Report date:** 9th oct 2026
**Assessment:** 5th Division OSINT (Internship Task)

## 1. Executive Summary
I built a tool that checks whether a username has a public profile on several platforms. The platform list lives in a config file, so new platforms can be added without changing the code. Results are classified as found, not_found, unknown, error or skipped, and saved as JSON and a terminal table.

The final platform set is GitHub, GitLab, Dev.to and Keybase. During testing, three platforms gave unreliable or blocked answers and were removed. All 27 automated tests pass.

## 2. Scope and Rules Followed
- Only public profile pages were requested.
- No login, password guessing, or bypassing of any block.
- Delay between requests, timeouts and limited retries.
- Only a public figure's username (torvalds), a known GitLab account (sytses) and a random string were tested.

## 3. How It Works
| Module | Purpose |
|---|---|
| validators.py | Rejects URLs, spaces, slashes and symbols before any request |
| engine.py | Sends requests, follows redirects, retries, rate limits, classifies |
| main.py | CLI, terminal table, JSON output |
| platforms.json | Platform rules, editable without code changes |

## 4. Requirements Coverage
| # | Requirement | Status | Evidence |
|---|---|---|---|
| 1 | Username input | Done | --username option, validator tests |
| 2 | Configurable platforms | Done | platforms/platforms.json; Dev.to and Keybase added with no engine change |
| 3 | HTTP response analysis | Done | status codes and body markers |
| 4 | Redirect handling | Done | manual redirects, limit of 3 |
| 5 | Timeout handling | Done | 10 second timeout, tested |
| 6 | Rate limiting | Done | 1.5 second delay per request |
| 7 | Retry handling | Done | retries on timeout, 429 and 5xx |
| 8 | False-positive reduction | Done | see section 5.2 |
| 9 | Result classification | Done | five result classes |
| 10 | JSON and terminal output | Done | output/final_run/*.json |

## 5. Findings
### 5.1 Final results
| Platform | torvalds | zq8x7k2m9vw4 (random) |
|---|---|---|
| GitHub | found | not_found |
| GitLab | not_found | not_found |
| Dev.to | not_found | not_found |
| Keybase | found | not_found |

GitLab found sytses (a real account), which proves its found path works.

> **[Screenshot 1: final run for torvalds]**
> **[Screenshot 2: final run for zq8x7k2m9vw4]**

### 5.2 False-positive findings (runs before platform removal)
- **PyPI** returned HTTP 200 for a random username. The page was a bot-challenge page, not a profile. Removed. The tool does not try to bypass it.
- **Instagram** returned found (200) for both a real and a random username. The answer could not be trusted. Removed.
- **Reddit** returned HTTP 403 to automated requests. The tool reported unknown. Removed so the tool stops sending requests to a platform that refuses them.
- **Discord** was excluded because it has no public profile page.

Evidence: output/evidence_before_removal/

> **[Screenshot 3: Reddit unknown and Instagram false positive]**

### 5.3 Verified facts vs inference
- Found or not_found on a high-reliability platform is based on a direct HTTP answer.
- unknown means the platform did not give a usable answer. No guess is made.
- Not found only means the name is not registered on that platform, not that the person does not exist.

## 6. Testing
27 offline tests (network mocked) cover validation, found and not_found, GitLab empty body, 403 and 429 handling, login redirects, too many redirects, timeouts, retries, connection errors, unexpected status, record format, platform failure isolation and bad config.

Run: python3 -m pytest tests -v
Results: output/test_results.txt

> **[Screenshot 4: 27 passed]**

## 7. Security Controls
- Username validation before any request
- Public URLs only, no credentials, no API keys
- Delay, timeouts, limited retries, clear User-Agent
- One failing platform never stops the run
- Blocks are reported as unknown and never bypassed

## 8. Limitations
- Platforms can change their pages or start blocking at any time.
- The tool trusts status codes and simple markers, so a platform that changes behavior can give wrong answers.
- Only four platforms remain; coverage is small by design.
- A found result does not prove the account belongs to a specific person.
- Terms of service were not reviewed in full for each platform [check before submitting].

## 9. False-Positive Considerations
- Some sites return 200 for any name, which is why PyPI and Instagram were removed.
- Common usernames may exist on a platform for an unrelated person.

## 10. Conclusion
The tool meets all 10 Task 2 requirements, keeps platform rules outside the code, reports uncertain answers as unknown instead of guessing, and stays within public, authorized access.

## 11. Evidence Index
| File | Purpose |
|---|---|
| output/final_run/ | Final JSON output (four platforms) |
| output/evidence_before_removal/ | Runs showing Reddit 403 and Instagram false positive |
| output/test_results.txt | 27 passing tests |
| platforms/platforms.json | Platform configuration |
EOF
wc -l report.md
