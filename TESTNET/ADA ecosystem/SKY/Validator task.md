# Validator Guide

How to stake and validate tasks as a Validator

This guide provides step-by-step instructions for the entire Validator workflow. By the end you will have successfully staked as a Validator, requested task assignment(s), run a validator script, uploaded loss score(s), and claimed your staking rewards.

Be sure you have completed all pre-requisite tasks.

You can only choose one role per task!

## 1. Stake `FML` as `Validator` from `train.flock.io`
In order begin receiving validation assignments, you must stake FML as to a task at `train.flock.io`.

![image](https://github.com/user-attachments/assets/7cfef769-abcc-4a72-9778-b352e2fe9237)


Select `task` and `stake FML`

Navigate to the `Validator` tab on the `Stake page`

Select `task` to validate

Enter number of `FML tokens` to stake

Stake FML

Once you’ve confirmed and approved the transaction in `your web3 wallet`, you will see a box on the `Validator tab` with `your stake details`.

![image](https://github.com/user-attachments/assets/420cfdcb-5ef7-4738-af11-2f766a2700ac)


## 2. Get `API key`

Your `API key` is required for all remaining `Validator` steps. You can get it from the web app.

![image](https://github.com/user-attachments/assets/ea249ff3-c459-4cf3-ae5f-9c7d776b2d4a)

![image](https://github.com/user-attachments/assets/0cac8e6e-485a-4ec5-a044-894d025c6568)


Select `dropdown` in upper right corner of web app

Select `API`

On the `API page`, copy `your API key`

Once you have `your API key`, you can proceed to the `next step`.

`NOTE: If you have issues generating an `API key` try removing any `ad blocker extensions` and/or `clearing your cookies`.`

Video Tutorial:

https://youtu.be/qmHTxYRAzAM

## 3. Set up your environment

To set up your environment, run the following commands:

Windows
For Windows users, we suggest installing `WSL`. Follow the guidance: `WSL installation`

Mac/Linux
You can install `Anaconda` via `HERE`: `https://www.anaconda.com/download/success`

## 4. Clone the Validator Repo

The validator repo contains everything you need to run our script. To clone it run:

```
git clone https://github.com/FLock-io/llm-loss-validator.git
```

To set up all packages within the project directory

```
conda create -n llm-loss-validator python==3.10
conda activate llm-loss-validator
pip install -r requirements.txt
```

## 5. Run Validation Script

Now that you have staked as a `Validator` and have `your API key` you can run the `validation script` by running the following loop in your command line:

Unix/Linux
WSL
PowerShell

```
cd /src
CUDA_VISIBLE_DEVICES=0 \
bash start.sh \
--hf_token your_hf_token \
--flock_api_key your_flock_api_key \
--task_id your_task_id \
--validation_args_file validation_config.json.example \
--auto_clean_cache False
```

### NOTE: If you would like to customise your validation script, visit this repo `https://github.com/FLock-io/llm-loss-validator` and check out How to run validation script section

Make sure the `HF_TOKEN` you passed in havs access to the gated base models like `gemma`, `llama2`, and `llama3` to succesfully validate models fine-tuned based on those.

Links:

```
https://huggingface.co/google/gemma-7b
```
```
https://huggingface.co/meta-llama/Meta-Llama-3-8B
```
```
https://huggingface.co/meta-llama/Llama-2-7b-hf
```

## 4. Claim rewards
Reward distribution is triggered every `24 hours` at `midnight UTC`. You can claim your rewards via the `web app`.

Once you’ve connected your web3 wallet to `train.flock.io`, you can `claim rewards` with the following steps:

Navigate to the `Validator tab` on the `Stake page`

Verify you have `FML` available to `claim` for the task you are staking

Click `Claim FML`
![image](https://github.com/user-attachments/assets/3cea7314-90ee-4969-b0aa-caebd73e6ac5)


Once you’ve approved the transaction in `your web3 wallet` you will see a dialogue box confirming your claim.

![image](https://github.com/user-attachments/assets/cb9df28c-ea0a-4b7a-98b2-dd2c849d13ee)


# Congratulations! You’ve successfully completed the entire validator workflow.
