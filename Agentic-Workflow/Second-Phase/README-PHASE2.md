Project Title: AI-Powered Agentic Workflow for Project Management
Phase 2: Implementing an Agentic Workflow Using a Predefined Agent Library

Phase 2 builds directly on the agent library developed in Phase 1. In this phase, I use the previously implemented agent classes to construct a complete agentic workflow that simulates real-world project management tasks.

Agentic workflows differ from traditional automation pipelines in that they are not rigid or purely sequential. Instead of following a fixed set of predefined steps, multiple AI agents collaborate dynamically to determine how work should be executed based on the input prompt and context.

For this phase, I implemented a general-purpose agentic workflow focused on product development project management. Each agent operates with role-specific domain knowledge, such as interpreting product specifications, generating user stories, defining product features, and breaking work into engineering tasks. Conceptually, the workflow is driven by a Technical Program Manager (TPM) persona that coordinates interactions between specialized agents.

This phase does not involve building a chatbot. Rather, the goal is to design an agentic system that accepts a single high-level prompt and produces a structured, multi-stage output. The system is validated using golden prompts, which represent realistic inputs a TPM might provide in a real project environment.

Workflow Agents Library

Before implementing the workflow, I verified the following prerequisites:

The workflow_agents directory contains the base_agents.py file with all agent class implementations completed and tested as required in Phase 1.

All Phase 2 workflow logic is implemented inside the agentic_workflow.py file, which imports and orchestrates agents from the workflow_agents.base_agents module.

Workflow Script Implementation

The implementation follows the TODO markers provided in the agentic_workflow.py starter file. Below, I describe how each step was completed.

1. Importing Agents (TODO 1)

I imported the required agent classes—ActionPlanningAgent, KnowledgeAugmentedPromptAgent, EvaluationAgent, and RoutingAgent—from the workflow_agents.base_agents module.

2. Loading the OpenAI API Key (TODO 2)

I loaded my OpenAI API key from environment variables using a .env file and the python-dotenv library. The key was stored in a variable named openai_api_key.

3. Loading the Product Specification (TODO 3)

I loaded the contents of the Product-Spec-Email-Router.txt file into a string variable named product_spec. This document serves as the core product context for the workflow.

4. Instantiating the Action Planning Agent (TODO 4)

I instantiated the ActionPlanningAgent using the predefined knowledge_action_planning string provided in the starter code. This agent is responsible for converting the initial workflow prompt into a structured list of execution steps.

5. Completing Product Manager Knowledge (TODO 5)

The knowledge_product_manager string was partially provided. I appended the loaded product_spec content to the end of this string so the Product Manager agent would have direct access to the full product specification as part of its knowledge base.

6. Instantiating the Product Manager Knowledge Agent (TODO 6)

I instantiated a KnowledgeAugmentedPromptAgent to represent the Product Manager role. This agent was configured using the provided persona_product_manager and the completed knowledge_product_manager string.

7. Instantiating the Product Manager Evaluation Agent (TODO 7)

Next, I defined and instantiated an EvaluationAgent for the Product Manager. This evaluation agent assesses the output of the Product Manager knowledge agent using the following configuration:

Persona:
"You are an evaluation agent that checks the answers of other worker agents"

Evaluation Criteria:
"The answer should be stories that follow the following structure: As a [type of user], I want [an action or feature] so that [benefit/value]."

The product_manager_knowledge_agent was passed as the agent_to_evaluate parameter.

8. Instantiating Program Manager Agents (Before and TODO 8)

Before completing TODO 8, I instantiated a KnowledgeAugmentedPromptAgent for the Program Manager role using the provided persona_program_manager and knowledge_program_manager.

For TODO 8, I then instantiated an EvaluationAgent for the Program Manager using the provided evaluation persona and the following evaluation criteria:

"The answer should be product features that follow the following structure: " \
"Feature Name: A clear, concise title that identifies the capability\n" \
"Description: A brief explanation of what the feature does and its purpose\n" \
"Key Functionality: The specific capabilities or actions the feature provides\n" \
"User Benefit: How this feature creates value for the user"

9. Instantiating Development Engineer Agents (Before and TODO 9)

Similarly, before TODO 9, I instantiated a KnowledgeAugmentedPromptAgent for the Development Engineer role using the provided persona and knowledge strings.

For TODO 9, I instantiated an EvaluationAgent for the Development Engineer using the specified persona and the following evaluation criteria:

"The answer should be tasks following this exact structure: " \
"Task ID: A unique identifier for tracking purposes\n" \
"Task Title: Brief description of the specific development work\n" \
"Related User Story: Reference to the parent user story\n" \
"Description: Detailed explanation of the technical work required\n" \
"Acceptance Criteria: Specific requirements that must be met for completion\n" \
"Estimated Effort: Time or complexity estimation\n" \
"Dependencies: Any tasks that must be completed first"

10. Instantiating the Routing Agent (TODO 10)

I instantiated the RoutingAgent and defined a list of routing configurations. Each route is represented as a dictionary containing:

name: The role name (e.g., Product Manager)

description: A description of that role’s responsibilities

func: A lambda or function reference that executes the appropriate support logic

Routes were created for the Product Manager, Program Manager, and Development Engineer, and this list was assigned to the agents attribute of the routing agent.

11. Defining Support Functions (TODO 11)

I defined support functions for each role referenced by the routing agent. Each support function:

Accepts an input query (a single step from the action plan).

Calls the corresponding Knowledge Augmented Prompt Agent’s respond() method.

Passes the response to the matching Evaluation Agent’s evaluate() method.

Returns the final validated output, typically from the final_response field.

12. Implementing the End-to-End Workflow (TODO 12)

Finally, I implemented the full agentic workflow:

The Action Planning Agent was used to extract workflow steps from the workflow_prompt.

An empty list named completed_steps was initialized.

Each step was processed sequentially:

The step was printed for traceability.

The step was routed through the routing_agent.route() method.

The validated output was appended to completed_steps.

The result of each step was printed.

After all steps were processed, the final workflow output (typically the last element in completed_steps) was printed.

Summary

This phase demonstrates how independently designed agents can be orchestrated into a cohesive, adaptive workflow that mirrors real-world project management processes. By combining action planning, knowledge grounding, evaluation, and routing, the system dynamically produces structured, role-specific outputs from a single high-level prompt.
