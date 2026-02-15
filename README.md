# SocialCOMPACT

## Background
AI agents are poised to take on roles in the real world, interacting with third parties, including other people and other agents, requiring them to navigate complex social situations. This creates a need for evaluating social intelligence. Social intelligence involves the ability to achieve objectives in mixed cooperative-competitive multi-agent environments of variable sizes and compositions, while communicating and interacting with other agents over time, possibly under uncertainty. 

SocialCOMPACT is an arena of such games. At each round of a game, agents must first communicate with each other in natural language, then expressly anticipate each others' actions, and then reason about their own actions and make decisions (COMmunicate-Predict-ACT). The communication stage is conducted pairwise between players with limited message-passing in natural language. The prediction stage requires the players to privately reflect on the conversations and the history of the game and predict the next actions of the other players. Players then simultaneously reason strategically and make their next decision.

## Games
There are five classic games in the arena:

### Survivor
<img width="56" height="56" alt="military-ship" src="https://github.com/user-attachments/assets/d2def0dc-cccd-409e-9bc7-25880af83600" /> a simplified version of "Diplomacy" (or "Risk") but with no board. Players are given ammunition and lives, and their aim is to eliminate all other players and remain the last one standing. The game tests alliance formation and betrayal; 

### Tragedy of the Commons
<img width="56" height="56" alt="earth" src="https://github.com/user-attachments/assets/02e378a0-905e-4492-887b-6d1abbc7bb70" /> a public resource utilization game. There is a limited resource which can only be preserved over time if the players show self-restraint, but which could also invite free-riders. The game tests social dilemmas between the individual and the collective good;

### Coalition
<img width="56" height="56" alt="coalition2" src="https://github.com/user-attachments/assets/28e8cf4b-a448-4bef-a4e0-0403fd65bd3f" /> a classic cooperative game where a subset of players form a coalition, dependent on an agreement regarding the distribution of the benefits. The game tests group formation and resource allocation;

### Scheduler
<img width="56" height="56" alt="schedule" src="https://github.com/user-attachments/assets/228d6989-100e-45a8-be46-d983f41d036a" /> a multiplayer generalization of "Battle of the Sexes", where players seek agreement on a choice from a set of options with different preference orders, but failure to coordinate is the worst case for all. The game tests coordination and compromise under preference conflict;

### HUPI
<img width="56" height="56" alt="HUPI" src="https://github.com/user-attachments/assets/b489deb7-ec69-4454-9f2e-598546d5abc4" />
a game where players try to pick the "Highest Unique Positive Integer", testing complex k-level reasoning.  

<br />
<br />

Players are initialized with random names and private preferences, but the game logic is deterministic in all games, and the informational and reward structures are strictly symmetric. Each game has two alternative semantic back-stories to test for agent robustness to framing. 

For full game descriptions, see the pdf file "GameDescriptions.pdf".

## Agent Skills and Game Flow

All communications between the assessor and the agents are handled in text. The assessor passes a json-formatted string to the agent, in which the "task" key identifies the type of response required of the agent, the "message" key contains the prompt for the agent, and the "info" key might include some extra information. In the first communication at the beginning of a game, the "task" is set to "background" and the "message" contains a prompt providing the agents with the game description, their names and their private preferences, with no response required. During the game, there are three basic agent skills that are called upon iteratively, which are triggered by the "task". These are: "chat", then "predict" and then "act". At the end of each round in a game, the "task" key is set to "observe", to signify that the incoming message has observations resulting from the agent actions in the last round, but no response is required (the agent can optionally use the opportunity to reflect). Then the chat-predict-act sequence recylces in the next round.

In response to "chat" messages from the assessor, the agents are required to formulate communications for each other. The chats are handled centrally through the assessor which is responsible for passing the communications between the agents. In response to "predict" and "act" messages from the assessor, agents are required to respond in json-formatted strings. These have game-specific structure, which are defined in the assessor message. Agents will also be required to provide reasoning between <reasoning> </reasoning> tags before the predictions or actions. Reasoning produced by the agents is useful for later analysis but not used in the current leaderboard evaluation. See the source code for the arena and for the baseline agent for details in handling assessor messages.

Ideas for agent development beyond the baseline:
- Chain of thoughts or promptimization for any or all of the COMPACT stages.
- External tools (e.g. bayesian calculations for predictions, game-theoretic optimization for decisions etc.).
- Fine-tuning the base LLM.

## Assessment

The assessor agent receives a set of agents and orchestrates their participation in the games, in multiple compositions of players.


The chief metric used to rate agents is an Elo score, based on pairwise comparison of agent rewards in the games. For Elo scores to be significant, there needs to build up a critical mass of games for each agent. Preferably, they should also be balanced across the game types. 


In addition to the Elo score, agents are rated on their ability to predict other agents' actions, by comparing an agent's output at the Predict stage of a game round to the outputs of the other agents at the Act stage of the same round. Since the difficulty of the prediction task and the form of the action space vary across games, these scores are normalized within game types (where the combination of game category and the number of players constitutes a "game type"), and rescaled to the interval [0, 1] before taking the mean for each agent. This gives us an indication of relative predictive capabilties between agents. 


The directed pairwise predictive ability of agents can also be reversed (i.e. "how well did other agents predict this agent's action?") to give us a "transparency" metric for each agent. This is also normalized within game types and rescaled, and the mean per agent is displayed as a third measure on the leaderboard.


Why these metrics?
- Social intelligence must generalize across both games and player compositions. An Elo score is comparative between players. It is also based on binary pairwise comparisons (who got a higher score) thereby ignoring the idiosyncracies of the per-game reward structures.
- Prediction accuracy of actions is a proxy metric for "Theory of Mind" (modeling other agents). Many believe that this is fundamental to social intelligence, and whether they are an emergent property in LLMs is controversial.
- The transparency metric reflects how well intentions are communicated (or obfuscated). Since communication is a crucial part of social interaction, this is an important measurement.
- It isn't clear how prediction or transparency (or other social metrics that aren't in the current leaderboard but can be extracted from the game data) might lead to different outcomes. Experiments with LLM agents can shine new light on how elements such as "Theory of Mind" or clarity of intent relate to actual performance in social settings. 

## Instructions for Running Games

Fork the repository. In the scenario.toml file, define a name and an environment for each participant. 
- The agent "name" should be a unique identifier that is reused across tests. Preferably reflecting the agent methodology and/or LLM that is being used.
- The environment variables defined in "env" should include:
    - "PLATFORM": identifying the LLM provider (currently supports: "OPENAI" and "OPENROUTER");
    - "MODEL": identifying the model that is being called on the platform, and
    - "API_KEY": the api key for using the platform. For security, use Github secrets to pass this in. 
- The "config" can define the following optional parameters:
    - "max_runs": a budget of matches to sample from all possible combinations of games/agents (defaults to every possible combination of 5 games X 2 framings X all possible agent compositions from 2-player to full). This configuration parameter can be useful as the amount of possible combinations can become intensive when several agents are passed in.
    - "max_size": the largest number of players to allow in each match (defaults to the number of agents passed in to the assessor agent). This configuration parameter can be useful when there are several agents passed in, since large game sizes can become intensive (since the pairwise communication and predictions that occur between players grows quadratically with number of players).
    - "min_size": the minimum number of players to allow in each match (defaults to 2). This can be useful to induce more multiplayer games.
-  "required": a list of the participant names that must be included by the assessor agent in all matches. This is useful to enable a new entrant to gain a critical mass of games with other agents which have already run enough games between themselves. 

 
