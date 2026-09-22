# Riskora

**Payment safety before you pay.**

Riskora is an AI-assisted payment-safety and investigation prototype that brings together transparent rule-based signals, machine-learning scoring, behavioral context, payment-message context, and optional community feedback before a user proceeds with a payment.

The product is designed as a **human decision-support layer**. It does not claim to prove fraud, predict fraud with certainty, or automatically block a payment.

> **Prototype / hackathon project:** Riskora is not a production financial-security service and should not be treated as a guarantee against scams or fraud.

---

## 1. What Riskora Does

Riskora provides two complementary experiences:

### AI Financial Guardian

A consumer-facing pre-payment safety check. The user enters the information they actually know about a payment, and Guardian combines the available signals into a simple result:

- **READY TO REVIEW** — no meaningful elevated signal was identified from the available information.
- **PAUSE & CHECK** — one or more meaningful warning signals need verification.
- **STOP & VERIFY** — multiple or stronger signals indicate that the user should verify before continuing.

Guardian explains **why** it reached the state and gives practical next steps instead of showing a raw ML/dashboard-style result.

### Analyst Mode / Test & Investigation

A technical workflow for developers, judges, and investigators. It exposes the underlying transaction, rule-engine, ML, payment/order, feature-source, and Test Mode information needed to inspect the prototype.

The technical investigation workflow is intentionally separate from the consumer Guardian experience.

---

## 2. Core Guardian Pipeline

```text
User payment information
        |
        v
Input validation
        |
        +--------------------+
        |                    |
        v                    v
Rule Engine             ML Inference
        |                    |
        +---------+----------+
                  |
                  v
          Behavioral Analysis
                  |
                  v
           Context Analysis
                  |
                  v
          Community Signals
                  |
                  v
          Signal Aggregation
                  |
                  v
        Guardian Explanation
                  |
                  v
       READY / PAUSE / STOP
```

Riskora keeps the source results available for technical investigation while presenting a simpler human-readable explanation in Guardian.

---

## 3. Guardian Signals

### Rule-based signals

The existing rule engine can identify signals such as:

- Multiple failed attempts
- New-customer status
- Unusual transaction amount compared with available history
- IP and billing-country mismatch
- Very new account
- Large first-time purchase
- Large transaction combined with failed attempts

The rule engine produces its existing risk score, risk band, and recommendation. Guardian adds a separate aggregation layer rather than replacing the underlying engine.

### Machine-learning signal

Riskora uses the saved scikit-learn model for an ML Risk Score from **0–100**.

Important: this value is a **model score, not a calibrated fraud probability**.

ML can contribute to a Guardian review state, but ML alone does not produce the highest Guardian state.

### Behavioral analysis

When history is actually available, Riskora can examine:

- Unusual amount ratio/deviation
- Previous order count
- Failed attempts
- Payment-method changes
- Device changes
- Country/location changes
- Recipient/payment familiarity

If historical information is unavailable, Riskora records the limitation rather than inventing a history or assuming that the user is suspicious.

### Context analysis

Optional payment messages or instructions are checked for patterns including:

- Urgency
- Account threats
- Pressure to act
- Unusual payment instructions

These are warning signals, **not proof of fraud**.

### Community feedback

For eligible higher-value payments, the prototype can collect a simple:

- **Verified**
- **Unverified**

community signal.

The current prototype keeps this feedback in Streamlit session state. It is supporting evidence only; it is not a real multi-user reputation network.

A production implementation would require persistent storage, authenticated users, abuse prevention, reputation weighting, recency controls, and reliable merchant/recipient identity matching.

---

## 4. Prototype Guardian Aggregation

Guardian deliberately separates **evidence** from **certainty**.

The aggregation layer:

- Preserves rule-engine and ML results.
- Deduplicates semantically equivalent signals for the final status.
- Keeps all source signals available for explanation/investigation.
- Treats limited history as a limitation, not as evidence of low or high risk.
- Allows multiple meaningful available signals to elevate the Guardian state.
- Allows an elevated ML result to request review, but not to independently produce the highest state.
- Does not capture, approve, reject, cancel, or block a payment.

