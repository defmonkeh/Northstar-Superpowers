# GOAL

## North-star
The `/health` endpoint reliably tells Railway (and engineers) that the
app is alive: it returns HTTP 200 with a plain-text body of exactly `OK`,
with no redirect chain and no server-side error masquerading as a 200.

## Success criteria

- [ ] `GET http://localhost:3000/health` produces **exactly one** network
  entry (no redirect chain) with HTTP status **200** and no error body —
  verified by: `preview_network` on `http://localhost:3000/health`;
  assert entry count === 1, entry.status === 200.

- [ ] The response body is **exactly** the string `OK` (plain text,
  strict equality — not substring, not `OK plus extra`) —
  verified by: `preview_snapshot` on `http://localhost:3000/health`;
  assert `document.body.innerText.trim() === "OK"`.

- [ ] ESLint reports zero errors or warnings —
  verified by: `npx eslint . --max-warnings 0`; command exits with
  code 0.

- [ ] The Node.js process starts without crashing (no broken imports or
  syntax errors) —
  verified by: `node app.js &` then `curl -s -o /dev/null
  -w "%{http_code}" http://localhost:3000/health` returns `200` within
  5 s, confirming the process is listening.

## Standing criterion (every goal inherits this)
- [ ] Zero verifiable defects across all available surfaces (ESLint,
  Node startup, browser/dev console + network via preview_* tools).

## Non-goals
- Do NOT add authentication or middleware to `/health`.
- Do NOT change the HTTP port or server configuration.
- Do NOT refactor any route other than `/health`.
- Do NOT change the response format from `text/plain` to JSON or HTML.
- Do NOT write new test files.
- Do NOT update the README or any documentation.

## Done-definition
The loop stops only when every box above is checked by real verification
output — not by assertion. Evidence (tool output, screenshot paths,
command exit codes) must be on file for each criterion before the loop
may declare success.
