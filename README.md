# DEBATunE (ACL'24)

[Can LLMs Speak For Diverse People? Tuning LLMs via Debate to Generate Controllable Controversial Statements](https://arxiv.org/abs/2402.10614) (ACL'24)<br>
Chinese Version: [[知乎]](https://zhuanlan.zhihu.com/p/720237237)

<p align="center" width="40%">
<a ><img src="images/alpaca_debate.png" alt="overview" style="width: 40%; min-width: 300px; display: block; margin: auto;"></a>
</p>

This is the repo for the DEBATunE project, which introduces a novel debate & tuning ("DEBATunE") pipeline finetuning LLMs to equip LLMs with the capability to **generate controllable controversial statements via debate.**

The repo contains:

- The code for this project.
- The largest debate topic dataset so far that contains 710 debate topics. 
- The debate data used for fine-tuning the model. 
- The model checkpoints (7B) that were trained using our data.

(Feel free to email minglii@umd.edu for any questions or feedback.)

## News
- [2024/05] Our paper has been accepted to the **ACL 2024** findings! 
- [2024/02] We released the paper, codes, and data for this project.

## Contents
- [Overview](#overview)
- [Highlights](#highlights)
- [Install](#install)
- [Code for DEBATunE](#code-for-debatune)
- [Code for Evaluation](#code-for-evaluation)
- [Data](#data)
- [ToDo](#todo)
- [Citation](#citation)
- [Our Related Works](#our-related-works)

## Overview

Making LLMs speak for different, especially minority groups of people, and generate statements supporting their diverse or even controversial perspectives is critical to creating an inclusive environment. However, existing LLMs lack sufficient controllability to the stance of their generated content, which often contains inconsistent, neutral, or biased statements. In this paper, we improve the controllability of LLMs in generating statements supporting an argument the user defined in the prompt. We find that multi-round debates between two LLMs with opposite stances generate higher-quality and more salient statements for each, which are important training data to improve the controllability of LLMs. Motivated by this, we develop a novel debate & tuning ("DEBATunE") pipeline finetuning LLMs to generate the statements obtained via debate. To examine DEBATunE, we curate the largest dataset of debate topics so far, which covers 710 controversial topics and corresponding arguments for each topic. Evaluations by the GPT-4 judge with a novel controversy controllability metric show that LLMs' capability of expressing diverse perspectives is significantly improved by DEBATunE. Moreover, such controllability can be generalized to unseen topics, generating high-quality statements supporting controversial arguments. 

<p align="center" width="40%">
<a ><img src="images/main.png" alt="overview" style="width: 40%; min-width: 300px; display: block; margin: auto;"></a>
</p>

The pipeline of our DEBATunE. In the **Debate phase (top)**, the agents are prompted to debate upon the given topic with an argument. After several rounds of debate, an agent (positive in the example) concludes the debate based on all the previous debate records. 
The conclusion is a more salient, detailed, and higher-quality statement for the agent. It will be used to train an LLM in the **Training phase (bottom)** to improve the controllability of generating statements for the given stance (positive in the example). 

## Highlights

* While existing works focus on achieving a consensus on divergent opinions to finetune LLMs, we study a novel debate pipeline that instead **strengthens the statements of controversial stances** and uses them to **improve the controllability of LLMs in expressing different opinions of diverse people**.
* We develop a dataset comprising 710 controversial debate topics, which is the largest dataset of its kind so far, and introduce a metric assessing LLM's controllability on controversial topics.
* We are the first to **evaluate several open-sourced LLMs on controversial debate topics** and analyze the existing models' strengths and limitations in this specific context.

## Install

Install the dependencies with `pip install -r requirements.txt`

## Code for DEBATunE

1. Debate
```
bash scripts/step1_do_debate.sh
```

```json_path```: Debate topic path.<br>
```save_path```: Debate log save path.<br>
```arg_num```: Number of auguments for each topic.<br>

2. Make the SFT Dataset
```
bash scripts/step2_make_train_data.sh
```

```json_path```: Debate log path.<br>
```save_path```: SFT data save path. 

## Code for Evaluation

1. Do Inference

```
bash evaluation/scripts/step1_generate_inference.sh
```

```prompt```: The template used for finetuning. This code contains the templates of ```Alpaca```, ```WizardLM```, ```Vicuna```, ```LLaMA2 Chat```, ```ZEPHYR```.<br>
```json_path```: The test set, no need to change.<br>
```model_name_or_path```: Model path to be tested.<br>
```output_dir```: Output path.<br>

2. Controversy Controllability

```
bash evaluation/scripts/step2_eval_controllability.sh
```

```result_file```: The inference results obtained from the previous step.<br>
```save_name```: The path to save the Controversy Controllability evaluate results. <br>

Note: In the script, we utilize the results of our DEBATunE 7b as an example. 

3. Response Quality

```
bash evaluation/scripts/step3_eval_quality.sh
```

```dataset_name```: No need to change.<br>
```fname1```: The result path of LLM 1.<br>
```fname2```: The result path of LLM 2.

Note: This code partially originated from [Cherry_LLM](https://github.com/MingLiiii/Cherry_LLM), which contains common pair-wise evaluation prompts. <br>
In the script, we utilize the comparison between our DEBATunE 7b and Vicuna 7B v1.5 as an example. 

## Data

1. Our manually collected and filtered debate topic data can be found in ```Debate_topic_only```. <br>
2. The hold-out test set with arguments can be found in ```Debate_topic_arguments/debate_test_v1_arg.jsonl```. <br>
3. The instruction tuning data set can be found in ```Debate_data_SFT/Final_v1_ChatGPT_2round_5argument.json```.<br>
4. We also provide the inference results on the hold-out test sets of some existing LLMs for better understanding, which can be found in ```inference_results_of_exsisting_models```.

## ToDo
- [x] Release the code, data, and models. 

## Citation

Please consider citing our paper if you think our codes, data, or models are useful. Thank you!
```
@inproceedings{li-etal-2024-llms-speak,
    title = "Can {LLM}s Speak For Diverse People? Tuning {LLM}s via Debate to Generate Controllable Controversial Statements",
    author = "Li, Ming  and
      Chen, Jiuhai  and
      Chen, Lichang  and
      Zhou, Tianyi",
    editor = "Ku, Lun-Wei  and
      Martins, Andre  and
      Srikumar, Vivek",
    booktitle = "Findings of the Association for Computational Linguistics ACL 2024",
    month = aug,
    year = "2024",
    address = "Bangkok, Thailand and virtual meeting",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2024.findings-acl.956",
    pages = "16160--16176",
}

@inproceedings{li-etal-2024-selective,
    title = "Selective Reflection-Tuning: Student-Selected Data Recycling for {LLM} Instruction-Tuning",
    author = "Li, Ming  and
      Chen, Lichang  and
      Chen, Jiuhai  and
      He, Shwai  and
      Gu, Jiuxiang  and
      Zhou, Tianyi",
    editor = "Ku, Lun-Wei  and
      Martins, Andre  and
      Srikumar, Vivek",
    booktitle = "Findings of the Association for Computational Linguistics ACL 2024",
    month = aug,
    year = "2024",
    address = "Bangkok, Thailand and virtual meeting",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2024.findings-acl.958",
    pages = "16189--16211",
}

@inproceedings{li-etal-2024-superfiltering,
    title = "Superfiltering: Weak-to-Strong Data Filtering for Fast Instruction-Tuning",
    author = "Li, Ming  and
      Zhang, Yong  and
      He, Shwai  and
      Li, Zhitao  and
      Zhao, Hongyu  and
      Wang, Jianzong  and
      Cheng, Ning  and
      Zhou, Tianyi",
    editor = "Ku, Lun-Wei  and
      Martins, Andre  and
      Srikumar, Vivek",
    booktitle = "Proceedings of the 62nd Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)",
    month = aug,
    year = "2024",
    address = "Bangkok, Thailand",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2024.acl-long.769",
    pages = "14255--14273",
}

```

## Our Related Works

If you are interested in **Data Selection** for Instruction Tuning, please see [Cherry_LLM](https://github.com/MingLiiii/Cherry_LLM) and [Superfiltering](https://github.com/tianyi-lab/Superfiltering). <br>
If you are interested in **human/LLM-free Data Augmentation** for Instruction Tuning, please see [Mosaic-IT](https://github.com/tianyi-lab/Mosaic-IT) and [RuleR](https://github.com/MingLiiii/RuleR). <br>
If you are interested in **Data Improvement** for Instruction Tuning, please see [Reflection_Tuning](https://github.com/tianyi-lab/Reflection_Tuning). <br>
If you are interested in **Knowledge Distillation** in the LLM era, please see this [Survey](https://github.com/Tebmer/Awesome-Knowledge-Distillation-of-LLMs). <br>

