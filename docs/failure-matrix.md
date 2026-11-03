# COMPOSITOR WINDOWS — FINAL FAILURE MATRIX (exact HEAD `3a90d51210df`)
Definitive full noninteractive CTest run. 1021/1061 PASS (96%), 40 FAIL.
Every nonpassing test identity is listed below with its classified root cause.
`imports.ui` now PASSES (constructed EXIF/profile fixtures resolved the gap).

## Category A — BLOCKED: maintainer-published frozen fixtures unavailable (22)
All 22 `subject_input.*` cases. The frozen fixture corpus (`protocol.json`,
`generate_fixtures.py`, `fixtures-02/`) is hash-pinned by the test runner to
the maintainer's unpublished copies. Searched: upstream main, true-upstream
IAmTheBlurr repo, every branch of both forks, GitHub code search, and full
git history — never published. Per mission rules, these are NOT EXECUTED /
BLOCKED, never counted as passes.

| # | Test identity | Root cause | Next step |
|---|---|---|---|
| 1–22 | `subject_input.{identity,down_even,down_odd,up_small,mixed_wide,mixed_tall,thin_wide,thin_tall,alpha_down,alpha_up,constant,transparent,padded_stride,cancel_before,cancel_validation,cancel_resample,budget_reject,invalid_premultiplication,invalid_stride,source_unchanged,maximum_axis_memory,dimension_preflight}` | `run-case.py` asserts `manifest['protocol_sha256']==sha(BASE/'protocol.json')` — the protocol and generator are not in the repository | Maintainer must publish the frozen fixture corpus |

## Category B — Documented upstream discrepancies (6)
These are the same six retained in the upstream KNOWN-ISSUES.md.

| # | Test identity | Failure signature | Root cause | Next step |
|---|---|---|---|---|
| 23 | `source_selection.cursor` | Thumbnail cursor behavior differs from the frozen source expectation | Windows cursor rendering path diverges from the pinned Swift source | Upstream product decision |
| 24 | `source_selection.m_key` | `expect line392/394/400` — the pinned source expects repeated M presses to cycle marquee shape (rect↔ellipse) and remember it across tool switches; the Windows M key switches tools without cycling | **Genuine product behaviour gap**: the Windows M key binding is `selectTool(Marquee)` without shape cycling | Implement M-key shape cycling to match frozen source lines 392–400 |
| 25 | `source_selection.l_key` | `expect line457/459/461/465` — the pinned source expects repeated L presses to cycle lasso kind (freehand↔polygonal) and remember it | Same class as m_key | Implement L-key kind cycling |
| 26 | `layer_panel.source_cursor_conflict` | Frozen source route produces a duplicate cursor action | Test-oracle conflict | Upstream reconciliation |
| 27 | `parity.gaussian_source_test_conflict` | "Mac reference execution remains required" | **BLOCKED** on Mac reference capture (reference/README.md) | Execute the Mac reference workflow |
| 28 | `cancellation.adjustment_contracts` | "Cancellation adapter changed exact adjustment pixels" | Frozen Levels cancellation reference contains a repaired defect (upstream-documented stale oracle) | Upstream must update the frozen reference |

## Category C — Deterministic host-sensitivity failures (10)
Reproduce consistently on this 8 GB Windows 10 host. Do NOT appear in the
development host's recorded full-suite runs. Root causes investigated by
direct code analysis and controlled reruns.

| # | Test identity | Failure signature | Root cause | Next step |
|---|---|---|---|---|
| 29 | `layer_panel.visibility_swipe` | "press must preview first eye in open transaction" | Eye-press gesture path doesn't fire; `LayerPanel.cpp` changed in upstream bc97800 (after last upstream full-suite record) | Dev-host confirmation |
| 30 | `drop_sequence.closed_captured_destination_skips_images` | "missing captured destination skips images while project URL still opens" | Drop-queue closed-destination handling changed in upstream b8bd36e (2026-10-23) | Dev-host confirmation |
| 31 | `project_open_batch.welcome_project_then_image_skipped` | "later image targeting removed welcome is skipped before creating an import queue" | Recent-projects handling changed in upstream b7c79a5 (2026-10-24) | Dev-host confirmation |
| 32 | `project_open_batch.recent_save_record` | Recent-save record assertion | Same upstream commit b7c79a5 as #31 | Dev-host confirmation |
| 33 | `retouch_sparse.smudge_preview` | "sparse warp live pixels differ from dense preview at display scale" | Sparse warp CPU path produces different pixels from dense preview on this GPU/driver | Dev-host confirmation or pixel-diff analysis |
| 34 | `retouch_sparse.liquify_preview` | Same sparse-vs-dense pixel equivalence | Same as #33 | Same as #33 |
| 35 | `palette_swatches.stroke_disabled` | "stroke_disabled palette control disabled during brush gesture" | Palette control disable-during-gesture doesn't fire on this host | Dev-host confirmation |
| 36 | `palette_accessibility.nonfinite_rejects_set` | "Nonfinite writes preserve hue" | Accessibility hue-preservation check fails on this host | Dev-host confirmation |
| 37 | `ui.pending_interactions` | "appearance_locked_for_captured_brush: Brush did not lock appearance" (17/18 subcases pass) | Brush gesture doesn't lock appearance controls on this host | Dev-host confirmation |
| 38 | `update_window.release_later_skip` | Deferred update notice timing — Later button doesn't close the notice within the harness timeout | Timing-sensitive under offscreen rendering | Test-harness timing tolerance |

## Category D — Load-sensitive failures (2)
These pass in isolation but fail under full-suite load on this 8 GB host.

| # | Test identity | Failure signature | Root cause | Next step |
|---|---|---|---|---|
| 39 | `brush_cpu_batch.real_800px_two_strokes` | 250-second timeout | Frozen brush benchmark exceeds timeout under full-suite memory pressure on this 8 GB host | Increase timeout or run on a machine with more RAM |
| 40 | `remembered_filter.background_remembering` | Passes standalone, fails under suite load | Timing-sensitive filter remembered-state assertion | Test-harness timing tolerance |

## Summary
| Category | Count | Classification |
|---|---|---|
| A: BLOCKED (fixtures) | 22 | NOT EXECUTED — maintainer must publish |
| B: Upstream discrepancies | 6 | 2 product gaps (M/L cycling), 2 test-oracle conflicts, 1 Mac-reference-blocked, 1 stale oracle |
| C: Deterministic host-sensitivity | 10 | Needs development-host confirmation |
| D: Load-sensitive | 2 | Pass in isolation; 8 GB host limitation |
| **Total** | **40** | **22 BLOCKED + 18 FAIL** |
