# Readiness

A personal readiness score calculator that combines multiple health signals to provide guidance on training readiness and recovery status.

## Overview

Readiness compares six key health metrics against your own personal baseline to generate a single readiness score (0-10) that falls into one of four categories:

- **0–2 "Recover"** – Rest or very light movement. Signals point to incomplete recovery.
- **3–4 "Pace Yourself"** – Keep it easy. Technique work, a walk, or a session well short of failure.
- **5–7 "Ready"** – Normal training is reasonable. Nothing is flagging unusually.
- **8–10 "Go For It"** – Everything is at or above baseline. A harder session is well supported.

## Metrics

The score is calculated from up to six health signals:

| Metric | Unit | Weight | Notes |
|--------|------|--------|-------|
| Heart Rate Variability (HRV) | ms | 35% | Log-transformed; higher is better |
| Resting Heart Rate (RHR) | bpm | 25% | Lower is better |
| Sleep | hours | 20% | Capped at +1.5 (rebound, not surplus) |
| Respiratory Rate | br/min | 10% | Only raised values count against you |
| Wrist Temperature | °C | 5% | Only raised values count against you |
| Training Load | min/day | 5% | 7-day mean; higher is worse |

## How It Works

### Baseline Comparison
Each signal is compared with your own trailing baseline—every value in the series except the most recent:

- **Standard metrics** (RHR, sleep, training load): Z-score = (today − mean) / SD
- **Log-transformed metrics** (HRV): Log-normalized before z-score calculation
- **One-directional metrics** (respiratory rate, temperature): Only upward deviation counts against you

### Score Calculation
1. Each metric produces a z-score (−2.5 to +2.5)
2. Z-scores are weighted by the metric's importance
3. Weights are renormalized if any metric is missing
4. Final score: `5 + 2.0 × (weighted average z-score)`, clamped to 0–10

### Data Requirements
- Minimum 8 readings per metric for it to be included
- Metrics with fewer than 8 readings are excluded and other weights renormalized
- The app gracefully handles missing data

## Usage

### Data Format

Data should be provided as a JSON object with metric keys and comma/space/newline-separated values:

```json
{
  "date": "2024-01-15",
  "hrv": "52.2, 57.5, 41.9, ...",
  "rhr": "53, 52, 49, ...",
  "slp": "6.15, 7.38, 5.28, ...",
  "rr": "15.65, 16.5, 15.84, ...",
  "tmp": "36.2, 36.1, 36.3, ...",
  "exm": "45, 50, 30, ..."
}
```

### Delivery Methods

1. **Hash encoding** – Data is passed as a base64-encoded JSON string in the URL hash
2. **Inline data** – The Shortcut can replace the `/*__DATA__*/null/*__END__*/` token with inline JSON
3. **Sample data** – If no data is provided, sample data is displayed

## Features

- **Responsive design** – Optimized for iOS and mobile devices
- **Dark mode support** – Automatically adapts to system color scheme
- **Accessibility** – ARIA labels, keyboard navigation, focus indicators
- **Visual feedback** – Dial gauge, sparkline charts, and detailed breakdowns
- **Transparency** – Info buttons explain how each metric contributes to the score

## Technical Details

### Styling
- System font stack for native feel: `-apple-system, BlinkMacSystemFont, "SF Pro Display"`
- CSS custom properties for light/dark theming
- Accessible color palette with sufficient contrast
- Safe area support for notched devices

### Architecture
- Self-contained HTML file with embedded JavaScript (no dependencies)
- Numeric formatting with tabular numerals for clarity
- SVG dial visualization with animated stroke
- Sparkline charts for each metric's history

## Disclaimer

- **Heuristic, not validated** – These weights are not scientifically validated.
- **For informational use only** – Not a medical device and not a diagnosis.
- **Trust the band, not the decimal** – Confidence varies based on number of signals.
- **If you feel unwell, that outranks the number.**

## Integration

This HTML file can be used standalone or integrated with the Shortcuts app on iOS/macOS to automate data collection from health apps.

### Shortcut Integration
The included Shortcut:
1. Queries HealthKit for the six metrics
2. Formats the data as JSON
3. Encodes it into the URL
4. Opens this HTML file with the data hash

---

**Last Updated:** Generated from readiness.html  
**Confidence Levels:** High (5+ signals), Moderate (4 signals), Low (3 or fewer signals)
