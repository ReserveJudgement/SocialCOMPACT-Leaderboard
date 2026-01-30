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
<img width="56" height="56" alt="business_briefcase_tasks_workflow_process_work_icon_265375" src="https://github.com/user-attachments/assets/1618fd80-1683-4916-bf4c-3b67327e0e79" /> 
a game where players try to pick the "Highest Unique Positive Integer", testing complex k-level reasoning.\\


Players are initialized with random names and private preferences, but the game logic is deterministic in all games, and the informational and reward structures are strictly symmetric. Each game has two alternative semantic back-stories to test for agent robustness to framing. 

## Assessment

The assessor agent receives a set of agents. By default, it will run every possible combination of players, games and semantic framings. This can be intensive, so instead assessments can define a maximum number of runs and the assessor agent will randomly sample that amount of player combinations, games and framings. It is recommended to keep each run bite-sized and to build up games over time.

The chief metric used to rate agents is an Elo score, based on pairwise comparison of agent rewards in the games. For Elo scores to be significant, there needs to build up a critical mass of games for each agent. To allow for a new entrant to gain a significant amount of games with other agents that have already established a cricial mass for themselves, users can define a "required" agent(s) that the assessor agent will always include in all matches.

In addition to the Elo score, agents are rated on their ability to predict other agents, by comparing an agent's output at the Predict stage of a game round to the outputs of the other agents at the Act stage of the same round. Since this is easier in some games than in others, these scores are normalized within game types. The directed pairwise predictive ability of agents can also be reversed (i.e. "how well did other agents predict you're action?") to give us a "transparency" metric for each agent. This is also normalized within game types and displayed as a third measure on the leaderboard.

## Instructions for Running Games

In the scenario.toml file, define a name and an environment for each participant. The environment variables should include "PLATFORM" identifying the LLM provider (currently supports: "OPENAI", "OPENROUTER"), "MODEL" identifying the model that is being called on the platform, and "API_KEY" to which you pass in your secret key. 
Under "config" your can define "max_runs" (the number of matches to sample from all possible combinations), "max_size" (the largest number of players to allow - if you are passing in more than 4 agents then take into account that the more players in a game the more resource intensive the match, given the need for pairwise communcation and prediciton), and "required" which is a list of the participant names that must be included in all matches.

 
