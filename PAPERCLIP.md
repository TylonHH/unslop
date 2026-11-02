# unslop for Paperclip

This is a Paperclip-compatible packaging of [unslop](https://github.com/heydamir/unslop) by Damir Sokolovsky ([@heydamir](https://x.com/heydamir)). The design workflow is unchanged. Only the file layout was adjusted so Paperclip's skill importer accepts the package.

## What changed

Paperclip classifies every file under `references/` as a text-only reference before it looks at the extension. The five `.webp` reference images were therefore rejected with:

> Non-asset skill files must be UTF-8 text.

This fork moves them to `assets/refs/` and updates the links in `SKILL.md` and `references/refs.md`. Paperclip classifies files under `assets/` as binary assets, which are allowed up to 1 MB per file. All five images are under that limit. No prompt, pass, script or design instruction was changed.

## Import into Paperclip

Use the Paperclip UI as a board/operator:

1. Open **Skills → Sources → Import from GitHub**.
2. Repository: `https://github.com/TylonHH/unslop`
3. Branch: `main`
4. Skill path: `skills/unslop`
5. Import, then pin the resulting commit or the `paperclip-v1` tag.

The repository is public, so no GitHub connection is required. Behind the UI, Paperclip uses the `/companies/:companyId/skill-sources` endpoints; automation should use the same board session and the same flow.

After import:

- Expect non-blocking warnings for `scripts/snapshot.mjs` and `scripts/story.mjs` (`script_trust`), `assets/refs/*.webp` (`asset_trust`) and `agents/openai.yaml` (`other_trust`). Warnings do not block the import. Review the two scripts before enabling the skill.
- Enable the skill per agent under **Agent → Skills**. Good fits are Web & Shop Manager, WordPress Developer, Developer, Social & Video Producer, and optionally Content & SEO Specialist. Do not enable it for every agent; the 31 KB `SKILL.md` is roughly 8k tokens per run.
- Agents may not import or modify skills. This is an operator/board action.

## Runtime dependencies

The skill is instructions plus two local Node scripts. Paperclip does not install them for you.

- **Playwright and Chromium** are required for `snapshot.mjs` and `story.mjs`. Install them once as the operator. If the agent runs inside the Paperclip Seatbelt sandbox, set `PLAYWRIGHT_BROWSERS_PATH` to a path the sandbox can read, for example `/Library/Application Support/PaperclipSecurity/browsers`. The default `~/Library/Caches/ms-playwright` is outside the read allowlist. Per-workspace installs also work but duplicate ~150 MB per agent.
- **Node** is available at `/opt/homebrew/bin/node` on the supported Paperclip host.
- **ffmpeg** is only needed for the story video. It is already present at `/opt/homebrew/bin/ffmpeg` on the supported host.
- Write screenshots, saved pages, story files and video output to `./task-artifacts/<Issue-ID>/` inside the agent workspace. Do not use `/tmp` or host paths.

## Sandbox notes

- **Live page snapshots** need outbound HTTPS, which the current Paperclip profile allows. A local dev server on a port that is not allowlisted will be blocked. Use a staging HTTPS URL, a static `file://` export, or a board-approved scoped port addition.
- **Story video** is the one part that does not work in the current sandbox as-is. `story.mjs` starts a loopback HTTP server (`server.listen(0, "127.0.0.1")`), but the Paperclip Seatbelt profile has no `network-bind` or `network-inbound` allow. Either render the story video in an operator step outside the sandbox, or make a reviewed, backed-up profile change. The audit, direction and nine passes work without the video.

## Paperclip house rules for a fork

The upstream skill assumes an interactive user. In Paperclip, adapt these points in the company copy:

- Publish the audit and the five direction lines as an issue comment and request the native human approval/decision. Do not edit before approval unless the user already said to just do it.
- Do not install Playwright, Chromium or ffmpeg at runtime. Report a missing dependency as a blocker.
- Do not self-connect MCP servers or credentials. Image generation only through an assigned company Connector, with approval before every paid generation.
- Keep publication, merge, deployment and production changes behind the normal human approval stage.
- Keep the CC BY 4.0 credit and the upstream link in `SKILL.md` and in any adaptation.

## License and attribution

unslop is licensed under [CC BY 4.0](LICENSE) by Damir Sokolovsky ([@heydamir](https://x.com/heydamir)). This packaging keeps the credit and the license. Use it, change it and share it, including commercially, as long as you credit the author and link back to the upstream repository. The packaging changes in this fork are offered under the same license.

Upstream: https://github.com/heydamir/unslop
