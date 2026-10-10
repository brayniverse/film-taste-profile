# Film Taste Profile

Film Taste Profile (working title) is a machine learning experiment, using a film recommendation engine (cliche, I know) as a tool to teach how Artificial Intelligence (classic machine learning, not an LLM) builds personalised content for you, the user.

## Teaching Robots to Learn

There are many ways to teach a machine. Two such ways that I know of are called _supervised_ and _unsupervised_ learning.

### Supervised Learning

One way to teach the machine to "think for itself" is to feed it **labelled** data where you already know what the correct answer is, and you let the machine guess until it gets it right. The more data you feed it, the better it'll get at guessing. Eventually, you'll be able to use it to make critical decisions in your software, like: Is this email spam? Does that payment look like fraud? Should I share my master's bank account details with this perfectly nice stranger? Or, in our case, does it seem like the user will like this film?

### Unsupervised Learning

The other way, unsupervised learning, is where we let the machine "think for itself" without any care about what it'll do. It's where we just keep fattening it up with more and more data. Weirdly enough, when we're not telling the machine whether it's right or wrong, it seems to become pretty good at discovering things we never thought of. It's not bound by the limitations of our own knowledge. This is pretty handy for recommendation engines, image compression, and automatically grouping your extended family into "safe" and "highly toxic" categories based on their holiday conversation patterns.

### Semi-Supervised Learning

Supervised and unsupervised _both_ sound pretty interesting, right? If only there was a middle ground... oh, wait... what is this semi-supervised learning? Well, it's where you label just a tiny handful of films, and the machine looks at the hidden patterns in the rest of the massive, unlabelled catalogue to automatically guess the labels for you.

#### Active Learning

Active learning is a close cousin of semi-supervised learning where the machine picks **unlabelled** samples from the data and asks for a human to do the work for it. Once it's copied the human's answers, it can take what it's learned from the human and attempt to guess based on its new understanding of the dataset.

##### Preference Learning

The specific approach I'm interested in experimenting with for this project is Preference Learning, combined with Active Learning to choose the pairs. It's where the user is given two distinct choices. Whichever choice they make will have an informative impact on what the machine thinks is relevant to that user's taste profile.

The machine shouldn't pick random pairs. It doesn't want to confirm what it already knows. So, the machine picks two options that will help it find the boundary of what it _doesn't_ know. If the user consistently picks action over romance, asking them to choose between _Die Hard_ and _The Terminator_ won't tell the machine anything new because it already knows the user likes both. Pitting _The Terminator_ against an action-comedy like _Hot Fuzz_ pinpoints exactly where their genre boundaries lie. You want the machine to ask questions where it's 50/50 whether the user will choose one or the other.

Training the machine to learn means assigning both the user and the films a set of stats. If the user chooses _Alien_ (high sci-fi, high horror) over _Notting Hill_ (high romance, high comedy), the machine ramps up the user's stats closer to the _Alien_ numbers and pushes it away from the _Notting Hill_ numbers.

### Multi-Armed Bandit

I know what you're thinking, and yes, the Multi-Armed Bandit problem _is_ a decision-making and reinforcement learning concept named after casino slot machines, and not a really cool western bad guy. Slot machines are referred to as _One_-Armed Bandits; you have one option: pull the arm and play. If you have a player facing up against multiple slot machines, you get the Multi-Armed Bandit problem. The player now has multiple choices and must calculate which machine is likely to pay out. The player could stay playing one machine that's paying out fairly well, but they could take their chances on a different machine which could pay out even more. So, the player must explore the machines to figure out which one to exploit. How does this relate to a film recommendation engine? Well, we don't want to always show users wildly dissimilar and unexpected recommendations because the user will feel like they aren't getting anywhere. Therefore, we want to have periods of time where they're getting films they recognise. We're positively reinforcing the knowledge we have about their taste profile. Then BLAM, we throw them a curveball and we see what they do. If we constantly give them the same old Rom-Com safe recommendations, they'll get bored, so we give them a Rom-Com vs a Comedy Action instead. If they choose the Comedy Action, we now know something new about them, and have a new thread to pull on.

## The Maths Part

In machine learning, a "vector" is just a fancy word for a list of numbers. To make this work, the machine secretly assigns every film a stat card. Let's say we are only tracking three genres out of 10: Sci-Fi, Horror, and Romance.

