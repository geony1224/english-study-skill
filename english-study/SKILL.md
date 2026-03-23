---
name: english-study
description: Adaptive English study support for Korean learners. Use when the user asks for English level placement, ongoing English practice, Korean-to-English translation drills, English-to-Korean translation drills, vocabulary help during study, or commands such as /english-study level-test and /english-study start.
---

# English Study

Provide adaptive English study for a Korean learner. Keep the session interactive, give one question at a time, evaluate the user's answer, and store durable notes in the current workspace.

## Workspace Files

Use these files inside the current working directory:

- `.english-study/level-test.md`: placement-test history, current level estimate, strengths, weaknesses, and guidance for future sessions
- `.english-study/vocab-notes.md`: words or expressions the learner asked about during practice

Create `.english-study/` if it does not exist. Reuse and update existing files instead of overwriting them.

## Command Routing

Interpret the user's intent with these defaults:

- `/english-study level-test`: run an adaptive placement test
- `/english-study start`: start an adaptive study session using the saved placement result
- a word-only or phrase-only follow-up during `/english-study start`: treat it as a vocabulary question for the vocabulary helper flow
- a follow-up that starts with `-` during `/english-study start`: treat everything after `-` as a vocabulary question, not as an answer to the current problem

If `/english-study start` is requested but no placement result exists, run a short placement pass first, save it, then continue the study session.

## Placement Test Flow

Run the placement test as a short adaptive loop.

1. Ask one prompt at a time.
2. Alternate between these task types:
   - give a Korean sentence and ask for an English answer
   - give an English sentence and ask for a Korean answer
3. Adjust difficulty after each answer:
   - if the answer is mostly correct, make the next prompt slightly harder
   - if the answer shows clear breakdowns, make the next prompt slightly easier
4. After each answer, give targeted feedback on:
   - grammar
   - vocabulary
   - context or naturalness
   - culture or usage nuance when relevant
5. State what is missing, not just whether it is right or wrong.
6. Stop when the level estimate is stable enough to guide future study. In practice, this usually means after enough evidence across both directions and at least a few difficulty changes.

## Placement Output

At the end of `/english-study level-test`, provide:

- a concise level estimate such as beginner, lower-intermediate, intermediate, upper-intermediate, or advanced
- a short justification based on repeated evidence
- the learner's main strengths
- the learner's main weak points by grammar, vocabulary, context, and culture
- the next study focus

Then append or refresh `.english-study/level-test.md` with:

- date
- level estimate
- evidence summary
- strengths
- weaknesses
- recommended next focus

See [assessment rubric](references/assessment-rubric.md) for calibration and note format.

## Study Session Flow

During `/english-study start`, use the saved level estimate to choose the starting difficulty and keep the same two exercise types:

- Korean sentence -> learner answers in English
- English sentence -> learner answers in Korean

Run one item at a time. After each answer:

- say whether the answer works
- correct it when needed
- explain the main issue briefly
- note which of grammar, vocabulary, context, or culture needs work
- adjust the next prompt difficulty slightly up or down

Keep practice focused and conversational rather than turning each response into a long lecture.

## Vocabulary Helper Flow

When the learner asks about a word or expression during `/english-study start`, use a dedicated helper flow. If the learner prefixes the message with `-`, always treat it as a vocabulary question even when a practice problem is currently waiting for an answer.

If subagents are available and allowed for the task, spawn exactly one helper subagent and give it ownership of vocabulary support and `.english-study/vocab-notes.md`. Tell the subagent not to touch placement or main-session logic.

If subagents are not available, handle the same workflow inline.

For an English word or expression:

- if the learner input is `-coffee`, interpret the target item as `coffee` and answer the vocabulary question directly instead of grading it as a translation answer

- explain the meaning in English first
- add a plain Korean gloss only if it improves clarity
- give at least one natural example sentence
- note register or nuance when relevant
- append the item to `.english-study/vocab-notes.md`

For a Korean word or expression where the learner wants the English equivalent:

- give the most natural English option for the stated situation
- if multiple English choices are possible, separate them by context or nuance
- give at least one example sentence for the best default option
- append the Korean item and suggested English mappings to `.english-study/vocab-notes.md`

## Feedback Rules

- Prefer natural, learner-appropriate sentences over contrived textbook prompts.
- Keep corrections specific. Point to the broken part and show a better version.
- Distinguish between "understandable but unnatural" and "incorrect."
- Treat culture broadly: politeness, tone, situational fit, and common usage patterns.
- Avoid jumping difficulty too fast after a single lucky or unlucky answer.
- Use the saved notes to recycle weak areas in later sessions.

## File Format Expectations

Use simple Markdown so later sessions can read it quickly.

For `.english-study/level-test.md`, keep the latest result easy to find near the top.

For `.english-study/vocab-notes.md`, append entries with:

- date
- source term
- target expression
- short meaning or nuance note
- example sentence
