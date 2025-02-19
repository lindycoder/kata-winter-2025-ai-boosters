Short Overview

* Short answer reviewing
    * For existing questions
        * We have evaluated answers
        * Store good and bad answers to each question
        * Find similar answer, good and bad with grade and feedback, add to the prompt
        * Extract the grade and feedback (RESPONSE FILTER)
        * If we have ???% confidence: grade the answer  (NEED RESEARCH) consider F+ F-
            * Only passing can be automated, failing should have the feedback manually reviewed
        * else: show to the reviewer with what would be the result
            * They choose the answer, and we feed that back in the engine for the next time
    * For new question
        * The answers will need manually reviews until we build confidence


* Long answer reviewing (assisted grading)
    * For each criteria, have the LLM evaluate the case study and return a short summary and feedback
        * Feed the acceptance of refusal of suggestion back
        * as prompt augmentation
            * provide good summaries and feedback of other submissions
    * Present this to the reviewer to help
    * Add a chat prompt for the user to ask question about the case study
    * Use some copilot to write the feedback faster