- Alien has a stat card that looks like this: `[Sci-Fi: 9, Horror: 8, Romance: 0]`
- Notting Hill looks like this: `[Sci-Fi: 0, Horror: 0, Romance: 9]`

The machine also gives you, the user, a stat card. When you first join, it might just be zeroes, but over time, it builds a profile of your tastes.

### The Compatibility Score (Dot Product)

When the machine wants to guess how much you'll like a film, it calculates a "Dot Product". Weird name, but it's to do with how the boffins write these in their fancy equations. It literally just means multiplying your stats by the film's stats and adding them up.

If your personal stat card is `[Sci-Fi: 9, Horror: 5, Romance: 2]`, and it wants to know if you'll like `Alien [Sci-Fi: 9, Horror: 8, Romance: 0]`, it does this:

- `(9 x 9) + (5 x 8) + (2 x 0) = 121`

Then it calculates Notting Hill:

- `(9 x 0) + (5 x 0) + (2 x 9) = 18`

The machine now has a mathematical score _proving_ you will vastly prefer face-hugging aliens over Hugh Grant. I don't get it, personally, how could you say no to Hugh Grant's charming smile?

### The 50/50 Coin Flip (Logistic Curve)

When the machine pits two films against each other for Preference Learning, it compares their compatibility scores. But raw numbers like 121 and 18 are hard to work with, so it feeds the _difference_ between them into a curve that converts them into a percentage between 0% and 100%.

If the scores are miles apart, the machine might calculate a 99% probability you'll pick Alien. But if the machine pits Alien (score: 121) against the Terminator (score: 121), the maths perfectly balances out to exactly 50%. This is the exact mathematical boundary we are looking for to test your limits.

### The Surprise Factor (Stochastic Gradient Descent)

This is how the machine actually learns. When you pick a film, the machine updates your personal stat card. It does this using a rule called "Stochastic Gradient Descent", which is just a very aggressive way of saying "learning from your mistakes".

The machine relies on the **Surprise Factor**.

- If the machine was 99% sure you'd pick Alien, and you did, the machine pats itself on the back and only tweaks your stats by a microscopic fraction.
- But, if you wildly defy expectations and pick Notting Hill, the machine effectively screams, "WHAT AM I DOING WITH MY LIFE?" The massive surprise factor forces the machine to drastically rewrite your stat card, pulling your numbers heavily toward Romance and away from Sci-Fi.

## Remembering

A taste profile is only as good as how well it knows you, and it will struggle to _know_ you if it can't remember the choices you've made. For this problem, I introduce the age-old concept of Event Sourcing. It's essentially what an accountant does but with a fancy name. Every time something happens, we write it down as a fact and never rub it out. By replaying the events, we can figure out what the current state is. For instance, if you're given £10.00 by your delightful nan for being such a good person, you spend £2.00 on the bus fare, £3.50 on coffee, and £4.00 on a ceramic dog figurine to add to your collection. If I were to ask you how much money you have left without counting what's in your hand, you'd have to replay all the transactions in your head: £10 - £2 - £3.50 - £4 = 50p. That's it. That's all that event sourcing is. A log of all the transactions that we use to figure out what the current state is.

This comes with some beautiful bonuses over only storing your current stat card and overwriting it each time.

1. We can change the logic behind our recommendation machine and recalculate everyone's taste profile by replaying every choice through a tweaked learning rate or Surprise Factor.
2. We can know exactly what choices you've made over time with the machine, which makes it easier for us to explain why we made certain recommendations.
3. We can build as many read models (different views built from the same log) as we need for specific use cases. For example, a dashboard, a daily summary, testing new engines, whatever you can dream up.

## Side-Thought

I just had a thought that I need to fit in somewhere within this README.

The data I'll be sourcing will be incomplete at best. Therefore, some films will have more fleshed out category data, while others may have a handful, or none at all. This causes three problems when calculating the dot product:

1. Films with more tags will score higher, and get recommended more often, than films with fewer tags. To combat this, I'll shrink every film's stat card to the same overall length, so a film can't win just by having more tags. The boffins call this "cosine similarity".
2. Vague tags like `drama` are on so many films that they'll massively skew recommendations towards other dramas. To combat this, I'll weight each tag by how rare it is, using `log(total films / films with that tag)`. The boffins call this "inverse document frequency". The `log` stops common tags from being punished too hard, which a straight divide would do.
3. Films with no tags at all will score 0 for everyone, so picking one teaches the machine nothing. These will need filling in, or leaving out of the pairs entirely.
