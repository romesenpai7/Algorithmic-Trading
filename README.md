# Algorithmic-Trading (Jerome S. Agas)

import MetaTrader5 as mt5
from datetime import datetime
import pandas as pd
import numpy as np
import pandas_ta as ta

# Function to calculate technical indicators
def calculate_indicators(prices, rsi_period, stoch_period, adx_period):
    # Calculate RSI
    rsi_signal = prices.ta.rsi(length=rsi_period)

    # Calculate Stochastic Oscillator
    stoch_signal = prices.ta.stoch(high=prices['high'], low=prices['low'], close=prices['close'], fastk_period=stoch_period, slowk_period=3, slowd_period=3)

    # Calculate ADX
    adx_signal = prices.ta.adx(length=adx_period)

    return rsi_signal, stoch_signal, adx_signal


# Function to execute trading strategy and calculate performance metrics
def execute_strategy(prices, rsi_period, stoch_period, adx_period):
    # Calculate technical indicators
    rsi_signal, stoch_signal, adx_signal = calculate_indicators(prices, rsi_period, stoch_period, adx_period)
    
    # Placeholder for strategy execution and performance metrics calculation
    # Your trading strategy implementation here

    # Example: Placeholder for calculating performance metrics
    maximal_drawdown_equity = 0.1  # Example value for maximal drawdown equity
    maximal_drawdown_balance = 0.15  # Example value for maximal drawdown balance
    recovery_factor = 2.5  # Example value for recovery factor
    num_trades = 100  # Example value for number of trades
    total_lots = 500  # Example value for total lots
    total_win = 60  # Example value for total win
    total_lose = 40  # Example value for total lose
    net_profit = total_win - total_lose  # Example value for net profit
    win_rate = total_win / num_trades  # Example value for win rate

    return maximal_drawdown_equity, maximal_drawdown_balance, recovery_factor, num_trades, total_lots, total_win, total_lose, net_profit, win_rate


# Define optimization parameter ranges
rsi_period_range = range(7, 15)
stoch_period_range = range(7, 15)
adx_period_range = range(20, 31)

# Initialize MetaTrader 5
mt5.initialize()

# Define symbol, timeframe, and date range
symbol = 'EURUSD'
timeframe = mt5.TIMEFRAME_M5
date_from = datetime(2024, 1, 1)
date_to = datetime.now()

# Get historical price data
prices = pd.DataFrame(mt5.copy_rates_range(symbol, timeframe, date_from, date_to))
prices['time'] = pd.to_datetime(prices['time'], unit='s')


# Optimization loop
results = []

for rsi_period in rsi_period_range:
    for stoch_period in stoch_period_range:
        for adx_period in adx_period_range:
            # Execute strategy and calculate performance metrics
            maximal_drawdown_equity, maximal_drawdown_balance, recovery_factor, num_trades, total_lots, total_win, total_lose, net_profit, win_rate = execute_strategy(prices, rsi_period, stoch_period, adx_period)

            # Check optimization constraints
            if recovery_factor >= 2 and maximal_drawdown_equity <= 20 and recovery_factor >= 2:
                results.append((maximal_drawdown_equity, maximal_drawdown_balance, recovery_factor, num_trades, total_lots, total_win, total_lose, net_profit, win_rate, rsi_period, stoch_period, adx_period))


# Print top 50 results with performance metrics
top_results = sorted(results, reverse=True)[:50]
for i, result in enumerate(top_results, start=1):
    print(f"Top {i} result:", result)
    # Retrieve data for specific parameter combination
    rsi_period, stoch_period, adx_period = result[9], result[10], result[11]
    prices = pd.DataFrame(mt5.copy_rates_range(symbol, timeframe, date_from, date_to))
    prices['time'] = pd.to_datetime(prices['time'], unit='s')
    maximal_drawdown_equity, maximal_drawdown_balance, recovery_factor, num_trades, total_lots, total_win, total_lose, net_profit, win_rate = execute_strategy(prices, rsi_period, stoch_period, adx_period)
    print("Maximal Drawdown (Equity):", maximal_drawdown_equity, "\t",
          "Maximal Drawdown (Balance):", maximal_drawdown_balance, "\t",
          "Recovery Factor:", recovery_factor, "\t",
          "Number of Trades:", num_trades, "\t",
          "Total Lots:", total_lots, "\t",
          "Total Win:", total_win, "\t",
          "Total Lose:", total_lose, "\t",
          "Net Profit:", net_profit, "\t",
          "Win Rate:", win_rate)

          
