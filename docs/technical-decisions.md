# Technical Decisions & Trade-Offs — BackOption

## Decision 1: TradingView Lightweight Charts over D3.js or Chart.js

- **Context:** The application requires high-frequency rendering of thousands of historical candlesticks alongside multiple indicator overlay lines while maintaining interactive zooming and panning.
- **Decision:** Selected `lightweight-charts` by TradingView.
- **Trade-off:**
  - *Advantage:* Highly optimized HTML5 Canvas rendering pipeline capable of 60 FPS under continuous price streams; natively familiar UX for financial traders.
  - *Limitation:* Less flexible DOM styling compared to SVG-based libraries (e.g., D3.js). Custom drawing required implementing custom series primitives.

## Decision 2: Single-Page Application with Vite over Next.js SSR

- **Context:** The trading terminal is an interactive, authenticated application requiring persistent WebSocket sockets that must never be disrupted by SSR route transitions or server-side hydration cycles.
- **Decision:** Built as a client-rendered SPA using Vite 6 and React 18.
- **Trade-off:**
  - *Advantage:* Instantaneous local dev hot-module reloading, ultra-small build bundles, and direct uninhibited WebSocket lifecycle management without hydration mismatches.
  - *Limitation:* Minimal public SEO footprint, which is standard and expected for a behind-login trading terminal.

## Decision 3: Client-Side Mathematical Indicator Calculation

- **Context:** Deciding whether technical indicators (SMA, EMA, Bollinger Bands) should be calculated on the backend or client.
- **Decision:** Executed calculations client-side using `technicalindicators`.
- **Trade-off:**
  - *Advantage:* Zero backend latency penalty; users can customize indicator periods (e.g., changing SMA 14 to SMA 50) instantaneously without sending network roundtrips.
  - *Limitation:* Requires client CPU allocation; mitigated by keeping indicator arrays windowed to the active viewport.
