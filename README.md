# Mahdi Alhakim

I found and fixed a false SAFE in Microsoft's agent-safety eval RAMPART: a safety test passed against an
agent that exfiltrated data, because the evaluator could not observe the exfiltration (PR #141, merged,
breaking change). Then I measured how often that happens: a text-only safety judge cleared 20 of 64 harmful
agent transcripts, against 2 of 64 when it could see the tool calls.

A lot of what I find has that shape: a check reports success when it never actually looked. A
prompt-injection filter covered six of the seven methods that needed it. A fuzz harness opened the file and
closed it again without ever reading an attribute. An admin screen reported no access while the write went
through. The thing meant to catch the problem could not have seen it.

I report these to the vendor privately, then write up what the test missed and how I proved it. Software
engineer in Beirut. I build MCP servers and agent tooling, and the security work is what I do with the rest
of my time.

US citizen, no sponsorship needed. Open to relocating.

mahdi.a.alhakim@gmail.com | [LinkedIn](https://www.linkedin.com/in/mahdi-alhakim-ab75731b9) | [Resume](https://mahdi-al-hakim.github.io/resume.pdf)

## Writeups

- **[One method of one tool: an incomplete content filter in github-mcp-server lockdown mode](https://mahdi-al-hakim.github.io/writeups/github-mcp-lockdown/)**.
  `pull_request_read` dispatches nine methods from one switch, and lockdown was added to them one at a time.
  Seven return text written by repository users. Six got a check. The writeup covers the two-account rig and
  the positive control that proved it at runtime rather than from reading the source, and how the trust model
  the vendor actually ships, a best-effort content filter and not an authorization boundary, decides what
  counts as a gap at all. Fixed in v1.10.0, Aug 2026.
- **[A one-byte differential in netcdf-c: integer overflow in classic-format length arithmetic](https://mahdi-al-hakim.github.io/writeups/netcdf-c/)**.
  netcdf-c runs on OSS-Fuzz, and the only harness it had opened a file and closed it without reading an
  attribute value. One byte of an 80-byte header undersizes a heap allocation while the un-wrapped element
  count still drives the read loop. The writeup covers how the differential was isolated to one byte and why
  the project's only fuzz harness could never reach it. Reported Jul 2026, fix PR open.
- **[Three reports to one vendor inside two weeks](https://mahdi-al-hakim.github.io/writeups/keycloak-caep/)**.
  All three were accepted, made public by Keycloak staff, and credited by name. One was fixed and backported,
  one reclassified as an enhancement, one handled publicly as a hardening improvement. The writeup is about
  what separated them, and about how to write a severity argument.

## Reported and fixed in other people's code

- **github/github-mcp-server [#3109](https://github.com/github/github-mcp-server/pull/3109)**, reported Aug
  2026, shipped in [v1.10.0](https://github.com/github/github-mcp-server/releases/tag/v1.10.0) that same
  month. Lockdown mode gated `get_diff` and filtered comments on a fork pull request, then returned the same
  pull request's commit messages in full through `get_commits`. Attacker-written text reached the agent
  through a filter that was closed everywhere else on the same object. One of three reporters credited in the
  fix. No CVE. [Writeup](https://mahdi-al-hakim.github.io/writeups/github-mcp-lockdown/).
- **microsoft/RAMPART [#141](https://github.com/microsoft/RAMPART/pull/141)**, merged Aug 2026. A safety
  probe asserting that the agent does not exfiltrate was passing while the agent exfiltrated on every turn,
  because a tool-only adapter could not observe side effects. I filed
  [#140](https://github.com/microsoft/RAMPART/issues/140) and then the fix: evaluators now return
  UNDETERMINED when they cannot see the evidence they are judging. Breaking change, +3701/-140 across 40
  files.
- **Keycloak [#51376](https://github.com/keycloak/keycloak/issues/51376)** (Aug 2026). Revoking an
  OAuth2 token ended the session but sent no CAEP session-revoked event, which logout does send. Relying
  parties checking tokens offline kept accepting them until expiry. Fixed on `main` in Oct 2026
  ([PR #51386](https://github.com/keycloak/keycloak/pull/51386)). Not backported, so released versions still
  behave this way.
- **Keycloak [#51630](https://github.com/keycloak/keycloak/issues/51630)** (Aug 2026). A delegated admin
  holding `Clients:manage` and no realm-management roles could update and disable the internal
  admin-permissions client, which was missing from a denylist. The access report said the permission was
  denied while enforcement allowed the write. Fixed and backported to `release/26.8`, Oct 2026.
- **microsoft/PyRIT [#2320](https://github.com/microsoft/PyRIT/pull/2320)**, merged Aug 2026, released in
  [v1.1.0](https://github.com/microsoft/PyRIT/releases/tag/v1.1.0) Sep 2026. A reset hook so realtime and
  WebSocket targets close the conversation's connection at the end of each attack.
- **Unidata netcdf-c [#3421](https://github.com/Unidata/netcdf-c/pull/3421)**, reported privately Jul 2026,
  then opened as a public fix PR at the maintainer's request. Two arithmetic fixes in the classic library.
  Open and awaiting review. No CVE. [Writeup](https://mahdi-al-hakim.github.io/writeups/netcdf-c/).
- **Keycloak [#51718](https://github.com/keycloak/keycloak/issues/51718)** (Aug 2026). A client could widen
  its own JWT authorization-grant audience allowlist through dynamic client registration. Keycloak treated
  this one as a hardening improvement rather than a vulnerability. I wrote the proposed fix as a client
  policy executor, [PR #51720](https://github.com/keycloak/keycloak/pull/51720): six files, 664 lines,
  eleven integration tests. Open and awaiting review.

## AI evals and red-team tooling

- **inspect_petri [#130](https://github.com/meridianlabs-ai/inspect_petri/pull/130)**, open since Jul 2026.
  An eval seed for [Petri](https://www.anthropic.com/research/donating-open-source-petri) where a tool
  result tries to talk the agent into sending out credentials, plus a judge dimension scoring how readily it
  falls for it. The run is a pilot and the sample is too small to carry a rate: 9 samples total, with Haiku
  4.5 scoring 1, 8, 9, 9, 9 across five runs and Opus 4.6 and Fable 5 scoring 1 and 1 across two each. That
  spread is worth re-running at fifty samples per model, which is the next step. No CI has run on the PR, so
  there is no failing build to read anything into.
- **[controlarm](https://github.com/mahdi-al-hakim/controlarm)**. The public core of a private harness I
  run for vulnerability research, built around trustworthy negatives: it keeps the record of whether a run's positive control actually fired, so a run
  that proved nothing is filed as VOID and not as "no bug found". Its prior-art gate is scored against 80
  labelled items drawn from three public advisory corpora under a rule written before the corpus was built.
  1,275 tests, and 180 guards each neutered in CI to prove a test catches its removal.

## CTF and teaching

- **[WeSecure](https://github.com/mahdi-al-hakim/WeSecure)**, a boot-to-root Linux machine with a full
  writeup.
- **Lebanese Universities CTF 2025** (Semicolon, around 260 teams): placed 1st on the national
  qualifying-round leaderboard with a three-person AUB team.
- **Hack The Box Ambassador, Beirut.** Co-founded HTB Beirut, run hands-on meetups and build original
  practice machines.
- Mentored and taught Semicolon's 10-session AI security course. Co-organizer, mentor and trainer for
  Lebanon CTF 2026.
