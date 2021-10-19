---
title: ShapeShift Yield Farming
author: Package Build
date: '2021-10-18'
slug: shapeshift-yield-farming
categories:
  - DeFi
tags:
  - Yield Farming
description: ''
draft: yes
---

### Intro ###

What is DeFi and Yield Farming?  DeFi stands for Decentralized Finance and it represents financial services such as lending and borrowing that are provided over the blockchain.  The *decentralized* part means that instead of having a bank stand between the lender and the borrower, taking a large cut, a protocal (some code and a blockchain) is the intermediary instead.  The protocal still takes a cut, but it is much smaller than in TradFi (Traditional Finance).

I got started in Crypto about a year ago and up on recently, I was only using centralized exchanges to earn interest on my crypto holdings.  Why are holders of crypto able to earning interest?  The same way banks do, by lending it out to people who want to borrow  it.

### Why Borrow ###

* Traders need to borrow cyrpto so they can arbitrage the price differences on different exchanges.  There are literally hunders of different exchanges to buy and sell crypto and they don't necessarily offer the same prices for the same token.  Arbitraguers can simultaneously buy and sell the same token on different exchanges and collect the difference as profit.  These artitrageurs provide a benefit by keeping the prices of different exchanges similar and adding liquidity to the exchanges. 

* Long-term holders of crypto sometimes bororow against their holdings if they need liquidity.  If you sell some of your crypto, you have to pay taxes on the gains and you loose out of future price appreciation potential.   Instead, some people choose to borrow against their own crypto so they can spend some of the money without having to sell it.  These loans are usually well over-collaterlized, which means that the risk is small and thus the interest rate is fairly low.  They still hold the same amount of cyrpto as before, so if the price goes up, they benefit.

### Liquidity Pools and Yield Farming ###

Today I am going to give you my experience yield farming on ShapeShift[https://shapeshift.com/].  ShapeShift has a governance token called FOX.  In order to provide liquidity for their token, ShapeShift created a pool where liquidity providers can deposit an equal dollar amount of two tokens and earn interest for doing so.  In this case the two tokens are ETH and FOX.  When you deposit the pair you earn a deposit yield of around one percent.  One percent is decent compared to a bank account, but given the extra risk associated with crypto, it may not be worth it by itselfl.   The reason people get excited about DeFi is because most platforms that issue tokens provide additional incentives to persuade people to stake liquidity for their token, often called *farming* rewards.  In this case the *farming* rewards are over 100% APY.  The *farming* rewards are always paid in the native token (FOX tokens in this case) which ShapeShift issues from their treasury, so that explains how they are able to fund these rewards.

### Details ###

Now for the details.  Since this was my first time staking, I decided to keep it small, say $1000.  I already had some ETH that I had transfered into ShapeShift, so I used some to purchase $500 worth of Fox tokens, which amount to about 1700 FOX at the time of purchase.  ShapeShift has a *Primary* tab and an *Earn* tab.  The *Primary* tab is your wallet and shows how many tokens you hold and how much they are worth.  The *Earn* tab is where you can stake liquidity.  I deposited 0.134 ETH and 1699 FOX and the app converted that pair into Liquidity Pool (LP) tokens.  In my case 13.73 LP tokens worth around $1000.  As I mentioned, the deposit yield APY is around 1% (paid in FOX tokens) and the *farming* rewards are an additional 100%+ APY, but the incentive period is less than a year, so you won't earning the bonus rate for long enough to actually double your money.  

Actually what prompted me to write up this example is that the prior incentive period just ended in early October and a new one just started.  Unfortunately, the staking doesn't automatically switch to the new pool.  Instead, you have to unstake from the old pool that is no longer paying *farming* rewards and stake with the new pool.  That entails two separate Ethereum transactions.  If you have been following crypto at all, you have probably heard people complain about the Ethereum gas fees.  Gas fees are the fees paid to miners to process transactions.  The miners are the important people who keep the (Ethereum) blockchain going and they do deserve to be compensated.  It is not the miner's fault that the gas fees are high.  The reason is because there are more transaction that are being sent to the blockchain than it can handle, and thus the high gas fees are used to priorize the transactions.

The following two screen shots show how much the gas fees can very during the same day.

![SS_unstake_low](/img/SS_unstake_low.png) 
![SS_unstake_high](/img/SS_unstake_high.png) 

As you can see, the gas fees for a staking or unstaking transaction can be anywhere from $50 to $600 depending on the amount of congestion at a particular time of day.  The best times to do Ethereum transactions is between midnight and 6 am Eastern time (when most people are asleep).  I think I was able to get the unstaking transaction done for around $40 by waiting until 3am.  As you can see at the bottom of the screen shot I earned about $222 worth of rewards, so since I had to perform three separate Ethereum transactions to deposite the pair, stake, and unstake, I probably lost half of my earnings to gas fees.

Even though the Ethereum gas fees are high, since I already have the 13 LPs, I did decie to re-stake them into the new ShapeShift pool.  That transaction cost me about $38, as shown in the following screen shot:

![SS_unstake_high](/img/SS_stake.png)

To be fair, I only entered the prior stake pool two months ago, so my stake didn't have very much time to earn rewards.  This time the new incentive stake pool lasts until February 2021, so I should be able to earn a lot more this time around.  In all DeFi can be a fun wat learn more about crypto and earn some money, but the high Ethereum gas fees do take a lot of the fun away.  In the next few days I will post about DeFi options on different chains with much lower gas fees.

### Disclaimer ###

These posts are for informational and educational purposes only and should not be construed as investment advice or recommendations.  In this series I am just outlining what I am doing to learn more about the cyrpto space.  Just because I am doing this, that does not mean these activies are suitable for you.  Please do your own research and stay skeptical.  These markets are not normal.
