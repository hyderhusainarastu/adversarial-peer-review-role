# Worked Example: Data-Discovery Verifier (Synthetic)

A discovery step (a person or another agent) proposes candidate facts or
records, for example "dataset X is published under licence Y" or "the
current threshold is N". This verifier checks each candidate and tries to
disprove it. Every name, source and date below is invented for illustration.

## Filled-in persona (excerpt)

> You are the adversarial verifier for **candidates.jsonl**. For each
> candidate you try to disprove it before you accept it.
>
> - Citable evidence is a primary source you opened yourself: the
>   publisher's own page or file, a direct query result, or a computation
>   you re-ran. A summary, mirror or earlier answer from another agent is
>   secondary and cannot confirm anything.
> - When sources disagree, the **newest dated primary source wins**. Record
>   its date in `newest_primary_source_date`. A newer secondary source does
>   not outrank an older primary one.
> - Record how you got the evidence in `evidence_method` (enum in
>   `verdict.schema.json`). If you could not check it, the verdict is
>   `UNVERIFIED`, never `CONFIRMED`.
> - Every candidate needs at least one entry in `disproof_attempts`: an
>   objection you actually ran and whether it was ruled out or stands.
> - Verdict vocabulary: **CONFIRMED / REJECTED / UNVERIFIED**.
> - Output: one JSONL file of verdict records at the path in the brief. No
>   edits to the candidate file, no fixes, no marking your own file accepted.

## Sample verdict records (synthetic)

```json
{"candidate_id": "c-014", "verdict": "REJECTED", "evidence_method": "primary-source-read",
 "evidence": [{"locator": "registry-page.html:42", "quote": "Retention period: 5 years (revised)", "source_date": "2026-03-02"}],
 "disproof_attempts": [
   {"objection": "The candidate cites a 3-year period; is there a newer revision?", "outcome": "stands", "why": "The registry page dated 2026-03-02 says 5 years."}],
 "newest_primary_source_date": "2026-03-02"}
```

```json
{"candidate_id": "c-019", "verdict": "UNVERIFIED", "evidence_method": "secondary-source-only",
 "evidence": [], "disproof_attempts": [
   {"objection": "Does a primary source exist?", "outcome": "stands", "why": "Only a blog post repeating the claim was reachable."}]}
```

## Completeness critic

A second pass, run by a different invocation, reads the candidate list and
the verdict file and fills `completeness_gaps`. It asks what was never
examined: sources in the brief with no candidate drawn from them, candidates
with no verdict, and verdicts whose `disproof_attempts` are all
`ruled-out` with no stated reasoning. A gap is a finding with a locator like
any other; the critic does not add candidates itself.

## Validate the output

```sh
python3 -m pip install jsonschema
python3 - <<'PY'
import json
from jsonschema import Draft202012Validator
schema = json.load(open("verdict.schema.json"))
for n, line in enumerate(open("verdicts.jsonl"), 1):
    for e in Draft202012Validator(schema).iter_errors(json.loads(line)):
        print(f"line {n}: {e.message}")
PY
```

For a first run, check a small batch by hand before scaling up. See
[pilot-before-scale](https://github.com/hyderhusainarastu/multi-agent-orchestration-playbook/blob/main/docs/pilot-before-scale.md).
