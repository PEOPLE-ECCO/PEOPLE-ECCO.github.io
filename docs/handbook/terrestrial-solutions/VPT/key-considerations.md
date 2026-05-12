# Key Considerations for Non-Expert Users

This page will explain four key considerations necessary to run the VPT solution:

1. Which recovery target to use.
2. Which spectral index to use.
3. Which algorithm to use.
4. How to interpret the outputs.

You do not need to be a remote sensing specialist to make these decisions. A practical approach is to start with clear monitoring goals, choose simple settings first, and refine only if needed.

## 1. Choosing a Recovery Target

Your recovery target is the condition you compare your site against.

### Option A: Historical target

Use this when:

- You have good pre-disturbance records for the same site.
- You want to measure "return toward past conditions".
- There are no suitable reference areas nearby.

Benefits:

- Easy to explain.
- Site-specific baseline.

Limitations:

- Past conditions may no longer be realistic under current climate and land-use pressures.

### Option B: Reference target

Use this when:

- You have nearby areas in stable, desirable condition.
- You want to compare with current landscape conditions.
- The goal is functional recovery, not necessarily exact historical return.

Benefits:

- More realistic for dynamic landscapes.
- Often better aligned with restoration planning.

Limitations:

- Quality depends on choosing ecologically appropriate reference sites.

### Quick decision rule

- If your project goal is "return to previous state", start with a historical target (if there is imagery that goes back to a pre-disturbance date).
- If your project goal is "recover to a healthy present-day state", start with a reference target.
- If unsure, test both and compare whether conclusions are consistent.

## 2. Choosing a Spectral Index

Different indices respond to different vegetation properties. Choose the one that best matches your ecological question.

### Common starting choices

- **NDVI**: good for general vegetation greenness and early recovery signals.
- **NBR**: useful when disturbance and woody vegetation recovery are central (for example fire-affected areas).
- **SAVI**: useful in areas with sparse vegetation or exposed soil, because it reduces soil-background effects.
- **TCW**: useful when canopy or vegetation moisture conditions are important, and often helpful for tracking structural recovery.

These are all reasonable options to test, and SAVI and TCW are good additional indices to include when you want to compare results across tools.

### Quick comparison table

| Index | Best used when | Main strength | Watch out for |
| --- | --- | --- | --- |
| NDVI | You want a simple first look at vegetation greenness | Easy to interpret and widely used | Can saturate in dense vegetation and be influenced by soil/background effects |
| NBR | Disturbance and recovery (especially woody vegetation) are central to your question | Strong disturbance/recovery signal in many landscapes | Interpretation can vary by ecosystem type and disturbance context |
| SAVI | Vegetation is sparse and bare soil is visible | Reduces soil-background influence compared with NDVI | Less intuitive for some users than NDVI and still depends on data quality |
| TCW | Moisture condition and canopy/structural recovery are important | Sensitive to moisture-related vegetation changes | Can be harder to explain without context and should be interpreted with local knowledge |

### Quick index decision rule

- Choose **NDVI** for a simple first look at overall greenness.
- Choose **NBR** when woody vegetation regrowth is central to your question.
- Choose **SAVI** where bare soil is common and may bias greenness indices.
- Choose **TCW** when moisture-related vegetation condition is a key concern.
- If possible, run at least two indices and check whether they tell a consistent story.

## 3. Choosing the Algorithm

In this VPT setup, both algorithms produce the same two outputs: **R80P** and **DeltaIR**. The key difference is the input time scale.

- **Spectral recovery algorithm** uses annual composites.
- **Seasonal Sen's slope algorithm** uses monthly time series.

### When to prefer each

- Prefer **spectral recovery (annual)** when:
	- You have yearly composites.
	- You want a stable year-to-year summary.
	- You want a simpler first-pass analysis.

- Prefer **seasonal Sen's slope (monthly)** when:
	- You have monthly data.
	- Seasonal behavior matters for your site.
	- You need a trend estimate that is robust to short-term noise.

### Recommended practice

If data availability allows, run both. Matching results increase confidence. Differences can highlight where seasonal effects or data quality deserve closer review.

## 4. Interpreting the Outputs

### R80P

R80P shows whether a pixel/site is close to reaching 80% of its recovery target.

- Higher R80P generally means closer to target.
- Lower R80P suggests more recovery is still needed.

### DeltaIR

DeltaIR shows change in index value over the monitoring period.

- Positive DeltaIR generally indicates improvement.
- Near-zero DeltaIR suggests little net change.
- Negative DeltaIR suggests decline or setback.

### Interpreting both together

- **High R80P + positive DeltaIR**: strong and continuing recovery.
- **High R80P + near-zero DeltaIR**: close to target, now stabilizing.
- **Low R80P + positive DeltaIR**: improving, but not yet near target.
- **Low R80P + negative DeltaIR**: priority area for investigation or intervention.

## 5. A Simple Decision Workflow

1. Define your restoration question in plain language.
2. Choose a recovery target that matches that question.
3. Start with one index (NDVI, NBR, SAVI, or TCW), then add another if needed.
4. Choose algorithm based on available time scale (annual vs monthly).
5. Interpret R80P and DeltaIR together, not separately.
6. Flag areas where methods disagree for follow-up checks.

## 6. Common Pitfalls to Avoid

- Assuming high vegetation greenness always means ecological success.
- Using a reference site that is not ecologically comparable.
- Ignoring uncertainty caused by clouds, mixed pixels, or short time windows.
