# Home Credit Voice Agent: LAP Qualification

An AI voice agent built on Retell AI for the SalesAgents AI Prompt Engineer assignment. It calls an existing Home Credit customer, presents a pre-approved Loan Against Property (LAP) offer of up to ₹75 lakh, checks 7 eligibility points, and hands qualified leads to a senior loan expert.

Full write-up: [Submission document](https://docs.google.com/document/d/13aDlOcD6F1_nlouJ8_9IqpQ3nJg469Ab-X5P5HUjR48/edit)

## What is in this repository

| Path | What it is |
| --- | --- |
| `System_Prompt.txt` | The final system prompt, with the `{{variable}}` placeholders kept |
| `Test_Calls/` | 12 test call audios and 12 transcripts, named `Test1_Eligible` to `Test12_SelfCorrection` |
| `README.md` | This file |

## How the call works

1. Greet and verify the customer. If they are busy, ask for a callback time and end the call.
2. Present the offer as a thank-you for their loyalty.
3. Collect 7 eligibility points, in this order:
   1. Property type
   2. Ownership (sole or joint)
   3. Original documents available
   4. Loan amount
   5. Occupation and income mode
   6. Market value of the property
   7. Loan tenure
4. Disqualify politely and end the call the moment a rule fails.
5. If the customer mentions an existing loan or wants a lower EMI, say a loan-transfer specialist will call, and end the call.
6. After all 7 points pass, say a senior loan expert will call back with the exact interest rate, and end the call.

## Eligibility rules

| Item | Eligible | Not eligible |
| --- | --- | --- |
| Property type | Residential, Commercial, Industrial | Agricultural |
| Ownership | Sole or joint | None |
| Documents | Originals available | Photocopies only |
| Loan amount | Up to ₹75 lakh | Above ₹75 lakh: the agent offers ₹75 lakh instead of rejecting |
| Occupation | Salaried or self-employed | None |
| Income mode | Bank | Cash |
| Market value | Any value is recorded | No minimum rule |
| Tenure | 3 to 15 years | Below 3 or above 15 years |

## Design choices

- **Extract, validate, update, ask.** After every reply the agent saves every fact it heard, checks the rules, updates its state, and asks only the earliest unanswered item. Facts given out of order are never asked again.
- **Handoff gate.** The agent hands off only when all 7 items are answered. A question skipped during a side topic is asked later.
- **No invented facts.** It never makes up interest rates or product details. It uses the product knowledge variable, or says the senior expert will share the details.
- **Transfer detection.** The agent never asks about an existing loan; it only reacts when the customer mentions one.
- **Natural speech.** It handles fillers, corrections (the latest answer wins), interruptions, and answers that sound unsure or misheard, which it confirms once.
- **Call ending.** A dedicated section makes the agent finish its goodbye and then use the end call function.

## Prompt variables

`company_name`, `customer_name`, `agent_name`, `agent_gender`, `current_date`, `current_day`, `current_time`, `additional_context_from_rag`, `language_to_speak`, `conversation_history`, `customer_utterance`.

## How to run it

1. Create a free account on [Retell AI](https://www.retellai.com) and create a **Single Prompt** agent.
2. Paste `System_Prompt.txt` into the prompt box.
3. Add the **End Call** function and choose a professional voice.
4. Set the dynamic variables (for example `company_name` = Home Credit, `agent_name` = Dharshini, `agent_gender` = Female, `language_to_speak` = English). `conversation_history` and `customer_utterance` are filled in by the platform.
5. Start a test call and follow one of the scenarios below.

## Test scenarios

All 12 scenarios passed as voice calls.

| # | Scenario | Expected result |
| --- | --- | --- |
| 1 | Eligible customer | Handoff to the senior loan expert after all 7 answers |
| 2 | Agricultural property | Polite disqualification |
| 3 | Cash income | Polite disqualification |
| 4 | No original documents | Polite disqualification |
| 5 | Tenure too short (2 years) | Polite disqualification |
| 6 | Tenure too long (20 years) | Polite disqualification |
| 7 | Loan above ₹75 lakh (₹90 lakh) | Offers ₹75 lakh, then continues |
| 8 | Existing loan or EMI reduction | Loan-transfer specialist message |
| 9 | Busy customer | Asks for a callback time, no pitch |
| 10 | Out-of-order answers | Skips answered questions |
| 11 | Interest rate question | No rate invented, returns to the checklist |
| 12 | Self-correction | Uses the latest answer |

## Bugs found and fixed during testing

1. **The call did not hang up** (Tests 6 and 9). Added an "ENDING THE CALL" section and named the end call function in the busy step.
2. **A question was asked twice** (Test 10). Added a rule to read the conversation history before every question and to confirm unclear details once.
3. **Unsure answers were accepted** (Test 1). Added a rule to confirm unsure or misheard answers once.
4. **The first draft asked about existing loans**, which would be an eighth question. The prompt now only watches for the customer to mention it.

## Links

- Submission document: https://docs.google.com/document/d/13aDlOcD6F1_nlouJ8_9IqpQ3nJg469Ab-X5P5HUjR48/edit
- Screen recording: add your Google Drive link here

## Author

Asmitha M
