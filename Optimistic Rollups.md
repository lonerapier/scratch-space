# Optimistic Rollups

## Links

[(Almost) Everything you need to know about Optimistic Rollup](https://research.paradigm.xyz/rollups)

[The Cheater Checking Problem: Why the Verifier’s Dilemma is Harder Than You Think](https://medium.com/offchainlabs/the-cheater-checking-problem-why-the-verifiers-dilemma-is-harder-than-you-think-9c7156505ca1)

## Asserter/Checker Problem

- Asserter makes a claim $A$, checker checks the claim for cost $C$ and gets R as reward, if successful.
- If Asserter cheats without getting caught, Checker loses $L$ in the form of loss of items of value.
- Two main threats to worry about: Laziness and Bribery
- Bribery: Asserter bribes Checker more than the reward $R$. Prevent this by large bond by Asserter so that bribe can’t be bigger than the reward $R$.
- Laziness: Checker does not check intentionally.
- If we assume, asserter cheats with prob. X, then
checker’s utility comes as:

> $-X*L$, if checker doesn’t
>
> $R*X-C$, if checker checks

- So, checking is only worthwhile, if utility of checking > not checking.

> $X > C/(R+L)$

- Thus, *asserter* can cheat with random prob < required and not get caught.
- This doesn’t depend on how much *asserter* gains from cheating, as long as it’s non-zero which is a bad result.
- Even adding more *checkers* doesn’t help, as the reward gets distributed which only reduces *checker’s* incentives.

Reason this is a problem:

- *Asserter* controls the behaviour of the checker, as the utility of checker depends on asserter’s prob.
- Need to add an attention parameter in *checker’s* incentives where checker pre-computes *asserter’s* claim off-chain beforehand and has to verify it on-chain time to time.
- Two new parameters:

> $P$, fraction of time *checker's* will post response
>
> $A$, penalty in case *checker* gives wrong answer

- New equation becomes,

$$R*X-C$$

$$-L*X-P*A$$

- If $P*A > C$, then checking is better than not checking
- cost of checking is low

> Assume 1 assertion / 5 mins and $C = $0.001$ If P = 0.3%, checker will deposit $3.
cost per assertion = $0.0003 => $0.01*0.3%
interest cost of locking = $0.0003
total cost = $0.0006

- Multiple checkers need to submit proofs differently thus, scaling to multiple checkers efficient.

## Technical Details

Checker: private key $k$, public key $g^k$

Hash fn: $H$

Computation to solve: $f(x)$

Asserter challenge: $(x, g^r)$

Checker post on-chain iff $H(g^{rk}, f(x)) < T$

> Note: Only checker and asserter knows $g^k$ and $r$, and $T$ requires $f(x)$

Checker can guess $f(x)$ with prob. G, then multiply deposit with $1/1-G$

- Asserter publishes f(x), can challenge checker's response while publishing r
- Check the accusation and penalise checker, half the deposit to asserter
- If asserter $f(x)$ incorrect, accusation reverted.
- Each checker will have different prob of posting on-chain due to use of private key, thus can’t copy others computation.
- Asserter now instead of bribing checker, will try to mislead him into giving false information on-chain.
