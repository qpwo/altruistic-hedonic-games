Compile this with `pandoc --webtex explanation.md -o explanation.html`

## What is a hedonic game?

### Basic idea

You have a set of people which for some reason needs to split into groups. The
people are called _players_ and the groups are called _coalitions_. Maybe the
set of people is your kindergarten class and they need to split up so each
group can build a lego tower. A player might really want to be in some
coalitions, but not in others. These are the player's _preferences_. Once the
whole set of players is split into coalitions, it's called a _partition_ or a
_coalition formation_. **Hedonic games are the study of how people cooperate
and form groups together and when partitions are stable and when partitions
fall apart.**

More at [Wikipedia](https://en.wikipedia.org/wiki/Hedonic_game) or in
[Woe2013].

Hedonic games were independently introduced in [BKS2001] and [BJ2002].

### Notation

$N$ is the set of players, $n = |N|$ is the number of players, and
$\Gamma$ is a partition of $N$. Usually, $A \subseteq N$ is a coalition
and $a$ is a player in $A$. And $i$ is a player from anywhere in $N$.

If some player $i$ would rather be in coalition $A$ than coalition $B$,
then we say $A >_i B$. In principle, $i$'s preferences are defined by
listing all the subsets of $N$ which contain $i$ in order of decreasing
desirability. But that would take tons of computer memory or paper and ink, so
we mainly focus on hedonic games which are _compactly representable_. This
means that there is some concise way of describing each player's preferences
over the possible coalitions. Sometimes that means you have a _score function_
which takes a player and a coalition as input and outputs a real number; the
larger the number the more that player likes that coalition. Once you know the
score functions, you can concisely say that $A >_i B$ if and only if
$score_i(A) > score_i(B)$.

Most of the notation here is from [NRRRS2016]. Other stuff I added to make it
more programmer-friendly.

## Classes of Hedonic Games

A player is **friend-oriented** if she tries to maximize friends and, in the case
of a tie, minimize enemies. This preference relation is defined by the score function
$$score_i^{FO}(A) = n | A \cap F_i | - | A \cap E_i |$$
Introduced in [DBHS2006].

A player is **enemy-oriented** if she tries to minimize enemies and, in the case
of a tie, maximize friends. This preference relation is defined by the score function
$$score_i^{EO}(A) = | A \cap F_i | - n | A \cap E_i |$$
Introduced in [DBHS2006].

A player is **selfish-first** if she uses her own preferences first and only
uses her friends' preferences to break ties. This preference relation is
defined by the score function
$$score_i^{SF}(A) = n^5 score_i^{FO}(A) + \sum_{a \in A \cap F_i} \frac{score_a^{FO}(A)}{|A \cap F_i|}$$
Introduced in [NRRRS2016].

A player is **equal-treatment** if she puts equal weight on her own opinion and
each friend's opinion. This preference relation is defined by the score
function
$$score_i^{EQ}(A) = \sum_{a \in A \cap F_i \cup \{i\}} \frac{score_a^{FO}(A)}{|A \cap F_i \cup \{i\}|}$$
Introduced in [NRRRS2016].

A player is **altruistic-treatment** if she uses her friends' preferences first
and only uses her own preferences to break ties. This preference relation is
defined by the score function
$$score_i^{AL}(A) = score_i^{FO}(A) + n^5 \sum_{a \in A \cap F_i} \frac{score_a^{FO}(A)}{|A \cap F_i|}$$
Introduced in [NRRRS2016].

In **fractional hedonic games**, a player's score of a coalition is the average
of her scores of the players in it. Scores could be anything, but this
simulator only allows scores that are simple and symmetric.
$$score_i^{FR}(A) = |A \cap F_i| / |A|$$
Introduced in [ABH2014].

In **additively seperable hedonic games**, a player's score of a coalition is the
sum of her scores of the players in it. In unweighted, undirected graphs,
this is simply the degree of the player in the coalition.
$$score_i^{AS}(A) = |A \cap F_i|$$
I don't know when/where additively seperable hedonic games were introduced. If
you know, then please email me!

## Notions of stability

A partition is **individually rational** if every player is happier in her home
coalition than she would be alone. In other words, $\Gamma$ is individually
rational iff
$$\forall i \in N: \Gamma(i) \geq_i \{i\}$$

A partition is **Nash-stable** if every player is happier in her home coalition
than she would be in any other coalition. In other words, $\Gamma$ is
Nash-stable iff
$$\forall i \in N: \forall C \in \Gamma: \Gamma(i) \geq_i C \cup \{i\}$$

A partition is **individually stable** if every player who wants to transfer to a
new coalition is unwanted by someone in that coalition. In other words,
$\Gamma$ is individually stable iff $\forall i \in N: \forall C \in \Gamma:$
$$(\Gamma(i) \geq_i C \cup \{i\} \lor (\exists j \in C: C >_j C \cup \{i\} ))$$

A partition is **contractually individually** stable if every player who wants to
transfer to a new coalition is either unwanted by someone in that coalition or
is not permitted to leave by someone in her home coalition. In other words,
$\Gamma$ is contractually individually stable iff
$\forall i \in N: \forall C \in \Gamma:$
$\Gamma(i) \geq_i C \cup \{i\} \lor (\exists j \in C: C >_j C \cup \{i\} ) \lor$
$(\exists k \in \Gamma(i) \setminus \{i\}: \Gamma(i) >_k \Gamma(i) \setminus \{i\})$

A partition is **popular** if the majority of players weakly prefer it to any other
partition. In other words, $\Gamma$ is strictly popular iff
$$\forall \Gamma': |\{ i \in N: \Gamma(i) >_i \Gamma'(i) \}| \geq |\{ i \in N: \Gamma'(i) >_i \Gamma(i) \}|$$

A partition is **strictly popular** if the majority of players strictly prefer
it to any other partition. In other words, $\Gamma$ is strictly popular iff
$$\forall \Gamma': |\{ i \in N: \Gamma(i) >_i \Gamma'(i) \}| > |\{ i \in N: \Gamma'(i) >_i \Gamma(i) \}|$$

A partition is **core-stable** if there is no possible strictly blocking
coalition. (A coalition strictly blocks if everyone prefers it to their current
homes.) In other words, $\Gamma$ is core-stable iff
$$\not\exists C \in 2^N: \forall i \in C: C >_i \Gamma(i)$$

A partition is **strictly core-stable** if there is no possible weakly blocking
coalition. (A coalition weakly blocks if everyone is at least as happy in the
new one and someone is even happier.) In other words, $\Gamma$ is strictly
core-stable iff
$$\not\exists C \in 2^N: (\forall i \in C: C \geq_i \Gamma(i)) \land (\exists j \in C: C >_j \Gamma(i))$$

A partition is **perfect** if every player is in one of their favorite possible
coalitions. In other words, $\Gamma$ is perfect iff
$$\forall i \in N: \not\exists C \in 2^N: C >_i \Gamma(i)$$

## References

In chronological order:

\[BKS2001]:
Suryapratim Banerjee, Hideo Konishi, and Tayfun Sönmez.
"Core in a simple coalition formation game."
Social Choice and Welfare.
2001.

\[BJ2002]:
Anna Bogomolnaia and Matthew O Jackson.
"The Stability of Hedonic Coalition Structures."
Games and Economic Behavior.
2002.

\[DBHS2006]:
Dinko Dimitrov, Peter Borm, Ruud Hendrickx, and Shao Chin Sung.
"Simple priorities and core stability in hedonic games."
Social Choice and Welfare.
2006.
[PDF](http://fmwww.bc.edu/repec/esNASM04/up.9919.1074605860.pdf).

\[Woe2013]:
Gerhard J Woeginger.
"Core Stability in Hedonic Coalition Formation."
SOFtware SEMinar (SOFSEM).
2013.
[arXiv](https://arxiv.org/abs/1212.2236).

\[ABH2014]:
Haris Aziz, Feliz Brandt, and Paul Harrenstein.
"Fractional Hedonic Games."
The International Conference on Autonomous Agents and Multi-Agent Systems.
2014.
[arXiv](https://arxiv.org/abs/1705.10116).

\[NRRRS2016\]:
Nhan-Tam Nguyen, Anja Rey, Lisa Rey, Jörg Rothe, and Lena Schend.
"Altruistic Hedonic Games."
The International Conference on Autonomous Agents and Multi-Agent Systems.
2016.
[PDF](http://trust.sce.ntu.edu.sg/aamas16/pdfs/p251.pdf).

---

Public domain dedication. No rights reserved. Look at the source!

Hosted on [Github](https://github.com/qpwo/hedonic-games). Using graph visualization library [vis.js](http://visjs.org/).

To request a feature or report a bug, you can use github or you can email [luke.lambda@uky.edu](mailto:luke.lambda@uky.edu) . There is a standing bounty of $2.56, to be mailed to the first person to find a bug.
