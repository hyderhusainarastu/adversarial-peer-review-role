# adversarial-peer-review-role

An Agent Skill that defines a reviewer persona biased toward finding defects.

## Using this in your project

- Use it when you set up a review subagent, sign-off gate, code-review or editorial-review role, or a verifier that must try to disprove candidates.
- Read `SKILL.md` for the four rules: severity taxonomy (SEVERE/MAJOR/MODERATE/MINOR), file:line evidence, a duty to disprove, no self-approval.
- Copy `references/persona-template.md`, fill in the bracketed parts, and use it as the reviewer's system prompt.
- Examples: `examples/code-review-instantiation.md`, `examples/editorial-review-instantiation.md`, `examples/data-discovery-verifier.md`.
- For verifier output, validate records against `verdict.schema.json`.
- The reviewer writes exactly one review file, at the path in the brief. It never edits the reviewed material, never fixes defects, never marks its own verdict accepted.
- Every finding needs a severity, a locator and a quote. Anything unchecked is reported UNVERIFIED, not confirmed.
- Chat UIs without file access: paste `PROMPT.md`.
- Install with `./install.sh --help` (no flag changes nothing).

## Working on this repo

- No code to build. Test the installer offline: `./test_install.sh`.
- Keep examples synthetic. Do not add real names, paths or data.
- Keep the `SKILL.md` description at 300 characters or fewer.
