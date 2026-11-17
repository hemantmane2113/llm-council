---
name: llm-council
description: Run a question through an "LLM council" of parallel subagents (answer, anonymous peer-ranking, chairman synthesis), inspired by karpathy/llm-council. Use when the user invokes /llm-council or asks for a council, multiple perspectives, or a second opinion on a hard question.
---

# LLM Council

Question to answer: `$ARGUMENTS` (if empty, ask the user for the question).

Follow the three stages. Use the Agent tool for every council member and reviewer, and launch the agents of each stage in ONE message so they run in parallel.

## Stage 1: Individual answers
Spawn 3 subagents (subagent_type "general-purpose"), each given the question verbatim and told to answer independently and concisely, without tools unless the question needs them. Vary the models for diversity: one with `model: "opus"`, one `"sonnet"`, one `"haiku"`. Do not tell them about each other.

## Stage 2: Anonymous peer review
Label the three answers Response A, B, C in shuffled order (do not reveal which model wrote which). Spawn 3 reviewer subagents (same three models). Each gets the question and all labeled responses, and must: critique each response briefly, then end with a line `FINAL RANKING: A > C > B` (best first).
Aggregate the rankings (average rank per response) yourself.

## Stage 3: Chairman synthesis
Spawn one subagent with `model: "opus"` as chairman. Give it the question, all stage 1 answers, all stage 2 critiques, and the aggregate ranking. It must write one final answer that keeps the strongest points, resolves disagreements, and flags remaining uncertainty.

## Output to the user
Show: (1) the chairman's final answer first, (2) a short table of the aggregate ranking with which model wrote each response (reveal it now), (3) one line on the main disagreements. Offer the full stage 1 and stage 2 text on request. Keep it concise.
