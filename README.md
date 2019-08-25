# Introduction
![Intoduction](https://github.com/kimbring2/MineRL/blob/master/image/18-28-41.png)

The code uploaded here is for MineRL Competition 2019. The purpose of the competition is to find an effective reinforcement learning algorithm using human play datasets.

# Purpose
Requesting in the competition is to resolve MineRLObtainDiamond-v0. In order to solve this, advance work such as moving to a specific place or collecting trees is necessary.

![obtaining a diamond process](https://github.com/kimbring2/MineRL/blob/master/image/19-14-57.png)

# Imitation Learning
The data that people played directly on the game is provided, so I tried to solve the problem by using Imitation Learning first.

## Network Structure
The network structure for Imitation Learning seems to be very simple CNN extracts features on the game screen, processes them through flatten and FC, and finally outputs the probability for each action.

![Structure image](https://github.com/kimbring2/MineRL/blob/master/image/03-17-22.png)

## Result
After completing the learning, the agent can go to the two trees in the environment and attack it to collect the woods. However, it stops. Therefore, the agent cannot collect more wood.

![Treechop-v0 agent video](https://github.com/kimbring2/MineRL/blob/master/monitor/ezgif.com-video-to-gif.gif)

To collect more wood in one environment, I need to solve this problem.

![Treechop-v0 traning loss graph](https://github.com/kimbring2/MineRL/blob/master/image/14-47-20.png)

Loss graph shows that there is no ploblem in traning process. However, it looks like it need more training step.

# Reinforcment Learning
Changing the number and combination of learning behaviors to follow or changing the number of networks did not improve performance any more. So we examined how to use reinforcement learning additionally.

## Network Structure
The reinforcement learning network structure is the same as the supervised learning network structure, but the loss for learning uses the reward value that the agent earns.

## Result
For imitation learning, we first called the weights of the learned networks and used them in reinforcement learning. When experimenting with these procedures, we were unable to confirm an improvement in performance that should be unique.

# What is problem of two approach
At this point, we decided that changes in network type and structure could no longer improve performance. So, other approaches were devised in the case of the TreeChop task.

## Solution
The first thing to consider was that in order to collect items like wood, you had to take attack action for a certain amount of time. If you train agents to take one action per frame without considering the duration of these specific actions, you can easily see that the number has increased significantly.

## Action Example
The agent selects one action using the final value output from the network.

```
if (action2_index == 0):
  action['camera'][0] = 0; action['camera'][1] = -1; action['forward'] = 0; action['jump'] = 0; 
  action['attack'] = 1
elif (action2_index == 1):
  action['camera'][0] = 0; action['camera'][1] = 1; action['forward'] = 0; action['jump'] = 0;
  action['attack'] = 1
elif (action2_index == 2):
  action['camera'][0] = 1; action['camera'][1] = 0; action['forward'] = 0; action['jump'] = 0;  
  action['attack'] = 1
elif (action2_index == 3):
  action['camera'][0] = -1; action['camera'][1] = 0; action['forward'] = 0; action['jump'] = 0; 
  action['attack'] = 1
elif (action2_index == 4):
  action['camera'][0] = 0; action['camera'][1] = 0; action['forward'] = 0; action['jump'] = 0; 
  action['attack'] = 1
elif (action2_index == 5):
  action['camera'][0] = 0; action['camera'][1] = 0; action['forward'] = 1; action['jump'] = 1; 
  action['attack'] = 0
```

Here, in order to collect trees, we added a section that repeats actions based on the output of other networks, considering that the attack action must be continued several times.
```
if (action2_index == 4):
  for q in range(0, action3_index[0]):
    obs1, reward, done, info = env.step(action)
    netr += reward

    if (done == True):
      break
    else:
      obs1, reward, done, info = env.step(action)
```

# How to use a code
First, you need to install dependent package by using a requirement.txt file. 
Second, you need to modify a path for model and summary of tensorflow in MineRL_IL.ipynb file.
Third, you should check code operate properly by running IPython Notebook.
