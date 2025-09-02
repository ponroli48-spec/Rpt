<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crypto Risk Calculator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0d1117;
            color: #d1d5db;
        }
        .container {
            max-width: 800px;
            margin: auto;
            padding: 1rem;
        }
        .calculator-card {
            background-color: #161b22;
            border-radius: 1rem;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            padding: 2rem;
        }
        .input-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 1.5rem;
        }
        @media (min-width: 768px) {
            .input-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }
        .input-group label {
            display: block;
            margin-bottom: 0.5rem;
            font-weight: 500;
            color: #a0aec0;
        }
        .input-group input, .input-group select {
            width: 100%;
            padding: 0.75rem;
            border: 1px solid #30363d;
            background-color: #0d1117;
            color: #ffffff;
            border-radius: 0.5rem;
            transition: all 0.2s;
        }
        .input-group input:focus, .input-group select:focus {
            outline: none;
            border-color: #58a6ff;
            box-shadow: 0 0 0 3px rgba(88, 166, 255, 0.5);
        }
        .result-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 1rem;
        }
        @media (min-width: 768px) {
            .result-grid {
                grid-template-columns: repeat(3, 1fr);
            }
        }
        .result-box {
            background-color: #21262d;
            border: 1px solid #30363d;
            padding: 1.5rem;
            border-radius: 0.5rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            text-align: center;
        }
        .result-label {
            font-weight: 500;
            color: #a0aec0;
            margin-bottom: 0.5rem;
        }
        .result-value {
            font-weight: 700;
            font-size: 1.5rem;
            word-wrap: break-word;
        }
        .value-green {
            color: #34d399;
        }
        .value-red {
            color: #f87171;
        }
        .value-blue {
            color: #58a6ff;
        }
        .value-yellow {
            color: #eab308;
        }
    </style>
