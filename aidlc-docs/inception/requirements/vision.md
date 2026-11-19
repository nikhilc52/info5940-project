# Vision: Course AIdvisor Application

## Executive Summary

Course AIdvisor is a chatbot interface for Cornell students that allows them to ask questions about course selections for the current semester. Specifically, users can ask about which courses they should take based on their interests and course descriptions, which courses have the highest ratings on CU Reviews, and which courses fulfill their major or minor requirements.

## Features In Scope (MVP)

- Course summarizing: based on the course description, explain what the course is about, when it is typically offered, and how it potentially relates to the user’s interests
- Course prerequisites/overlaps: based on the course description, ensure the user is aware of forbidden overlaps and required courses (or knowledge)
- Course filtering: basic filtering (credits, subject, time of day) via parameters to an API call, to allow users to better understand offerings
- Course/instructor reviews: for each course (and instructor of that course), access past reviews on CU Reviews, and output both quantitative metrics (quality, difficulty ratings) as well as sentiment from individual user reviews
- Program fulfillment: be able to assess which courses align with major or minor requirements, based on current program curricula
- Advising capability: be able to tell the user which courses to take, based on their interests, past courses, and future semester offerings

## Features Explicitly Out of Scope (MVP)

- Any features that require a login to a Cornell account:
	- Course evaluations data
	- Course syllabi
	- Student transcripts or course history (that is not provided by students)
- Information from Rate My Professor sites (AI is blocked from these pages)

## Target Users

- Cornell undergraduate and graduate students

## Key Success Metrics

- 5% of Cornell students utilize the course advisor within 3 months (around 1,300 students)
- Response time for each user question under 10s (should be significantly faster than manual lookups)
- <1% critical accuracy bugs (wrong course descriptions or course recommendation results)

## Open Questions

- Should the course advisor provide detailed plans for each semester throughout the 4 years, or only a detailed plan for the upcoming/current semester and then a vague plan for the following semesters? (Having a detailed plan so many years in advance may lead to inaccuracies from flawed assumptions).
- When recommending courses, should the Course AIdvisor also take into account the student’s desired job trajectory or career plan, or just take into consideration their intended major and/or minor(s)? (Taking into account the desired job trajectory may make the schedule more personal, but the Course AIdvisor should prioritize ensuring the student gets their necessary classes done first.)
- Should students be able to give their Cornell login credentials to the Course AIdvisor so it can access more Cornell secure information such as course evaluations? (However, giving passwords to an AI/LLM is not secure and may end up in a data leak and lead to leaked passwords and credentials. But with this information, the LLM can provide more detailed or personalized information to the student).
