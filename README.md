<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>โปรแกรมคำนวณภาษี</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f4f4f9;
        }
        .container {
            max-width: 500px;
            margin: 50px auto;
            background-color: #ffffff;
            padding: 20px;
            border: 2px solid #4CAF50;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }
        h1 {
            text-align: center;
            color: #4CAF50;
        }
        label {
            font-weight: bold;
        }
        input {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        button {
            width: 100%;
            padding: 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        .result {
            margin-top: 20px;
            padding: 10px;
            background-color: #e7f3e7;
            border: 1px solid #4CAF50;
            border-radius: 5px;
            color: #2e7d32;
            font-weight: bold;
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>โปรแกรมคำนวณภาษี</h1>
        <label for="income">รายได้สุทธิต่อปี (บาท):</label>
        <input type="number" id="income" placeholder="กรอกจำนวนรายได้สุทธิ">

        <label for="spouse">มีคู่สมรสหรือไม่?</label>
        <select id="spouse">
            <option value="0">ไม่มี</option>
            <option value="60000">มี</option>
        </select>

        <label for="children">จำนวนบุตรที่ลดหย่อนได้:</label>
        <input type="number" id="children" placeholder="จำนวนบุตร" min="0" value="0">

        <label for="socialSecurity">ประกันสังคมที่จ่าย (สูงสุด 9,000 บาท):</label>
        <input type="number" id="socialSecurity" placeholder="กรอกจำนวนเงิน" min="0" value="0">

        <label for="fund">เงินกองทุนสำรองเลี้ยงชีพ / ประกันชีวิต:</label>
        <input type="number" id="fund" placeholder="กรอกจำนวนเงิน" min="0" value="0">

        <button onclick="calculateTax()">คำนวณภาษี</button>
        <div class="result" id="result"></div>
    </div>

    <script>
        function calculateTax() {
            let income = parseFloat(document.getElementById("income").value);
            let spouseDeduction = parseFloat(document.getElementById("spouse").value);
            let children = parseInt(document.getElementById("children").value) * 30000;
            let socialSecurity = Math.min(parseFloat(document.getElementById("socialSecurity").value) || 0, 9000);
            let fund = parseFloat(document.getElementById("fund").value) || 0;

            if (isNaN(income) || income < 0) {
                document.getElementById("result").innerText = "กรุณากรอกจำนวนรายได้ที่ถูกต้อง!";
                return;
            }

            // ค่าลดหย่อน
            let baseDeduction = 60000; // ค่าลดหย่อนส่วนตัว
            let totalDeductions = baseDeduction + spouseDeduction + children + socialSecurity + fund;
            let taxableIncome = Math.max(income - totalDeductions, 0);

            let tax = 0;
            const taxBrackets = [
                { limit: 5000000, rate: 0.35 },
                { limit: 2000000, rate: 0.30 },
                { limit: 1000000, rate: 0.25 },
                { limit: 750000, rate: 0.20 },
                { limit: 500000, rate: 0.15 },
                { limit: 300000, rate: 0.10 },
                { limit: 150000, rate: 0.05 },
                { limit: 0, rate: 0.00 }
            ];

            let remainingIncome = taxableIncome;
            for (let bracket of taxBrackets) {
                if (remainingIncome > bracket.limit) {
                    tax += (remainingIncome - bracket.limit) * bracket.rate;
                    remainingIncome = bracket.limit;
                }
            }

            document.getElementById("result").innerText = 
                `รายได้สุทธิ: ${income.toLocaleString()} บาท\n` +
                `ค่าลดหย่อนรวม: ${totalDeductions.toLocaleString()} บาท\n` +
                `รายได้ที่ต้องเสียภาษี: ${taxableIncome.toLocaleString()} บาท\n` +
                `ภาษีที่ต้องชำระ: ${tax.toFixed(2)} บาท`;
        }
    </script>
</body>
</html>