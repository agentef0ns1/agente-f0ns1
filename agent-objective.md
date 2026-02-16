---
layout: default
---

# Defining the Agent’s Objective

A clear objective is critical for an autonomous pentesting agent. It determines what the agent is allowed to do and when it should stop.

---

## Scope of the Pentest

Define:

- Target systems (IP ranges, domains, environments)  
- Allowed tools (e.g., nmap, curl, custom scripts)  
- Time and resource limits  
- Data sensitivity and storage rules  

---

## Legal and Ethical Boundaries

Your agent must only operate in **authorized environments**.

- Always have written permission  
- Log all actions for auditability  
- Avoid destructive actions unless explicitly allowed in a lab  

You can encode some of these constraints in the **system prompt** and in the **agent logic**.

---

## Success Criteria

I don't want to write long texts related to ethics. As with all software, the actions you take and what you want to train your model on are your responsibility.The example that will be continued throughout the course is to create an expert model in network enumeration with Nmap.

A good enumeration in a network is complex, and nmap contains many parameters, so the number of combinations that our model must learn depending on the situation it is in or that it is instructed to analyze will be very high.

To learn, it needs two things:
- A good dataset with normalized data, as extensive as possible.
- An environment to learn in, with an algorithm that teaches it.

---

[back](./)