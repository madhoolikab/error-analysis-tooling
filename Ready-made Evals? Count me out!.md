# Ready-made Evals? Count me Out!
You build an amazing product with GenAI at the heart of the solution. It has the "Wow!" factor, an attention-grabbing demo, and solves real problems. Your team figures that they need an evaluation strategy to keep the momentum going and to continuously improve the product. You start measuring "hallucination", "helpfulness", "correctness", "ROUGE score", and a bunch of other standard metrics as suggested by Google( [1](https://docs.cloud.google.com/gemini-enterprise-agent-platform/optimize/evaluation/manage-metrics), [2](https://cloud.google.com/transform/gen-ai-kpis-measuring-ai-success-deep-dive), [3](https://cloud.google.com/transform/the-kpis-that-actually-matter-for-production-ai-agents)), Microsoft( [4](https://learn.microsoft.com/en-us/ai/playbook/technology-guidance/generative-ai/working-with-llms/evaluation/list-of-eval-metrics), [5](https://learn.microsoft.com/en-us/ai/playbook/technology-guidance/generative-ai/working-with-llms/evaluation/g-eval-metric-for-summarization)), and other big names. Fast-forward couple of months, you have dashboards with green scores against the metrics and a bunch of frustrated users complaining that the product is not serving their needs! So, what really happened here? Let's unpack and see how to do justice to your vision.
## What most teams do now
It is quite common for teams to **rely on metrics like helpfulness, hallucination,** etc. And they do make sense. For the Google Fiber customer support agent, you need the it to be helpful and not hallucinate. What does it really mean? When the user asks for any $50 plans, if it says sorry there are no such plans - it is technically a helpful response, but, as a Product, we would want the Agent to also provide the customer with information that will lead them to choosing a plan. **Go one step further from the generic metric, be specific about how your system should behave.** Here it could be to "ask follow-up questions".
## What is missing
Everyone accepts that LLMs are nondeterministic and the need to develop systems that balance their creativity with reliability. **Generic metrics miss the system thinking, taste, and human judgement that goes into product evaluations.** Evaluate the product from a user point-of-view, be ruthless about its output quality, and develop a sense of how you want them to feel. By using ready-made evals and the over-reliance on LLMs for evals you are leaving the core reason for users to come back to your product - your taste and judgement ([6](https://aakashgupta.medium.com/the-ai-evaluation-revolution-why-every-product-manager-must-master-this-critical-skill-in-2025-0458c4ac6097)). 
## What you should do
Rely on your ***human instinct and taste*** to evaluate your product, do not make LLMs the center of your evaluation process. **Use AI to make it very easy for the Product Owner/Domain expert to judge the LLM responses effectively.** That will infuse your taste into the product. Outsourcing evals to another LLM is a sure shot way to build slop when you can do so much better!
## How to make progress
### Read your data
Yes, that's where all the insights, issues, and solutions are. See exactly **where your product is showing mediocrity or failing**. Only then you will see data samples where Pizza Hut's chatbot acts as a Coding Agent while it is supposed to nudge users order a large pizza ([7](https://www.reddit.com/r/ChatGPT/comments/1rt93cl/which_corporate_chat_bot_are_you_misusing_as_your/?utm_source=chatgpt.com)), and an Airlines support agent schedules you to leave from Paris even before you arrive there ([8](https://vbsowmya.wordpress.com/2026/03/20/short-story-how-to-arrive-before-you-leave/))!
### Make it very easy to read your data
Of course I am aware of how hard it is to go through LLM responses. And now, imagine doing it frequently over a large number of examples to surface issues. On top of that, you need to employ your judgement to every question-answer pair and determine what should change. This necessitates easy access to data, a neat way to record feedback, and provide visual cues to what's identified in each of the datapoints the team is working with.
### Ease the Process with a little help
You need to **reduce the friction involved in reading and analyzing the data**. Building out a custom tool will help enormously. This is the day and age for making custom tools for very specific datasets you curate for evaluating your product. Use Nielsen's Ten Heuristic Principles ([9](https://www.nngroup.com/articles/ten-usability-heuristics/)) as guidance for UI design to speed up the process. Some features I found useful in such custom tools are:
1. Ability to read the user-LLM conversation, tools used, their outputs, and reasoning text clearly.
2. A view that matches the end-user's view.
3. Space to make notes on what did not work as expected for each datapoint.
4. Keyboard shortcuts for navigation and saving the notes - one would not think this is important, but talk to me after going through 100 rows of data!
5. Color code the rows based on status' (complete, to review, todo, etc).
6. Progress bar for motivation :). Human data analysis is one of the costliest parts of the LLM apps pipeline, a clearly visible Progress bar of how much work is done/pending is helpful.
7. Search - based on keywords, tags you added, tool usage, tokens, failure modes.
8. A Handy system prompt view.
9. And more.. depending on the kind of traces your application produces and what you intend to improve in the system.
### Redesign the Development Evaluations Loop
Many teams tend to see AI product development and designing evals as separate entities. What I observed after working on a multitude of AI solutions ranging from Information Retrieval, Co-Pilots, to Autonomous Agents is, dev and evals must go hand-in-hand to build effective products. It is a continuous dance between development and evaluations. The results from evals should port back into the dev lifecycle. And that's how the product evolves into its reliable version.
### Technical terms
#### Trace
A Complete record of all actions, messages, tool calls, and data retrievals from a single initial user query through to the final response ([10](https://hamel.dev/blog/posts/evals-faq/what-is-a-trace.html)) forms a **Trace**.
#### Annotation
A domain expert reviews each trace, takes note of what didn't work and what should change. This process is called **Annotating**, and the notes per datapoint forms an **Annotation**.
#### Open-Coding 
Free flowing notes what did not work as expected, with a focus on the initial failures seen in each trace ([11](https://langfuse.com/academy/monitoring/error-analysis#error-analysis)). Similar to annotation, but focuses on the first failure in the entire trace.
## Walkthrough with a Real Problem
I'm going to briefly discuss development of an app that uses GenAI, issues I faced with it, how I tracked the failures and reduced them by following this development →← error analysis →← development loop.
### The Setup
I'm making a Recipe Bot to help me cook. I'm an amateur, looking for vegetarian recipes that I can make at home. My requirements are - be edible, follow what I asked for, give me the nutritional information, and be very specific with what I should do.
### Reality Check
On spot checking with a few simple recipe requests, I noticed interesting hallucinated responses. Instructions to just ***soak rice in milk*** to make Kheer, **Rajma and rice cooked together** with very little water, and more such ideas.

![Wrong Recipe Example](wrong_recipe_example.png)
### Error Analysis made Easy with Custom Tools
To make systematic improvements, we need the issues and their frequency over a statistically significant dataset. You might be tempted to give the query-response pairs to ChatGPT or the like and ask for errors. I did that too, and observed 2 major issues: 1) Surfaces the same generic errors across all recipes (allergen information, cultural differences in preparation) 2) Hallucinates to include problems that the recipes did not have! These kinds of issues are to be expected for any LLM analysis. It is important that a PM/Domain expert goes through the initial dataset manually and open-codes the failure modes. This helps **develop the intuition for how LLMs respond to your data and system prompt**, and gives first hand insights into the issues your system has. Then as a next step you can scale effectively your judgement using any AI tools.

This need for human analysis leads us to develop custom tools that will ease the process. Use AI coding agents (Claude, Lovable, Cursor, any of them will work) to develop this. I used Claude Code. Don't worry about everything you might have to do with the tool in the future. *Focus only on **what you need Now***. Add features incrementally as you start needing them. The prompt evolves as you do a few annotations and observe new needs. For instance, I added a Progress bar, and added keyboard shortcuts for navigation. 
#### Prompt
Below is the prompt I used with Claude for such custom tool. Note that the `results.json` file with query id, query, and responses are uploaded too. I mentioned the features I needed, posed a design question, and asked to confirm if all looks good before implementing. 

You can **use this same prompt along with your data to get a custom tool** for your case! You can always improve it as you progress.

`I have a list of jsons like the uploaded file (results.json). I want a viewer.html for reviewing the results and to write the open coding notes as I review the response for each query. These are the features I need:`

1. `I should be able to choose a file to review.`
2. `The notes should be saved to the same results file as additional fields for each json and render on the viewer the next time I load it. If there is no notes, just show blank space to take notes. Notes should be editable, have save and cancel buttons.`
3. `I am using a macbook air, suggest if the notes should be on the right side of recipe or below it.`
4. `Want to be able to select "done", "review" for each of the rows I add notes to, and please color code them. And the ones without any notes can be seen as grey.`
5. `Have a sidebar that shows all the queries, and the color code should be reflected on this bar for easy access.`

`Ask any questions you have before implementing. Think of any other simple features will make this viewer helpful during evaluations and open coding, and suggest those too for implementation.`
#### Viewer
This is the tool view after a few iterations. Feel free to zoom in and notice the details. I used it to: 
1. Conduct error analysis of all the AI responses. Added open coding notes and failure modes I observed when evaluating the responses against what I expected.
2. Progress check - Get a sense of how much more human annotation is needed.
3. Search bar to estimate the frequency of errors observed, for example: It used 1 cup water to cook 1 cup Toor dal (way less than needed). How many times does similar error occur?

![Eval Viewer](eval_viewer.png)
#### The Dev ↔ Eval ↔ Dev Loop

![Dev Evals Cycle](dev-evals-cycle.png)

Here's the Checklist to define product improvements systematically:
- [ ] Build a **golden dataset of queries (inputs)** that you care about and want to get right
- [ ] Run the product against this dataset and **save the traces**
- [ ] Open coding on traces - First run - these are your **baseline metrics**
- [ ] Come up with a **taxonomy of failure modes** and their frequencies
- [ ] **Prioritize the issues** based on the work involved, impact of the failure, ROI
- [ ] **Implement the fixes** and run the new version of the product (V2) on the golden dataset
- [ ] **Open coding on traces from V2 of product.** Be aware that new failures may start showing up after the changes, so proceed with a fresh mind, not only looking for the earlier issues.
- [ ] Build the taxonomy again and **compare with the baseline**.
- [ ] **Iterate on the product** and golden dataset until you reach the expected quality

I implemented this checklist on the recipe bot on a dataset of ~40 traces to either eliminate or reduce frequency of most errors.

![Results Comparison View](results_comparison.png)
## GitHub link
Here's a Github repo with the resources: https://github.com/madhoolikab/error-analysis-tooling/.

Feel free to use this as a reference point to build out your own tools for annotations and error analysis. This is the highest leverage thing you can do when starting out. Don't fixate on a perfect system prompt initially, start with a decent one and iterate using the framework we just discussed!
## Concluding Thoughts
Always bear in mind the probabilistic nature of LLMs and treat their responses for what they are - a model that has seen large amounts of text tokens, captured the patterns within it, and can predict the next token well enough. When you introduce such a component into deterministic systems, they become non-deterministic too. Design your Evaluation strategy accounting for this reality with: ***human judgement, error analysis, and iteration***, rather than trying to make it work with generic readily available solutions.
## References
https://docs.cloud.google.com/gemini-enterprise-agent-platform/optimize/evaluation/manage-metrics
https://cloud.google.com/transform/gen-ai-kpis-measuring-ai-success-deep-dive
https://cloud.google.com/transform/the-kpis-that-actually-matter-for-production-ai-agents
https://learn.microsoft.com/en-us/ai/playbook/technology-guidance/generative-ai/working-with-llms/evaluation/list-of-eval-metrics
https://learn.microsoft.com/en-us/ai/playbook/technology-guidance/generative-ai/working-with-llms/evaluation/g-eval-metric-for-summarization
https://aakashgupta.medium.com/the-ai-evaluation-revolution-why-every-product-manager-must-master-this-critical-skill-in-2025-0458c4ac6097
https://www.reddit.com/r/ChatGPT/comments/1rt93cl/which_corporate_chat_bot_are_you_misusing_as_your/?utm_source=chatgpt.com
https://vbsowmya.wordpress.com/2026/03/20/short-story-how-to-arrive-before-you-leave/
https://www.nngroup.com/articles/ten-usability-heuristics/
https://hamel.dev/blog/posts/evals-faq/what-is-a-trace.html
https://langfuse.com/academy/monitoring/error-analysis#error-analysis

## Feedback
What you should do - Give a very a simple example and explain it where every one can connect about the things that you discussed in the paragraph

Read your data” may be slightly misleading I think this section is broader than just reading the data. 

Explain “annotation” in very simple language before the tips section

It is not Tips for annotating, It is step by step process to come up with an annotation tool for your Gen AI product. Consider that in that way, and come up with annotation product life cycle. How you start, how you enrich. You will develop the annotation tool incrementally. How you are going to do that. if needed spend some time on that

Rest all is good. That woulld be if you add a video of Eval viewer 

I will help you to brain storm if you want

Your Goal here is : 1. Let people know how important to understand and use the data effectively, create a tool, come up with an easy process to create the annotations tool as per the product.

## Scratch Pad
Practical ways to align the AI product with your intent and taste:
- Wear the Product Manager hat
- What experience should your user leave this interaction with
- How do you want the system to respond to different types of scenarios - should a human be looped in here, do I want to provide the user with a few choices instead of asking open-ended questions, etc
- Keep your requirements visible, always check how the system is performing on those
- Look out for unexpected things the LLM is doing - is it aligned with your product goals or do you want to direct it to respond differently
