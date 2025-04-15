
#  Bitcoin-TrustLayer: A Escrow System for Secure Peer-to-Peer Transactions

TrustBridge is a decentralized escrow and trust protocol that facilitates secure and transparent peer-to-peer (P2P) transactions on the [Stacks blockchain](https://www.stacks.co/). It leverages smart contracts to manage deals, payments, and reputation scoring, enabling users to transact with greater confidence and reduced risk.

## Overview

TrustBridge allows two parties to:

- Initiate a deal with an escrowed amount.
- Complete the payment only upon confirmation.
- Rate their counterparties to build on-chain trust.
- Query public trust scores for informed decisions.

The contract handles dispute reduction through escrow, and incentivizes good behavior via a reputation system.

---

## 🛠 Features

- 🔐 **Escrow Mechanism** – Funds are locked until deal completion.
- 👥 **P2P Deal Management** – Initiate and track deals between users.
- 🧾 **Reputation System** – Participants rate each other post-deal.
- 🔍 **Read-Only Queries** – Access deal/payment/trust info on-chain.
- ❌ **Validation & Error Handling** – Extensive checks to ensure deal integrity.

---

## 📦 Contract Components

### 🔁 Maps (Storage)

- `payments`: Stores payment records for each transaction.
- `deals`: Tracks each escrowed deal and its metadata.
- `trust-profiles`: Accumulates trust scores per user.

### 🔢 Data Variables

- `payment-id-counter`: Counter for uniquely identifying payments.
- `deal-counter`: Counter for uniquely identifying deals.

### Constants

Error constants (`ERR-*`) handle validation failures (e.g., unauthorized access, invalid users, zero-value transactions).

---

##  Functions

###  Public Functions

#### `initiate-deal(counterparty, value)`
Initiates a new escrow-based deal with a counterparty.

- Funds are held in escrow.
-  Validates counterparty and value.
-  Adds entries to `payments` and `deals`.

Returns: `(ok deal-id)` or `(err ...)`

---

#### `complete-payment(payment-id)`
Completes the escrowed payment. Only the **sender** can call this.

- Transfers STX from sender to recipient.
- Marks the payment as complete.

Returns: `(ok true)` or `(err ...)`

---

#### `rate-counterparty(deal-id, rating)`
Allows the **counterparty** to rate the deal initiator.

- 🧠 Builds a trust profile for each user.
- 🔢 Updates cumulative score and deal count.
- ✨ Stores individual deal ratings.

Returns: `(ok true)` or `(err ...)`

---

#### `get-trust-profile(address)`
Fetches the trust profile (total score and number of ratings) for a given user.

Returns:
```clojure
{ cumulative-score: uint, deal-count: uint }
```

---

#### `get-payment-info(payment-id)`
Returns payment details for a given payment ID.

---

#### `get-deal-info(deal-id)`
Returns deal metadata for a given deal ID.

---

###  Private Functions

#### `validate-counterparty(counterparty)`
Ensures a user is not initiating a deal with themselves or the admin.

---

#### `validate-deal-id(deal-id)`
Checks if a deal ID is valid (greater than 0 and less than current counter).

---

#### `is-valid-user(recipient)`
Confirms that the recipient is not the sender or admin.

---

##  Example Flow

1. **Alice initiates a deal** with Bob for 100 STX.
2. The deal is stored in the smart contract, funds held in escrow.
3. Once conditions are met, **Alice completes the payment**.
4. **Bob rates Alice**, updating her trust profile.

---

## Errors

| Error Code | Meaning |
|------------|---------|
| `ERR-NO-AUTH (u1)` | Unauthorized payment completion attempt |
| `ERR-LOW-VALUE (u2)` | Deal amount must be > 0 |
| `ERR-INVALID-USER (u3)` | Invalid counterparty (e.g. self or admin) |
| `ERR-NO-PAYMENT (u4)` | Payment not found |
| `ERR-NOT-AUTHORIZED (u100)` | Only the deal counterparty can rate |
| `ERR-ZERO-AMOUNT (u101)` | Payment amount is zero |
| `ERR-SELF-DEAL (u102)` | Cannot create deal with oneself |
| `ERR-DEAL-NOT-EXIST (u103)` | Deal doesn't exist |
| `ERR-BAD-RATING (u104)` | Rating must be greater than 0 |
| `ERR-INVALID-DEAL-ID (u105)` | Deal ID out of valid range |

---

## Example Usage

### Initiate Deal

```clarity
(initiate-deal 'SP3ABC...XYZ u100)
```

### Complete Payment

```clarity
(complete-payment u1)
```

### Rate Counterparty

```clarity
(rate-counterparty u0 u5)
```

### Get Trust Profile

```clarity
(get-trust-profile 'SP3ABC...XYZ)
```

---

## Security Considerations

- Prevents self-deals and admin impersonation.
- Funds only released upon user confirmation.
- No external admin authority over deals/payments.
- Users can only rate deals they're part of.

---

## Future Enhancements

- 🧑‍⚖️ **Dispute resolution layer**
- ⛓ **Cross-contract integrations (e.g. NFT escrow)**
- 💬 **Off-chain metadata integration**
- 📊 **Weighted trust scores (e.g., decay, context-aware)**

---
