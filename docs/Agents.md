# Agents

## Overview

This section details agents that already exist in the current infrastructure, aswell as instructions and steps for creating new agents.

## Current Agents

The current implementation of the AI Bot is mainly based upon the Initial Risk Assessement in which only contains Tier 1 questions. Each Tier 1 question is related to a specific domain. As such each Tier 1 domain has a specific agent that is assigned to it. 

The complete list of agents are:


* Access Control
* Anti-virus
* Asset-Management
* Backups
* Data Protection Privacy
* Incident Response
* System Security 
* Training Awareness
* Greeting

To Reiterate, each of the above agents are assigned to a particular domain within Tier 1 and will only ask questions related to there domain. For example the Access Control Agent mentioned above will only ask Tier 1 questions in the Access Control domain, the Asset Management Agent will only ask Tier 1 questions in the Asset Management domain and so on.

The greeting agent is the only exception to this. Instead of focusing on a domain it is specifically meant to greet the user and to kick off the assessment.

All the above agents and there configurations can be found in the  `src/app/agentConfigs/initialRiskAssessment` directory


## Creating an Agent

### Anatomy of an Agent

All Agents must be defined in the `src/app/agentConfigs/` directory. To add a new agent simply create a new config file in this directory. It is best to name the file something descriptive to its function. 

An agent is primarily composed of 5 key values within an object:


| Field    | Description |
| -------- | ------- |
| Name  | The name of the agent    |
| publicDescription | A description of a particular agents function and capabilities    |
| Instructions    | The specific instructions given to an agent to carryout    |
| Tools    | The various tools a particular agent has access to    |
| downstreamAgents    |  Other agents that the current agent can handoff/transfer to    |


A simple example of creating an agent with this structure can be found in `src/app/agentConfigs/simpleExample.ts` as shown below:


```ts title="simpleExample.ts" linenums="1"
import { AgentConfig } from "@/app/types";
import { injectTransferTools } from "./utils";

// Define agents
const haiku: AgentConfig = {
  name: "haiku",
  publicDescription: "Agent that writes haikus.", // Context for the agent_transfer tool
  instructions:
    "Ask the user for a topic, then reply with a haiku about that topic.",
  tools: [],
};

const greeter: AgentConfig = {
  name: "greeter",
  publicDescription: "Agent that greets the user.",
  instructions:
    "Please greet the user and ask them if they'd like a Haiku. If yes, transfer them to the 'haiku' agent.",
  tools: [],
  downstreamAgents: [haiku],
};

In addition to this Agents can have a predefined state machine

// add the transfer tool to point to downstreamAgents
const agents = injectTransferTools([greeter, haiku]);

export default agents;
```

Once you have finished creating your agent you must create an index.ts file. This file agregates all your agents into an agent set, in which can then be accessible via the front end. You can also define your down stream agents here aswell.


This can be found in `src/app/agentConfigs/index.ts` of this can be found here:

``` ts title="index.ts"
import { AllAgentConfigsType } from "@/app/types";
import frontDeskAuthentication from "./frontDeskAuthentication";
import customerServiceRetail from "./customerServiceRetail";
import simpleExample from "./simpleExample";
import riskAssessment from "./riskAssessment";
import initialRiskAssessment from "./initialRiskAssessment";

export const allAgentSets: AllAgentConfigsType = {
  frontDeskAuthentication,
  riskAssessment,
  customerServiceRetail,
  simpleExample,
  initialRiskAssessment,
};

export const defaultAgentSetKey = "initialRiskAssessment";
```


### Prompting an Agent

An agents capability and performance is heavily based on how you prompt it. Within this infrastruture much of the functionality is defining through prompting the Agent via its instruction field. Thus it is best to be as descriptive as possible when prompting the AI. 

To assist with this we have provided a voice agent metaprompter template for you to build your prompts. This can be found in `src/app/agentConfigs/voiceAgentMetaprompt.txt`. Simply follow the instructions within this file to create your prompt.

### Creating a State Machine

<TO BE FILLED OUT>

### Multi-Agents

To create a MultiAgent infrastructure: 

1. Simply package all of your individual agents into a particular directory. 

  For an example of this view `src/app/agentConfigs/frontDeskAuthentication`

2. Add an index.ts file within this directory that specifies all the downstream agents

  For an example of this view `src/app/agentConfigs/frontDeskAuthentication/index.ts`

3. Modify the `index.ts` file within /src/app/agentConfigs/index.ts to account for your new multi-agent framework

  For an example of this view `src/app/agentConfigs/index.ts`
  
## Next Steps 

Learn how to add tools to your agents within the [Tools](Tools.md) section.





