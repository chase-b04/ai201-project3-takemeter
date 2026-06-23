# Community choice and reasoning

r/NBA - NBA Draft is upcoming and a massive trade (Giannis) just happened. The NBA is extremely interesting right now as well as being super active because of these events.

# Label taxonomy: definitions and 2 examples per label

highlight - A video or clip showcasing a specific play, sequence, or notable moment from an NBA game, presented primarily for viewing rather than discussion or analysis, usually coming with little emotion in the post.

Examples:

1. Trae Young sends the series to Game 6 with a long three against the Boston Celtics
2. The Stop. Ten years ago today, Kevin Love played the defense of his life on Stephen Curry.

---

analysis — the post makes a structured argument backed by statistics, historical comparison, or tactical observation. Evidence is specific and verifiable.

Examples:

1. Players that aren’t scared to fail are often the most successful. Chet has played scared this series. Shrunk from the moment the first 4 games. It’s hurt OKC. Jered McCain? He DGAF. He’s shootin’. While shooting too much can be a problem, he and Caruso were the first to challenge Wemby at the rim. To go right at him. McCain had the highlight of game 3 by scoring over Wemby and flexing. OKC absolutely needed him to not care and just let it fly. I actually believe Isaiah Joe is the better overall player, especially considering defense, but he won’t shoot the ball unless he’s open. He won’t force the shot. It’s why McCain passed him in the rotation.
2. Stating The Obvious: Situation and Opportunity Can Make a Star. Brunson, Jokic, Ginóbili, Arenas, Draymond, etc etc. Second round picks to make multiple All Star games, some are HOFers, with Jokic being all-time, and there are more. I sometimes wonder who else is out there that, had they been given the opportunity, would have been able to piece together an elite career. I named 2nd round picks, but also someone like Jimmy Butler, who I believe was 30, and Tony Parker. Just an interesting thought experiment. Who else had that potential, but we just never knew?

---

hot_take — a bold, confident opinion stated without supporting evidence. The claim might be true, but the post asserts rather than argues

Examples:

1. SGA is actually pretty good. If he would just play instead of relying on his horrible acting he'd be both respectable and up there as one of the actual best players in the league
2. Replace Kobe with an all star caliber wing from the 2000s and Shaq still wins 3 straight with the Lakers

---

reaction — an immediate emotional response to a specific event. Little to no argument — the post is expressing a feeling in the moment.

Examples:

1. THE OKLAHOMA CITY THUNDER HAVE BEEN ELIMINATED FROM 2026 NBA CHAMPIONSHIP CONTENTION
2. Miami has to have another deal lined up with a free agent. Their team is gutted.

---

# 2–3 sentence description of your community, your labels, and why these distinctions matter to people in that community

r/NBA is a community focused on discussing NBA games, players, teams, trades, and news, especially during high-interest periods such as the NBA Draft, free agency, and the early off season where trades happen. The labels distinguish between different types of fan engagement: highlights share noteworthy on-court moments, analysis uses evidence and reasoning to make a case, hot takes present strong opinions without supporting argument, and reactions capture immediate emotional responses to recent events. These distinctions matter because NBA fans use the community for different purposes, some want to watch and relive key plays, while others want deeper discussion, debate, or a space to react to breaking news and game results.

# Hard Edge Cases: What type of post will be genuinely ambiguous between two labels? How will you handle it when you encounter it during annotation?

The most common ambiguity will be between analysis and hot_take. Many NBA posts contain opinions about players, teams, or trades, but the deciding factor will be whether the author provides evidence, reasoning, statistics, historical comparisons, or detailed tactical observations. If a post makes a strong claim without supporting evidence, it should be labeled hot_take even if the claim is plausible.

A second ambiguous case is reaction versus hot_take. Both can be opinionated, but reaction is tied to a recent event and is primarily emotional ("We are so cooked"), while hot_take makes a broader basketball claim ("This team will never win a title"). When unsure, I will prioritize the post's primary purpose: emotional expression → reaction; argumentative opinion → hot_take.

