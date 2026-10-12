# Teacher IPC — maintainer notes

The teacher-facing version of the coordinator TEKS Lookup (`MayraBT/Carnegie_DOT_CHART`). Single file, no build step: everything lives in `index.html`; released STAAR item images are in `items/`. The coordinator repo is separate and is never changed from here.

## What a teacher sees
- **This week** (home): the IPC week for the chosen course, day by day, then one card per lesson with its TEKS. Each TEKS opens to the student expectation, look-for, a misconception to watch for, a question to ask, a sentence stem, and one item (released STAAR item for Grades 6–8 and Algebra I; practice item for Geometry and Algebra II). Previous / Next week buttons.
- **Calendar**, **By lesson** (with TIG links), **By standard**, **STAAR performance** (called **Practice items** for Geometry and Algebra II), **Teaching moves**, **How to use**.
- Removed from the coordinator version: the Full grid tab, the vertical performance panel, the "Dallas ISD below Texas" filter, and the Walks, Appraisers and Vertical view guides.

## Data in index.html
| Constant | Status in the teacher version |
|---|---|
| `COURSES`, `GEO_DATA`, `A2_DATA` | Kept: lesson titles, TEKS marks, IPC pacing and calendar |
| `TEKS` | Kept (19 TAC §111) |
| `PERF` + `items/g*_*.jpg` | Kept: released STAAR items and Texas percents (lead4ward IQ Tool) |
| `TIG` | Kept: Drive file ids; opening one still needs district sign-in |
| `MOVES` | Kept, with the "suggestions, not a checklist, not for evaluation" framing |
| `DIST`, `DIST_META` | **Empty.** Dallas ISD results stay in the coordinator version |
| `ACP`, `ACP_SETS`, `items/acp_*` | **Removed.** Replaced by `PRACTICE` |
| `SCAF`, `SCAF_HS` | **Empty**, waiting for a free vertical-alignment source |
| `VOCAB` | **Empty**, waiting for a free vocabulary source |

`HAS_SCAF` and `HAS_VOCAB` switch features on automatically once those constants have data:
- `VOCAB` shape: `{blocks:[{cluster, sub, codes:[...], new:[...], prev:[...]}], byCode:{code:[block index]}}`. Vocabulary then appears under each TEKS in This week and By standard, in By lesson, and as a Calendar option.
- `SCAF` shape: `[{cluster, sub, src, ses:[{code, type, text, start}]}]`. Vertical alignment, the across-courses clusters and the aligned filter in Teaching moves come back. Before turning it on, update the "lead4ward TEKS Scaffold" wording in `renderAlign` to name the new source, and remove the Dallas ISD wording in `vertPerf`.

## Practice items (`PRACTICE`)
Keyed by TEKS, one TEKS per item:

```
{set, q, ai:true, itemType, points, correct, key, teks:[...], stem, svg, stem2, choices, unit,
 rationale, distractors:{choice: misconception}, check, approved:[...], reviewed}
```

- `stem`, `stem2`, `choices`, `rationale` are HTML. Fractions are stacked (`<span class="frac"><span>1</span><span>3</span></span>`), never with a slash; no vocabulary in parentheses; segments use `<span class="ol">`, arcs `<span class="arc">`.
- Figures are inline SVG using `currentColor`, so they stay sharp and editable.
- Every item is solved two independent ways before it is added; `check` records how.
- **Review:** teachers see only items with `reviewed:true`. Open the page with `?review` (for example `…/index.html?review#geo`) to see items still waiting, marked "Pending review". Julie and Tina review; set `reviewed:true` when they approve.
- Sets: `ai_geo`, `ai_a2` (`PRACTICE_SETS`).

## Hosting
Public repo on GitHub Pages. Everything in the file is public or already in teachers' hands; district results and ACP items are not in it. Link the page from the department Google Site or Canvas.

## Ground rules
- Test every change in a real browser before pushing: desktop 1440 px and phone 390 px, no page errors, no horizontal overflow.
- Browser storage keys use the `tipc-` prefix so they never mix with the coordinator version on the same github.io origin.
- Update "How to use" (`HELP`) whenever a feature changes.
