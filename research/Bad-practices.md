# Bad AI/anti-patterns during development by [Mohammed Ali Zubair](https://github.com/Alig1493)

Sources:

- https://huyenchip.com/2025/01/16/ai-engineering-pitfalls.html
- https://www.linkedin.com/blog/engineering/generative-ai/musings-on-building-a-generative-ai-product
- deepseek/chatgpt

### Starting too complex:

- Using an agentic framework when direct API calls are enough
- Not using term based retrieval solutions instead opting for embedded vectordb based solutions
    - An example of a term based solution could be using an elasticsearch based index to lookup certain terms from a list of documents.
    - Regex matching or exact matching
- Too much fine-tuning whereas basic prompting suffices. An example could be:
    - Example: Sentiment Analysis of Customer Reviews

      Task: Classify customer reviews as positive, neutral, or negative.

      Basic Prompting Approach

        ```python
        prompt = """
        Classify the sentiment of the following review into 'positive', 'neutral', or 'negative':
        Review: "I loved the product! It worked perfectly and arrived on time."
        Sentiment: positive
        Review: "The item was okay, but shipping took too long."
        Sentiment: neutral
        Review: "This is the worst purchase I've ever made. Broken and useless."
        Sentiment: negative
        Review: "{user_review}"
        Sentiment:
        """
        ```

      Why Basic Prompting Works Here:

        - Task Simplicity:

          Sentiment analysis is a well-understood task, and large language models (LLMs) like GPT-3/4 or Claude are already trained on vast amounts of text that include sentiment expressions (e.g., reviews, social media posts).

          The model doesn’t need to learn new patterns—it just needs to follow examples.

        - Clear Patterns:

          Words like "loved," "worst," or "okay" strongly correlate with sentiment labels. The model can infer the correct label based on these cues without additional training.

        - Few-Shot Learning:

          Providing 3 examples (positive, neutral, negative) in the prompt gives the model enough context to generalize to new reviews. No fine-tuning is required.

        - Cost and Speed:

          Basic prompting avoids the time and cost of curating a labeled dataset, training, and maintaining a fine-tuned model.

    - Fine tuning works when:
        - The task is domain-specific (e.g., legal, medical, or technical jargon).
        - The model needs to learn new patterns or rare labels.
        - Prompt engineering alone leads to inconsistent or inaccurate results.

### Underestimating Increasing complexity from demo to product

- LinkedIn achieved 80% of its desired user experience in one month but underestimated the grueling, discouraging effort required to surpass 95% over the next four months, as incremental improvements—especially reducing hallucinations—proved exponentially harder.
- Some challenges include:
    - Accuracy/latency tradeoff: more planning/self-correction = more nodes = higher latency
    - hard for agents to differentiate similar tools (not sure if this is going to be our case).
        - Example:
          An AI agent for travel planning has two tools:

            ```python
            book_flight(): Books a flight ticket immediately (non-refundable).
            
            hold_flight(): Temporarily reserves a seat for 24 hours (no payment required).
            ```

          Both tools require the same parameters (e.g., destination, date, passenger info). When a user asks, “Can you secure me a flight to Tokyo next week?”, the agent struggles to distinguish whether the user wants a confirmed booking (book_flight) or a temporary hold (hold_flight). The similarity in tool names, inputs, and use cases leads to errors, such as accidentally charging a user who only wanted a reservation hold.

          Result: The agent might default to the wrong tool without clarifying the user’s intent, causing frustration or financial consequences.

- Other than the issues of hallucinations, latency, latency/accuracy tradeoff, tool use, prompting, testing, as mentioned, teams also run into issues, such as:
    - Reliability from the API providers. A team told me that 10% of their API calls timed out. Or product’s behaviors change because the underlying model changes.
    - Compliance, e.g. around AI output copyrights, data access/sharing, user privacy, security risks from retrieval/caching systems, and ambiguity around training data lineage.
    - Safety, e.g. bad actors abusing your product, your product generates insensitive or offensive responses.

### **Forgoing human evaluation**

- To automatically evaluate AI applications, many teams opt for the AI-as-a-judge (also called LLM-as-a-judge) approach — using AI models to evaluate AI outputs. A common pitfall is forgoing human evaluation to rely entirely on AI judges. If the AI judge isn’t developed properly, it can give misleading evaluations about your application’s performance. AI judges must be evaluated and iterated over time, just like all other AI applications.
- The teams with the best products I’ve seen all have human evaluation
  to supplement their automated evaluation. Every day, they have human
  experts evaluate a subset of their application’s outputs, which can be
  anywhere from 30 - 1000 examples.
- Daily manual evaluation serves 3 purposes:
    1. Correlate human judgments with AI judgments. If the score by human evaluators is decreasing but the score by AI judges is increasing, you
       might want to investigate your AI judges.
    2. Gain a better understanding of how users use your application, which can give you ideas to improve your application.
    3. Detect patterns and changes in users’ behaviors, using your
       knowledge of current events, that automated data exploration might miss.

### Overly reliant on Assisted Development

- Risky code and Code quality issues:
    - AI models are usually trained on publicly available code, raising issues such as potential copyright infringement or ethical concerns.
    - Additionally, there's more to consider. AI language models can also replicate biases from their training data, leading to bad practices in the code they generate.
    - Not to mention existing code, where the model fabricates information entirely, often suggesting non-existent functions.
    - Many machine learning models work like a "black box," meaning it's hard to understand how or why they make certain code decisions. This lack of transparency makes troubleshooting, debugging, and optimization more difficult, as you can't easily trace the logic behind the code.
- Security Vulnerabilities:
    - Generated code can introduce potential security flaws by using weak or outdated cryptographic algorithms, including:
        - Outdated libraries
        - Old frameworks
        - Obsolete code snippets
        - Insecure error handling
        - Error leaks
        - Hard-coded credentials
        - Incorrect data structures
        - Improper configuration
- Compliance and Intellectual Property (IP) Concerns
    - Publicly available generative AI systems are trained on on public and private code which exposes them to code without transparent sources or ownership.
    - In organizations with little oversight of developers' use of AI, there is a risk that proprietary code, customer data, or other secrets may be publicly exposed, which can result in significant compliance violations, especially in highly regulated industries.
- Rigorous Testing & Validation can get rid of most of these issue via:
    - Thorough code reviews
    - Automated testing
    - Security analysis
