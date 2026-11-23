# Adversarial Peer-Review Role

A reusable persona definition for an adversarial reviewer agent — a role
whose job is structurally biased toward finding defects in a piece of work
rather than confirming its author's hopes. It packages four rules that make
a review trustworthy on their own, independent of whatever domain checklist
you plug in:

- **A fixed severity taxonomy** (SEVERE / MAJOR / MODERATE / MINOR) so
  findings are comparable across reviews and reviewers.
- **An evidence citation discipline** — every finding must point at an
  exact, checkable location (`file:line` or the nearest equivalent) and
  quote the offending content, or it doesn't go in the review.
- **A disconfirmation duty** — negative verdicts are ordinary, admissible
  outcomes, never softened to match what someone hoped for, and a passing
  review has to show which objections it actually tried and ruled out.
- **A no-self-approval output contract** — the reviewer writes exactly one
  review artifact and touches nothing else: no fixes, no edits to the
  material under review, no marking its own verdict as accepted.

It generalizes a role that was proven over several review cycles in a
research-paper production pipeline (gate reviews, thesis-space rulings,
manuscript sign-offs), but nothing in the package is specific to that
domain. The same four rules apply directly to a code-review bot, a
manuscript or grant-proposal reviewer, a legal-document screener, or any
other pipeline stage where one agent needs to be a harder grader than the
agent that produced the work.

## What it does, in plain language

Most "review this" prompts quietly ask an agent to be agreeable: find a
couple of typos, say it looks good, move on. This skill is the opposite
instinct, made concrete and repeatable. It gives you a copy-paste system
prompt template plus the reasoning behind each clause, so that when you
stand up a review step you get:

1. Findings labeled with a severity everyone in the pipeline reads the same
   way, instead of vague adjectives that let real problems slide by as
   "minor."
2. Findings a third party can actually check, because each one names where
   to look and quotes what's wrong there.
3. A reviewer that will genuinely fail work that deserves to fail, and that
   shows its work when it passes something instead of rubber-stamping it.
4. A review that can never quietly become an approval, because the reviewer
   is not allowed to touch the thing it reviewed or sign off on its own
   finding.

## Works with

| Surface | How | Status |
|---|---|---|
| Claude Code | native skill (`./install.sh --claude`) | not tested |
| Codex | native skill via `.agents/skills` (`./install.sh --agents`) | not tested |
| Gemini CLI | native skill via `.agents/skills`, pointer in `GEMINI.md` | not tested |
| Cursor | native skill via `.agents/skills`, reads `AGENTS.md` | not tested |
| Copilot | pointer to `AGENTS.md` (`./install.sh --copilot`) | not tested |
| ChatGPT / Claude.ai / Gemini (chat) | paste `PROMPT.md` | not tested |

## Installation

```sh
git clone https://github.com/hyderhusainarastu/adversarial-peer-review-role
cd adversarial-peer-review-role
./install.sh --claude              # ~/.claude/skills/adversarial-peer-review-role
./install.sh --agents --project .  # .agents/skills (Codex, Cursor, Gemini CLI)
./install.sh --help                # all options; --dry-run and --uninstall supported
```

For chat UIs, paste [`PROMPT.md`](PROMPT.md). No dependencies, no build step.

## Worked usage example (synthetic)

Say you're standing up a pull-request review agent for a repository. You'd
follow the skill's "How to instantiate this role" steps:

1. Decide what counts as citable evidence in your repo: "a passing CI test,
   an existing type signature, or a linked ADR doc."
2. Write down what SEVERE/MAJOR/MODERATE/MINOR mean for code specifically —
   e.g., SEVERE is a swallowed exception that silently defeats the PR's
   stated purpose; MINOR is an unused import.
3. Pick a verdict vocabulary: `APPROVE / CHANGES-REQUESTED / BLOCK`.
4. Copy `references/persona-template.md`, fill in the brackets with the
   three decisions above, and drop the result into the review subagent's
   system prompt.

The resulting reviewer, pointed at a synthetic pull request titled "Add
retry logic to invoice sync," produces output like:

```
SEVERE — sync/invoice_client.py:88 — `except Exception: pass` swallows the
PaymentGatewayTimeoutError this function is supposed to retry on...
Required change: narrow the bare except or re-raise after logging, and add
a test that asserts a retry actually happens on PaymentGatewayTimeoutError.

...

Verdict: CHANGES-REQUESTED
Required changes:
1. Fix the swallowed exception at invoice_client.py:88 (SEVERE).
2. Add a boundary test for MAX_RETRIES (MAJOR).
...
```

The full worked example — including the "objections considered and ruled
out" section that demonstrates the disconfirmation duty in practice — is in
[`examples/code-review-instantiation.md`](examples/code-review-instantiation.md).
A second worked example applying the same template to a manuscript/editorial
review is in
[`examples/editorial-review-instantiation.md`](examples/editorial-review-instantiation.md).
All names, files, and findings in both examples are synthetic.

## Package contents

- `SKILL.md` — the full methodology: the four load-bearing rules, the
  severity taxonomy, the evidence and disconfirmation disciplines, the
  output contract, instantiation steps, and anti-patterns to watch for.
- `references/persona-template.md` — a complete, copy-paste-ready system
  prompt with bracketed placeholders for your domain's specifics.
- `examples/code-review-instantiation.md` — synthetic worked example for a
  software code-review persona.
- `examples/editorial-review-instantiation.md` — synthetic worked example
  for a manuscript/document-review persona.
- `examples/data-discovery-verifier.md` — synthetic example of a verifier
  that tries to disprove each candidate; the newest dated primary source
  wins, with a completeness critic.
- `verdict.schema.json` — JSON Schema for the verifier's per-candidate
  verdict records (`evidence_method` enum).
- `PROMPT.md` — self-contained paste version.
- `AGENTS.md` and `install.sh` — agent instructions and the installer.

No scripts are bundled with this skill — it is a persona/instruction
package, not a tool.

## Related

- [multi-agent-orchestration-playbook](https://github.com/hyderhusainarastu/multi-agent-orchestration-playbook): see [pilot-before-scale](https://github.com/hyderhusainarastu/multi-agent-orchestration-playbook/blob/main/docs/pilot-before-scale.md) before running a reviewer over a full batch.
- [agent-report-evidence-gate](https://github.com/hyderhusainarastu/agent-report-evidence-gate)
- [cross-model-second-opinion](https://github.com/hyderhusainarastu/cross-model-second-opinion)
- [hallucinated-citation-checker](https://github.com/hyderhusainarastu/hallucinated-citation-checker)

## Limits

Instructions only: nothing enforces the no-self-approval rule except the
file permissions you give the reviewer. The platform rows above are
untested.

## Keywords

adversarial review, code review agent, peer review, red team, reviewer
persona, severity taxonomy, agent skill, SKILL.md, AGENTS.md

## License

MIT. See [`LICENSE`](LICENSE).

---
Part of palimnote-skills - more at https://github.com/hyderhusainarastu
