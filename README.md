
# OrderFlow Institutional Footprint Suite for NinjaTrader 8

An advanced experimental **NinjaTrader 8 order-flow and footprint analytics suite** designed for institutional-style market microstructure analysis.

The project includes a custom BarsType, normalized footprint data models, order-flow metrics, OBMM-inspired micro-trend detection, pin-bar wick-volume analysis, and a SharpDX-based visualizer for high-performance chart rendering.

<img width="2816" height="1536" alt="Gemini_Generated_Image_ialv7oialv7oialv" src="https://github.com/user-attachments/assets/1e5cd535-7eee-42fb-a41c-7e401aa88de6" />


Short youtube video:
[![OrderFlow Institutional Footprint Suite](https://img.youtube.com/vi/QQdtT8qe_So/hqdefault.jpg)](https://www.youtube.com/watch?v=QQdtT8qe_So)>

**Disclaimer:**  
> This project is for research, education, and experimental trading-system development only.  
> It is not financial advice, not an investment recommendation, and not a guaranteed trading strategy.  
> Trading futures, forex, equities, crypto, or any leveraged product involves substantial risk.

---

## Main Features

### Custom OrderFlow BarsType

The suite includes a custom NinjaTrader 8 BarsType for minute-based order-flow aggregation.

Key features:

- Tick-rule based volume classification
- Buy / sell / unclassified volume tracking
- Per-bar footprint dictionary
- Per-price-level volume statistics
- Cached POC and high-volume node metrics
- Out-of-order tick handling
- Session-aware state reset
- Compatibility-oriented V8-style data model

---

### Footprint Data Model

The system uses normalized, strongly typed footprint structures:

- `PriceLevelData`
- `FootprintBarData`
- `ImbalanceLevel`
- `ImbalanceStack`
- `FlowMetrics`
- `MetricsSettings`

These models decouple the calculation layer from the rendering layer.

---

### Metrics Engine

The metrics engine performs pure calculation without UI dependencies.

Implemented analytics include:

- Bar-level delta
- Delta percentage
- POC detection
- Max node volume
- Diagonal imbalance detection
- Stacked imbalance detection
- Footprint range metrics
- OBMM-inspired pressure metrics
- OBMM micro-trend scoring fields
- Pin-bar volume and wick-rejection fields

The design goal is to keep calculations modular, deterministic, and independent from SharpDX rendering.

---

## OBMM-Inspired Micro-Trend Logic

The suite includes an experimental micro-trend detector inspired by **Order Book Inspired Automated Market Making** concepts.

The signal uses a square-root impact model:

```text
R = kappa * |deltaRatio|^beta
````

Default parameters:

```text
beta  = 0.5
kappa = 0.05
```

The micro-trend detector looks for multiple consecutive bars with aligned order-flow pressure.

It evaluates:

* OBMM pressure score
* Directional delta consistency
* POC migration
* Pressure centroid alignment
* Consecutive same-direction confirmation

The visualizer displays the confirmed micro-trend as a subtle ribbon:

* Bullish micro-trend: cyan / blue ribbon
* Bearish micro-trend: magenta / purple ribbon
* Hidden automatically when the chart becomes too dense

***

## Pin-Bar Volume Analysis

The suite also includes pin-bar volume analysis.

The logic detects whether a candle has a pin-bar structure and then evaluates where volume was concentrated:

* Upper wick volume
* Lower wick volume
* Body volume
* Upper wick delta
* Lower wick delta
* Dominant wick side
* Rejection / absorption behavior

### Bullish Pin Bar

A bullish pin-bar signal may be detected when:

* The lower wick is large relative to the full candle range
* The candle body is relatively small
* The lower wick has dominant trading volume
* Price closes back higher into the candle range
* Sell pressure appears absorbed near the lower wick

### Bearish Pin Bar

A bearish pin-bar signal may be detected when:

* The upper wick is large relative to the full candle range
* The candle body is relatively small
* The upper wick has dominant trading volume
* Price closes back lower into the candle range
* Buy pressure appears trapped or absorbed near the upper wick

The visualizer marks these events with compact chart markers:

* Bullish pin-bar rejection: green / cyan marker near the lower wick
* Bearish pin-bar rejection: red marker near the upper wick
* Optional label at higher zoom levels, such as:

```text
PB L 72
PB U 68
```

Where:

```text
PB = Pin Bar
L  = Lower wick dominant
U  = Upper wick dominant
72 = Signal strength score
```

***

## SharpDX Visualizer

The visualizer is implemented as a NinjaTrader 8 indicator using SharpDX / Direct2D / DirectWrite rendering.

It supports:

* Footprint heatmap cells
* Bid / ask volume rendering
* POC markers
* Imbalance markers
* Stacked imbalance zones
* Reversal markers
* OBMM micro-trend ribbons
* Pin-bar wick-volume markers
* Profile visualization
* Adaptive density rendering
* Dark and light visual presets
* Reduced clutter at compressed zoom levels

The visualizer is designed to reduce cognitive load by automatically hiding or simplifying visual elements when the chart becomes too dense.

***

## Project Structure

```text
NinjaTrader 8/
└── bin/
    └── Custom/
        ├── AddOns/
        │   └── OrderFlowPro/
        │       ├── OrderFlowMinuteAdapter.cs
        │       ├── OrderFlowProCoreModels.cs
        │       └── OrderFlowProMetricsEngine.cs
        │
        ├── BarsTypes/
        │   └── OrderFlowMinuteBarsTypeV8Compat.cs
        │
        └── Indicators/
            ├── OrderFlowMinuteProVisualizer_NO_LEGACY_DOM.cs
            └── DOMBrickStandaloneOrderFlowV1.cs
```

Depending on the package version, the BarsType file may also appear under a custom no-conflict name such as:

```text
OrderFlowCustomMinuteBarsTypeV9.cs
```

***

## Core Files

### `OrderFlowMinuteBarsType.cs` / `OrderFlowMinuteBarsTypeV8Compat.cs`

Custom BarsType for building order-flow minute bars.

Responsibilities:

* Tick-rule volume classification
* Per-price footprint accumulation
* Bar-level flow dictionary
* Cached POC and max-node metrics
* Compatibility with visualizer and strategy modules

***

### `OrderFlowMinuteAdapter.cs`

Adapter layer for converting raw BarsType footprint data into normalized footprint models.

Responsibilities:

* Translate BarsType data into `FootprintBarData`
* Map price-level volume data into `PriceLevelData`
* Decouple legacy BarsType structures from modern metrics logic

***

### `OrderFlowProCoreModels.cs`

Strongly typed normalized data model layer.

Includes:

* `PriceLevelData`
* `FootprintBarData`
* `ImbalanceLevel`
* `ImbalanceStack`
* `FlowMetrics`
* `MetricsSettings`

***

### `OrderFlowProMetricsEngine.cs`

Pure calculation engine.

Responsibilities:

* POC calculation
* Delta metrics
* Diagonal imbalance detection
* Stacked imbalance detection
* OBMM-inspired pressure metrics
* Signal-ready metric output

This file does not perform rendering.

***

### `OrderFlowMinuteProVisualizer_NO_LEGACY_DOM.cs`

Main visual overlay indicator.

Responsibilities:

* SharpDX chart rendering
* Footprint display
* Adaptive visual density
* OBMM micro-trend visualization
* Pin-bar volume signal visualization
* Profile and signal overlays

***

## Installation

1. Close NinjaTrader 8 completely.

2. Copy the project folders into:

```text
Documents\NinjaTrader 8\bin\Custom\
```

3. Ensure the final folder structure matches:

```text
Documents\NinjaTrader 8\bin\Custom\AddOns\OrderFlowPro\
Documents\NinjaTrader 8\bin\Custom\BarsTypes\
Documents\NinjaTrader 8\bin\Custom\Indicators\
```

4. Open NinjaTrader 8.

5. Go to:

```text
New > NinjaScript Editor
```

6. Right-click inside the editor and select:

```text
Compile
```

7. If compilation succeeds, restart NinjaTrader 8 completely.

8. Open a chart and select the custom BarsType from the Data Series menu.

Example BarsType display names may include:

```text
OrderFlow Minute PRO V8 Compat
OrderFlow Custom Minute PRO V9
```

***

## Important Compatibility Notes

NinjaTrader compiles every `.cs` file under the `bin/Custom` directory.

If you keep duplicate older files, you may get errors such as:

```text
CS0101: namespace already contains a definition
CS0111: type already defines a member
CS0229: ambiguity between members
CS0246: type or namespace could not be found
```

Before compiling, make sure duplicate legacy files are removed or moved outside the NinjaTrader custom folder.

Common duplicate files to check:

```text
OFmin.cs
OFMinuteAdapter.cs
OrderFlowMinuteBarsType.cs
OrderFlowMinuteBarsTypeV8NoOutput duplicates
```

Only one BarsType should define:

```text
OrderFlowMinuteBarsTypeV8NoOutput
OrderFlowMinuteProOrderFlowDataV8NoOutput
OrderFlowMinuteProPriceLevelDataV8NoOutput
```

***

## Visual Design Philosophy

The visual layer is designed around three principles:

1. **Overview first**
   * Important pressure, trend, and reversal information should be visible quickly.

2. **Low cognitive load**
   * Signals are subtle and do not cover the full chart unnecessarily.

3. **Adaptive density**
   * Visual details automatically simplify or disappear when the chart is compressed.

***

## Research Concepts Used

The system contains experimental logic inspired by:

* Limit order book behavior
* Footprint delta analysis
* Volume-at-price distribution
* Square-root market impact models
* POC migration
* Wick-volume rejection
* Pin-bar absorption
* Micro-trend continuation pressure

The OBMM-style logic is not an AMM implementation inside NinjaTrader.  
It is used as an analytical inspiration for order-flow pressure and micro-trend detection.

***

## Limitations

* Tick-rule classification is an approximation.
* It is not true exchange bid/ask aggressor classification unless the data feed provides that information.
* Historical and real-time behavior may differ depending on data provider quality.
* The signals are analytical tools, not standalone trading systems.
* Performance depends on chart density, symbol liquidity, machine resources, and NinjaTrader rendering load.

***

## Recommended Usage

Use this suite as part of a broader discretionary or systematic order-flow workflow.

Suggested confirmations:

* Higher timeframe market structure
* Volume profile context
* Session VWAP / value area
* Liquidity zones
* DOM / resting liquidity
* Delta divergence
* Absorption and exhaustion
* Pin-bar wick-volume confirmation
* OBMM micro-trend ribbon alignment

***

## Development Status

Experimental / research-grade.

This repository is intended for advanced NinjaTrader 8 users, quantitative developers, and order-flow researchers.

Planned improvements may include:

* Fully parameterized UI controls for OBMM signals
* Fully parameterized pin-bar signal settings
* Strategy API outputs
* Additional signal export methods
* Better multi-timeframe support
* Performance profiling
* Unit-style validation utilities
* Cleaner modular separation of render components

***

## Requirements

* NinjaTrader 8
* Windows
* C# / NinjaScript
* SharpDX support through NinjaTrader
* Market data feed with sufficient tick data quality

***

## Disclaimer

This software is provided for educational and research purposes only.

It does not provide financial advice, investment advice, or trading recommendations.  
Use at your own risk. The author assumes no responsibility for trading losses, data-feed issues, platform instability, or incorrect interpretation of signals.

Trading involves significant risk and may not be suitable for all users.

***

## License

Choose a license appropriate for your intended distribution.

Suggested options:

* MIT License for open-source permissive usage
* GPLv3 if derivative works should remain open-source
* Proprietary / private license if distribution is restricted

***

## Author

https://www.linkedin.com/in/elod/
```
