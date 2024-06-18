Super Constrained Variational Policy Optimization for Safe Reinforcement Learning
==================================


SCVPO is a novel Expectation-Maximization approach to naturally incorporate constraints during the policy learning,It has the ability to take into account past strategies to make updates smoother.


## Table of Contents

- [Super Constrained Variational Policy Optimization for Safe Reinforcement Learning](#constrained-variational-policy-optimization-for-safe-reinforcement-learning)
  - [Table of Contents](#table-of-contents)
  - [Installation](#installation)
      - [1. System requirements](#1-system-requirements)
      - [2. System-wise dependencies installation](#2-system-wise-dependencies-installation)
      - [3. Anaconda Python env setup](#3-anaconda-python-env-setup)
  - [Training](#training)
    - [How to run a single experiment](#how-to-run-a-single-experiment)
    - [How to run multiple experiments in parallel](#how-to-run-multiple-experiments-in-parallel)
  - [Check experiment results](#check-experiment-results)
  - [Notes and acknowledgments](#notes-and-acknowledgments)

The structure of this repo is as follows:
```
Safe RL libraries
├── safe_rl  # core package folder
│   ├── policy # safe model-free RL methods
│   ├── ├── model # stores the actor critic model architecture
│   ├── ├── policy_name # RL algorithms implementation
│   ├── util # logger and pytorch utils
│   ├── worker # collect data from the environment
│   ├── runner.py # core module to connect policy and worker
├── script  # stores the training scripts.
│   ├── config # stores some configs of the env and policy
│   ├── run.py # launch a single experiment
│   ├── experiment.py # launch multiple experiments in parallel with ray
│   ├── button/circle/goal.py # hyper-parameters for each experimental env
├── data # stores experiment results
```

## Installation
#### 1. System requirements
- Tested in Ubuntu 20.04, should be fine with Ubuntu 18.04
- I would recommend to use [Anaconda3](https://docs.anaconda.com/anaconda/install/) for python env management

#### 2. System-wise dependencies installation
Since we will use `mujoco` and `mujocu_py` for the `safety-gym` environment experiments, so some dependencies should be installed with `sudo` permissions. To install the dependencies, run
```
cd envs/safety-gym && bash setup_dependency.sh
```
And enter the sudo password to finish dependencies installation.

#### 3. Anaconda Python env setup
Back to the repo root folder, **activate a python 3.6+ virtual anaconda env**, and then run
```
cd ../.. && bash install_all.sh
```
It will install the modified `safety_gym` and this repo's python package dependencies that are listed in `requirement.txt`. Then install pytorch based on your platform, see [tutorial here](https://pytorch.org/get-started/locally/).

Some experiments (CarCircle, BallCircle) are done in BulletSafetyGym, so if you want to try those environments, install them with following commands:
```
cd envs/Bullet-Safety-Gym
pip install -e .
```

## Training
### How to run a single experiment
Simply run
```
python script/run.py -p cvpo -e SafetyCarCircle-v0
```
where `-p` is the policy name, `-e` is the environment name. More configs could be found in `script/config` folder and in `run.py` and in `safe_rl/runner.py`.

To evaluate a trained model, run:
```
python script/run.py -m eval -d /model_dir -e SafetyCarCircle-v0
```
Note that if you are going to render bullet_safety_gym environments, such as `SafetyCarCircle-v0`, you need to add the argument `-e SafetyCarCircle-v0`.

### How to run multiple experiments in parallel
We use the [Ray Tune](https://docs.ray.io/en/latest/tune/index.html) tool to conduct experiments in parallel.
For instance, running all the off-policy methods in the button environments, run:
```
python script/experiment.py cvpo sac_lag ddpg_lag td3_lag --env button --cpu 4 --thread 1
```
where `--env` is the environment name and should be selected from `button`, `circle` or `goal`. `--cpu` specifies the maximum cpu you want to use to run all the experiments, and `--thread` is the cpu resource for each experiment trial. See [Ray](https://docs.ray.io/en/latest/tune/index.html) for more details.


## Check experiment results

You may either use `tensorboard` or `script/plot.py` to monitor the results. All the experiment results are stored in the `data` folder with corresponding experiment name.

For example:
```
tensorboard --logdir data/experiment_folder
python script/plot.py data/experiment_folder -y EpRet EpCost
```


