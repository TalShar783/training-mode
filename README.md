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

At this time, Training Mode assesses the user's skill rating based on their own assertions and a ledger that keeps track of what tasks the user has completed and the agent verified. Default behavior is to consider a task "mastered" after 10 repetitions over at least 2 different days, since we remember better through repetition over multiple sessions. Each individual task a user completes is added to the ledger, and once mastered the model will offer to complete the task on its own. Optionally, the user may just have it keep tutoring and guiding and never take any action regardless of mastery level. Skills are rated one to ten stars based upon how completely their mastered tasks cover the breadth of the skill, and at this time have no hard-coded effect.

Training Mode works nicely with other skills that teach the agent the learning material. It is still very much in development and is at time of writing in a rudimentary state, but with Claude (which is what it's being developed for specifically, no guarantees for other models) the new behavior is already very promising. So far the agent:

* Asks the user what direction they want to take at the beginning of the session and each time a natural pivot point is reached.
* Keeps the project moving one thing at a time, stopping whenever it detects a task that the user hasn't mastered.
* Reviews the user's output and flags errors, pointing them to the line(s) and giving an explanation of what is going wrong, allowing the user to self-diagnose and fix the problem on their own terms.
* Does a pretty good job of arranging plans of work to train foundational skills before advanced ones.

Contributions are very welcome.




The agent-facing rules live in `SKILL.md` — that file, not this one, is what the agent reads.

## Layout

```
SKILL.md        the mode's binding rules: what the agent may/may not do, the request-handling loop, star rating
progress.json   the mastery ledger: skills -> star rating + rationale + mastered tasks (persists across sessions)
```

## Making it truly default-on

A skill only affects a session once it is loaded into context. To have Training Mode active from the first message of *every* session (rather than only when the skill happens to be invoked), add a pointer in your global `CLAUDE.md`/`RTK.md` instructing the agent to load `training-mode` at session start, or wire a session-start hook. Without one of those, invoke it with `/training-mode`.
