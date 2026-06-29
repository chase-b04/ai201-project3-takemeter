# ai201-project3-takemeter

# Community choice and reasoning

r/NBA - We just had an intense NBA finals where the Knicks won on multiple comebacks, the NBA Draft is finished when I am writing this, and a massive trade (Giannis) just happened. The NBA is extremely interesting right now as well as being super active because of these events.

# Label taxonomy: definitions and 2 examples per label

highlight - A video or clip showcasing a specific play, sequence, or notable moment from an NBA game, presented primarily for viewing rather than discussion or analysis, usually coming with little emotion in the post.
Examples:

1. Trae Young sends the series to Game 6 with a long three against the Boston Celtics
2. The Stop. Ten years ago today, Kevin Love played the defense of his life on Stephen Curry.

analysis — the post makes a structured argument backed by statistics, historical comparison, or tactical observation. Evidence is specific and verifiable.
Examples:

1. Players that aren’t scared to fail are often the most successful. Chet has played scared this series. Shrunk from the moment the first 4 games. It’s hurt OKC. Jered McCain? He DGAF. He’s shootin’. While shooting too much can be a problem, he and Caruso were the first to challenge Wemby at the rim. To go right at him. McCain had the highlight of game 3 by scoring over Wemby and flexing. OKC absolutely needed him to not care and just let it fly. I actually believe Isaiah Joe is the better overall player, especially considering defense, but he won’t shoot the ball unless he’s open. He won’t force the shot. It’s why McCain passed him in the rotation.
2. Stating The Obvious: Situation and Opportunity Can Make a Star. Brunson, Jokic, Ginóbili, Arenas, Draymond, etc etc. Second round picks to make multiple All Star games, some are HOFers, with Jokic being all-time, and there are more. I sometimes wonder who else is out there that, had they been given the opportunity, would have been able to piece together an elite career. I named 2nd round picks, but also someone like Jimmy Butler, who I believe was 30, and Tony Parker. Just an interesting thought experiment. Who else had that potential, but we just never knew?

hot_take — a bold, confident opinion stated without supporting evidence. The claim might be true, but the post asserts rather than argues
Examples:

1. SGA is actually pretty good. If he would just play instead of relying on his horrible acting he'd be both respectable and up there as one of the actual best players in the league
2. Replace Kobe with an all star caliber wing from the 2000s and Shaq still wins 3 straight with the Lakers

reaction — an immediate emotional response to a specific event. Little to no argument — the post is expressing a feeling in the moment.
Examples:

1. THE OKLAHOMA CITY THUNDER HAVE BEEN ELIMINATED FROM 2026 NBA CHAMPIONSHIP CONTENTION
2. Miami has to have another deal lined up with a free agent. Their team is gutted.

# Data collection source, labeling process, label distribution, and 3 difficult-to-label examples with your decisions

Data Source Collection:
I manually collected exactly 200 examples of data by scrolling posts and comments on r/nba specifically looking for all 4 labels. I would search up threads asking for hot takes, analytics, and highlights to find posts that would help me find each of what I was looking for. I also could scrape reactions and hot takes under posts of important trades, draft picks, or events that had recentely happened.

Labeling process:
For labeling each example that I could find, I would honestly set out to find examples of exactly one label, making the process easy as I knew what I was looking for. I also scrolled by some of the more popular/trending posts and used my better judgement on any post that felt like it fit my labels. I kept myself close to my data and specifically picked out what I was looking for and would only stop for interesting examples that I believed fit my criteria.

Label Distribution:
analysis - 34 (17%)
hot_take - 58 (29%)
reaction - 58 (29%)
highlight - 50 (25%)

Difficult-to-label examples:

1. THE OKLAHOMA CITY THUNDER HAVE BEEN ELIMINATED FROM 2026 NBA CHAMPIONSHIP CONTENTION
   Label Decision: This was labeled as a reaction as it was a post made right after the Spurs eliminated the Thunder. It makes no real argument or has a video attached to it, just someone reacting to it and giving a general statement. However, I was worried that the model would thing this could be a highlight, as it's worded as a general statement over an event that happened, like some of the captions that were labeled as highlights.

2. Miami has to have another deal lined up with a free agent. Their team is gutted.
   Label Decision: This was labeled as a reaction to the Giannis trade. A comment to a post about the trade shortly after it was announced, it to me fits the label of a reaction still in the suprise of the moment of what this user thinks the Heat should do. I was between reaction and hot_take, and thought the model could see it as a hot take because of "their team is gutted." I think this example is better fit as a in the moment generalized reactionary observation and recommendation related to what the user thinks of Miami's roster post trade, and not a very confident opinionated take or statement with much formed judgement.

