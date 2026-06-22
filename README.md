# 🤖 SECJ3553: Artificial Intelligence — Reflection
---

## Project Overview — MindConnect AI

All three assignments and the final project report in this course centred on a single system: **MindConnect**, an AI-based diagnostic support application for preliminary mental health detection. The project was structured in three phases — Knowledge Representation, State Space Search, and Intelligent Agent Design — with each assignment building directly on the last, culminating in a full Proof of Concept (PoC) with a working mobile interface prototype.

The system is designed to help individuals experiencing stress, anxiety, or early-stage depression access non-judgmental, personalised support — without replacing clinical professionals. A strict ethical rule was embedded into the design from the start: MindConnect will never attempt a formal clinical diagnosis; it exists only for early detection, support, and safe hand-offs to professionals.

---

## Assignment 1 — Knowledge Representation (FOL)

### What It Covered

This assignment established the reasoning backbone of MindConnect using **First-Order Logic (FOL)** across five domains: preliminary diagnosis, therapy selection, user relationships, crisis triage, and language analysis. Each domain used a different knowledge representation scheme.

### What I Learned

**Knowledge representation is not one-size-fits-all.** The hybrid approach was the most important design insight of this assignment. Rule-Based Systems work well for clear IF-THEN diagnostic logic (e.g. if a user reports excessive worry AND restlessness AND it has lasted weeks → indicates GAD). But therapy selection required Frame-Based Systems to hold structured user profiles with slots for progress and severity — because the AI needs to retrieve a user's history, not just evaluate a single input. Semantic Networks were better suited for mapping relational concepts like `Stress → caused_by → Workload` or `Anxiety → alleviated_by → BreathingExercise`, where the connections between concepts matter as much as the concepts themselves.

**Fuzzy logic handles the subjectivity of mental health data.** For crisis triage, standard binary logic is insufficient — a user's distress is not simply "high" or "not high." Using fuzzy membership values (e.g. `User_Rating IS Poor` with µ=0.8, `Heart_Rate IS Elevated` with µ=0.9, combined with the MIN operator) allowed the system to reason about degrees of severity rather than hard thresholds. The crisis escalation rule — `IF (Self_Harm_Indicator IS Present) OR (Suicide_Ideation IS Extreme) THEN (Triage_Level IS Emergency)` — showed me why precision in these rules matters enormously: the wrong logic in a crisis detection system has real consequences.

**NLP as the bridge between language and logic.** The language analysis section made clear that raw text must be converted into structured facts before any reasoning can happen. A message that expresses sadness and repetitive worry gets translated into `Mood(T, Down) ∧ StuckOnIssue(T)` before the FOL rule `→ NeedsSupport(T)` can fire. Understanding this pipeline — text → NLP → structured facts → inference → response — gave me a clearer mental model of how conversational AI systems actually work.

### Challenge

Writing FOL that is both logically correct and clinically responsible was harder than expected. For the crisis triage rules especially, we had to think carefully about false negatives (missing a high-risk user) vs. false positives (unnecessary escalation). The ethical constraint — never diagnosing, only detecting — forced every rule to be phrased as "indicates a potential condition" rather than "the user has X", which is a meaningful distinction in language and in logic.

---

## Assignment 2 — State Space Search

### What It Covered

This assignment modelled MindConnect's entire decision flow as a **State Space Search diagram**, mapping every possible path from a user's first interaction to a goal state.

### What I Learned

**State space search makes implicit logic explicit.** Before drawing the diagram, the system's flow existed as prose descriptions. Formalising it — defining the initial state (`User_Exist`), all possible actions, goal states (`Dr_Appointment`, `Precaution`, `Reminder`), and every transition condition — forced us to find gaps and ambiguities we had not noticed. For example, the loop between `Update_User_Progress` and `Symptom` (for active users under observation) only became clear when we had to draw every edge explicitly.

**Different paths represent different AI reasoning methods.** The symptom-detected path (Questionnaire/ChatBot → Symptom → Indicate_Issue → Duration → Severe/Mild Case) executes FOL rules. The no-symptom path (→ Under_Observation → Update_User_Progress or Reminder) uses Semantic Networks to monitor engagement. Seeing both paths in the same diagram made the connection between knowledge representation techniques and system behaviour concrete.

### Challenge

Deciding what counts as a "state" vs. an "action" vs. a "path cost" was genuinely difficult at first. `Duration` is a state that the system enters and evaluates — but calculating duration is also an action. Working through these distinctions carefully, and ensuring the diagram was consistent with the FOL rules from Assignment 1, required several revisions.

---

## Assignment 3 — Intelligent Agent & PEAS Model

### What It Covered

