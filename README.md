<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>完整期貨試算器 - NQ/MNQ/GC/MGC</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #000;
            color: #fff;
            text-align: center;
            padding: 20px;
        }
        .calculator {
            max-width: 500px;
            margin: 0 auto;
            background: #111;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
        }
        .calculator input, .calculator select, .calculator button {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: none;
            border-radius: 5px;
        }
        .calculator input {
            background: #222;
            color: #fff;
        }
        .calculator button {
            background: #d4af37;
            color: #000;
            font-weight: bold;
            cursor: pointer;
        }
        .calculator button:hover {
            background: #c89f32;
        }
        .output {
            margin-top: 20px;
            font-size: 18px;
        }
        .instructions {
            margin-bottom: 20px;
            text-align: left;
            font-size: 14px;
            line-height: 1.6;
            background: #222;
            padding: 15px;
            border-radius: 10px;
        }
        .title {
            color: #d4af37; /* 金色 */
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <div class="calculator">
        <h1 class="title">完整期貨試算器 - NQ/MNQ/GC/MGC</h1>
        <div class="instructions">
            <h2>使用說明</h2>
            <p>1. 選擇您要交易的商品，例如 NQ、MNQ、GC 或 MGC。</p>
            <p>2. 輸入進場價格和停損價格，或者輸入獲利/虧損點數，系統會自動計算相關數據。</p>
            <p>3. 若未填寫某些欄位（例如停損價格或點數），系統將根據已輸入的數據進行推算。</p>
            <p>4. 輸入合約數量，系統將幫助您計算損失金額及獲利金額。</p>
            <p>5. 按下「計算」按鈕，即可查看結果。</p>
            <p>6. 每點的美元價值（如 NQ 每點 20 美元）會顯示在結果中，方便您了解風險與報酬。</p>
        </div>
        <div>
            <label for="product">選擇商品</label>
            <select id="product">
                <option value="NQ">NQ (納斯達克期貨)</option>
                <option value="MNQ">MNQ (迷你納斯達克期貨)</option>
                <option value="GC">GC (黃金期貨)</option>
                <option value="MGC">MGC (迷你黃金期貨)</option>
            </select>
        </div>
        <div>
            <label for="entry-price">進場價格</label>
            <input type="number" id="entry-price" placeholder="輸入進場價格 (選填)">
        </div>
        <div>
            <label for="stoploss-price">停損價格</label>
            <input type="number" id="stoploss-price" placeholder="輸入停損價格 (選填)">
        </div>
        <div>
            <label for="profit-points">獲利點數</label>
            <input type="number" id="profit-points" placeholder="輸入獲利點數 (選填)">
        </div>
        <div>
            <label for="profit-price">獲利價格</label>
            <input type="number" id="profit-price" placeholder="輸入獲利價格 (選填)">
        </div>
        <div>
            <label for="loss-points">虧損點數</label>
            <input type="number" id="loss-points" placeholder="輸入虧損點數 (選填)">
        </div>
        <div>
            <label for="contracts">合約數量</label>
            <input type="number" id="contracts" value="1" min="1">
        </div>
        <button onclick="calculate()">計算</button>
        <div class="output" id="output"></div>
    </div>
    <script>
        const productDetails = {
            NQ: { pointValue: 20, tickSize: 0.25 },
            MNQ: { pointValue: 2, tickSize: 0.25 },
            GC: { pointValue: 100, tickSize: 0.1 },
            MGC: { pointValue: 10, tickSize: 0.1 }
        };

        function calculate() {
            const product = document.getElementById('product').value;
            const entryPrice = parseFloat(document.getElementById('entry-price').value);
            const stoplossPrice = parseFloat(document.getElementById('stoploss-price').value);
            const profitPoints = parseFloat(document.getElementById('profit-points').value);
            const profitPriceInput = parseFloat(document.getElementById('profit-price').value);
            const lossPoints = parseFloat(document.getElementById('loss-points').value);
            const contracts = parseInt(document.getElementById('contracts').value);

            if (isNaN(contracts) || contracts <= 0) {
                document.getElementById('output').innerText = "請填寫合約數量並確保數值正確。";
                return;
            }

            const { pointValue } = productDetails[product];

            // 初始化計算結果
            let calculatedStoplossPrice = stoplossPrice;
            let calculatedProfitPrice = profitPriceInput;
            let calculatedProfitPoints = profitPoints;
            let calculatedLossPoints = lossPoints;

            // 根據輸入數據進行推算
            if (!isNaN(entryPrice)) {
                if (isNaN(stoplossPrice) && !isNaN(lossPoints)) {
                    calculatedStoplossPrice = entryPrice - lossPoints;
                }
                if (isNaN(profitPriceInput) && !isNaN(profitPoints)) {
                    calculatedProfitPrice = entryPrice + profitPoints;
                }
                if (isNaN(profitPoints)) {
                    if (!isNaN(profitPriceInput)) {
                        calculatedProfitPoints = profitPriceInput - entryPrice;
                    }
                }
                if (isNaN(lossPoints)) {
                    if (!isNaN(stoplossPrice)) {
                        calculatedLossPoints = entryPrice - stoplossPrice;
                    }
                }
            }

            // 計算損失金額
            const lossAmount = !isNaN(calculatedLossPoints)
                ? Math.abs(calculatedLossPoints) * pointValue * contracts
                : 0;

            // 計算獲利金額
            const profitAmount = !isNaN(calculatedProfitPoints)
                ? Math.abs(calculatedProfitPoints) * pointValue * contracts
                : 0;

            // 顯示每點的美元價值
            const pointValuePerContract = pointValue;

            // 顯示結果
            document.getElementById('output').innerHTML = `
                <p>商品: ${product}</p>
                <p>進場價格: ${entryPrice ? entryPrice.toFixed(2) : "未提供"}</p>
                <p>停損價格: ${calculatedStoplossPrice ? calculatedStoplossPrice.toFixed(2) : "未提供"}</p>
                <p>獲利價格: ${calculatedProfitPrice ? calculatedProfitPrice.toFixed(2) : "未提供"}</p>
                <p>獲利點數: ${calculatedProfitPoints ? calculatedProfitPoints.toFixed(2) : "未提供"}</p>
                <p>虧損點數: ${calculatedLossPoints ? calculatedLossPoints.toFixed(2) : "未提供"}</p>
                <p>1 點等於: ${pointValuePerContract} USD</p>
                <p>損失金額: ${lossAmount.toFixed(2)} USD</p>
                <p>獲利金額: ${profitAmount.toFixed(2)} USD</p>
            `;
        }
    </script>
</body>
</html>