3. Hugo González defensive highlights from his rookie season. The 19-year-old ended the season 3rd among rookies in plus-minus at +246, despite only playing 14 minutes per game
   Label Decision: This was labeled asa highlight, as this is the caption that follows a clip. I used this label in the first place as it was by far the most popular kind of post in the subreddit, and wanted to test if the model could figure out when something was a highlight. For this one in particular, I had my concerns that the model could see it as a analysis since it compounds stats and numbers over a rookie. However, it isn't making an argument, and is just explaining accomplishments.

# Fine-tuning approach: base model, training setup, and at least one hyperparameter decision

Base Model: Groq
Training Platform: Google Colab

Training Setup: The model used is distilbert-base-uncased loaded with a sequence classification head configured for 4 output labels (highlight, analysis, hot_take, reaction). Training was run for 3 epochs using the HuggingFace Trainer API on a T4 GPU, with evaluation and checkpointing performed at the end of each epoch. The best model checkpoint was automatically restored at the end of training based on validation accuracy. A data collator handled dynamic padding so sequences within each batch are padded to the same length rather than padding everything to the global maximum, which saves memory and speeds up training

Hyperparameter Decision: The learning rate was kept at 2e-5, which is the standard recommendation for fine-tuning BERT-family models. Fine-tuning pretrained transformers is sensitive to large learning rates, too high and the pre-trained weights get "forgotten" rather than adapted, a phenomenon called catastrophic forgetting. With only ~100–140 training examples (roughly 70% of 204 rows), 3 epochs strikes a reasonable balance: enough passes for the model to adapt its classification head without overfitting to the small dataset. A warmup_steps=50 ramp-up was also kept, which gradually increases the learning rate from zero at the start of training, giving the optimizer a stable start before full-scale updates begin. Given that the model still made 13/30 errors at the baseline stage, particularly confusing reaction and hot_take, increasing epochs beyond 3 would risk memorizing those ambiguous examples rather than generalizing from them.

# Baseline description:

Baseline Approach
The baseline classifier used llama-3.3-70b-versatile via the Groq API in a zero-shot setting, meaning the model received no training examples from the dataset and was never fine-tuned. For each post in the test set, a single API call was made with a structured system prompt followed by the post text. Responses were collected with a 0.1 second delay between requests to respect Groq's free-tier rate limits, and parsed by matching the model's raw output against the known label strings. If the output didn't match any label, the response was marked unparseable and excluded from accuracy calculations.

Prompt That used and how results where collected:
The system prompt defined the community context, gave a plain-language definition of each label, included one representative example post per label, and explicitly instructed the model to output only the label name with no explanation. One important issue discovered during development was a label name mismatch: the CSV used highlight (singular) while the prompt and valid labels list used highlights (plural), which caused all 30 initial responses to be unparseable. After fixing this inconsistency, the model produced valid outputs. The four labels: analysis, hot_take, reaction, and highlight, were listed explicitly at the end of the prompt as the only valid outputs, and temperature=0 was set to make responses deterministic and reduce output variability.

# Full evaluation report: metrics for both models (accuracy + per-class), confusion matrix (written as a markdown table in the README, not only the committed image), 3 specific wrong predictions with analysis, and a sample-classifications table (3–5 posts with predicted label and confidence, one correct example explained)

metrics for both models (30 test examples each)
baseline_accuracy: 0.6333 (63.33%)
finetuned_accuracy: 0.5667 (56.7%)
Difference: -0.0667 improvement (-6.7%)

The fine-tuned model did not outperform the zero-shot baseline, which is unusual but explainable given the small dataset size (roughly 140 training examples across 4 classes) and the inherent ambiguity between certain label boundaries discussed below.

confusion matrix:
| | **Predicted: analysis** | **Predicted: hot_take** | **Predicted: reaction** | **Predicted: highlight** |
|---|---|---|---|---|
| **True: analysis** | 0 | 3 | 1 | 1 |
| **True: hot_take** | 0 | 7 | 2 | 0 |
| **True: reaction** | 0 | 5 | 4 | 0 |
| **True: highlight** | 0 | 0 | 1 | 6 |

3 specific wrong predictions with analysis:

Example 1: "The 2026 NBA Draft Big Board doesn't reflect the NBA's recent trend towards International players. It has been well-documented that a player born in the United States hasn't won MVP since James Harden..."
What went wrong: analysis predicted as hot_take

Analysis: The model predicted hot_take here, but this is a structured argument making a verifiable claim with historical evidence. The failure likely comes down to a training data distribution problem: the model saw very few analysis examples during fine-tuning (only ~25% of the dataset), and the ones it did see may not have represented this style of argument, a trend-based claim framed as an observation rather than using explicit stat language like percentages or plus/minus figures. The model appears to have learned that "opinions about players" = hot_take, without learning that the presence of evidence and structure changes the label. Fixing this would require more analysis examples that show opinion-flavored language backed by reasoning, not just stat-heavy posts.

