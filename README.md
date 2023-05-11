### Introduction:
Reinforcement learning is a type of machine learning that focuses on training an
agent to make optimal decisions in an environment by interacting with it. The
agent learns to take actions in the environment to maximize a cumulative reward
from the environment after each action. The goal of reinforcement learning is to
find an optimal policy, which is a mapping from states to actions, that maximizes
the expected cumulative reward over time. Reinforcement learning is different from
supervised learning, where a model is trained on labeled data to make predictions.
The agent explores the environment, takes actions, and learns from the
consequences of its actions, often through trial-and-error.

RL is used to teach an agent to play Doom
by allowing the agent to autonomously interact with the game environment and
learn from feedback (Rt) in the form of rewards or penalties. The agent observes
the state (St) of the game environment, such as the position of the player, enemies,
and objects, and selects an action (At) to take based on a policy. The policy can be
a set of rules or a function that maps states to actions. The selected action is then
executed in the game environment, and the agent receives a reward or penalty
based on the outcome of the action.

For example, if the agent kills an enemy, it receives a positive reward, while if it
takes damage or dies, it may receive a negative penalty. The agent uses these
rewards and penalties to update its policy and improve its performance over time.
The main goal is defined by the environment the agent is in. In this project, the
agent is placed in 3 environments or levels: Basic, Defend the Center and Deadly
Corridor. These environments are predefined by the config file from the ViZDoom
library. However, users can define additional variables (weapons, armor or metrics
such as Damage_Taken, Killcount) to nudge the agent’s actions in the appropriate
environment. Another set of attributes that can be used to modified to improve
the agent’s interactions is the action space, which is the set of actions the agent
can perform during exploration and eventually exploit to maximize the reward
(depending on the level) leveraging the action space and variables to understand
the objective and thereby complete it.

Training is performed using Temporal Difference Learning where the agent is
trained at each timestep, this is done for multiple episodes where each episode
starts with the agent placed in the initial state of the environment and ends when
the agent dies or if the objective is completed or simply when the total
timesteps/permitted actions allotted for the episode ends. The trained agent is
then placed in an evaluation environment where we test the agent’s ability to
complete the objective in another or more complex environment.

To evaluate the performance we use tensorboard to generate time series plots of
the performance metrics such as average reward, length of an episode, and policy
gradient loss among others which are generated from the logs upon training.

ViZDoom provides a realistic and challenging first-person shooter game
environment that is well-suited for testing and evaluating RL algorithms. The game
contains complex dynamics and high-dimensional sensory input, making it an ideal
testbed for developing and evaluating RL algorithms. It allows users to define and
customize a variety of tasks and scenarios, ranging from simple navigation tasks to
complex combat scenarios. This flexibility allows developers to explore a wide
range of RL algorithms and techniques, and to test the performance of these
algorithms under different conditions. This is why the project appealed to me,
along with easy integration with with OpenAI Gym, Pytorch and Tensorflow for
flexibility in evaluating RL algorithms.


## Task Definition:

## 1. Agent Tasks

### Navigation: 
The agent is tasked with navigating through a complex mazelike
environment to reach a specific goal location. The agent can receive a
positive reward for reaching the goal and a negative reward for hitting
obstacles or taking too long to reach the goal

### Combat:
The agent is tasked with defeating one or more enemies in a combat
scenario. The agent can receive a positive reward for killing enemies and a
negative reward for taking damage or being killed. In more complex levels
the agent learns to strategize i.e., improving aim, taking cover and taking the
shortest path, etc.,

### Exploration:
The agent is tasked with exploring a complex environment to
discover hidden objects or areas. The agent can receive a positive reward for
discovering new weapons and a negative reward for getting stuck or
repeating actions.

### Survival:
The agent is tasked with surviving in a hostile environment with
limited resources. The agent can receive a positive reward for finding and
using resources (such as ammo or health packs) and a negative reward for
taking damage or running out of resources.
These tasks are defined using actor-critic methods. The specific algorithm used
will depend on the task and the desired performance metrics as we will see in
the next section.


## 2. Envrionments

The algorithm focuses on teaching the agent to navigate 3 environments
- Basic
- Defend the Center (Survival)
- Deadly Corridor (Survival + Goal)

Training is done through episodes. An episode is a sequence of interactions between
an agent and an environment, starting from an initial state and ending in a terminal
state. During an episode, the agent takes actions based on its current state, and
receives rewards and observations from the environment in response to its actions.
The length of an episode can vary depending on the task and the environment
(defined in the config file). The concept of episodes is important in reinforcement
learning because it allows the agent to learn from multiple experiences and to
update its policy based on the cumulative rewards received over multiple time steps.
By running multiple episodes, the agent can explore different strategies and learn to
optimize its behavior for the given task.

### - Basic

In this environment the agent has 3 available actions, move left, right and shoot. This
level is very straightforward wherein the agent (player) spawns on one side of the
wall. The objective is to shoot the enemy who spawns randomly across from the
agent (other side of the wall or arena) 

The goal is to shoot the enemy as quickly as possible (least number of timesteps),
Episode finishes when monster is killed or on timeout. Map is a rectangle with gray
walls, ceiling and floor. Player is spawned along the longer wall, in the center. A red,
circular monster is spawned randomly somewhere along the opposite wall. Player
can only (config) go left/right and shoot. 1 hit is enough to kill the monster.
REWARDS:  
+101 for killing the monster   
-5 for missing shots  
living reward = -1 (incentivizes killing off the enemy quickly)


### - Survival Environment
Here, the main objective of the agent is to survive as long as possible while a hoard
of enemies attack the agent from all sides. The purpose of this scenario is to teach
the agent that killing the monsters is GOOD and when monsters kill you is BAD. In
addition, wasting ammunition is not very good either. Agent is rewarded only for
killing monsters so he has to figure out the rest for himself.
Map is a large circle. Player is spawned in the exact center. 5 melee-only, monsters
are spawned along the wall. Monsters are killed after a single shot. After dying
each monster is respawned after some time. Episode ends when the player dies (it's
inevitable because of limited ammo).
REWARDS: +1 for killing a monster  
death penalty = 1

### - Deadly Corridor Environment
The purpose of this scenario is to teach the agent that killing the monsters is GOOD
and when monsters kill you is BAD. In addition, wasting ammunition is not very good
either. Agent is rewarded only for killing monsters so he has to figure out the rest
for himself.

Map is a large circle. Player is spawned in the exact center. 5 melee-only, monsters
are spawned along the wall. Monsters are killed after a single shot. After dying each
monster is respawned after some time. Episode ends when the player dies (it's
inevitable because of limited ammo).  
REWARDS: +1 for killing a monster  
death penalty = 1

