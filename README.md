# Training-Mode — Agent Skill

_Do you want or need to use LLMs without turning your brain to soup by outsourcing all thought? Are you looking to learn a new skill, but find that the documentation and tutorial material is lacking? Is your LLM overthinking or overdoing, running rampant writing spaghetti code in orders that make no sense to a mortal mind? Are you trying to learn a tech stack a mile high with overlapping levers and a list of conventions and rules big enough to make your eyes cross? Ask your inner self (and/or your therapist) if Training Mode is right for you._

I am developing Training Mode as an attempt to turn your local LLM into what every junior engineer wants but can rarely get: Someone to look over their shoulder and read every character of every line, to point out when they do something wrong, and to give them enough guidance to their solution that they learn without wasting time chasing the wrong answer. Someone who can clarify in real time and explain things the developer doesn't quite understand, providing just enough assistance to bridge the gap between what they know and what they don't know so that they can learn their material in the most effective way possible: By participating in the material.

Both my professional and recreational development experience has seen many hours of frustration as I work with tech stacks that look like an overly optimistic order at an IHOP during the Unlimited Pancakes event. I've spent a lot of time poring over documentation and tutorial videos, trying to figure out enough that I feel comfortable touching the stack. But there's only so much semantic information I can cram into my head at a time before it starts falling out the other ear. Only having procedural experience really makes it stick for me. And if the tech stack is high enough, I'll never have enough room for all the semantic info to feel comfortable touching the stack. I often need someone who will hear my questions and tell me what I need to know to get the job done, providing me context for what I'm doing and why, and allowing me to explore the concepts I'm touching on and their neighbors without having to keep it all in my head at once. Enter Training Mode. I hope it will be as useful to you as it has been to me.

### My Recommendations
* I recommend using the more lightweight models (Haiku, Sonnet for Claude) when using Training Mode, since it will naturally slow you down to do one step at a time.
* I recommend including instructions in your main CLAUDE.md to cultivate its behavior toward simple facts-based instruction. Instructions I use include things like:
* Do not attempt to manage my emotions.
* Cut out social language.
* Don't waste words apologizing. You should point out errors, but keep your diagnosis within facts.
* Remain brief in responses and stay within the realm of concrete facts.
* Prefer concrete, official documentation wherever possible. Use proven patterns if not, and training data as a last resort. Make note of it if you have to lean on training data.
* Understand that this skill is meant to make the agent more cooperative and useful in its role as a learning aid. It is incumbent upon the user to ask questions and clarify concepts they do not fully grasp. If you complete an action without understanding why you have completed it, you've failed yourself if your goal is to learn. The agent can help you learn, but it cannot *make* you learn. The process has to fundamentally be driven by your own curiosity.

At this time, Training Mode assesses the user's skill rating based on their own assertions and a ledger that keeps track of what tasks the user has completed and the agent verified. Default behavior is to consider a task "mastered" after 10 repetitions over at least 2 different days, since we remember better through repetition over multiple sessions. Each individual task a user completes is added to the ledger, and once mastered the model will offer to complete the task on its own. Optionally, the user may just have it keep tutoring and guiding and never take any action regardless of mastery level. Skills are rated one to ten stars based upon how completely their mastered tasks cover the breadth of the skill, and at this time have no hard-coded effect.

Training Mode works nicely with other skills that teach the agent the learning material. It is still very much in development and is at time of writing in a rudimentary state, but with Claude (which is what it's being developed for specifically, no guarantees for other models) the new behavior is already very promising. So far the agent:

* Asks the user what direction they want to take at the beginning of the session and each time a natural pivot point is reached.
* Keeps the project moving one thing at a time, stopping whenever it detects a task that the user hasn't mastered.
* Reviews the user's output and flags errors, pointing them to the line(s) and giving an explanation of what is going wrong, allowing the user to self-diagnose and fix the problem on their own terms.
* Does a pretty good job of arranging plans of work to train foundational skills before advanced ones.

Contributions are very welcome.

## Weaknesses
In an endeavor which may well be catalogued one day in a history book under the chapter title "Stupid Mistakes that Heralded the End," I have included instructions for the agent to identify and catalogue weaknesses of the user. This is because in a previous session using training-mode, Claude independently (and very incorrectly) identified the fact that I had made five distinct errors that shared a root cause. It did this despite having no explicit instructions to do so. The diagnosis was very wrong, and its explanation was fragmented and nonsensical until I directed it to examine its reasoning, at which point it reported that the entire fiasco had been done in error.

As a result, I've codified the process. If it's going to do it, it may as well do it right.

* An Error is a mistake the user made **despite knowing or being informed of the error condition ahead of time**. A mistake because the user didn't know something does not count an error. Your first one is always free. An error is a single event with a distinct time and circumstances: "Today the user pushed his changes to main despite knowing better." 
* Errors are grouped together by the agent's assessment of their broad root cause under Weaknesses. The agent logs the nature of each error and the day it occurred, and groups errors with similar root causes: "The user disregards best practices for github pushes in favor of speed."
* Once three errors have accumulated under an individual weakness, that weakness will be switched to "active" and the agent will begin to take special pains to warn the user when they're undertaking a task that is likely to expose them to their weakness behavior. "Remember to switch to a new branch before you do your work. Remember what happened last time."
* When a user is in a situation where their weakness is present but they take correct action, they score a "refutation" against that weakness.
* Scoring ten consecutive refutations without committing that same error will cause the agent to "retire" that weakness. 
* The weakness will be reactivated if the user commits errors within its purview five times after it was retired. You don't get to rest on your laurels.
* Every logging action, be it error or refutation, is passed by the user with veto privileges.
* The user is explicitly allowed to assert any changes or modifications to the ledger that they deem appropriate.
* To the best of my knowledge, this addition will not make the agent any better at identifying its user's type weaknesses, critical hit spots, or unresolved traumas.


The agent-facing rules live in `SKILL.md` — that file, not this one, is what the agent reads.

## Layout

```
SKILL.md        the mode's binding rules: what the agent may/may not do, the request-handling loop, star rating
progress.json   the mastery ledger: skills -> star rating + rationale + mastered tasks (persists across sessions)
```

## Making it truly default-on

A skill only affects a session once it is loaded into context. To have Training Mode active from the first message of *every* session (rather than only when the skill happens to be invoked), add a pointer in your global `CLAUDE.md`/`RTK.md` instructing the agent to load `training-mode` at session start, or wire a session-start hook. Without one of those, invoke it with `/training-mode`.
