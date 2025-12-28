# Quinn-Pay

Fork of [Quinn](https://github.com/quinn-rs/quinn) with native PAYMENT frame support.

## What's Added

**PAYMENT frame (type 0x402)** - Transport-layer payment authorization tied to QUIC streams.

### Frame Structure

```rust
pub struct Payment {
    pub stream_id: StreamId,
    pub amount: u64,
    pub payer: [u8; 32],       // Ed25519 public key
    pub token: [u8; 32],       // SPL token mint
    pub recipient: [u8; 32],   // Ed25519 public key
    pub timestamp: u64,
    pub nonce: u64,
    pub signature: [u8; 64],   // Ed25519 over SHA256(all fields)
}
```

### Transport Parameters

- `payment_supported: bool` - Server capability flag
- `accepted_tokens: Vec<[u8; 32]>` - Accepted token mints
- `payment_recipient: Option<[u8; 32]>` - Default recipient

### API (Conceptual)

```rust
// Server receives stream with payment
let payment = recv_stream.payment();
payment.verify_signature()?;

// Client sends stream with payment
send_stream.attach_payment(payment);
```

## Connection to x402

Similar goal as [x402](https://github.com/coinbase/x402) (activating HTTP 402), but at the transport layer instead of application layer. Payment and data delivery are atomic - no additional round trips.

## For Everything Else

See [Quinn documentation](https://github.com/quinn-rs/quinn) - all standard QUIC features unchanged.

## Status

🚧 Experimental - not production ready

## License

MIT OR Apache-2.0 (same as Quinn)
