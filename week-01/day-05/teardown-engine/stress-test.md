# Stress Test Output — Basic Calculator App

**LLM Used:** Claude  
**Prompt Version:** V2

---

Layer 1: Data Foundation

**What's happening:** A basic calculator app has virtually no data pipeline. User inputs are ephemeral — a sequence of button taps processed in memory and discarded after the calculation completes. There is no persistent storage of user calculations, no ingestion pipeline, and no training data being collected (unless the developer adds analytics). The only "data" layer is the local device state holding the current expression string.

**Key technologies:** Local memory / stack data structure, maybe SQLite if history is persisted locally, Firebase Analytics or similar if the developer is tracking usage events

**Engineering challenge:** Floating point precision. IEEE 754 double-precision arithmetic produces results like 0.1 + 0.2 = 0.30000000000000004, which is technically correct but visually wrong to users. The tradeoff: use arbitrary-precision decimal libraries (e.g., Python's decimal module, Java's BigDecimal) at the cost of slightly higher compute overhead — acceptable since calculations are microsecond-scale anyway.

**Required skill:** Junior mobile developer with basic understanding of numeric types and floating point representation.

**Honesty check:** This layer is essentially absent. There is no data infrastructure here.

---

Layer 2: Statistics & Analysis

**What's happening:** Essentially none for a basic calculator. If the developer is sophisticated, they might run A/B tests on UI layout (button size, color, operation order) using a mobile experimentation platform, tracking tap accuracy rates or session length. But this is optional product polish, not core functionality.

**Key technologies:** Maybe Firebase A/B Testing or Optimizely for UI experiments; crash reporting via Crashlytics

**Engineering challenge:** There isn't a meaningful one at this layer. The closest approximation: if A/B testing button layouts, sample sizes need to be large enough to detect small UX differences (e.g., a 2% reduction in mis-taps), which requires understanding statistical power — but most calculator apps never run this analysis at all.

**Required skill:** None required for core product. Basic analytics literacy if doing UI optimization.

**Honesty check:** This layer is essentially absent. A basic calculator has no statistical modeling requirements.

---

Layer 3: Machine Learning Models

**What's happening:** None. A calculator is a deterministic system — the correct output for 7 × 8 is always 56, and no amount of training data changes that. There is no prediction, ranking, or classification problem to solve. The computation is a direct evaluation of a parse tree.

**Key technologies:** A recursive descent parser or shunting-yard algorithm (Dijkstra, 1961) to handle operator precedence. That's it.

**Engineering challenge:** Expression parsing correctness, not ML. Handling edge cases like -(-3), implicit multiplication 2(3+4), division by zero, and order of operations requires a properly implemented grammar. The tradeoff: building a full parser vs. using eval() — eval is faster to build but a severe security risk if the calculator ever accepts string input from an untrusted source.

**Required skill:** Software engineer with basic compiler/parser theory (context-free grammars, operator precedence).

**Honesty check:** This layer does not exist. ML would be the wrong tool. A decision tree that "predicts" 2+2=4 is not better than arithmetic.

---

Layer 4: LLM / Generative AI

**What's happening:** Not applicable for a basic calculator. Ironically, LLMs are worse at arithmetic than a calculator — they hallucinate numerical results because they pattern-match rather than compute. A calculator's entire value proposition is guaranteed deterministic correctness, which is precisely what LLMs cannot provide.

**Key technologies:** None warranted.

**Engineering challenge:** The challenge is actually avoiding this layer. If someone integrates an LLM for "natural language math input" (e.g., "what's 15% tip on $47?"), they must route the parsed intent to actual arithmetic computation rather than letting the LLM compute the number itself. The tradeoff: LLM adds useful NLU for complex word problems but introduces a failure mode (hallucinated answers) that didn't exist before, requiring a hybrid architecture where LLM extracts operands and operators, then hands off to deterministic computation.

**Required skill:** None required. If adding NL input: prompt engineer who understands LLM reliability limits.

**Honesty check:** This layer is absent and should stay absent. Using an LLM to do arithmetic is an anti-pattern.

---

Layer 5: Deployment & Infrastructure

**What's happening:** Deployment is a standard mobile app release — App Store / Google Play submission, versioning, and over-the-air update distribution. There is no server infrastructure because all computation is local. A calculator app with zero backend is genuinely one of the simplest deployment profiles in software engineering.

**Key technologies:** Xcode / Android Studio for builds, App Store Connect / Google Play Console for distribution, Fastlane for CI/CD automation, Crashlytics for crash reporting

**Engineering challenge:** App store review latency. A critical bug fix (e.g., a floating point error displaying wrong answers) can take 24–72 hours to reach users through App Store review, during which wrong answers are being served. The tradeoff: some teams use React Native or Flutter with CodePush/hot reload to push JS-layer fixes without a full app store review cycle, accepting the added framework complexity for faster patch velocity.

**Required skill:** Mobile DevOps with experience in App Store / Play Store release pipelines and CI/CD tooling.

**Honesty check:** Thin but real. Deployment still requires care, but there is no serving infrastructure, no model deployment, and no scaling problem.

---

Layer 6: System Design & Scale

**What's happening:** There is no distributed system. A basic calculator is a single-process, single-device application. "Scale" means handling the user pressing buttons very fast, which a modern CPU handles in nanoseconds. The architecture is: button tap → event handler → parser → arithmetic unit → display update. That's the entire system.

**Key technologies:** Whatever UI framework the platform provides: UIKit/SwiftUI (iOS), Jetpack Compose (Android), or a cross-platform framework like Flutter

**Engineering challenge:** UI responsiveness on low-end devices. On a $50 Android device with a slow main thread, a poorly implemented calculator that does layout recalculation on every keystroke can feel laggy. The tradeoff: debouncing display updates and keeping all computation off the main thread (even though it takes microseconds) maintains perceived responsiveness without meaningful engineering cost.

**Required skill:** Mobile developer with basic understanding of UI threading models.

**Honesty check:** This layer does not exist in any meaningful sense. One user, one device, one thread. There is no scale problem.

---

## Overall Analysis

**Most critical layer:** Layer 1 (Data Foundation) — specifically floating point precision handling. It's the only layer with a real technical trap. Every other layer is either absent or trivial, but a calculator that returns 0.30000000000000004 for 0.1 + 0.2 has failed at its single core responsibility: correct arithmetic. The entire product promise is deterministic correctness, so the one engineering decision that matters is numeric representation.

**Complexity rating:** Simple

**Justification:** A basic calculator is a solved problem from the 1970s. The shunting-yard algorithm handles operator precedence. IEEE 754 with careful rounding handles display. The app ships with zero backend, zero ML, zero distributed systems, and zero meaningful data infrastructure. Four of the six architectural layers are genuinely absent. Any complexity comes from the UI framework, not the domain.

**If rebuilding from scratch, first priority:** Choose your numeric representation before writing any arithmetic logic. Use BigDecimal or an equivalent arbitrary-precision library from day one. Switching from floating point to decimal arithmetic after launch requires touching every calculation path in the codebase, and floating point bugs in a calculator are trust-destroying — users notice immediately when 1.1 + 2.2 ≠ 3.3.
