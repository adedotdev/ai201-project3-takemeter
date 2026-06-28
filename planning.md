# Community
I chose the soccer community. This is because the online soccer community generates an enormous volume of discourse, from emotional match reactions to detailed tactical breakdowns, but these very different post types often get conflated in conversation. Classifying posts as reactions, hot takes, or evidence-backed analysis helps community members engage more deliberately, since knowing what kind of claim someone is making changes how it should be read and responded to.


# Labels
**reaction** - A spontaneous response to something that just happened, with no real debate intended, just sharing how it feels in the moment.

Examples
- "To be honest I cannot recall a single significant player who played with Messi and then bad mouth him on public. Not Zlatan, not Eto, not Henry, not Yaya Touré, not Coutinho, not Griezmann...
For a player of his stature, that's unbelievable. In this generation, only Haaland seems to have a very similar warm and friendly vibe."
- "Cape Verde advancing undefeated with Spain and Uruguay in a group at your first ever World Cup is fucking nuts. One of the greatest miracles in World Cup history."


**hot_take** - A strong, confident opinion stated as fact without backing it up (the point is made, not defended). Stats used to defend such opinions are often cherry-picked

Examples
- "People can say what they want about Brazil squad but most national teams would love having these 2 center backs (Gabriel and Marquinhos) and Vini and Raphinha on the front (even if Vini usually sucks in the seleçao)"
- "Not taking Trent was hard to understand before the WC began and doubly so now.
It was a dumb decision imo. England lacks creativity in the final third and Trent can give you exactly that. But I guess Tuchel went for a more defensive and physical approach."


**analysis** - A well-reasoned argument supported by concrete evidence (stats, historical context, or tactical breakdown) that can actually be checked or challenged.

Examples
- "Transfermarkt now says he's now missed, including these 2 England games, 147 games with injury at 26 years of age.
All but about 10 of them are from non-contact muscle and ligament injuries, his body is completely cooked."
- "Ireland currently hold the record for furthest progress in a World Cup without winning a game. We made it to the quarterfinals in our debut tournament 1990. It’s written in the stars."


# Hard edge cases
The trickiest overlap is between hot take and analysis. A post can open with a bold, assertive claim but then gesture toward evidence without fully developing it.

e.g "I mean if Cape Verde beats Argentina that would be the greatest sporting moment on US soil since 1980"

The most practical approach to handles this type of ambiguity is to anchor on the primary intent: if the evidence is specific and verifiable, call it analysis; if the claim is the point and the evidence feels incidental or decorative, call it a hot take.


# Data collection plan
Exmaples will be collected from Reddit posts into a CSV. There would be about 66 posts per label which makes up about 33% of all posts. Underrepresented labels will be targeted deliberately. Filtering for posts that fit the underrepresented label rather than sampling randomly and hoping the distribution evens out.


# Evaluation metrics
- Macro F1 - since I'm aiming for a balanced three-class classifier and discourse quality distinctions matter equally across all three labels, macro F1 averages performance per class without letting a dominant class (likely reaction) inflate the score. Accuracy alone fails here because a model that just predicts reaction constantly could still look decent on paper.

- Per-class metrics — these tell different things about failure modes. Low precision on hot_take means the model is overcalling it (lumping analysis in). Low recall means it's missing hot takes and probably absorbing them into one of the other classes.

- Confusion matrix — especially important for the hot take / analysis boundary I already identified as ambiguous. The matrix will show exactly where leakage is happening between those two labels, which tells whether the label definitions need tightening or the training examples need better coverage of the boundary cases.


# Definition of success
For a three-class problem with genuine label ambiguity, a macro F1 of 0.75–0.80 is the threshold where the classifier becomes genuinely useful in practice. Below that, the error rate is high enough that users will notice and lose trust in the labels. Above 0.80 is strong for this kind of subjective task.


# AI tool plan

## Label stress-testing
Before annotating any examples, I'll give Claude my label definitions and edge case description and ask it to generate 10 posts sitting at the boundary between hot_take and analysis (the overlap I've already flagged as the hardest to call). If those posts can't be cleanly classified using my current definitions, I'll tighten the definitions before touching the 200 example dataset.

## Annotation assistance
I'll use Claude to pre-label a batch of examples before reviewing them myself. Each pre-labeled example will be flagged with a `ai_prelabeled` column in the CSV so the origin is tracked throughout.

## Failure analysis
After evaluation, I'll pass my list of wrong predictions to Claude and ask it to identify patterns in the misclassifications. Specifically whether errors cluster around the hot_take/analysis boundary, whether certain post structures (e.g. claim + single stat) are consistently mislabeled, and whether any label is being systematically confused with another. I'll verify any pattern Claude identifies by manually reviewing the flagged examples myself before including findings in the writeup.

