# Now: test whether a checkable first answer lifts analyst activation

ChainSignal Verified Answers roadmap, v2: rescored against the Step 3 M3 metric · 2 engineers + 1 designer (assumed) · built with the Module 4 Roadmap Builder: Effort vs. Value, then Now / Next / Later and a Cut List.

**Persona**Maya, the Defensible Analyst: a non-SQL research analyst. Step 3 places her friction at the first answer

**Primary metric (M3)**Analyst first-session activation, 41% → 56%. User Success: verified answers per active analyst per week

**Moment of misery**Her first real question gets no answer she can check: 46% are unsupported, and an answer without a method can't be defended

**Guardrail**Answer error rate under 2%; trader and SQL-native activation must not fall

Select a point or a card to see its score and rationale.

## Roadmap

Now ships only what tests the hypothesis, and leaves the slack empty so the beta reads B1 cleanly. Next answers more first questions, with B11 waiting on the concierge test.

## Cut list

All three serve traders, the segment Step 3 shows is the weakest business (7% week-4 retention, 0.5% pricing intent).

## How this roadmap was built

The LLM gives a fast baseline from the feature descriptions alone. PM judgment then rescores each item on whether it moves the M3 metric, using the Step 3 data and Steps 4–5a.

1. **Score**Value 1–5 (5 = directly moves activation) and Effort 1–5 (1 = days, 5 = months)
2. **Baseline**Run the Senior PM prompt with no evidence: a cold first pass
3. **Judge**Rescore: if it doesn't move activation, Value can't be 5
4. **Roadmap**Decide Now / Next / Later / Cut; the lanes update live

| Feature | LLM baseline | Before Step 3 | Value | Effort | Quadrant | Decision | Why the judgment changed it |
| --- | --- | --- | --- | --- | --- | --- | --- |

The LLM baseline is a cold pass: the course's scoring prompt with no M2/M3 evidence, scored from the feature descriptions only. "Before Step 3" is the v1 decision, scored against the Step 5a north star. Value scores rest on Step 3 sample data and Steps 5–5a, and stay assumptions until real exports and 3–5 analyst interviews confirm them. Effort is a PM estimate, not engineering sizing. Quadrant rule: Value ≥ 4 is high, Effort ≤ 2 is low. Your edits are kept in this browser only.