</head>
<body class="bg-gray-950 text-gray-200">
    <div class="container mx-auto">
        <div class="calculator-card">
            <h1 class="text-3xl font-bold text-center mb-2">Crypto Trading Calculator</h1>
            <p class="text-center text-sm text-gray-400 mb-8">Calculate risk, position size, and R/R for your trades.</p>

            <div id="messageBox" class="bg-red-800 text-red-200 p-4 rounded-lg hidden mb-6 transition-all duration-300"></div>

            <div class="input-grid">
                <div class="input-group">
                    <label for="balance">Balance (USD)</label>
                    <input type="number" id="balance" oninput="updateResults()" class="mt-1" placeholder="e.g., 1000" required>
                </div>
                <div class="input-group">
                    <label for="riskPerTrade">Risk per Trade (%)</label>
                    <input type="number" id="riskPerTrade" oninput="updateResults()" class="mt-1" placeholder="e.g., 1" required>
                </div>
                <div class="input-group">
                    <label for="leverage">Leverage (x)</label>
                    <input type="number" id="leverage" oninput="updateResults()" class="mt-1" placeholder="e.g., 10" required>
                </div>
                <div class="input-group">
                    <label for="direction">Direction</label>
                    <select id="direction" onchange="updateResults()" class="mt-1 p-3 rounded-md">
                        <option value="long">Long</option>
                        <option value="short">Short</option>
                    </select>
                </div>
                <div class="input-group">
                    <label for="entryPrice">Entry Price (USD)</label>
                    <input type="number" id="entryPrice" oninput="updateResults()" class="mt-1" placeholder="e.g., 20000" required>
                </div>
                <div class="input-group">
                    <label for="stopLossPrice">Stop Loss Price (USD)</label>
                    <input type="number" id="stopLossPrice" oninput="updateResults()" class="mt-1" placeholder="e.g., 19000" required>
                </div>
                <div class="input-group">
                    <label for="takeProfitPrice">Take Profit Price (USD)</label>
                    <input type="number" id="takeProfitPrice" oninput="updateResults()" class="mt-1" placeholder="e.g., 22000" required>
                </div>
            </div>

            <h2 class="text-2xl font-bold text-center mt-8 mb-4">Calculated Results</h2>
            <div id="results" class="hidden result-grid">
                <div class="result-box">
                    <div class="result-label">Leveraged TP ROI</div>
                    <div id="tpRoiLeveraged" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">TP ROI</div>
                    <div id="tpRoiNonLeveraged" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Max Loss</div>
                    <div id="maxLoss" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Leveraged SL %</div>
                    <div id="slPercentLeveraged" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Max SL %</div>
                    <div id="slPercentNonLeveraged" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Margin Cost (Estimated)</div>
                    <div id="maxMargin" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Order Value</div>
                    <div id="orderValue" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Take Profit Amount</div>
                    <div id="takeProfitAmount" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Stop Loss Amount</div>
                    <div id="stopLossAmount" class="result-value"></div>
                </div>
                <div class="result-box">
                    <div class="result-label">Risk/Reward Ratio</div>
                    <div id="rrRatio" class="result-value"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        function showMessage(message) {
            const messageBox = document.getElementById('messageBox');
            messageBox.textContent = message;
            messageBox.classList.remove('hidden');
        }

        function updateResults() {
            const balance = parseFloat(document.getElementById('balance').value);
            const riskPerTrade = parseFloat(document.getElementById('riskPerTrade').value);
            const leverage = parseFloat(document.getElementById('leverage').value);
            const direction = document.getElementById('direction').value;
            const entryPrice = parseFloat(document.getElementById('entryPrice').value);
            const stopLossPrice = parseFloat(document.getElementById('stopLossPrice').value);
            const takeProfitPrice = parseFloat(document.getElementById('takeProfitPrice').value);

            const resultsDiv = document.getElementById('results');
            resultsDiv.classList.add('hidden');
            document.getElementById('messageBox').classList.add('hidden');

            if (isNaN(balance) || isNaN(riskPerTrade) || isNaN(leverage) || isNaN(entryPrice) || isNaN(stopLossPrice) || isNaN(takeProfitPrice)) {
                return;
            }

            if (balance <= 0 || riskPerTrade <= 0 || leverage <= 0 || entryPrice <= 0 || stopLossPrice <= 0 || takeProfitPrice <= 0) {
                showMessage("All values must be greater than zero.");
                return;
            }

            let slPercent, tpPercent;
            if (direction === 'long') {
                if (stopLossPrice >= entryPrice || takeProfitPrice <= entryPrice) {
                    showMessage("For a LONG position, SL must be lower than Entry and TP must be higher than Entry.");
                    return;
                }
                slPercent = ((entryPrice - stopLossPrice) / entryPrice) * 100;
                tpPercent = ((takeProfitPrice - entryPrice) / entryPrice) * 100;
            } else {
                if (stopLossPrice <= entryPrice || takeProfitPrice >= entryPrice) {
                    showMessage("For a SHORT position, SL must be higher than Entry and TP must be lower than Entry.");
                    return;
                }
                slPercent = ((stopLossPrice - entryPrice) / entryPrice) * 100;
                tpPercent = ((entryPrice - takeProfitPrice) / entryPrice) * 100;
            }

            if (slPercent === 0) {
                showMessage("Entry price cannot be the same as stop loss price.");
                return;
            }

            const maxLoss = balance * (riskPerTrade / 100);
            const orderValue = maxLoss / (slPercent / 100);
            const marginCost = orderValue / leverage;
            const rrRatio = (tpPercent / slPercent);
            
            const tpRoiWithLeverage = tpPercent * leverage;
            const slPercentWithLeverage = slPercent * leverage;

            const takeProfitAmount = orderValue * (tpPercent / 100);
            const stopLossAmount = orderValue * (slPercent / 100);

            document.getElementById('tpRoiLeveraged').textContent = `${tpRoiWithLeverage.toFixed(2)}%`;
            document.getElementById('tpRoiLeveraged').classList.add('value-green');
            
            document.getElementById('tpRoiNonLeveraged').textContent = `${tpPercent.toFixed(2)}%`;
            document.getElementById('tpRoiNonLeveraged').classList.add('value-green');

            document.getElementById('slPercentLeveraged').textContent = `${slPercentWithLeverage.toFixed(2)}%`;
            document.getElementById('slPercentLeveraged').classList.add('value-red');

            document.getElementById('slPercentNonLeveraged').textContent = `${slPercent.toFixed(2)}%`;
            document.getElementById('slPercentNonLeveraged').classList.add('value-red');

            document.getElementById('maxLoss').textContent = `${maxLoss.toFixed(2)} USD`;
            document.getElementById('maxLoss').classList.add('value-red');

            document.getElementById('maxMargin').textContent = `${marginCost.toFixed(2)} USD`;
            document.getElementById('maxMargin').classList.add('value-blue');

            document.getElementById('orderValue').textContent = `${orderValue.toFixed(2)} USD`;
            document.getElementById('orderValue').classList.add('value-blue');
            
            document.getElementById('takeProfitAmount').textContent = `${takeProfitAmount.toFixed(2)} USD`;
            document.getElementById('takeProfitAmount').classList.add('value-green');

            document.getElementById('stopLossAmount').textContent = `${stopLossAmount.toFixed(2)} USD`;
            document.getElementById('stopLossAmount').classList.add('value-red');

            document.getElementById('rrRatio').textContent = rrRatio.toFixed(2);
            document.getElementById('rrRatio').classList.add('value-yellow');

            resultsDiv.classList.remove('hidden');
        }
    </script>
</body>
</html>
