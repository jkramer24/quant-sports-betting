# quant-sports-betting

Overview

This project applies quantitative finance methodology to sports betting markets. The core idea is simple: betting lines are prices set by a market, and like any market, they can be mispriced. I built two independent spread prediction models, one for NBA games and one for NFL games, to identify situations where the betting market systematically undervalues certain teams or game conditions.
The project follows the same workflow used in systematic trading: signal generation, backtesting on out-of-sample data, and portfolio construction across uncorrelated strategies.

Motivation

Sports betting markets are an interesting testing ground for quant methods because they share many properties with financial markets. Lines move based on information flow, sharp money moves prices, and inefficiencies exist in specific situations that the market is slow to price. Unlike equities, the payout structure is fixed and the outcome is binary, which makes backtesting clean and interpretable.
I chose this project because it let me apply real quant concepts, including rolling features, out-of-sample testing, model selection, and portfolio correlation, to a domain with clear and verifiable results.


NBA Spread Model

Approach

I pulled game-by-game data from the NBA Stats API covering the 2020-21 through 2024-25 seasons. For each game I built features using only information available before tip-off, with no same-game stats that would leak future information.
I tested 10 model iterations using XGBoost, systematically adding and removing features to find the combination with the best out-of-sample betting performance. Key finding: simpler models consistently outperformed more complex ones, which is consistent with the overfitting problem in noisy prediction tasks.

Features (V7 Model)

Rolling 10-game offensive and defensive averages for both teams
Days of rest for both teams
Home court strength, measured as each team's historical margin at home vs on the road
Travel fatigue, measured as the time zone difference between home and away team cities

Results (2024-25 Season Backtest)

Bets placed: 725 (games where model disagreed with Vegas by 7+ points)
Win rate: 54.1% (breakeven is 52.4%)
Profit: $2,570 on $100 flat bets
Signal threshold: 7 points

The model beats the breakeven threshold consistently enough across months to suggest genuine edge rather than luck. The weakest months, October and November, align with early season when rolling averages have limited historical data to draw from.


NFL Spread Model

Approach

I used the Spreadspoke NFL dataset covering the 2016 through 2024-25 seasons. NFL has fewer games per season than NBA so I trained on more historical seasons to compensate. I applied the same systematic feature testing approach as the NBA model.

Features

Rolling 5-game offensive and defensive averages for both teams
Days of rest and rest advantage
Win rate over last 5 games to capture momentum
Game temperature and wind speed
Home field strength

Results (2024-25 NFL Season Backtest)

Bets placed: 223 (games where model disagreed with Vegas by 3+ points)
Win rate: 54.3%
Profit: $880 on $100 flat bets
Signal threshold: 3 points

Weather turned out to be a meaningful feature unique to NFL. Wind speed and temperature contribute real signal that has no NBA equivalent.

Portfolio Combination

The two strategies were backtested on overlapping time periods, from fall 2024 through spring 2025, allowing for a direct correlation analysis.

Weekly P&L correlation: -0.425
Combined profit: $3,450

The negative correlation is the most interesting finding. When the NBA model has a losing week, the NFL model tends to have a winning week and vice versa. This is the ideal property for combining strategies, not just diversification but active hedging. The combined equity curve is noticeably smoother than either strategy alone.

Tech Stack

Python (pandas, numpy, XGBoost, scikit-learn, matplotlib)
NBA Stats API via nba_api
Spreadspoke NFL dataset
Kaggle NBA betting dataset (2008-2025)

Model Selection Philosophy

A consistent finding across both sports was that adding features beyond a certain point hurt out-of-sample performance even when it improved in-sample accuracy. This is a classic overfitting problem. The final models use the minimum feature set that captures real signal, covering team quality, rest, home field advantage, and situational factors Vegas is slow to price.

Future Work

Retrain both models on 2025-26 season data when available, expected July 2026
Build a live pipeline that pulls tonight's games, calculates features, and compares against current DraftKings lines to generate real-time signals
Explore moneyline and totals markets with more complete historical data
