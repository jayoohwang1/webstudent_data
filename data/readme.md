# Data Collection Guideline

As part of the project, we will collect 2 datasets:

1. Task-level dataset: Each data point describes a task, method of evaluation, and the correct final output (or final page url).
Will be used for validating agent performance on full tasks.
2. Step-level dataset: Each data point consists of an instruction prompt + website description which will be provided, and a human-written answer. Will be used to instruction finetune the LLM to output the correct answer to a given prompt.

## Task-Level Dataset

A task is defined in a yaml file as follows:

```yaml
task:
    group_name: <Folder name describing the data group.>
    start_url: <Either an absolute url like www.google.com or one of the simulation websites [CLASSIFIEDS, SHOPPING, REDDIT, WIKIPEDIA, MAPS, GITLAB, SHOPPING_ADMIN]>
    intent: <Task instruction provided to the agent>
    eval_type: <How this task will be evaluated. Pick from "string_match" or "url_match">
    value: <The correct answer string.>
```

More details:

* **group_name:** We will organize the tasks based on attributes such as website (e.g. shopping), difficulty of task (easy vs hard), and whether it requires vision to solve (vision vs text). For example the group 'shopping_hard_text' would consist of high difficulty tasks on the shopping website that don't explicitly require vision.
    * hard = requires at least 5 steps to solve
* **start_url:** Can start on a page that is not the main page. For the WebArena simulation websites replace the main part of the url before the first '/' with the name of the site in all caps. For example the fake craigslist website url 'http://ec2-3-13-232-171.us-east-2.compute.amazonaws.com:9980/index.php?page=search&sCategory=8' should be 'CLASSIFIEDS/index.php?page=search&sCategory=8'.
* **intent:** Describe the task to complete on the current website in plain language, as if you are watching someone's screen and telling them what to do. Tasks can be simple: "Go to the gaming subreddit and upvote the first post", or complex: "Go to my list of items for sale and find the oldest one. Then search for similar items that other people are selling and if there are any that have a lower price than my item, lower my asking price by $20."
* **eval_type:** Tasks that use 'string_match' evaluation will check if the final answer provided by the LLM contains the answer string. Tasks with 'url_match' evaluation will check if the url of the final page the agent goes to is the correct one.
* **value:** Must be a url if the eval_type is 'url_match'.
    * If there is more than one correct answer split them with "|OR|", e.g. `value: "25000 |OR| 25,000 |OR| twenty-five thousand"`
    * If answer requires multiple strings (e.g. Provide list of names...) then split required values with "|AND|", e.g. `value: "John |AND| Jane |AND| Robert"`

Notes:

* Instructions should cover a diverse variety of tasks on each website to ensure comprehensive testing of the agent. 
* Tasks from the same group should all be placed in the corresponding folder.
* The task instruction should be specific so that the provided answer is the only correct answer.
* `string_match` will be case-insensitive.
* If using an external url like `www.google.com`, try to make sure that the starting webpage (and the page where the answer will be found) is stable and will be available for a long time.

Some examples:

```yaml
task:
    group_name: "classifieds_easy_text"
    base_url: "CLASSIFIEDS/index.php?page=search&sPattern=macbook+pro&sCategory=14"
    intent:
"Find me the cheapest Macbook pro on this page with Apple chip and at least 16GB of memory and a Retina display."
    eval_type: "url_match"
    value: "CLASSIFIEDS/index.php?page=item&id=79364"
```

```yaml
task:
    group_name: "shopping_hard_text"
    base_url: "SHOPPING"
    intent:
"Does the reviews on Mario Kart 8 say anything about any potential delivery issue?"
    eval_type: "string_match"
    value: "yes"
```

## Step-Level Dataset

@jayoo should write this
