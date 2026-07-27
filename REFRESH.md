# Refreshing the contribution terrain

`contribution-terrain.html` is a 3D voxel visualisation of commit activity in
**berkisodd/schedule**. It is published as a Claude artifact at:

```
https://claude.ai/code/artifact/bdfd27cd-eb49-48da-a523-1bc1ea46a8d6
```

A daily routine re-runs the procedure below so the published page keeps up with
new commits. The artifact URL never changes.

## What to update

Only the block between the `==DATA-START==` and `==DATA-END==` markers in
`contribution-terrain.html` should change. Everything else — layout, grid size,
weekday placement, the Most/Least markers, and all the header stats — is derived
from that block at runtime, so the page grows on its own as history accumulates.

The block holds exactly three things:

```js
var REPO = "berkisodd/schedule";
var GENERATED_AT = "YYYY-MM-DD";   // the day the refresh ran
var DATA = [ /* one entry per calendar day */ ];
```

Each `DATA` entry is one **calendar day**, including days with no activity —
gaps are what give the terrain its shape, so do not omit empty days:

```js
{date:"2026-07-22", commits:2, prs:1, msgs:["…", "…"]}
```

| field | meaning |
| --- | --- |
| `date` | `YYYY-MM-DD` |
| `commits` | commits authored that day |
| `prs` | pull requests **merged** that day |
| `msgs` | up to ~5 commit/PR subjects, then a `"+N more updates"` tail |

## Procedure

1. Fetch every commit authored by `berkisodd` in `berkisodd/schedule`
   (paginate until exhausted) and every pull request with a `merged_at` date.
2. Bucket commits by author date and merged PRs by merge date, in UTC.
3. Emit one `DATA` row per day from the first commit date through today,
   inserting `commits: 0, prs: 0, msgs: []` for any day with no activity.
4. Replace the marked block, set `GENERATED_AT` to today, and republish the file
   to the artifact URL above.

## Checks before publishing

- The file parses — open it and confirm the terrain renders with no console errors.
- Header stats are self-derived; confirm `Total` matches the commit count you fetched.
- Days are contiguous with no missing dates.
