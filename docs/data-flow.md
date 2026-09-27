# Data Flow & Indicator Lifecycle — BackOption

## 1. Tick Ingestion to Canvas Paint Flow

```mermaid
flowchart TD
    subgraph Network["Network Layer"]
        WSServer["Broker WebSocket Endpoint"]
        WSClient["Client WS Manager"]
    end

    subgraph StateTier["State & Calculation Layer"]
        RingBuffer["Ring Buffer"]
        CandleAggregator["Candle Aggregator"]
        MathEngine["Technical Indicators Engine"]
    end

    subgraph RenderTier["Browser UI Tier"]
        RAF["requestAnimationFrame Scheduler"]
        ChartSeries["Candlestick Series (Canvas)"]
        OverlaySeries["Line Series Overlays (Canvas)"]
        DOMWidgets["DOM Price Displays"]
    end

    WSServer -->|JSON / Binary Tick| WSClient
    WSClient --> RingBuffer
    RingBuffer --> CandleAggregator
    CandleAggregator --> MathEngine

    CandleAggregator --> RAF
    MathEngine --> RAF

    RAF --> ChartSeries
    RAF --> OverlaySeries
    RAF --> DOMWidgets
```

## 2. Multi-Timeframe Switching Lifecycle

When a user switches from 1-minute to 1-hour candles:
1. Active candlestick series is preserved while a background fetch retrieves historical batch OHLCV data.
2. Lightweight Charts replaces historical coordinate buffers in a single pass.
3. The WebSocket pipeline reconfigures the client aggregator to group subsequent live ticks according to the new 3600-second epoch boundary.
4. Indicators recompute over the new timeframe data without UI lag.