The current prototype also contains manual familiarity/amount thresholds used for the hackathon workflow. These are **prototype rules**, not universal financial-risk thresholds or claims about real-world fraud behavior.

---

## 5. Consumer Payment Flow

The Guardian interface is designed around a simple payment-review flow:

1. Enter the payment amount.
2. Choose **Merchant** or **Personal**.
3. Select recipient familiarity: **Familiar**, **New**, or **Unknown**.
4. Optionally provide the payment purpose.
5. Optionally provide payment/account instructions or message context.
6. For eligible payments, provide community feedback if available.
7. Guardian automatically analyzes the available information.
8. Review the explanation and recommended verification steps before paying.

Riskora does **not** request or simulate a user's payment PIN.

The product UI is designed to keep technical model details out of the primary Guardian experience. Technical details remain available in the investigation/analyst workflow.

---

## 6. Test & Investigation

Riskora contains an internal developer/investigation workflow for Test Mode payment testing.

It supports:

- Creating Test Mode orders.
- Opening Test Mode Checkout.
- Fetching Test Mode orders.
- Fetching Test Mode payments.
- Keeping payment IDs and order IDs as separate identifiers.
- Validating that a payment belongs to the expected order.
- Server-side signature verification.
- Mapping verified payment/order information into the existing transaction format.
- Running the existing rule engine and ML model on the resulting transaction.
- Inspecting feature coverage, feature sources, unavailable fraud features, compatibility fallbacks, and derived fields.

This is an **internal technical workflow**, not the consumer-facing Guardian flow.

---

## 7. Test Mode Payment Integration

Riskora uses the payment provider's **Test Mode only** for development and demonstration.

The integration supports:

- Test order creation
- Test order lookup
- Test payment lookup
- Standard Checkout Test Mode
- Payment/order relationship validation
- Server-side Checkout signature verification
- Verified payment-to-transaction mapping
- Payment failure and cancellation handling

No production payment processing is implemented.

### Security model

Checkout callback values are treated as untrusted browser input. Before Riskora accepts a payment for analysis, it:

1. Checks that the callback matches the pending Test Mode order.
2. Verifies the Checkout signature server-side.
3. Fetches the payment from the provider.
4. Confirms the payment belongs to the expected order.
5. Requires an authorized/captured payment state before treating it as verified.
6. Only then maps and analyzes the payment.

The secret key remains server-side and is not sent to browser JavaScript.

---

## 8. Payment Data and Feature Coverage

Payment-provider data does not automatically contain every feature required by the existing risk engine or ML model.

For example, provider payment data may contain:

- Payment ID
- Related order ID
- Amount
- Currency
- Payment status
- Payment method when returned
- Payment timestamp when returned
- Payment failure information when returned

It may not provide:

- Customer history
- Previous order count
- Average order value
- Device history
- IP history
- Billing-country history
- Historical failed attempts
- Full merchant/customer context

Riskora therefore tracks **feature provenance and coverage**.

When required fields are unavailable, compatibility values may be supplied to satisfy existing model interfaces. Those values are explicitly identified as compatibility/demo context and **must not be interpreted as verified provider facts or evidence of low risk**.

---

## 9. UI / Product Design

Riskora uses a responsive consumer-security visual system designed for both desktop and Android-sized screens.

The current design direction is:

> **80% controlled dark neutral + 20% electric colour**

Visual principles include:

- Deep graphite / midnight surfaces
- Electric cyan as the primary brand accent
- Hyper-blue used selectively
- Coral and amber reserved for risk states
- High contrast typography
- Subtle glow/aurora effects instead of excessive gradients
- Large, clear Guardian actions
- Responsive layouts for desktop and mobile
- Touch-friendly controls on narrow screens
- Technical information separated from the consumer decision flow

The same responsive Streamlit application is intended to serve desktop and mobile/Android-sized screens.

---

## 10. Tech Stack

- **Python**
- **Streamlit**
- **pandas**
- **scikit-learn**
- **joblib**
- **python-dotenv**
- **Razorpay Python SDK / Test Mode Checkout**
- **pytest**

---

## 11. Project Structure

