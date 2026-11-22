# Mahdi Alhakim

Security researcher on AI agents and identity systems, and a software engineer who builds MCP servers and
agent tooling. Based in Beirut. US citizen, no sponsorship needed, open to relocating.

- Found and fixed a false SAFE in Microsoft's [RAMPART](https://github.com/microsoft/RAMPART/pull/141): a
  safety probe passed while the agent it tested exfiltrated data on every turn, because a tool-only
  adapter couldn't observe side effects. Merged as a breaking change. Also merged a
  [PyRIT](https://github.com/microsoft/PyRIT/pull/2320) reset hook for realtime and WebSocket targets.
- Reported a prompt-injection gap in [github-mcp-server](https://github.com/github/github-mcp-server/pull/3109)'s
  lockdown mode, fixed in v1.10.0 ([writeup](https://mahdi-al-hakim.github.io/writeups/github-mcp-lockdown/)).
- Three credited reports to Keycloak: one fixed and shipped in 26.8.2, and my fix for another
  [in review](https://github.com/keycloak/keycloak/pull/51720)
  ([writeup](https://mahdi-al-hakim.github.io/writeups/keycloak-caep/)). A merged overflow fix in
  [netcdf-c](https://github.com/Unidata/netcdf-c/pull/3421).
- [Warden](https://github.com/mahdi-al-hakim/warden) runs a Claude agent on IT offboarding under policy,
  approval, audit and revocation. In 190 live runs on Claude Haiku, Sonnet and Opus 5.5
  where a note planted in the data asks the agent to disable a shared admin account, none proposed it;
  when the task itself asked, Warden blocked all 18 attempts.
- [controlarm](https://github.com/mahdi-al-hakim/controlarm) won't record a security test as negative until
  the same run shows the target answering and the plain version of the attack being refused.
- [malt-reward-hacking](https://github.com/mahdi-al-hakim/malt-reward-hacking): in METR's MALT dataset,
  a check for one word finds 223 of the 224 runs labeled sabotage and none of the 6,047 normal ones,
  because METR's edit leaves the agent stating its instructed intent. With those statements removed by
  an LLM rewrite, in a pre-specified
  follow-up, a monitor reading only the agent's messages loses 0.186 AUROC, and one that also sees the
  actions about 0.09.
- Hack The Box Ambassador in Beirut. I built [WeSecure](https://github.com/mahdi-al-hakim/WeSecure), a
  boot-to-root machine with a full writeup.

Writeups and resume: [mahdi-al-hakim.github.io](https://mahdi-al-hakim.github.io) ·
mahdi.a.alhakim@gmail.com · [LinkedIn](https://www.linkedin.com/in/mahdi-alhakim-ab75731b9)
