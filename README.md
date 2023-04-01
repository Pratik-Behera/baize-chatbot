<p align="center">
<img width="500px" alt="Project Baize" src="https://user-images.githubusercontent.com/22514219/229195563-0cddfa74-e52f-4413-b4b4-e4ba489c4b3d.png">
</p>
<hr>

## What's Baize?
Baize is an open-source chat model fine-tuned with [LoRA](https://github.com/microsoft/LoRA). It uses 100k dialogs generated by letting ChatGPT chat with itself. We also use Alpaca's data to improve its performance. We have released 7B and 13B models. 30B and 60B models coming soon. 

## Why it's called Baize?
Baize (白泽) is a mythical creature in Chinese folklore, who speaks human languages and knows everything. This is exactly what we expect from a chat model.


Welcome to the Baize project! Our goal is to build an open-source chat model that can benefit the community. This repository contains:

- 54K/57K [dialogs](data.zip) from Quaroand StackOverFlow questions
- [Code](collect.py) for collecting self-chat data
- [Code](finetune.py) for fine-tuning the model and the released fine-tuned model
- [Code](demo/app.py) for chat model demo and the released [demo](https://huggingface.co/spaces/project-baize/baize-lora-7B)



### 1. Demo

You can either host it on your local machine or access the [online demo](https://huggingface.co/spaces/project-baize/baize-lora-7B). The script fetches the [LLaMa](https://huggingface.co/decapoda-research/llama-7b-hf) model and the [LoRA weights](https://huggingface.co/project-baize/baize-lora-7B) from the Hugging Face model hub, then runs a user-friendly Gradio interface for chatting.

#### Setup

```
cd demo
pip install -r requirements.txt
```

#### Running

You can change into your model in the beging of [app.py](demo/app.py)

```
python app.py
```



### 2. Finetune

#### Setup

1. Install dependencies

```
pip install -r requirements.txt
```

2. If bitsandbytes doesn't work, [install it from source.](https://github.com/TimDettmers/bitsandbytes/blob/main/compile_from_source.md) Windows users can follow [these instructions](https://github.com/tloen/alpaca-lora/issues/17).

#### Data Collecting

You can use our [released data](data.zip) or collect the data from ChatGPT using the following command:

```bash
num_process=10 # The number of processes to collect data
max_total_tokens=500000 # Set maximum numbers of tokens to collect data
api_key=xxxxxx # Set your openai api key
for ((i=0; i<$num_process; i++))
do
	python collect.py $api_key $max_total_tokens $i $num_process stackoverflow &
	python collect.py $api_key $max_total_tokens $I $num_process quora &
done
```

After collecting data, you use the following command to preprocess data:

```bash
python preprocess.py
```

#### Fine-tuning

The fine-tuning code is designed to run on an A100-80G GPU. Th `finetune.py` script accepts three parameters: foundation model size (i.e., 7B, 13B, or 30B), batch size, and learning rate.

```
# For the 7B model (takes about 9 hours)
python finetune.py 7b 32 0.0002

# For the 13B model (takes about 16 hours)
python finetune.py 13b 16 0.0001

# For the 30B model (takes about 16 hours)
python finetune.py 30b 8 0.00005
```
