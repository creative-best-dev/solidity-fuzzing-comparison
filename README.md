# Solidity Fuzzing Challenge: Foundry vs Echidna vs Medusa #

A comparison of solidity fuzzing tools [Foundry](https://book.getfoundry.sh/), [Echidna](https://secure-contracts.com/program-analysis/echidna/index.html) & [Medusa](https://github.com/crytic/medusa). This challenge set is not intended to be an academically rigorous benchmark but rather to present the experiences of an auditor "in the trenches"; the primary goal is finding the best performance "out of the box" with as little guidance & tweaking as possible.

Many of the challenges are simplified versions of audit findings from my private audits at Cyfrin. These findings could have been found by the protocol developers themselves prior to an external audit if the protocol had written the correct fuzz testing invariants. Hence a secondary goal of this repo is to show developers how to write better fuzz testing invariants to improve their protocol security prior to engaging external auditors.

## Setup ##

Ensure you are using recent versions of [Foundry](https://github.com/foundry-rs/foundry), [Echidna](https://github.com/crytic/echidna) and [Medusa](https://github.com/crytic/medusa).

Configure [solc-select](https://github.com/crytic/solc-select) for Echidna & Medusa:

`solc-select install 0.8.23`\
`solc-select use 0.8.23`

To compile this project:

`forge build`

Every exercise has a `basic` configuration and/or `advanced` fuzz configuration for Foundry, Echidna & Medusa. The `basic` configuration does not guide the fuzzer at all; it simply sets up the scenario and allows the fuzzer to do whatever it wants. The `advanced` configuration guides the fuzzer to the functions it should call and helps to eliminate invalid inputs which result in useless fuzz runs.

## Results ##

### Challenge #1 Naive Receiver: (Winner TIED ALL) ###

In `basic` configuration Foundry, Echidna & Medusa are able to break the simpler invariant but not the more valuable and difficult one. In `advanced` configuration all 3 fuzzers can break both invariants. All 3 fuzzers reduce the exploit chain to a very concise & optimized transaction set and present this to the user in an easy to understand output. As a result they are tied and there is no clear winner.

### Challenge #2 Unstoppable: (Winner TIED ALL) ###

All Fuzzers in `basic` configuration can break both invariants; Foundry appears to be the slightly faster.

### Challenge #3 Proposal: (Winner TIED ALL) ###

Foundry, Echidna & Medusa in `basic` mode are able to easily break the invariant, resulting in a tie.

### Challenge #4 Voting NFT: (Winner TIED ALL) ###

In `basic` configuration Foundry, Echidna & Medusa are all able to break the easier invariant but not the more difficult one. All Fuzzers are able to provide the user with a minimal transaction set to generate the exploit. Hence they are tied, there is no clear winner. Please note that the fuzz solvers for this challenge are not able to be publicly released at this time.

### Challenge #5 Token Sale: (Winner MEDUSA) ###

In `basic` configuration Foundry & Echidna can only break the easier and more valuable invariant which leads to a Critical exploit but not the harder though less valuable invariant which leads to a High/Medium. However Medusa is able to almost immediately break both invariants in unguided `basic` mode, making Medusa the clear winner.

### Challenge #6 Rarely False: (Winner NONE) ###

Both Echidna & Foundry are unable to break the assertion in this stateless fuzzing challenge. Medusa [used](https://twitter.com/DevDacian/status/1732199452344221913) to be able to break it almost instantly but has [regressed](https://github.com/crytic/medusa/issues/305) in performance after recent changes and is now unable to break it. Hence there is no current winner.

### Challenge #7 Byte Battle: (Winner TIED FOUNDRY & ECHIDNA)

Foundry & Echidna are able to break the assertion in this stateless fuzzing challenge, but Medusa is [unable](https://github.com/crytic/medusa/issues/307) to break it.

### Challenge #8 Omni Protocol: (Winner MEDUSA)

All 3 Fuzzers configured in `advanced` guided mode attempted to break 16 invariants on Beta Finance [Omni Protocol](https://github.com/beta-finance/Omni-Protocol). Medusa is typically able to break 2 invariants within 5 minutes (often much sooner on subsequent runs) though on the first run can take a bit longer. Echidna can sometimes break 1 invariant within 5 minutes and Foundry appears to never be able to break any invariants within 5 minutes. Hence Medusa is the clear winner. The fuzzers written for this challenge were [contributed](https://github.com/beta-finance/Omni-Protocol/pull/2) to Beta Finance.