Example 2: "Wizards gonna have a squad next year"
What went wrong: reaction predicted as hot_take

Analysis: This is a short, enthusiastic in-the-moment reaction to a draft or roster move, but the model called it hot_take. This is the clearest example of the core failure pattern: short posts that express an opinion, even a fleeting, emotional one, get swept into hot_take because the model has learned hot_take as a catch-all for brief opinionated text. The boundary between a spontaneous reaction and a confident assertion is subtle and often comes down to context (what just happened) rather than the text itself, which a model with no conversational context cannot reliably detect. This is fundamentally a data problem: the model needs more reaction examples that look like opinions but are clearly time-stamped emotional responses.

Example 3: "Alex has the league's highest defensive rating among players who have played more than half of the season's games, and also the only one with a point allowed percentage below 100 points per 100 possessions..."
What went wrong: analysis predicted as highlight

Analysis: This is a dense stat-based argument, the clearest possible analysis post, yet the model predicted highlight. This is the most surprising error. The likely explanation is that the model, having seen zero analysis predictions across the whole test set, has essentially given up on that class entirely. With only ~30 training examples for analysis and a model as small as DistilBERT, the class boundary was never learned. The model may have latched onto the stat-heavy, player-specific language and associated it with highlight descriptions (which also name players and games). This is a labeling volume problem more than anything else, analysis needed significantly more training examples to establish its boundary.

a sample-classification table:

Fine-Tuned model:
| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| analysis | 0.00 | 0.00 | 0.00 | 5 |
| hot_take | 0.47 | 0.78 | 0.58 | 9 |
| reaction | 0.50 | 0.44 | 0.47 | 9 |
| highlight | 0.86 | 0.86 | 0.86 | 7 |
| **macro avg** | **0.46** | **0.52** | **0.48** | **30** |

Baseline model:
| Label | Approximate Performance | Notes |
|---|---|---|
| highlight | Strong | Structurally distinct posts, rarely confused |
| analysis | Moderate | Confused with hot_take when posts were opinionated |
| hot_take | Moderate | Confused with reaction on short posts |
| reaction | Weakest | Most frequently mislabeled as hot_take |

highlight was by far the strongest class, which makes sense given its structurally distinctive posts. hot_take had decent recall but poor precision, meaning the model over-predicted it. reaction was the weakest class for the baseline but analysis was the weakest for the Fine-Tuned model.

# Reflection: what the model learned vs. what you intended

The most significant failure pattern is the complete collapse of the analysis class and the over-prediction of hot_take. Looking at the confusion matrix, hot_take absorbed 8 out of the 14 total errors, with 3 true analysis posts and 5 true reaction posts all getting predicted as hot_take. This tells me the model basically learned hot_take as a default category. When it was unsure, it just picked hot_take. This makes sense because hot_take is the hardest label to define by what it actually contains. A short hot_take looks like a reaction, and a long hot_take looks like analysis. The only real difference is what is missing, either evidence or emotional immediacy, and that is a much harder thing for a small model to learn compared to something that is clearly present in the text.

The model also never really learned what analysis looks like at all, getting 0 correct predictions for that class. I think this comes down to not having enough training examples for it, and the ones that were there probably did not cover enough variety to show the model the full range of what counts as analysis. What I intended was a four way split where the line between analysis and hot_take is drawn by whether reasoning is present, and the line between reaction and hot_take is drawn by whether there is emotional immediacy tied to a specific moment. In practice the model only reliably learned one boundary, which was highlight vs everything else, because highlight posts are structurally so different from the other three that even a small model can pick up on it.

# Spec reflection: one way the spec helped you, one way implementation diverged from it and why

Going off of the specifications, I felt that they helped me a lot when it came to fine-tuning, as I mostly followed the recommended fine-tuning instructions, models, setup, and hyperameters. One way I diverged from the specification is that I went for four labels instead of 2 or 3, including the most common post type, highlights.

# AI usage section: at least 2 specific instances describing what you directed the AI to do and what you revised or overrode; disclose any annotation assistance

Example 1: I used AI to review a few things that I did, such as my prompt, my hyperameter, and my explanations, to make sure that things made sense and that I was doing things correctly. I would mostly just copy and paste or screenshot whatever I needed assistance and review with and submit it to claude for recommendations.

Example 2: Finding themes inbetween my failed test cases. I copied all my failed test cases, pasted it into claude's chatbot, and asked it to find any common themes or patterns between these test cases.

Example 3: I had to make an extra cell for my demo video so I could showcase 3-5 tests being predicted. I gave claude an explanation of my video and asked it how I should approach showcasing these specific test cases in my demo.

Demo Video:
https://youtu.be/mC1Tr-SybMo
