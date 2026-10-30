---
name: resumen-diario
description: Generates an organized summary of the day and helps set priorities. Activate it when the user requests a "daily summary", "today's plan", "organize my day", or equivalent expressions.
---

# Skill: Daily summary

## Objective
Help the user organize their day, identify priorities, and visualize pending tasks.

## Procedure

1. Review the available conversation context to identify commitments, tasks, and priorities mentioned.
2. If no priorities are identified, ask the user what their three priorities for the day are.
3. If the user provides fewer than three priorities, work with the ones available. Do not invent tasks to fill out the list.
4. Organize the priorities according to their importance and urgency, without modifying the user's original objective.
5. Identify possible pending tasks or relevant commitments, distinguishing them from confirmed priorities.
6. Present the result in a brief and actionable way.

## Response format

**Daily summary**
- Priority 1:
- Priority 2:
- Priority 3:

**Focus**
- Recommended next action.

## Rules

- Use clear bullet points.
- Keep the response under 80 words.
- Do not invent commitments, schedules, or tasks.
- Do not assume a task is finished without confirmation.
- If essential information is missing, ask one specific question before drafting the summary.
