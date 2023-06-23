# Style Tone_tags

We propose a tagset for XML rules that categorizes rules according to style and tone features. 

We call the labels for this tagset __toneTags__. 

By using toneTags, we can organize style and tone rules according to their content and purpose, making it easier to manipulate and manage the rules. 
Importantly, combinations of toneTags are mapped onto __Writing Goals__, triggering style and tone rules for specific purposes.

## How to apply toneTags to style rules
When applying toneTags to XML style rules for the backend implementation, here are some guidelines to follow:

- __Currently supported toneTags are:__ `clarity`, `formal`, `professional`, `confident`, `academic`, `povrem`, `scientific`, `objective`, `persuasive`, `informal`, `povadd`, `positive`

- toneTags should be applied __only__ to style rules.
  The backend makes no distinction between the grammar XML sheets and the style XML sheets, so it is important that we apply toneTags only to style sheets.
- Use the `tone_tags` attribute to specify the toneTags for each rule, rulegroup, or category. (N.B.: if we have a category tagged with `clarity`, a rulegroup tagged with `formal`, and the rule with `professional`, then the rule will be triggered when *any* of these three toneTags is activated due to the Writing Goal)
- While multiple toneTags can be supported in `tone_tags`, we strive for one toneTag per rule as much as possible.
- If you have to include multiple toneTags, separate them with whitespace, e.g.:
    ```xml
    <rule id="Formal_Clarity_TONE_RULE" tone_tags="formal clarity"/>
    ```
- Rules that are not tagged will still be displayed to the user, i.e. __untagged rules are always active.__
- Rules tagged with `clarity` are also always active, but it is still important to tag them as such in case changes need to be made to the backend.

## Description of toneTags

|---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tone Tags     | Description                                                                                                                                                                                                                                                                                                                                                  |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| academic      | Rules that emphasize a scholarly writing style suitable for academic or research-oriented content. Also included is adherence to culturally-specific academic conventions.                                                                                                                                                                                   |
| confident     | Rules that encourage a strong and assertive writing style. Encompasses rules aimed at conveying a sense of self-assuredness and conviction in the content. Promotes the use of confident language and straightforward statements.                                                                                                                            |
| clarity       | Rules that focus on ensuring clear and understandable communication. Encompasses rules aimed at eliminating ambiguity, simplifying complex ideas, and using precise language to convey information effectively. Rules can also be related to redundancies, sentence structure, and discourse anchors.                                                        |
| foreign_terms | Rules related to removing foreign terms from the text (user option).                                                                                                                                                                                                                                                                                         |
| formal        | Rules that emphasize a polished and elevated writing style. Encompasses rules aimed at maintaining a level of decorum and adherence to established conventions of formal language and etiquette. Encourages the use of proper grammar, formal vocabulary, and structured sentence patterns to maintain a sense of refinement and respect.                    |
| general       | Rules that are common sense or just generally good all-around style rules. Rules are not specific to any particular Writing Goal or tone, and they are always active.                                                                                                                                                                                        |
| inclusive     | Rules related to inserting inclusive language (user option).                                                                                                                                                                                                                                                                                                 |
| informal      | Rules that encourage a relaxed and casual writing style. Encompasses rules aimed at creating a friendly and conversational tone in written communication. Promotes the use of colloquial language, contractions, and a more relaxed grammatical structure to establish a connection with the audience and create a sense of informality and familiarity.     |
| objective     | Rules that prioritize unbiased and impartial writing. Encompasses rules aimed at presenting information in a neutral and detached manner, free from personal opinion or bias. Encourages the use of factual evidence, logical reasoning, and balanced language.                                                                                              |
| persuasive    | Rules that aim to influence and convince the audience. Encompasses rules that encourage the use of persuasive techniques, such as strong arguments, emotional appeals, and rhetorical devices, to sway the reader'-s opinion or prompt them to take a specific action. Focuses on crafting compelling and convincing content.                                |
| positive      | Rules that focus on presenting information or messages in a positive and constructive manner. Encompasses rules aimed at emphasizing benefits, solutions, and opportunities rather than focusing on problems or negative aspects. Encourages writers to frame content in a way that promotes optimism, highlights strengths, and inspires optimism.          |
| povadd        | Rules related to adding first/second-person pronouns.                                                                                                                                                                                                                                                                                                        |
| povrem        | Rules related to removing first- and second-person pronouns.                                                                                                                                                                                                                                                                                                 |
| professional  | Rules that emphasize a formal and polished writing style suitable for business and professional settings. Encompasses rules aimed at maintaining a level of competence, credibility, and adherence to professional standards. Differs from formal in the sense that it focuses on suggestions that are tailored for a business and professional environment. |
| scientific    | Rules that pertain to writing in a rigorous and objective manner within the scientific domain. Encompasses rules aimed at maintaining precision and adherence to scientific principles and methodologies. Promotes the use of specialized terminology, logical reasoning, and evidence-based arguments.                                                      |
| shorten_it    | Rules related to shortening sentence length (user option).                                                                                                                                                                                                                                                                                                   |
|---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


## Mapping toneTags to Writing Goals

|------------------------+-------------------------------------------------------------------|
| Writing Goal           | Relevant toneTags                                                 |
|------------------------|-------------------------------------------------------------------|
| Serious & Professional | clarity, confident, formal, general, positive, professional       |
| Objective & Scientific | academic, clarity, formal, general, objective, povrem, scientific |
| Confident & Persuasive | clarity, confident, general, persuasive, positive                 |
| Personal & Encouraging | clarity, general, informal, positive, povadd                      |
| Original & Expressive  | clarity, general                                                  |
|------------------------+-------------------------------------------------------------------|


__Note:__ 
- `clarity` is __always active__ by default.
- `foreign_terms`, `inclusive` and `shorten_it` are "user options" that can be selected by the user and are not associated with any particular Writing Goal.

## Goal-specific attribute
During the transitional period when Writing Goals are being rolled out to users slowly, it is necessary to introduce an attribute to XML style rules to ensure that rules that are very specific to a particular Writing Goal are triggered only when that particular Writing Goal is selected by the user. 
We therefore have the __`is_goal_specific` attribute.__

The `is_goal_specific` attribute modifies the behavior of the API such that:
- if no toneTag is specified, all rules that are not goal specific will be returned (i.e., when `is_goal_specific="true"`)
- if 1 or more toneTag is specified, the rules matching the given tags will be returned, including the goal-specific rules that match one of the tags
`is_goal_specific` is a Boolean value that is __set to `false` by default__

__The above describes the behavior of the API, not how the user interacts with the Writing Goals feature.__

More simply:
- when `is_goal_specific` is set to `true` on a rule, that rule will be shown to the user only if they have selected an associated Writing Goal;
- if the user has not selected an associated Writing Goal, rules with `is_goal_specific="true"` will not be returned to the user;
- `is_goal_specific` should therefore be used on rules that are very niche and particular to a Writing Goal;
- since the default value of `is_goal_specific` is `false`, you do not need to include the attribute in your rule unless you are changing its value (i.e., setting it to `true`);
- note that `is_goal_specific` should be used in conjunction with toneTags that are goal specific, i.e. it should not be used with toneTags that are general in nature (e.g. `general`, `clarity`).
