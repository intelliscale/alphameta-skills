# Multi-Leg / Combo Orders

Multi-leg orders execute multiple option legs simultaneously as a single atomic unit. Use when rolling options, spreads, straddles, or any strategy requiring all-or-nothing execution.

---

## When to Use Multi-Leg Orders

| Strategy | When to Use | Example |
|----------|-------------|---------|
| **Roll** | Close one expiry, open another (same strike or different) | Roll $440 Put May→Jun |
| **Vertical Spread** | Bull/Bear call/put spreads | Bull Call Spread |
| **Straddle/Strangle** | Volatility plays | ATM Straddle |
| **Iron Condor** | Range-bound outlook | Sell $430P/$450P, $440C/$460C |
| **Butterfly** | Fixed risk directional | 100/105/110 Call Butterfly |

---

## Leg Identifiers

| Code | Meaning | Action |
|------|---------|--------|
| `bto` | Buy to Open | Open new long position |
| `sto` | Sell to Open | Open new short position |
| `btc` | Buy to Close | Close existing short position |
| `stc` | Sell to Close | Close existing long position |

---

## Syntax

```bash
buy "<leg1> <ratio1> <occ1> <leg2> <ratio2> <occ2> ..." <qty> AF @ <net_price>
```

| Field | Meaning |
|-------|---------|
| `<ratio>` | Internal ratio per leg (e.g., 1 for even, 2 for butterfly body) |
| `<qty>` | External quantity multiplied by ratio = actual contracts |
| `AF` | Algo type (Adaptive Fast) |
| `<net_price>` | Net credit (+) or debit (-) per **base unit** (before × qty) |

**Note**: For simple rolls and vertical spreads, ratio is almost always `1:1`.
Ratio > 1 is used for unbalanced strategies like butterflies (ratio 1:2:1)
where one leg has more contracts than others.

**Examples**:

| Order | Internal Ratio | Qty | Actual Contracts |
|-------|----------------|-----|------------------|
| Straddle | 1:1 | 100 | 100 : 100 |
| Butterfly | 1:2:1 | 100 | 100 : 200 : 100 |
| Iron Condor | 1:1:1:1 | 50 | 50 : 50 : 50 : 50 |

**Note**: `buy` with negative ratio = sell. All multi-leg orders use `buy` command with quoted leg string.

---

## OCC Format Reminder

```
SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE
```

Strike: **price × 1000**, then pad to 8 digits.

| Strike | OCC Format | Example |
|--------|-----------|---------|
| $440 | 00440000 | `GLD260618P00440000` |
| $175 | 00175000 | `NVDA260501C00175000` |
| $17.50 | 00017500 | (wrong! this = $17.50) |

---

## Roll Examples

### Roll Put Forward (Same Strike)

**Scenario**: Short 8× GLD $440 Put (May 1) → Roll to June 18

```bash
# Internal ratio 1:1, external qty 8 → actual 8:8
# Net credit: $5.35/base unit (new premium - close premium)

buy "btc 1 GLD260501P00044000 sto 1 GLD260618P00044000" 8 AF @ 5.35
```

### Roll Put Down (Lower Strike)

**Scenario**: Short 8× GLD $440 Put → Roll to $430 Put (same expiry or different)

```bash
# Internal ratio 1:1, external qty 8 → actual 8:8
buy "btc 1 GLD260501P00044000 sto 1 GLD260501P00043000" 8 AF @ 2.50
```

### Roll Call Up (Higher Strike)

**Scenario**: Short 5× AAPL $150 Call → Roll to $160 Call

```bash
# Internal ratio 1:1, external qty 5 → actual 5:5
buy "btc 1 AAPL260515C00150000 sto 1 AAPL260515C00160000" 5 AF @ 3.00
```

---

## Spread Examples

### Bull Call Spread

```bash
# Buy 1 Call @ $170, Sell 1 Call @ $175 (same expiry)
# Internal ratio 1:1, external qty 1 → actual 1:1
buy "bto 1 AAPL260501C00170000 sto 1 AAPL260501C00175000" 1 AF @ 2.00
```

### Bear Put Spread

```bash
# Sell 1 Put @ $180, Buy 1 Put @ $170 (same expiry)
# Internal ratio 1:1, external qty 1 → actual 1:1
buy "sto 1 AAPL260515P00180000 bto 1 AAPL260515P00170000" 1 AF @ 3.50
```

### Iron Condor

