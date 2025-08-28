
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Gold Trading Martingale Calculator - คำนวณ Lot Size และ DD% สำหรับการเทรดทองคำ">
    <meta name="author" content="DR FX BOT BUILDERS">
    <title>Gold Martingale Calculator</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            background-color: #1a1a1a;
            color: #fff;
            padding: 20px;
            min-height: 100vh;
        }
        
        .container {
            background-color: #2a2a2a;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
            max-width: 1200px;
            margin: 0 auto;
        }
        
        h1 {
            text-align: center;
            color: #ffd700;
            margin-bottom: 10px;
        }
        
        .credit {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .credit a {
            color: #4267B2;
            text-decoration: none;
            font-weight: bold;
            transition: color 0.3s ease;
        }
        
        .credit a:hover {
            color: #5a7bc2;
        }
        
        .input-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            color: #ffd700;
            font-weight: 500;
        }
        
        input {
            width: 100%;
            padding: 12px;
            border: 1px solid #444;
            border-radius: 5px;
            background-color: #333;
            color: #fff;
            font-size: 16px;
            transition: border-color 0.3s ease;
        }
        
        input:focus {
            outline: none;
            border-color: #ffd700;
        }
        
        button {
            width: 100%;
            padding: 15px;
            background-color: #ffd700;
            color: #000;
            border: none;
            border-radius: 5px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 20px;
            transition: background-color 0.3s ease;
        }
        
        button:hover {
            background-color: #ffed4e;
        }
        
        button:active {
            transform: translateY(1px);
        }
        
        .results {
            margin-top: 30px;
            animation: fadeIn 0.5s ease-in;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            overflow-x: auto;
            display: block;
        }
        
        th, td {
            border: 1px solid #444;
            padding: 12px;
            text-align: center;
            white-space: nowrap;
        }
        
        th {
            background-color: #ffd700;
            color: #000;
            font-weight: bold;
            position: sticky;
            top: 0;
            z-index: 10;
        }
        
        tr {
            background-color: #1e3a5f; /* สีกรมเข้ม */
        }
        
        tr:nth-child(even) {
            background-color: #2c4a6d; /* สีกรมอ่อนกว่าสำหรับแถวคู่ */
        }
        
        tr:hover {
            background-color: #3a5a8a; /* สีกรมอ่อนเมื่อ hover */
        }
        
        .summary {
            background-color: #333;
            padding: 25px;
            border-radius: 8px;
            margin-top: 20px;
            border: 1px solid #444;
        }
        
        .summary h3 {
            color: #ffd700;
            margin-bottom: 15px;
        }
        
        .summary h4 {
            color: #ffd700;
            margin-top: 20px;
            margin-bottom: 10px;
        }
        
        .warning {
            color: #ff6b6b;
            font-weight: bold;
        }
        
        .success {
            color: #4ecdc4;
            font-weight: bold;
        }
        
        .info {
            color: #ffd93d;
            font-weight: bold;
        }
        
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 20px;
        }
        
        .summary ul {
            list-style-position: inside;
            margin-left: 10px;
        }
        
        .summary li {
            margin-bottom: 8px;
            line-height: 1.6;
        }
        
        .summary hr {
            border: none;
            border-top: 1px solid #555;
            margin: 20px 0;
        }
        
        .summary p {
            margin-bottom: 10px;
            line-height: 1.6;
        }

        /* Mobile responsive */
        @media (max-width: 768px) {
            .container {
                padding: 20px;
            }
            
            h1 {
                font-size: 24px;
            }
            
            .grid {
                grid-template-columns: 1fr;
            }
            
            table {
                font-size: 14px;
            }
            
            th, td {
                padding: 8px;
            }
        }

        /* Loading indicator */
        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid #333;
            border-radius: 50%;
            border-top-color: #ffd700;
            animation: spin 1s ease-in-out infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🥇 Gold Trading Martingale Calculator</h1>
        <p class="credit">
            <a href="https://www.facebook.com/DRFXBotBuilders" target="_blank" rel="noopener noreferrer">
                📘 DR FX BOT BUILDERS 📘
            </a>
        </p>
        
        <form id="calculatorForm">
            <div class="grid">
                <div class="input-group">
                    <label for="balance">ต้นทุน (Balance) $:</label>
                    <input type="number" id="balance" value="1000" step="100" min="100" required>
                </div>
                
                <div class="input-group">
                    <label for="initialLot">Lot เริ่มต้น:</label>
                    <input type="number" id="initialLot" value="0.01" step="0.01" min="0.01" max="10" required>
                </div>
                
                <div class="input-group">
                    <label for="multiplier">Lot Martingale (ตัวคูณ):</label>
                    <input type="number" id="multiplier" value="1.2" step="0.1" min="1" max="10" required>
                </div>
                
                <div class="input-group">
                    <label for="distance">ระยะเปิดไม้ (Point):</label>
                    <input type="number" id="distance" value="400" step="10" min="50" max="2000" required>
                </div>
                
                <div class="input-group">
                    <label for="maxDD">DD% สูงสุดที่รับได้:</label>
                    <input type="number" id="maxDD" value="30" step="5" min="5" max="100" required>
                </div>
                
                <div class="input-group">
                    <label for="targetProfit1">เป้าหมายกำไรที่ 1 (%):</label>
                    <input type="number" id="targetProfit1" value="10" step="1" min="1" max="100" required>
                </div>
                
                <div class="input-group">
                    <label for="targetProfit2">เป้าหมายกำไรที่ 2 (%):</label>
                    <input type="number" id="targetProfit2" value="20" step="1" min="1" max="200" required>
                </div>
                
                <div class="input-group">
                    <label for="targetProfit3">เป้าหมายกำไรที่ 3 (%):</label>
                    <input type="number" id="targetProfit3" value="30" step="1" min="1" max="300" required>
                </div>
                

            </div>
            
            <button type="submit">คำนวณ</button>
        </form>
        
        <div class="results" id="results"></div>
    </div>

    <script>
        // Prevent form submission and calculate
        document.getElementById('calculatorForm').addEventListener('submit', function(e) {
            e.preventDefault();
            calculate();
        });

        function calculate() {
            const balance = parseFloat(document.getElementById('balance').value);
            const initialLot = parseFloat(document.getElementById('initialLot').value);
            const multiplier = parseFloat(document.getElementById('multiplier').value);
            const distance = parseFloat(document.getElementById('distance').value);
            const maxDD = parseFloat(document.getElementById('maxDD').value);
            const targetProfit1 = parseFloat(document.getElementById('targetProfit1').value);
            const targetProfit2 = parseFloat(document.getElementById('targetProfit2').value);
            const targetProfit3 = parseFloat(document.getElementById('targetProfit3').value);

            
            // Validate inputs
            if (!balance || !initialLot || !multiplier || !distance || !maxDD) {
                alert('กรุณากรอกข้อมูลให้ครบถ้วน');
                return;
            }
            
            let html = '<h2>ผลการคำนวณ</h2>';
            html += '<div style="overflow-x: auto;">';
            html += '<table>';
            html += '<thead>';
            html += '<tr>';
            html += '<th>ไม้ที่</th>';
            html += '<th>Lot Size</th>';
            html += '<th>ระยะห่าง (Point)</th>';
            html += '<th>ขาดทุนสะสม ($)</th>';
            html += '<th>DD%</th>';
            html += '<th>จุดคุ้มทุน (Point)</th>';
            html += '</tr>';
            html += '</thead>';
            html += '<tbody>';
            
            let currentLot = initialLot;
            let realLot = initialLot;
            let totalLoss = 0;
            let totalLots = 0;
            let orderCount = 0;
            let maxOrders = 0;
            let tableRows = [];
            let orders = [];
            
            let continueCalculation = true;
            let maxDDReached = false;
            
            while (continueCalculation && orderCount < 50) {
                orderCount++;
                
                // Round lot size to 2 decimal places
                currentLot = Math.round(realLot * 100) / 100;
                if (currentLot < 0.01) currentLot = 0.01;
                
                orders.push({
                    lot: currentLot,
                    openAt: (orderCount - 1) * distance
                });
                
                let currentDistance = (orderCount - 1) * distance;
                
                // Calculate cumulative loss
                let tempTotalLoss = 0;
                for (let i = 0; i < orders.length; i++) {
                    let pipsMove = currentDistance - orders[i].openAt;
                    if (pipsMove > 0) {
                        let drawdown = orders[i].lot * pipsMove * 1;
                        tempTotalLoss += drawdown;
                    }
                }
                
                let tempTotalLots = 0;
                for (let i = 0; i < orders.length; i++) {
                    tempTotalLots += orders[i].lot;
                }
                
                let ddPercent = (tempTotalLoss / balance) * 100;
                
                let breakevenPips = 0;
                if (tempTotalLots > 0 && tempTotalLoss > 0) {
                    breakevenPips = Math.ceil(tempTotalLoss / tempTotalLots);
                }
                
                let rowData = {
                    order: orderCount,
                    lot: currentLot.toFixed(2),
                    totalDistance: currentDistance,
                    totalLoss: Math.round(tempTotalLoss).toFixed(0),
                    dd: ddPercent.toFixed(2),
                    breakeven: breakevenPips,
                    overLimit: ddPercent > maxDD
                };
                
                tableRows.push(rowData);
                
                if (!maxDDReached && ddPercent <= maxDD) {
                    totalLoss = tempTotalLoss;
                    totalLots = tempTotalLots;
                    maxOrders = orderCount;
                } else if (!maxDDReached) {
                    maxDDReached = true;
                }
                
                realLot = realLot * multiplier;
                
                if (currentLot > 100 || (maxDDReached && orderCount > maxOrders + 5)) {
                    continueCalculation = false;
                }
            }
            
            // Build table rows
            tableRows.forEach(row => {
                let rowStyle = '';
                if (row.overLimit) {
                    rowStyle = 'style="background-color: #8b0000;"'; // สีแดงเข้มสำหรับแถวที่เกิน DD
                }
                html += '<tr ' + rowStyle + '>';
                html += '<td>' + row.order + '</td>';
                html += '<td>' + row.lot + '</td>';
                html += '<td>' + row.totalDistance + '</td>';
                html += '<td class="warning">-' + row.totalLoss + '</td>';
                html += '<td class="' + (parseFloat(row.dd) > maxDD ? 'warning' : (parseFloat(row.dd) > 20 ? 'warning' : 'info')) + '">' + row.dd + '%' + (row.overLimit ? ' ⚠️' : '') + '</td>';
                html += '<td class="success">' + row.breakeven + '</td>';
                html += '</tr>';
            });
            
            html += '</tbody>';
            html += '</table>';
            html += '</div>';
            
            // Calculate summary values
            let actualMaxLoss = totalLoss;
            let actualDDPercent = (actualMaxLoss / balance) * 100;
            
            let tpToBreakeven = Math.ceil(actualMaxLoss / (totalLots * 1));
            let tpForProfit1 = Math.ceil((actualMaxLoss + balance * (targetProfit1 / 100)) / (totalLots * 1));
            let tpForProfit2 = Math.ceil((actualMaxLoss + balance * (targetProfit2 / 100)) / (totalLots * 1));
            let tpForProfit3 = Math.ceil((actualMaxLoss + balance * (targetProfit3 / 100)) / (totalLots * 1));
            
            
            // Summary section
            html += '<div class="summary">';
            html += '<h3>สรุปผลการคำนวณ</h3>';
            html += '<p>💰 ต้นทุน: <span class="info">$' + balance.toFixed(2) + '</span></p>';
            html += '<p>📊 จำนวนไม้สูงสุดที่แนะนำ: <span class="success">' + maxOrders + ' ไม้</span> (ภายใน DD ' + maxDD + '%)</p>';
            html += '<p>📈 Lot รวมทั้งหมด (' + maxOrders + ' ไม้): <span class="info">' + totalLots.toFixed(2) + ' lot</span></p>';
            
            let lossAmount = Math.round(actualMaxLoss);
            let lossPercent = actualDDPercent.toFixed(2);
            html += '<p>📉 ขาดทุนสูงสุด: <span class="warning">-$' + lossAmount + ' (' + lossPercent + '%)</span></p>';
            
            if (tableRows.length > maxOrders) {
                html += '<hr>';
                html += '<p class="warning">⚠️ ไม้ที่แสดงพื้นหลังสีแดงเข้มคือไม้ที่เกิน DD% ที่กำหนด</p>';
            }
            
            html += '<hr>';
            html += '<h4>🎯 เป้าหมาย Take Profit แนะนำ:</h4>';
            html += '<p class="info">คำนวณจาก DD% สูงสุดที่รับได้ (' + maxDD + '%) และ Lot รวมทั้งหมด ' + maxOrders + ' ไม้</p>';
            html += '<p>• <strong>จุดคุ้มทุน (Point)</strong> = ระยะที่ราคาต้อง<strong>ย้อนกลับ</strong>จากจุดที่โดนลาก เพื่อให้ได้กำไรรวม = 0</p>';
            html += '<p>• คืนทุน: <span class="info">' + tpToBreakeven + ' pips</span> (กำไร = $0)</p>';
            html += '<p>• กำไร ' + targetProfit1 + '%: <span class="success">' + tpForProfit1 + ' pips</span> (กำไร = $' + (balance * (targetProfit1 / 100)).toFixed(2) + ')</p>';
            html += '<p>• กำไร ' + targetProfit2 + '%: <span class="success">' + tpForProfit2 + ' pips</span> (กำไร = $' + (balance * (targetProfit2 / 100)).toFixed(2) + ')</p>';
            html += '<p>• กำไร ' + targetProfit3 + '%: <span class="success">' + tpForProfit3 + ' pips</span> (กำไร = $' + (balance * (targetProfit3 / 100)).toFixed(2) + ')</p>';
            
            
            html += '<hr>';
            html += '<h4>⚠️ คำเตือน:</h4>';
            html += '<ul>';         
            html += '<li>ต้องมี Free Margin เพียงพอสำหรับเปิดทุกไม้</li>';
            html += '<li>แนะนำให้ใช้ ATR Filter หลีกเลี่ยงช่วงตลาดผันผวน</li>';
            html += '<li>TP ที่คำนวณเป็นเพียงข้อแนะนำ ควรปรับตามสภาพตลาด</li>';
            html += '<li>การลงทุนมีความเสี่ยง ผู้ลงทุนควรศึกษาและทำความเข้าใจความเสี่ยงที่อาจเกิดขึ้นได้ ก่อนตัดสินใจลงทุน</li>';
            html += '</ul>';
            html += '</div>';
            
            document.getElementById('results').innerHTML = html;
        }
        
        // Calculate automatically when page loads
        window.onload = function() {
            calculate();
        }
    </script>
</body>
</html>
