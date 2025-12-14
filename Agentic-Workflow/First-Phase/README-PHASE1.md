Phase 1: Building the Agent Library
1. Introduction

In Phase 1 of this project, I developed a reusable library of agents intended to support agentic workflows. Although these agents will be applied directly within the Phase 2 Project Management workflow, they were intentionally designed to be general-purpose so they can be reused across multiple workflow types.

This phase involved both implementing the agent library itself and creating standalone scripts to instantiate and test each agent. These test scripts were used to validate correctness and to demonstrate that each agent behaves as expected based on its design and intended functionality.

By completing this phase, I have:

Implemented seven distinct agent classes within a single base_agents.py file, with each class representing a different agent workflow pattern.

Verified the behavior of each agent using an independent test script.

Structured the codebase as a clean, modular, and importable Python package that can be extended and reused in Phase 2.

2. Directory Structure

The contents of the phase_1 directory are organized as follows:

phase_1/
├── workflow_agents/
│   ├── __init__.py             ← (empty)
│   └── base_agents.py          ← Agent implementations
├── direct_prompt_agent.py
├── augmented_prompt_agent.py
├── knowledge_augmented_prompt_agent.py
├── rag_knowledge_prompt_agent.py
├── evaluation_agent.py
├── routing_agent.py
└── action_planning_agent.py


The workflow_agents directory is a Python package that contains all agent class definitions.

Each agent has a corresponding standalone script used to test and demonstrate its functionality.

Environment Setup:
To run the test scripts, I created a .env file inside the tests/ directory containing my OpenAI API key:

OPENAI_API_KEY=your_openai_api_key

3. Agent Library Implementation

All agent classes were implemented in workflow_agents/base_agents.py and tested using the provided scripts. The agents were developed and validated in the order described below.

3.1 Direct Prompt Agent

The Direct Prompt Agent represents the simplest interaction pattern with a Large Language Model (LLM). It forwards a user’s prompt directly to the model and returns the generated response without introducing memory, external context, or additional tools.

DirectPromptAgent Implementation

File: workflow_agents/base_agents.py

To implement this agent, I completed the following steps:

Imported the OpenAI class from the OpenAI Python SDK.

Stored the provided OpenAI API key as an instance attribute (openai_api_key) within the constructor.

Configured the agent to use the gpt-3.5-turbo model when requesting completions.

Passed the user’s prompt directly as a user message, without including a system prompt.

Implemented a respond method that returns only the textual content of the LLM response rather than the full API payload.

Testing the DirectPromptAgent

File: direct_prompt_agent_test.py

To validate this agent, I:

Imported the DirectPromptAgent class from base_agents.py.

Used the dotenv library to load my OpenAI API key from the environment.

Instantiated the agent as direct_agent using the loaded API key.

Sent the prompt:

"What is the capital of France?"


and printed the returned response.

Included a descriptive print statement explaining that the response was generated using the LLM’s general world knowledge.

3.2 Augmented Prompt Agent

The Augmented Prompt Agent extends basic prompting by enforcing a predefined persona. By explicitly setting a persona through a system prompt, the agent produces more targeted and context-aware responses.

AugmentedPromptAgent Implementation

File: workflow_agents/base_agents.py

The following steps were completed:

Added an attribute to store the agent’s persona.

Stored the result of the OpenAI chat completion call in a variable (e.g., response).

Constructed a system prompt instructing the agent to adopt the specified persona and to ignore any prior conversational context.

Ensured that the respond method returns only the generated text output.

Testing the AugmentedPromptAgent

File: augmented_prompt_agent_test.py

For testing, I:

Imported the AugmentedPromptAgent class.

Instantiated the agent using my OpenAI API key and a defined persona.

Sent a prompt and stored the output in augmented_agent_response.

Printed the response to confirm expected behavior.

Added explanatory comments describing:

The type of knowledge used by the agent.

How the enforced persona influenced the final output.

3.3 Knowledge Augmented Prompt Agent

The Knowledge Augmented Prompt Agent is designed to respond strictly based on explicitly provided knowledge, combined with a defined persona. This ensures the agent does not rely on the model’s inherent knowledge.

KnowledgeAugmentedPromptAgent Implementation

File: workflow_agents/base_agents.py

The agent was implemented by:

Defining attributes to store both persona and knowledge.

