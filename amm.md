# Automated Market-Makers (AMM)

## Some history
Prediction markets such as Gnosis and Augur ran into the problem of how to set the initial liquidity for their bets.  Each token in a particular betting market represents an outcome and the relative pricing of outcomes must be set to provide sufficient liquidity for the subsequent betting (trading).

Out of this problem, multiple approaches such as LMSR were proposed, but it was soon realized, notably by Vitalik Buterin, that providing liquidity for trading in decentralized fashion was a much wider problem in need of a simple solution.  The key insight he provided was that any good solution should only require transactions for the actual trades, not for order placement or orderbook management.  A mathematical formula should maintain the orderbook's state on-chain.

Martin Koppelmann (Gnosis) suggested to Vitalik that a constant product formula was simple and had the desired properties for market-making:
- the more bought, the greater the slipppage
- the ratio of token reserves gives the price of one token vs another (for an infinitesimal quantity)
- there is *always* liquidity for either token for the other
- market-making fees are easily incorporated


It is interesting to note that the motivation for Vitalik's post was to describe frontrunning mitigation by miners and the usage of "virtual" token reserves to do so.  However, what in fact happened was that the post caught interest of Haayden Adams, who then created Uniswap based on the idea, with no frontrunning mitigation in place.

One frequent, early object one finds on these discussion threads is that an AMM would be too inefficient and costly for actual trading.  One commenter remarks that it would be too difficult to mitigate frontrunning by miners.  The potential for composability of AMMs with other building blocks was not yet understood.

https://ethresear.ch/t/improving-front-running-resistance-of-x-y-k-market-makers/1281

## Examples

### Uniswap

Uniswap is the original constant-product AMM which originated out of Vitalik's influential AMM post.  Vitalik gave much input into its design and implementation (which explains why the original V1 platform is written in Vyper!).  

x * y = k

x_in = amount transferred in
x_in_fee = x_in * 0.997 (take out 30 basis points for fee)
(x + x_in_fee) (y - y_out) = k

y_out = y - k /  (x + x_in_fee) 
y_out = y - x * y / (x + x_in_fee)
y_out = y * x_in_fee / (x + x_in_fee)     (formula given in `getInputPrice`) 

Note that because x_in_fee is *smaller* than x_in, the actual invariant k given by product of the two reserves is greater than before the swap.  The invariant increases after each swap due to fees.

https://github.com/Uniswap/uniswap-v1/blob/master/contracts/uniswap_exchange.vy

### Balancer



### Curve



## Traditional vs Crypto

## Bonding Curves
https://kauri.io/an-introduction-to-bonding-curves/f7853f4914bd42b6bee19758a97b42ee/a
https://medium.com/coinmonks/token-bonding-curves-explained-7a9332198e0e
https://yos.io/2018/11/10/bonding-curves/#:~:text=%E2%80%93%20The%20Bancor%20whitepaper.,sell%20against%20the%20AMM%20contract.

## Decentralization

## Impermanent Loss

## Front-running