This assignment classified MindConnect as a **learning agent** and formalised its design using the **PEAS framework** (Performance, Environment, Actuators, Sensors).

### What I Learned

**The PEAS framework forces precise thinking about what an AI system actually does.** Before this assignment, I understood the system's behaviour narratively. Filling in the PEAS table required identifying exactly what the agent measures (accuracy of emotion detection and response quality), what it perceives (keyboard input, NLP analysis of text, system clock timestamps), what it acts upon (user reaction and subsequent behaviour), and what outputs it produces (trigger alerts, psychological precautions, medical referrals). Each box in the table is a design decision, not just a description.

**A learning agent is the right choice because mental health is not static.** A simple reflex agent would respond to inputs without memory, which is clearly inadequate — a user's emotional state today only makes sense in the context of their history. The learning agent model allows MindConnect to build and continuously update a psychological baseline per user, adjusting its responses as the user's condition evolves. This connects back to the Frame-Based Systems from Assignment 1: the "frame" for each user is what the learning agent reads and updates.

**The system clock as a sensor was a novel insight.** Including time-of-day as a sensor input — not just user text — directly enables the FOL rule from Assignment 1 (`IF Time_of_Day IS Night AND Anxiety_Level IS High → Offer BreathingGuide`). Sensors in a real AI system are not limited to direct user inputs; contextual signals like time, frequency of interaction, and streak consistency are also data the agent can reason over.

### Challenge

The boundary between sensors and actuators was sometimes blurry. The NLP analyzer both reads user input (sensor role) and produces an interpretation (which feels more like processing). Understanding that sensors gather raw data while actuators produce effects in the environment — and that NLP sits in between as a processing layer — clarified the distinction.

---

## Final Project Report — Full System Integration & Proof of Concept

### What It Covered

The final report brought together all three phases and demonstrated a working **Proof of Concept (PoC)** mobile interface, including a login system, AI chatbot, questionnaire with risk-tiered results, monitoring/mood tracking page, and user profile with wellbeing history.

### What I Learned

**Translating backend AI logic into a user interface requires rethinking the output.** The questionnaire's risk classification into three tiers (Under Observation, Mild Symptoms Detected, Significant Symptoms Detected) directly implements the FOL severity rules from Assignment 1 — but the UI has to present this in language that is helpful, not alarming. Writing "We're Here to Support You" rather than "High Risk Detected" is a UX decision, but it is also an ethical AI decision: how the system communicates its inferences matters as much as the accuracy of those inferences.

**The monitoring page demonstrates the learning agent in action.** The consistency streak, daily check-in tracker, and 7-day mood trend graph are all direct outputs of the agent continuously reading sensor data (system clock, chatbot NLP) and updating user progress. Seeing these features in a real interface made the PEAS model tangible — the mood trend graph is the actuator output of weeks of sensor readings and agent inference.

**Ethical AI design is not an add-on.** The disclaimer on the questionnaire results page — explicitly stating that MindConnect is an early-detection tool, not a clinical diagnosis — is the ethical rule from our design manifested in the UI. Every design decision throughout the project (always recommending professional help for severe cases, never labelling a user with a disorder, framing outputs as "indicates potential") was a consequence of building that ethical constraint into the system from Phase 1.

### Challenge

The hardest part of the PoC was designing the crisis triage flow in the interface. The logic is clear in FOL — if self-harm or suicidal ideation indicators are detected, trigger Emergency response — but deciding exactly how the app should communicate this to a real user in distress, without causing additional harm, required careful thought about tone, immediacy, and what resources to surface. It was a reminder that AI systems operating in sensitive domains carry a level of responsibility that goes beyond technical correctness.

---

## Overall Reflection

| | Assignment 1 | Assignment 2 | Assignment 3 | Final Project |
|---|---|---|---|---|
| **Focus** | Knowledge representation | Decision flow modelling | Agent design | Full system + PoC |
| **Key technique** | FOL, fuzzy logic, semantic networks | State space search | PEAS, learning agent | UI/UX + ethics integration |
| **Output** | Logic rules | State diagram | PEAS table + diagram | Working mobile prototype |

This course fundamentally changed how I think about AI. Before it, I thought of AI mostly as pattern recognition and prediction. After designing MindConnect across these three assignments, I understand that AI systems in sensitive domains also require formal reasoning, explicit ethical constraints, and careful thought about how outputs affect real people.

The most lasting lesson: intelligence in a system is not just about getting the right answer — it is about representing knowledge correctly, searching the right spaces, acting with the right sensors and actuators, and communicating outputs in ways that are safe, honest, and human.

---

*Last updated: June 2025 · ePortfolio maintained on GitHub*
