<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gold Martingale Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1a1a1a;
            color: #fff;
            padding: 20px;
            max-width: 1200px;
            margin: 0 auto;
        }
        
        .container {
            background-color: #2a2a2a;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
        }
        
        h1 {
            text-align: center;
            color: #ffd700;
        }
        
        .input-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            color: #ffd700;
        }
        
        input {
            width: 100%;
            padding: 10px;
            border: 1px solid #444;
            border-radius: 5px;
            background-color: #333;
            color: #fff;
            font-size: 16px;
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
        }
        
        button:hover {
            background-color: #ffed4e;
        }
        
        .results {
            margin-top: 30px;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        
        th, td {
            border: 1px solid #444;
            padding: 10px;
            text-align: center;
        }
        
        th {
            background-color: #ffd700;
            color: #000;
        }
        
        tr:nth-child(even) {
            background-color: #333;
        }
        
        .summary {
            background-color: #333;
            padding: 20px;
            border-radius: 5px;
            margin-top: 20px;
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
        }
        
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🥇 Gold Trading Martingale Calculator</h1>
        <p style="text-align: center; margin-top: -10px;">
            <a href="https://www.facebook.com/DRFXBotBuilders" target="_blank" style="color: #4267B2; text-decoration: none;">
                📘 DR FX BOT BUILDERS  📘
            </a>
        </p>
        
        <div class="grid">
            <div class="input-group">
                <label for="balance">ต้นทุน (Balance) $:</label>
                <input type="number" id="balance" value="1000" step="100">
            </div>
            
            <div class="input-group">
                <label for="initialLot">Lot เริ่มต้น:</label>
                <input type="number" id="initialLot" value="0.01" step="0.01">
            </div>
            
            <div class="input-group">
                <label for="multiplier">Lot Martingale (ตัวคูณ):</label>
                <input type="number" id="multiplier" value="1.2" step="0.1">
            </div>
            
            <div class="input-group">
                <label for="distance">ระยะเปิดไม้ (Pips):</label>
                <input type="number" id="distance" value="400" step="10">
            </div>
            
            <div class="input-group">
                <label for="maxDD">DD% สูงสุดที่รับได้:</label>
                <input type="number" id="maxDD" value="30" step="5">
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
        
        <button onclick="calculate()">คำนวณ</button>
        
        <div class="results" id="results"></div>
    </div>

    <script>
        function calculate() {
            const balance = parseFloat(document.getElementById('balance').value);
            const initialLot = parseFloat(document.getElementById('initialLot').value);
            const multiplier = parseFloat(document.getElementById('multiplier').value);
            const distance = parseFloat(document.getElementById('distance').value);
            const targetProfit1 = parseFloat(document.getElementById('targetProfit1').value);
            const targetProfit2 = parseFloat(document.getElementById('targetProfit2').value);
            const targetProfit3 = parseFloat(document.getElementById('targetProfit3').value);
            const maxDD = parseFloat(document.getElementById('maxDD').value);
            
            let html = '<h2>ผลการคำนวณ</h2>';
            html += '<table>';
            html += '<tr>';
            html += '<th>ไม้ที่</th>';
            html += '<th>Lot Size</th>';
            html += '<th>ระยะห่าง (Pips)</th>';
            html += '<th>ขาดทุนสะสม ($)</th>';
            html += '<th>DD%</th>';
            html += '<th>จุดคุ้มทุน (Pips)</th>';
            html += '</tr>';
            
            let currentLot = initialLot;
            let realLot = initialLot; // เก็บค่า lot จริงก่อนปัดเศษ
            let totalLoss = 0;
            let totalLots = 0;
            let orderCount = 0;
            let maxOrders = 0;
            let tableRows = [];
            let orders = []; // เก็บข้อมูลออเดอร์ทั้งหมด
            
            // คำนวณไม้ต่างๆ
            let continueCalculation = true;
            let maxDDReached = false;
            
            while (continueCalculation && orderCount < 50) { // จำกัดไว้ที่ 50 ไม้
                orderCount++;
                
                // ปัดเศษ lot ให้เป็นทศนิยม 2 ตำแหน่ง
                currentLot = Math.round(realLot * 100) / 100;
                if (currentLot < 0.01) currentLot = 0.01;
                
                // เก็บข้อมูลออเดอร์
                orders.push({
                    lot: currentLot,
                    openAt: (orderCount - 1) * distance // ไม้ที่ 1 เปิดที่ 0, ไม้ที่ 2 เปิดที่ 400, ...
                });
                
                // ระยะห่างจากจุดเริ่มต้นถึงไม้ปัจจุบัน
                let currentDistance = (orderCount - 1) * distance;
                
                // คำนวณขาดทุนสะสมทั้งหมด
                let tempTotalLoss = 0;
                for (let i = 0; i < orders.length; i++) {
                    // แต่ละไม้ขาดทุน = lot × ระยะที่ราคาเคลื่อนจากจุดที่เปิดไม้นั้น
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
                
                // คำนวณ DD%
                let ddPercent = (tempTotalLoss / balance) * 100;
                
                // คำนวณจุดคุ้มทุน
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
                
                // ถ้ายังไม่เกิน DD ให้อัพเดทค่าจริง
                if (!maxDDReached && ddPercent <= maxDD) {
                    totalLoss = tempTotalLoss;
                    totalLots = tempTotalLots;
                    maxOrders = orderCount;
                } else if (!maxDDReached) {
                    maxDDReached = true;
                }
                
                // คำนวณ lot ถัดไปแบบไม่ปัดเศษ
                realLot = realLot * multiplier;
                
                // หยุดถ้า lot มากเกิน 100 หรือแสดงไม้เกิน DD แล้ว 5 ไม้
                if (currentLot > 100 || (maxDDReached && orderCount > maxOrders + 5)) {
                    continueCalculation = false;
                }
            }
            
            // แสดงตาราง
            tableRows.forEach(row => {
                let rowClass = row.overLimit ? 'style="background-color: #4a1a1a;"' : '';
                html += '<tr ' + rowClass + '>';
                html += '<td>' + row.order + '</td>';
                html += '<td>' + row.lot + '</td>';
                html += '<td>' + row.totalDistance + '</td>';
                html += '<td class="warning">-' + row.totalLoss + '</td>';
                html += '<td class="' + (parseFloat(row.dd) > maxDD ? 'warning' : (parseFloat(row.dd) > 20 ? 'warning' : 'info')) + '">' + row.dd + '%' + (row.overLimit ? ' ⚠️' : '') + '</td>';
                html += '<td class="success">' + row.breakeven + '</td>';
                html += '</tr>';
            });
            
            html += '</table>';
            
            // คำนวณค่าต่างๆ สำหรับสรุป
            let actualMaxLoss = totalLoss;
            let actualDDPercent = (actualMaxLoss / balance) * 100;
            
            // คำนวณ TP ที่ควรตั้ง
            let tpToBreakeven = Math.ceil(actualMaxLoss / (totalLots * 1));
            let tpForProfit1 = Math.ceil((actualMaxLoss + balance * (targetProfit1 / 100)) / (totalLots * 1));
            let tpForProfit2 = Math.ceil((actualMaxLoss + balance * (targetProfit2 / 100)) / (totalLots * 1));
            let tpForProfit3 = Math.ceil((actualMaxLoss + balance * (targetProfit3 / 100)) / (totalLots * 1));
            
            // สรุปผล
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
	        html += '<p>•  จุดคุ้มทุน (Pips)</strong> = ระยะที่ราคาต้อง<strong>ย้อนกลับ</strong>จากจุดที่โดนลาก เพื่อให้ได้กำไรรวม = 0</p>';
            html += '<p>• คืนทุน: <span class="info">' + tpToBreakeven + ' pips</span> (กำไร = $0)</p>';
            html += '<p>• กำไร ' + targetProfit1 + '%: <span class="success">' + tpForProfit1 + ' pips</span> (กำไร = $' + (balance * (targetProfit1 / 100)).toFixed(2) + ')</p>';
            html += '<p>• กำไร ' + targetProfit2 + '%: <span class="success">' + tpForProfit2 + ' pips</span> (กำไร = $' + (balance * (targetProfit2 / 100)).toFixed(2) + ')</p>';
            html += '<p>• กำไร ' + targetProfit3 + '%: <span class="success">' + tpForProfit3 + ' pips</span> (กำไร = $' + (balance * (targetProfit3 / 100)).toFixed(2) + ')</p>';
 
            
            // คำเตือน
            html += '<hr>';
            html += '<h4>⚠️ คำเตือน:</h4>';
            html += '<ul>';         
            html += '<li>ต้องมี Free Margin เพียงพอสำหรับเปิดทุกไม้</li>';
            html += '<li>แนะนำให้ใช้ ATR Filter หลีกเลี่ยงช่วงตลาดผันผวน</li>';
            html += '<li>TP ที่คำนวณเป็นเพียงข้อแนะนำ ควรปรับตามสภาพตลาด</li>';
	        html += '<li>การลงทุนมีความเสี่ยง ผู้ลงทุนควรศึกษาและทำความเข้าใจความเสี่ยงที่อาจเกิดขึ้นได้ ก่อนตัดสินใจลงทุน </li>';
            html += '</ul>';
            html += '</div>';
            
            document.getElementById('results').innerHTML = html;
        }
        
        // คำนวณอัตโนมัติเมื่อโหลดหน้า
        window.onload = function() {
            calculate();
        }
    </script>
</body>
</html>
