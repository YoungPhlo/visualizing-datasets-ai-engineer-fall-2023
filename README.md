# AI.Engineer Fall 2023 Talk

## Slides
Here's a slide by slide annotation of the talk:
- [Slideshow Notes](https://phlo.info/visualizing-datasets-ai-engineer-fall-2023/)

### Summary
- Ideal for anyone working with large datasets that wants to understand the topics, themes, clusters within their data before fine-tuning a model.
- Keep your data private and local - does not require uploading datasets to third party services.
- See conversations and themes you may not expect within your data.
- Caveats: Can't fit extremely large datasets, models are non-deterministic so multiple runs may yield slightly different results.

### Plots
If you've seen one of these scatter plots and want to play around with it for yourself visit these links
**NOTE**: Please give each page around 15 seconds to load. The page size is huge and my server is slow.
- [Puffin dataset](https://phlo.info/visualized-datasets/puffin/)
- [Open Hermes dataset](https://phlo.info/visualized-datasets/open-hermes/)
- [Textbook Quality dataset](https://phlo.info/visualized-datasets/textbook-quality/)

## Tutorial

### Source
- [Original Repo (BERTopic)](https://github.com/MaartenGr/BERTopic)
- [Original Notebook Tutorial](https://colab.research.google.com/drive/1QCERSMUjqGetGGujdrvv_6_EeoIcd_9M?usp=sharing)

### Setup
- Duplicate `!topics_template.ipynb`
- Rename `!topics_template-Copy1` to `dataset_name`
- Close `!topics_template` to avoid any confusion
- Install required libraries

### Import and prepare the dataset
- Paste the `repo/dataset_name` string into `load_dataset`
- Use `["train"]` split in most cases
- Set dataset name and title
- Examine Dataset card on Hugging Face to understand content of columns
- Create variables for relevant columns

### Convert dataset
"All you need is a list of strings to run BERTopic"
- Create a list columns you actually want data from
- Set it equal to `conversations_raw`
- Print one row of that data (json dumps make it easier to read)
- Create a new list named `conversation_strings`
- This list will be the turn of each conversation combined to make one large string
- To reiterate, this new list is one element per conversation

### Analyze dataset
- Print the length of the new `conversation_strings` list
- In this case it's the same length (but that may not always be the case)
- Print the same row from earlier to see how it changed
- Print the next few to make sure it's one convo per element in the list
- Find shortest and longest conversations
	- If you take the longest convo and put it into The Tokenizer Playground sometimes you'll find it doesn't fit in the LM's context window

### Prepare dataset
- Reduce size if needed, less than 100,000 is ideal
- My hardware doesn't do well past 150,000 rows
- Truncate the conversations to fit the context window

### Modeling
- Load LLAMA-2 model and tokenizer
- This method uses the quantized version
- Create pipeline to generate topic labels
- Configure the prompt
	- Could be optimized further

### Embeddings
- Prepare embeddings with SentenceTransformer
	- I like how `BAAI/bge-base-en-v1.5` performs in terms of quality vs speed
	- `BAAI/bge-large-en-v1.5` is slow on my machine but may not take as long for you
		- The difference for me is about 30 minutes for ~100k conversations
- In the original Topic Modeling tutorial he used `BAAI/bge-small-en` which is much faster
- I'm in search of resources on the tradeoffs of using a bigger or smaller embedding model

- Define Bertopic sub-models
	- `min_cluster_size` is very important and it may take a few test runs to understand what the best size is for your dataset
	- For roughly 100,000 conversations I find that `min_cluster_size = 50` works best for me
	- The goal of Maarten in the original Llama 2 tutorial was 100 topics after embedding
		- In his case he use a `min_cluster_size` of `150` on ~100k rows
		- These embeddings get used for x, y coordinates later

### Training
- Train models and create topic visualization pipeline

### Visualizations
- Visualize topics in tables and graphs
- How many topics did you end up with?
- Reminder: If the graph clusters are wonky you can change the `min_cluster_size` and run the process from that cell down

### Save work for later if needed
- In the BERTopic repo he goes into detail about what this cell does

### Restart kernel and run all cells
- Task Mgr > Performance > GPU
	- Monitor VRAM, 12GB is typical