```bash
# Short $380P/$390P spread + Short $450C/$460C spread
# Internal ratio 1:1:1:1, external qty 50 → actual 50:50:50:50
buy "sto 1 AAPL260515P00380000 btc 1 AAPL260515P00390000 sto 1 AAPL260515C00450000 btc 1 AAPL260515C00460000" 50 AF @ 1.50
```

### Straddle

```bash
# ATM Straddle (same strike, same expiry)
# Internal ratio 1:1, external qty 100 → actual 100:100
buy "bto 1 AAPL260501C00170000 bto 1 AAPL260501P00170000" 100 AF @ 8.00
```

### Strangle

```bash
# OTM Strangle (different strikes, same expiry)
# Internal ratio 1:1, external qty 100 → actual 100:100
buy "bto 1 AAPL260501C00175000 bto 1 AAPL260501P00165000" 100 AF @ 4.50
```

### Butterfly

```bash
# Butterfly: 1:2:1 ratio
# Internal: bto 1 : sto 2 : bto 1
# External qty 100 → actual: 100 : 200 : 100
buy "bto 1 AAPL260501C00170000 sto 2 AAPL260501C00175000 bto 1 AAPL260501C00180000" 100 AF @ 5.00
```

---

## Pricing Multi-Leg Orders

### Net Credit (You Receive)

```bash
# Spread nets you $2.00 credit per contract
buy "bto 1 AAPL260501C00170000 sto 1 AAPL260501C00175000" 2.00 AF
```

### Net Debit (You Pay)

```bash
# If net is negative, you pay (debit spread)
buy "bto 1 AAPL260501C00170000 bto 1 AAPL260501P00170000" -8.00 AF
# Negative price = debit (you pay)
```

### Midpoint Pricing

```bash
# Use AF (Adaptive Fast) for automatic price optimization
buy "btc 1 GLD260501P00044000 sto 1 GLD260618P00044000" 8 AF
```

---

## Common Patterns

### Pattern: Roll Near-Term to Long-Term

```bash
# Internal ratio 1:1, external qty N → actual N:N
buy "btc 1 <near_occ> sto 1 <far_occ>" <qty> AF @ <net_credit>
```

### Pattern: Close Position Entirely

```bash
# Close short position (no new position)
buy "btc 1 <occ>" <qty> MKT
```

### Pattern: Open Spread

```bash
# Open vertical spread (internal ratio 1:1)
buy "bto 1 <lower_strike_occ> sto 1 <higher_strike_occ>" <qty> AF @ <net_credit>
```

---

## Common Pitfalls

### 1. OCC Strike Format

Strike = **price × 1000**, then pad to 8 digits.

| Strike | Correct | Wrong (×100) |
|--------|---------|--------------|
| $440 | `00044000` | `00044000` |
| $175 | `00175000` | `00017500` |
| $17.50 | `00017500` | `00001750` |

### 2. Quantity Per Leg

Internal ratio vs external quantity. For an 8-contract roll:

```bash
# Correct: internal ratio 1:1, external qty 8 → actual 8:8
buy "btc 1 GLD260501P00044000 sto 1 GLD260618P00044000" 8 AF @ 5.35

# Wrong: internal ratio 8:4 → actual 8:4 (imbalanced!)
buy "btc 8 GLD260501P00044000 sto 4 GLD260618P00044000" 8 5.35 AF
```

### 3. Price = Net Per Base Unit

Price is the **net** credit/debit for the combo **per base unit** (before × external qty).

```bash
# $5.35 net credit PER BASE UNIT, not total
# External qty 8 × $5.35 = $4,280 total credit
buy "btc 1 GLD260501P00044000 sto 1 GLD260618P00044000" 8 AF @ 5.35
```

### 4. Preview Before Execution

Always preview to check margin impact:

```bash
buy "btc 1 GLD260501P00044000 sto 1 GLD260618P00044000" 8 AF @ 5.35 preview
```

---

## Algo Types

| Algo | When to Use |
|------|-------------|
| `AF` | Adaptive Fast - best for most orders, auto-optimizes price |
| `MKT` | Market order - use only when speed matters more than price |
| `MID` | Midpoint - use when you want to hit bid/ask midpoint |
| `limit` | Limit order - specify exact price |

---

## Troubleshooting

### "Failed: insufficient liquidity"

- Increase spread between bid/ask
- Try smaller quantity first
- Use `AF` algo instead of limit

### "Failed: margin exceeded"

- Calculate margin impact with `preview`
- Reduce position size
- Close other positions first

### Legs won't execute together

- Use COB (Complex Order Book) syntax
- Check that legs are on same exchange
- Verify OCC format is correct