```text
Razor pay/
|
+-- app.py
|
+-- data/
|   +-- mock_transactions.csv
|   +-- ml_training_transactions.csv
|   +-- ml_training_transactions_v2.csv
|
+-- models/
|   +-- riskora_model.joblib
|   +-- riskora_model_v2.joblib
|
+-- src/
|   +-- __init__.py
|   +-- risk_engine.py
|   +-- signal_detection.py
|   +-- ml_inference.py
|   +-- razorpay_client.py
|   +-- behavioral_analyzer.py
|   +-- context_analyzer.py
|   +-- guardian_engine.py
|   +-- guardian_explainer.py
|   +-- train_model.py
|   +-- evaluate_model_v2.py
|
+-- tests/
|   +-- test_razorpay_client.py
|   +-- test_context_analyzer.py
|   +-- test_guardian_engine.py
|   +-- test_guardian_explainer.py
|
+-- requirements.txt
+-- .env.example
+-- README.md
```

### File responsibilities

| File | Responsibility |
|---|---|
| `app.py` | Streamlit UI, Guardian, Analyst Mode, Test & Investigation workflows |
| `src/risk_engine.py` | Existing rule-based risk scoring and risk bands |
| `src/signal_detection.py` | Suspicious-signal detection |
| `src/ml_inference.py` | ML feature preparation and inference |
| `src/razorpay_client.py` | Test Mode order/payment operations and verification |
| `src/behavioral_analyzer.py` | Behavioral and historical-context signals |
| `src/context_analyzer.py` | Payment/message/context signals |
| `src/guardian_engine.py` | Central Guardian aggregation/orchestration |
| `src/guardian_explainer.py` | Human-readable Guardian explanations and actions |
| `src/train_model.py` | Model training utility |
| `src/evaluate_model_v2.py` | Model evaluation utility |

---

## 12. Run Locally

From the project root on Windows:

```powershell
.\razorpay.venv\Scripts\python.exe -m pip install -r requirements.txt
.\razorpay.venv\Scripts\python.exe -m streamlit run app.py
```

Then open the local Streamlit URL shown in the terminal.

For a normal Python syntax check:

```powershell
.\razorpay.venv\Scripts\python.exe -m py_compile app.py
```

---

## 13. Environment Variables

Create `.env` locally from `.env.example` and provide Test Mode credentials:

```text
RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
```

Use Test Mode credentials only. Never commit `.env` or expose the secret to browser JavaScript.

The repository is configured so `.env` is not tracked by Git.

---

## 14. Testing

The project includes automated tests for:

- Guardian aggregation
- Context analysis
- Guardian explanations
- Payment integration behavior
- Signature verification and payment/order validation paths

At the latest development checkpoint, the complete test suite reported:

```text
47 passed
```

The project also includes Python syntax validation with `py_compile`.

Test Mode payment testing is performed without real money. A failed or cancelled hosted Test Mode payment is not treated as a successful payment.

---

## 15. Limitations

Riskora is a prototype and has important limitations:

- It is not a production fraud-detection or financial-protection service.
- Test Mode is used for payment integration; production payment processing is not implemented.
- The ML Risk Score is not a calibrated probability.
- The quality of behavioral analysis depends on the history actually available.
- Missing history is represented as limited coverage rather than invented evidence.
- Community feedback is currently session-state based.
- Community feedback is not a verified reputation system.
- Compatibility fallback values exist for some model/rule interfaces and are not provider facts.
- Real-world deployment would require stronger identity, abuse prevention, privacy, security, monitoring, compliance, and persistence controls.
- The current Android experience is based on the responsive web application; native Android-specific capabilities are not yet implemented.

---

## 16. Future Roadmap

Potential next steps include:

- Persistent backend for community signals
- Authenticated community feedback
- Anti-abuse and rate-limiting controls
- Verified merchant/recipient identity matching
- Richer merchant-side transaction history
- Verified device and location signals
- Stronger feature provenance and data-quality controls
- Automated hosted Checkout end-to-end testing
- Production-grade security and monitoring review
- Android packaging/distribution of the responsive Riskora application
- Additional usability and accessibility improvements

---

## 17. Product Principle

Riskora is built around one simple idea:

> **Give people understandable safety signals before they pay, without pretending that an algorithm can make the decision for them.**

The system provides evidence, explanations, limitations, and verification steps. The final payment decision remains with the human user.
