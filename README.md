# Film Taste Profile

Film Taste Profile (working title) is a machine learning experiment, using a film recommendation engine (cliche, I know) as a tool to teach how Artificial Intelligence (machine learning, not an LLM) builds personalised content for you, the user.

## Teaching Robots to Learn

There are many ways to teach a machine. Two such ways that I know of are called _supervised_ and _unsupervised_ learning.

### Supervised Learning

One way to teach the machine to "think for itself" is to feed it **labeled** data where you already know what the correct answer is, and you let the machine guess until it gets it right. The more data you feed it, the better it'll get at guessing. Eventually, you'll be able to use it to make critical decisions in your software, like: Is this email spam? Does that payment look like fraud? Should I share my master's bank account details with this perfectly nice stranger? Or, in our case, does it seem like the user will like this film?

### Unsupervised Learning

The other way, unsupervised learning, is where we let the machine "think for itself" without any care about what it'll do. It's where we just keep fattening it up with more and more data. Weirdly enough, when we're not telling the machine whether it's right or wrong, it seems to become pretty good at discovering things we never thought of. It's not bound by the limitations of our own knowledge. This is pretty handy for recommendation engines, image compression, and automatically grouping your extended family into "safe" and "highly toxic" categories based on their holiday conversation patterns.

### Semi-Supervised Learning

Supervised and unsupervised _both_ sound pretty interesting, right? If only there was a middleground... oh, wait... what is this semi-supervised learning? Well, it's where you label just a tiny handful of films, and the machine looks at the hidden patterns in the rest of the massive, unlabeled catalogue to automatically guess the labels for you.

#### Active Learning

Active learning is a form of semi-supervised learning where the machine picks **unlabeled** samples from the data and asks for a human to do the work for them. Once it's copied the human's answers, it can take what it's learned from the human and attempt to guess based on its new understanding of the dataset. 

##### Preference Learning

The specific type of Active Learning I'm interested in experimenting with for this project is called Preference Learning. It's where the user is given two distinct choices. Whichever choice they make will have an informative impact on what the machine thinks is relevant to that user's taste profile.

The machine shouldn't pick random pairs. It doesn't want to confirm what it already knows. So, the machine picks two options that will help it find the boundary of what it _doesn't_ know. If the user consistently picks action over romance, asking them to choose between _Die Hard_ and _The Terminator_ won't tell the machine anything new because it already knows the user likes both. Pitting _The Terminator_ against an action-comedy like _Hot Fuzz_ pinpoints exactly where their genre boundaries lie. You want the machine to ask questions where it's 50/50 whether the user will choose one or the other.

Training the machine to learn means assigning both the user and the films a set of stats. If the user chooses _Alien_ (high sci-fi, high horror) over _Notting Hill_ (high romance, high comedy), the machine ramps up the user's stats closer to the _Alien_ numbers and pushes it away from the _Notting Hill_ numbers.
