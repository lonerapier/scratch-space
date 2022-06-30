# Sense Math

## Notation

$s(t)$
: the scale at the time $t$, i.e. the price of the Target token in the underlying token terms at the time moment $t$

$\theta$
: The tilt value, i.e. the part of the principal that belong to Claims

$t_0$
: The issue time

$t_m$
: The maturity time

$S(t)$
: The maximum scale value earnings were collected at up until the time moment $t$

$x$
: The amount of the Target token being deposited by a user

$X$
: The effective amount of the Target token being deposited

$\Delta x$
: The addition amount of the Target token, collected during issuance and added to the amount deposited by the user

$y$
: An amount of Zero or Claim token

$x_z$
: The ideal amount of the Target token redeemed by a Zero holder

$X_z$
: The actual amount of the Target token redeemed by a Zero holder

$x_c$
: The ideal total amount of the Target token redeemed by a Claim holder

$x'_c$
: The ideal amount of the Target token redeemable by a Claim holder before maturity

$x''_c$
: The ideal amount of the Target token redeemable by a Claim holder after maturity

$X_c$
: The actual total amount of the Target token redeemed by a Claim holder

$X''_c$
: The actual amount of the Target token redeemable by a Claim holder after maturity

## Issue

At the time moment $t_0$, a user deposits $x$ amount of the Target token.  He gets: $y = x \cdot s(t_0)$ units of Zero token, and the same amount of Claim token.

In case $s(t_0) < S(t_0)$ the user may immediately collect a certain amount of the Target tokens from just issued Claim token.  It would be reasonable to consider this addition amount of the Target token to be added to the original deposited amount.  The exact additional amount could be found from the following equation:

$$
\Delta x = (x + \Delta x) \cdot s(t_0) \cdot \left( \frac{1}{s(t_0)} - \frac{1}{S(t_0)} \right) = (x + \Delta x) \cdot \left( 1 - \frac{s(t_0)}{S(t_0)} \right) \\
\Delta x \left( 1 - \left( 1 - \frac{s(t_0)}{S(t_0)} \right) \right) = x \cdot \left( 1 - \frac{s(t_0)}{S(t_0)} \right) \\
\Delta x \cdot \frac{s(t_0)}{S(t_0)} = x \cdot \left( 1 - \frac{s(t_0)}{S(t_0)} \right) \\
\Delta x = \frac{x \cdot \left( 1 - \frac{s(t_0)}{S(t_0)} \right)}{\frac{s(t_0)}{S(t_0)}} = x \cdot \left( \frac{S(t_0)}{s(t_0)} - 1 \right)
$$

So the full deposited amount is:

$$
X = x + \Delta x = x + x \cdot \left( \frac{S(t_0)}{s(t_0)} - 1 \right) = x \cdot \frac{S(t_0)}{s(t_0)}
$$

and the full amount of issued tokens (the same for Zero and Claim tokens) is:

$$
Y = X \cdot s(t_0) = x \cdot \frac{S(t_0)}{s(t_0)} \cdot s(t_0) = x \cdot S(t_0)
$$

Note, that the outcome for the user is as if the current scale was $S(t_0)$ rather than $s(t_0)$. Thus is would be reasonable for the protocol to use the current max scale instead of the current scale when calculating the number of Zeros and Claim to be issued.

In case the user already have some amount of Claim token when issuing more tokens, earnings should be collected from these Claim token amount before issuing more Claim token.  Let's assume that the user already has the amount $Y_l$ of Claim token and earnings were last colected at the time $t_l$. Then the user may collect the following amount $x_l$ of Target token from his existing Claim holdings:

$$
x_l = Y_l \cdot \left( \frac{1}{s(t_l)} - \frac{1}{S(t_0)} \right)
$$

Thus the full amount of issued tokens, that takes into account the collected earnings from existing Claim holdings, is:

$$
Y = \left( x + Y_l \cdot \left( \frac{1}{s(t_l)} - \frac{1}{S(t_0)} \right) \right) \cdot S(t_0)
$$

## Redeem

### Ideal Case

In the ideal case, when a user redeems $y$ amount of Zero token, he gets the following amount of the Target token:

$$
x_z =
y\frac{(1 - \theta)}{s(t_m)}
$$

In the ideal case, when a user redeems $y$ amount of Claim token, he gets the following total amount of the Target token:

$$
x_c =
y\left( \frac{\theta}{s(t_m)} + \frac{1}{s(t_0)} - \frac{1}{s(t_m)} \right) =
y \left( \frac{1}{s(t_0)} - \frac{(1 - \theta)}{s(t_m)} \right)
$$

Note that:

$$
x_z + x_c =
\frac{y}{s(t_0)} =
\frac{x \cdot s(t_0)}{s(t_0)} =
x
$$

i.e. the whole deposited amount is redeemed.

### Real Case

The following amount could be collected by Claim token holders before maturity:

$$
x'_c =
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right)
$$

So the following amount is to be collected after maturity:

$$
x''_c =
x_c - x'_c =
y\left( \frac{1}{s(t_0)} - \frac{(1 - \theta)}{s(t_m)} \right) - y\left( \frac{1}{s(t_0)} - \frac{y}{(t_m)} \right) = y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right)
$$

As it is impossible to collect a negative amount, the actual amount collected after maturity is:

$$
X''_c =
\max (0, x''_c) =
\max \left( 0, y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right)
$$

Thus the actual total amount of the Target tokens got by the Claim token hoder is:

$$
X_c =
x'_c + X''_c =
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) + \max \left( 0, y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right)
$$

and the actual amount of the Target tokens got by the Zero token holder is:

$$
X_z =
x - X_c =
\frac{y}{s(t_0)} - y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) - \max \left( 0, y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) =\\=
y\left( \frac{1}{S(t_m)} - \max \left( 0, \left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) \right)
$$

### Sunny Day

When

$$
y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \geqslant 0
$$

we have:

$$
X_c =
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) + \max \left( 0, y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) =\\=
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) + y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) =\\=
y\left( \frac{1}{s(t_0)} - \frac{(1 - \theta)}{s(t_m)} \right) = x_c
$$

$$
X_z =
y\left( \frac{1}{S(t_m)} - \max \left( 0, \left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) \right) =\\=
y\left( \frac{1}{S(t_m)} - \left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) =\\=
y \frac{(1 - \theta)}{s(t_m)} = x_z
$$

So, in a sunny day scenario, $X_z = x_z$ and $X_c = x_c$.

### Not So Sunny Day

When the day is not so sunny, i.e.

$$
y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) < 0
$$

we have:

$$
X_c =
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) + \max \left( 0, y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) =\\=
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) + 0 =\\=
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right)
$$

$$
X_z =
y\left( \frac{1}{S(t_m)} - \max \left( 0, \left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \right) \right) =\\=
y\left( \frac{1}{S(t_m)} - 0 \right) =\\=
\frac{y}{S(t_m)}
$$

### Sunny Day Condition

Lets transform the sunny day condition a bit:

$$
y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) \geqslant 0 \\
\frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \geqslant 0 \\
s(t_m) - S(t_m) \cdot (1 - \theta) \geqslant 0 \\
s(t_m) \geqslant S(t_m) \cdot (1 - \theta) \\
\frac{s(t_m)}{S(t_m)} \geqslant 1 - \theta
$$

### The Final Formulas

So, the final formulas are:

Zero token holders get the following amount of the Target tokens after maturity:

$$
X_z =
\begin{cases}
y \frac{(1 - \theta)}{s(t_m)} & \text{if} & \frac{s(t_m)}{S(t_m)} \geqslant 1 - \theta, \\
\frac{y}{S(t_m)} & \text{if} & \frac{s(t_m)}{S(t_m)} < 1 - \theta
\end{cases}
$$

Claim token holders get the following mount of the Target token after maturity:

$$
X''_c =
\begin{cases}
y\left( \frac{1}{S(t_m)} - \frac{(1 - \theta)}{s(t_m)} \right) & \text{if} & \frac{s(t_m)}{S(t_m)} \geqslant 1 - \theta, \\
0 & \text{if} & \frac{s(t_m)}{S(t_m)} < 1 - \theta
\end{cases}
$$

Claim token holders get the following total amount of the Target tokens:

$$
X_c =
\begin{cases}
y\left( \frac{1}{s(t_0)} - \frac{(1 - \theta)}{s(t_m)} \right) & \text{if} & \frac{s(t_m)}{S(t_m)} \geqslant 1 - \theta, \\
y\left( \frac{1}{s(t_0)} - \frac{1}{S(t_m)} \right) & \text{if} & \frac{s(t_m)}{S(t_m)} < 1 - \theta
\end{cases}
$$