Building a respond method that constructs a system message containing:

A persona definition instructing the agent to forget all prior context.

A directive to use only the supplied knowledge.

A final instruction to base the answer solely on that knowledge.

Appending the user prompt as a separate message in the API call.

Testing the KnowledgeAugmentedPromptAgent

File: knowledge_augmented_prompt_agent.py

To test this agent, I:

Imported the class from base_agents.py.

Loaded my OpenAI API key from the .env file.

Instantiated the agent using:

Persona:

"You are a college professor, your answer always starts with: Dear students,"


Knowledge:

"The capital of France is London, not Paris"


Sent the prompt:

"What is the capital of France?"


Printed the response to confirm that the agent relied solely on the provided knowledge.

3.4 RAG Knowledge Prompt Agent

The RAG Knowledge Prompt Agent uses retrieval-augmented generation to dynamically source information. This agent was provided and did not require implementation. I reviewed the code to understand its structure and behavior. Additional background on RAG can be found in the linked resources.

3.5 Evaluation Agent

The Evaluation Agent evaluates responses produced by another agent (referred to as the worker agent) against predefined criteria and can iteratively refine responses.

EvaluationAgent Implementation

File: workflow_agents/base_agents.py

The implementation included:

Declaring all required class attributes, including max_interactions.

Creating a loop constrained by the maximum interaction count.

Retrieving responses from the worker agent within each iteration.

Constructing an evaluation prompt based on the defined criteria.

Evaluating responses using the OpenAI API with temperature=0.

Generating correction instructions using a second API call, also with temperature=0.

Returning a dictionary containing the final response, evaluation result, and iteration count.

Testing the EvaluationAgent

File: evaluation_agent.py

To test this agent, I:

Imported both EvaluationAgent and KnowledgeAugmentedPromptAgent.

Created a worker agent using the professor persona and incorrect France knowledge.

Instantiated the EvaluationAgent with a maximum of 10 interactions.

Evaluated the prompt:

"What is the capital of France?"


and printed the evaluation output.

3.6 Routing Agent

The Routing Agent dynamically selects the most appropriate agent to handle a user prompt by comparing semantic similarity between prompt embeddings and agent descriptions.

RoutingAgent Implementation

File: workflow_agents/base_agents.py

I implemented this agent by:

Defining an agents attribute to store agent metadata and callable handlers.

Implementing a get_embedding method using the text-embedding-3-large model.

Creating a routing method that:

Generates an embedding for the user prompt.

Computes embeddings for each agent description.

Calculates cosine similarity scores.

Selects the agent with the highest similarity.

Returning the selected agent’s response.

Testing the RoutingAgent

File: routing_agent.py

Testing involved:

Instantiating three knowledge-augmented agents for Texas, Europe, and math topics.

Defining callable functions or lambdas for each agent.

Assigning these agents to the router.

Testing the router using the following prompts:

"Tell me about the history of Rome, Texas"

"Tell me about the history of Rome, Italy"

"One story takes 2 days, and there are 20 stories"

3.7 Action Planning Agent

The Action Planning Agent extracts and structures actionable steps from a user’s prompt using provided knowledge.

ActionPlanningAgent Implementation

File: workflow_agents/base_agents.py

This agent was implemented by:

Initializing attributes for the API key and agent knowledge.

Instantiating the OpenAI client.

Sending prompts to the gpt-3.5-turbo model using:

A system prompt defining the agent’s role as an action planner.

The user’s task description.

Processing the response to extract a clean, ordered list of actionable steps.

Testing the ActionPlanningAgent

File: action_planning_agent_test.py

To validate functionality, I:

Imported the required libraries and the agent class.

Loaded the OpenAI API key from environment variables.

Instantiated the agent.

Sent the prompt:

"One morning I wanted to have scrambled eggs"


and printed the extracted action steps.

4. Phase 1 Deliverables for Phase 2

At the conclusion of Phase 1, I produced the following artifacts:

A completed workflow_agents/base_agents.py file.

Seven test scripts demonstrating correct agent behavior.

Screenshots showing successful execution of all test scripts.

Note: These artifacts are included with the Phase 2 submission.

5. Phase 2 Preview

In Phase 2, the agent library developed in this phase will be used to construct a more complex, multi-step agentic workflow aimed at solving real-world problems.
