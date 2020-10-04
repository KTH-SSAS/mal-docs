# Attack steps

Attack steps of type **AND**, `&` and **OR**, `|` use probability distributions to specify the TTC (time to compromise) of that attack step. For example:

```
category Systems
{
  asset Computer
  {
    | compromise [Exponential(0.1)]
  }
}
```

This means that the time to perform the attack step `compromise` on an asset `Computer` is expressed with an exponential distribution with a rate of 0.1 (with a mean value of 10 days).

The following distributions are supported:

| Distribution | Parameters | Limits | Expected value |
| ------------ | ---------- | ------ | -------------- |
| Bernoulli | _p_, probability | 0 <= _p_ <= 1 | E[Bernoulli(_p_)] = _p_ |
| Binomial | _n_, trials and _p_, probability | 0 <= _n_, 0 <= _p_ <= 1 | E[Binomial(_n_, _p_)] = _n_ * _p_ |
| Exponential | _λ_, rate | 0 < _λ_ | E[Exponential(_λ_)] = 1 / _λ_ |
| Gamma | _k_, shape and _θ_, scale | 0 < _k_, 0 < _θ_ | E[Gamma(_k_, _θ_)] = _k_ * _θ_ |
| LogNormal | _μ_, mean and _σ_, standard deviation | 0 < _σ_ | E[LogNormal(_μ_, _σ_)] = _e_^(_μ_ + _σ_^2 / 2) |
| Pareto | _m_, minimum and _α_, shape | 0 < _m_, 0 < _α_ | E[Pareto(_m_, _α_)] = _infinity_ if _m_ <= 1, otherwise (_m_ * _α_) / (_α_ - 1) |
| TruncatedNormal | _μ_, mean and _σ_, standard deviation | 0 < _σ_ | E[TruncatedNormal(_μ_, _σ_)] = _μ_ |
| Uniform | _min_ and _max_ | _min_ <= _max_ | E[Uniform(_min_, _max_)] = (_min_ + _max_) / 2 |

Distributions can also be combined with addition, `+`, subtraction, `-`, multiplication, `*`, division, `/`, exponentiation `^`, and parentheses `(`, `)`.

The following ordinal distributions are supported:

| Ordinal distribution | Definition |
| -------------------- | ---------- |
| EasyAndCertain | Exponential(1) |
| EasyAndUncertian | Bernoulli(0.5) |
| HardAndCertain | Exponential(0.1) |
| HardAndUncertain | Bernoulli(0.5) * Exponential(0.1) |
| VeryHardAndCertain | Exponential(0.01) |
| VeryHardAndUncertain | Bernoulli(0.5) * Exponential(0.01) |

The following constants are supported:

| Constant |
| -------- |
| Infinity |
| Zero |

## Bernoulli behaviour

An attack step with a TTC function `Bernoulli(p)` means that the attack step can be performed immediately with a probability of _p_, and not at all with a probability of 1 - _p_.

If a Bernoulli distribution is used in multiplication, e.g. `Exponential(λ) * Bernoulli(p)`, that means that the TTC of the attack step is `Exponential(λ)` with a probability of _p_, and `Infinity` with a probability of 1 - _p_.

Bernoulli distributions can also be used in addition, but not in subtraction, division, or exponentiation.

# Defenses

Defenses, `#`, are either enabled or disabled with a probability. For example:

```
category Systems
{
  asset Computer
  {
    # protected [Bernoulli(0.4)]
  }
}
```

This means that the probability that the defense `protected` is enabled for an asset `Computer` is 0.4 (40%).

Defenses can use the following probabilies:

| Probability | Explanation |
| ----------- | ----------- |
| Bernoulli(_p_) | Defense is enabled with probability _p_ |
| Enabled | Same as Bernoulli(1) |
| Disabled | Same as Bernoulli(0) |