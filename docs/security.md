# Reliability & Security Architecture — BackOption

## 1. Network Failure Recovery & Exponential Backoff

WebSocket streams are prone to transient packet loss and proxy timeouts on public mobile connections. The client implements an automated reconnection state machine:

```
[CONNECTED]
    │ (Connection drop detected)
    ▼
[RECONNECTING - Attempt 1 (100ms)]
    │ (Fail)
    ▼
[RECONNECTING - Attempt 2 (250ms)]
    │ (Fail)
    ▼
[RECONNECTING - Attempt N (Exponential Backoff with Jitter up to 10s)]
    │ (Success)
    ▼
[SYNCING EPOCH STATE]
    │ (Authoritative candle backfill)
    ▼
[CONNECTED]
```

## 2. Order Execution & Client-Side Risk Verification

Before transmitting an order:
1. **Balance & Stake Validation:** Zod schemas verify that order amounts are positive numbers within dynamic minimum and maximum broker risk limits.
2. **Payout Timestamp Locking:** The payout percentage is stamped with a server-signed quotation ID to protect against payout manipulation during market volatility.
3. **Double-Submit Prevention:** Financial action buttons are locked with optimistic pending states and debounce barriers to eliminate accidental double clicks.

## 3. Sandboxed Paper Trading Simulation

To allow risk-free training, the simulated execution engine runs in an isolated state machine that mirrors production transaction flows without initiating live broker settlement requests.