Another edge case is highlight versus reaction when a highlight post contains an excited title. If the main content is a video or clip of a play, it will still be labeled highlight, since the media content is the primary focus.

# Data Collection Plan: Where will you collect examples? How many per label? What will you do if a label is underrepresented after 200 examples?

I will collect examples directly from r/NBA, using posts from different time periods to capture a variety of content types, including regular-season games, playoffs, free agency, trades, and the NBA Draft.
My target dataset will contain approximately:

50 Highlight
50 Analysis
50 Hot Take
50 Reaction

for a total of 200 labeled examples.
If a label is underrepresented after collecting 200 posts (most likely analysis, since it is less common than highlights or reactions), I will perform targeted sampling by searching relevant flairs, discussion threads, and historical posts that naturally contain that label. The goal is to maintain a reasonably balanced dataset so that the model learns each category equally well and does not simply favor the most common labels.

# Evaluation Metrics: Which metrics will you use to evaluate your model and why are those the right ones for this specific task? (Accuracy alone is not enough — explain what else you need and why.)

Accuracy will be reported, but it is not sufficient on its own because the dataset may contain labels that are naturally harder to distinguish.

I will also use:
-Precision for each label: measures how often predictions for a label are correct. Important because mislabeling analysis as hot_take would reduce trust in the classifier.
-Recall for each label: measures how many true examples of a label are successfully identified. Important because users want actual analysis posts to be found when they exist.
-F1 Score for each label: balances precision and recall. Useful because some labels may be less common and require a tradeoff between the two.
-Confusion Matrix: shows which labels are most frequently confused. Especially important for understanding mistakes between analysis/hot_take and reaction/hot_take.

The macro-averaged F1 score will be the most important overall metric because it gives equal weight to all labels rather than allowing common categories like highlights to dominate the evaluation.

# Definition of success: What performance would make this classifier genuinely useful? What would you accept as "good enough" for deployment in a real community tool?

The classifier would be genuinely useful if it can reliably separate the four major content types in a way that matches how regular r/NBA users perceive posts.

I would consider the project successful if it achieves:
-85%+ overall accuracy
-0.80+ macro F1 score
-No individual label with an F1 score below 0.70

For a real community tool, "good enough" deployment performance would be approximately:
-90%+ accuracy
-0.85+ macro F1 score
-Strong confusion matrix results showing limited confusion between analysis and hot_take

At that level, the classifier could assist moderators, improve content organization, and help users filter for the kinds of posts they want to read without requiring extensive manual review.

# AI tool plan:

# Label Stress-Testing

-Before beginning large-scale annotation, I will use Claude to generate 5–10 synthetic r/NBA posts that intentionally sit on the boundary between my labels, particularly:
-Analysis vs. Hot Take
-Hot Take vs. Reaction
-Highlight vs. Reaction
-I will classify each generated post using my taxonomy. If I encounter examples that cannot be labeled confidently, I will revise the label definitions and edge-case rules before collecting my 200 examples. This process helps identify ambiguities early and improves annotation consistency.

# Annotation Assistance

-I will use Claude as an initial labeling assistant for batches of collected r/NBA posts. Claude will assign a preliminary label based on my definitions, but I will manually review every example and make the final labeling decision.
-To maintain transparency, I will keep a record of which examples were claude pre-labeled by adding an "AI_Prelabel" field in my annotation spreadsheet (Yes/No). This will allow me to disclose the extent of AI assistance in the final project documentation and compare AI suggestions against my final annotations if needed.

# Failure Analysis

-After evaluating the classifier, I will collect examples that were incorrectly classified and provide them to an AI tool for pattern analysis. I will ask Claude to identify recurring sources of error, such as:
-Analysis posts being mistaken for Hot Takes
-Emotional reactions being mistaken for Hot Takes
-Highlights with opinionated titles being mistaken for Reactions

I will not rely solely on the Claude's conclusions. Instead, I will manually review the flagged examples and verify whether the suggested patterns are actually supported by the data. Any identified error patterns will be confirmed using the confusion matrix, per-label metrics, and representative misclassified examples before being included in the final evaluation report.
