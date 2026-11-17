# Record templates

In Phase 0, copy this whole section word for word to OUT/_harness/templates.md. Use the templates exactly: same field names, same order, every field filled.

**Record IDs.** A record ID is the repo ID, the stage or history code, and a two-digit sequence: R2-S05-01, R2-H3-02. An ID is never reused or renumbered, because the verifier and the synthesis refer to it.

## Finding record

```
### R2-S05-01  One-line neutral title of what was attempted
- Question: S05.1
- What was attempted: plain description of the approach, with citations
- Presence: CURRENT | DISABLED | REMOVED | BRANCH-ONLY, with citation
- Outcome: WORKED | PARTIAL | FAILED | UNVERIFIED
- Outcome evidence: OBSERVED | STATED | INFERRED, with citations. NONE if the outcome is UNVERIFIED.
- Why it turned out that way: the cause, graded OBSERVED | STATED | INFERRED, with citations. UNKNOWN if not evidenced.
- Depends on: versions, hardware, settings, design choices and outside conditions the outcome was tied to
- Open questions: what the evidence does not settle, written as questions. NONE if none.
- Confidence: HIGH | MEDIUM | LOW, with a one-line reason
```

## NOT FOUND record

```
### R2-S06-01  NOT FOUND
- Question: S06.3
- Searched: the terms, paths and commands used
- Nearest related material: citation, or NONE
```

## Filled illustration

The content below is invented to show the form. Do not reuse any of it.

```
### R9-S04-01  Automatic reconnect to the last source at boot
- Question: S04.3
- What was attempted: a boot-time script loops over stored devices and requests a connection to each (scripts/reconnect.sh:5-22)
- Presence: DISABLED. The service that calls the script is commented out (install.sh:48)
- Outcome: FAILED
- Outcome evidence: STATED. Commit a1b2c3d message: "disable auto reconnect, phone connects then drops after 2s"
- Why it turned out that way: UNKNOWN. No cause is given in the commit, the code or the notes.
- Depends on: the one phone model named in commit a1b2c3d; the stack version pinned in install.sh:12
- Open questions: Did the drop occur with any other source device? Was the cause investigated?
- Confidence: MEDIUM. STATED evidence only.
```

**History file layout.** H2, H3, H4, H5 and H7 use finding records. Their Question field names the history question and the stage question it relates to, for example H2, relates to S05.1, or H2, relates to NONE. H1 and H8 are short factual lists with citations. H6 is a table with the columns location, marker, text (at most 25 words) and related stage.

**File ending.** Every stage file and history file ends with a tally: questions answered with findings, questions answered NOT FOUND, and the total. The total must equal the number of questions in the brief.
