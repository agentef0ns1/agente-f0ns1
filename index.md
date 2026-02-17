# Autonomous Pentesting Agents with Local LLMs

Welcome to this course on building your own **autonomous security‑testing agent** powered by a **local Large Language Model (LLM)**.  
The goal of this project is to teach you how to design, train, deploy, and run an autonomous agent capable of performing controlled pentesting tasks while maintaining full data privacy and offline execution.

---

## Introduction

Autonomous agents are systems capable of making decisions, planning actions, and executing tasks without continuous human intervention. When combined with a **local LLM**, these agents can analyze environments, generate hypotheses, execute commands, and iterate on results in a closed feedback loop.

This course focuses on a **single‑connection architecture**, where the agent interacts with a **local LLM running on Ollama**. This architecture ensures:

- Full data privacy (no external API calls)  
- Low latency  
- High control over model behavior  
- Reproducible and isolated execution  
- Compatibility with containerized environments (Docker)  

The agent runs inside a **Docker container**, while the LLM runs locally on the host machine through Ollama. The agent communicates with the LLM through a single HTTP endpoint, and can **execute OS commands and actions instructed by the LLM**, closing the loop between reasoning and real system interaction.

---

## Architecture Diagram

![Architecture Diagram](./assets/images/Local-architecture.png)


### 1. [Installing Ollama on Linux](./install-ollama-linux.md) 
### 2. [Searching for Local Models](./search-local-models.md) 
  - #### 2.1 [Running the Model on a Local Web Interface](./run-model-web.md) 
### 3. Training Your Agent 
  - #### **3.1 [Defining the Agent’s Objective](./agent-objective.md)** 
  - #### **3.2 [Creating the Dataset](./dataset-creation.md)** 
  - #### **3.3 [Training the Model](./model-training.md)** 
  - #### **3.4 [Creating and Running the Model in Ollama](./ollama-custom-model.md)** 
### 4. [Building the Agent Inside a Docker Image](./docker-agent.md) 
### 5. [Testing, Execution, and Improvements](./testing-and-improvements.md)