---

### The Current Formula

$$
X''_c = \max \left( 0, y \cdot \frac{\theta}{S(t_m)} - \left( y \cdot \frac{1 - \theta}{s(t_m)} - y \cdot \frac{1 - \theta}{S(t_m)} \right) \right)
$$

## Add Liquidity

### Target $\leftrightarrow$ Zero Pool

A user wants to provide the amount $x$ of a Target token as liquidity to a Balancer pool that trades this Target token against a Zero token derived from this Target token.

The user splits the oritinal Target amount into two parts: $x = x' + x''$.  The first part $x'$ goes directly to the pool.  The second part $x''$ is used to issue Zero that will go to the pool.  The amount of Zero issued is:

$$
y = x'' \cdot S(t)
$$

Let $X$ be the pool reserves of the Target token, and $Y$ be the pool reserves of Zero.  The user needs to provide the tokens at the same proportion, so:

$$
\frac{x'}{y} = \frac{X}{Y} \\
\frac{x'}{x'' \cdot S(t)} = \frac{X}{Y} \\
\frac{x - x''}{x'' \cdot S(t)} = \frac{X}{Y} \\
(x - x'') \cdot Y = x'' \cdot S(t) \cdot X \\
x \cdot Y = x'' \cdot \left( S(t) \cdot X + Y \right) \\
x'' = x \cdot \frac{Y}{S(t) \cdot X + Y}
$$

### Underlying $\leftrightarrow$ Zero Pool

A user wants to provide the amount $x$ of a Target token as liquidity to a Balancer pool that trades the Underlying token of this Target token to a Zero token derived from this Target token.

The user splits the oritinal amount into two parts: $x = x' + x''$.  The first part $x'$ is redeemed for the Underlying token.  The amount of obtained underlying token is:

$$
z = x' \cdot s(t)
$$

The second part $x''$ is used to issue Zero that will go to the pool.  The amount of Zero issued is:

$$
y = x'' \cdot S(t)
$$

Let $Z$ be the pool reserves of the Underlying token, and $Y$ be the pool reserves of Zero.  The user needs to provide the tokens at the same proportion, so:

$$
\frac{z}{y} = \frac{Z}{Y} \\
\frac{x' \cdot s(t)}{x'' \cdot S(t)} = \frac{Z}{Y} \\
\frac{(x - x'') \cdot s(t)}{x'' \cdot S(t)} = \frac{Z}{Y} \\
(x - x'') \cdot s(t) \cdot Y = x'' \cdot S(t) \cdot Z \\
x \cdot s(t) \cdot Y = x'' \cdot \left( S(t) \cdot Z + Y \right) \\
x'' = x \cdot \frac{s(t) \cdot Y}{S(t) \cdot Z + s(t) \cdot Y}
$$

---

### The Current Formula

$$
x'' = x \cdot \frac{Y}{X + Y}
$$

### Sell Claim Mode

1. A user provides amount $x$ of the Target token
2. The amount is split into two parts: $x'$ and $x''$: $x = x' + x''$
3. The $x''$ amount is used to issue an amount $y$ of Zero and the same amount $y$ or Claim
4. The $x'$ amount of Target and $y$ amount of Zero are put into the pool
5. The pool return the amount $z$ of the LP token
6. The returned LP token amount is sent to the user
7. ... so far so good ...
8. Some amount of the Target token is borrowed
9. The borrowed Target tokens are used to buy the amount $y$ of Zero from the pool (we just put this amount of Zero into the pool and now bying it back!)
10. The amount $y$ of Zero and the same amount $y$ of Claim are combined into the amount $x'$ of Target (we just split and now recombine!)
11. A part of the proceeds from recombining is used to repay the debt
12. The rest proceeds of recombining are returned to the user (the user just provided some amount of Target token, and now we return part of it!)

The whole schema is more or less equivalent to this one:

1. A user provides amount $x$ of the Target token
2. The amount is split into two parts: $x'$ and $x''$: $x = x' + x''$
3. The $x''$ amount is used to buy an amount $y$ of Zero from the pool
4. The $x'$ amount of Target and $y$ amount of Zero are put into the pool
5. The pool return the amount $z$ of the LP token
6. The returned LP token amount is sent to the user