# Walmart Global Tech — Top 5 System Design Problems

These are the most frequently asked system design problems at Walmart Global Tech for senior/staff software engineer roles, ranked by evidence from interview reports, engineering blogs, and prep guides.

---

## Problem 1 — Real-Time Inventory Management System
**File:** `walmart-inventory-system-design.html` ✅ **Already generated**

**Brief:**
Design a system that tracks product inventory levels in real-time across Walmart's 10,000+ stores and warehouses. Every sale, return, restock, and damage event must update stock counts. The system must prevent overselling during concurrent checkouts and trigger restocking alerts when stock falls below thresholds.

**Why Walmart asks this:**
Inventory is Walmart's most business-critical system. Out-of-sync inventory costs millions in oversells, cancelled orders, and poor customer experience. Black Friday makes this a distributed systems stress test.

**Key themes to hit:**
- Strong consistency for reservations vs eventual consistency for reads
- Redis atomic ops to prevent overselling
- Kafka event-driven pipeline
- Sharding by location_id
- Offline-first POS behavior during network outages
- Black Friday peak load handling (10x)

---

## Problem 2 — High-Availability Checkout & Payment System
**File:** `walmart-checkout-payment-system-design.html` ⬅ **Generate this**

**Brief:**
Design Walmart's checkout and payment processing system that handles millions of transactions per day across in-store POS terminals and the e-commerce platform. The system must integrate with payment gateways (Visa, Mastercard, PayPal), handle retries and idempotency, maintain an immutable payment ledger, and process settlements and refunds. PCI compliance is required.

**Why Walmart asks this:**
Payment processing is irreversible and high-stakes. A double-charge or missed payment directly harms customers and violates regulations. Walmart processes billions in payments — the system must be both highly available and exactly-once.

**Key themes to hit:**
- Idempotency on all payment mutations (no double-charges on retry)
- Immutable ledger (append-only, never UPDATE a payment record)
- Payment state machine: INITIATED → AUTHORIZED → CAPTURED → SETTLED → REFUNDED
- Integration with external payment gateways (async, with timeouts and retries)
- PCI compliance: tokenization, no raw card data storage
- Saga pattern for distributed transaction across inventory + payment + order
- Settlement batch jobs (end-of-day reconciliation)
- Strong consistency for authorization, eventual for reporting

---

## Problem 3 — Product Search System
**File:** `walmart-product-search-system-design.html` ⬅ **Generate this**

**Brief:**
Design Walmart's product search system that indexes billions of product listings and serves search queries with low latency. The system must support keyword search, faceted filtering (category, price range, brand, rating), relevance ranking, and autocomplete/typeahead. Search results must reflect real-time inventory availability and price changes.

**Why Walmart asks this:**
Search drives the majority of Walmart's e-commerce revenue. A slow or irrelevant search experience directly reduces conversion. The scale (billions of products, millions of concurrent users) makes this a genuine distributed systems challenge.

**Key themes to hit:**
- Elasticsearch / inverted index for full-text search
- Separate indexing pipeline from query path
- Faceted filtering with aggregations
- Relevance ranking: TF-IDF + behavioral signals (click-through rate, purchase rate)
- Typeahead with trie or Redis sorted sets
- Near-real-time index updates when price or inventory changes (Kafka → index update consumer)
- Search result caching for popular queries
- A/B testing framework for ranking algorithm changes
- Read-heavy system: cache aggressively, replicate search nodes

---

## Problem 4 — Fraud Detection System
**File:** `walmart-fraud-detection-system-design.html` ⬅ **Generate this**

**Brief:**
Design a real-time fraud detection system for Walmart that evaluates every transaction (payment, return, account creation, coupon usage) for fraudulent signals. The system must make accept/reject decisions in under 100ms, learn from false positives/negatives, and provide explainability for rejected transactions.

**Why Walmart asks this:**
Retail fraud (return fraud, payment fraud, coupon abuse, account takeovers) costs Walmart billions annually. The system must be fast enough to not impact checkout UX, accurate enough to not block legitimate customers, and auditable for dispute resolution.

**Key themes to hit:**
- Online inference path (synchronous, <100ms) vs offline batch training (async)
- Feature store: pre-computed risk signals per user, device, location, time-of-day
- Rule engine for hard blocks (known stolen cards, velocity checks) — fast, no ML needed
- ML scoring layer for probabilistic signals (gradient boosting or neural net)
- Feedback loop: disputed decisions feed back to retrain models
- Explainability: every rejection must log which rules/features triggered it
- Shadow mode: run new model in parallel before promoting to production
- Separation of online inference service from offline training pipeline

---

## Problem 5 — Notification System (SMS / Push / Email at Scale)
**File:** `walmart-notification-system-design.html` ⬅ **Generate this**

**Brief:**
Design Walmart's notification delivery system that sends order confirmations, shipping updates, promotional offers, low-stock alerts, and price drop notifications across SMS, push notifications, and email. The system must handle 100M+ notifications per day, respect user preferences and opt-outs, and guarantee delivery without spamming users.

**Why Walmart asks this:**
Walmart sends notifications across every customer touchpoint — checkout, delivery, returns, promotions. At 100M+ DAU, even a bug in notification deduplication can result in millions of duplicate messages, causing customer complaints and regulatory risk.

**Key themes to hit:**
- Multi-channel fan-out (SMS via Twilio/SNS, push via APNS/FCM, email via SES)
- User preference service: channel opt-ins, quiet hours, frequency caps
- Deduplication: idempotency key per notification event to prevent duplicates
- Priority tiers: transactional (order confirm) > operational (low stock alert) > promotional
- Rate limiting per user per channel per time window
- Dead letter queue for failed deliveries + retry with exponential backoff
- Template service: personalized content rendering before dispatch
- Delivery tracking and read receipts where supported
- Kafka as the central event bus — producers publish notification events, consumers dispatch per channel
