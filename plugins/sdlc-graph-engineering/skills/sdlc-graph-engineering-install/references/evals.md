# Emitting the eval suite — step 9b in full

Load at step 9b, once the graph traces cleanly on paper and you are about to write the files.

Step 9 verifies the graph you are shipping today. **The graph will change**, and a paper trace does
not re-run. This is how you make the traces executable, and how you keep them worth running.

---

Step 9 verifies the graph you are shipping today. **The graph will change**, and a paper trace does
not re-run. Emit `evals/` alongside the four model files, and wire it to run on **every** edit to the
graph — a pre-commit hook, a CI job, or an editor hook, whichever the project already has.

Two suites, both plain scripts with no dependencies:

**`spec_consistency.py` — the files do not contradict each other.** The graph is described across
several files that restate the same facts, so **drift between them is the dominant defect class here,
ahead of any single-file bug**. One check per real defect, each named for the defect it caught:
vocabulary agreement, published counts, cycle counts, every state field read being declared, and the
diagram routing on the same predicate as the table. Name each check for the failure it prevents, not
for the property it asserts — a failing check should read as a diagnosis.

**`graph_walk.py` — a scripted run is legal.** Each fixture is a JSON list of transitions with every
node body mocked: nothing is built, nothing is spawned, no side effect occurs. The walker checks that
each transition is a declared edge, that the guard is quoted **verbatim** from the table, and that the
resulting state satisfies the schema's invariants. Four properties are worth more than the rest:

1. **Guards verbatim, not paraphrased.** A trail that does not match the table cannot be checked
   against it. Paraphrase is itself the finding.
2. **Coverage is a gate, not a report.** The fixture set as a whole must enter **every node** and
   traverse **every edge**. An untraversed edge is an untested guard, and it is precisely the
   configuration variant nobody traced. Fail the suite on a gap; the alternative is a coverage number
   nobody reads.
3. **Stops are derived from the contracts, never listed twice.** Parse the `human` field out of the
   node contracts and check the walk against *that* — a checker with its own hand-written copy of the
   stop list is the drift it exists to catch. Then assert each fixture's **exact ordered stop
   sequence**: it fails both when a stop is missing and when one appears that was not declared.
4. **Conditional stops get a fixture in each direction.** One where the condition holds and the stop
   must fire, one where it does not and the run must continue through. Failure mode 15 lives entirely
   in the second, and the second is the one nobody writes.

**Ship at least one negative control, and treat it as the load-bearing fixture.** A file of deliberate
violations the suite **must** reject, each named. If it ever passes, the checker has stopped checking
and every other green is worthless. Give it a legal step or two as well, so it cannot be satisfied by
a checker that rejects everything. Grow it whenever you add a check: the new check's first fixture is
a planted violation of it.

> **The rule that keeps the suite alive:** a change to the graph is not finished until it adds or
> updates the eval that would have caught its absence. A new edge needs a walk that traverses it — the
> coverage gate enforces that one for you. A new invariant needs a check. A new or changed stop needs
> both, plus a planted violation. Write this rule into the target project's own `CLAUDE.md`, next to
> the command that runs the suite; a convention that lives only in this skill is one the next
> contributor never sees.

