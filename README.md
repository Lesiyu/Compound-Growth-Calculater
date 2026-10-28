<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>滾雪球｜定期定額複利計算器</title>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            /* 奶茶與奶白暖色調系 */
            --bg-main: #FBF9F5;           /* 柔和奶白背景 */
            --bg-card: #FFFFFF;           /* 純白卡片 */
            --bg-subtle: #F4EFE6;         /* 燕麥奶底色 */
            --border-color: #E6DEC9;     /* 淡奶茶邊框 */
            
            --primary: #B58A63;           /* 經典奶茶主色 */
            --primary-hover: #9C724C;     /* 深奶茶色 */
            --primary-light: #F0E6D8;     /* 淺奶茶色 */
            
            --accent-green: #8A9A86;      /* 莫蘭迪綠（代表獲利成長） */
            --text-main: #4A3E3D;         /* 深暖灰棕文字 */
            --text-muted: #8C7E7A;        /* 淺棕灰文字 */
            
            --shadow-sm: 0 4px 20px rgba(181, 138, 99, 0.06);
            --shadow-md: 0 8px 30px rgba(181, 138, 99, 0.1);
            --radius-lg: 20px;
            --radius-md: 12px;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Plus Jakarta Sans', 'Noto Sans TC', sans-serif;
            color: var(--text-main);
        }

        body {
            background-color: var(--bg-main);
            min-height: 100vh;
            padding: 2rem 1rem;
            display: flex;
            justify-content: center;
            align-items: flex-start;
        }

        .container {
            max-width: 1200px;
            width: 100%;
            display: flex;
            flex-direction: column;
            gap: 2rem;
        }

        header {
            text-align: center;
            margin-bottom: 1rem;
        }

        header h1 {
            font-size: 2.2rem;
            font-weight: 700;
            color: var(--primary-hover);
            letter-spacing: -0.5px;
            margin-bottom: 0.5rem;
        }

        header p {
            color: var(--text-muted);
            font-size: 1rem;
        }

        .app-layout {
            display: grid;
            grid-template-columns: 380px 1fr;
            gap: 2rem;
        }

        @media (max-width: 900px) {
            .app-layout {
                grid-template-columns: 1fr;
            }
        }

        .card {
            background: var(--bg-card);
            border-radius: var(--radius-lg);
            padding: 1.8rem;
            box-shadow: var(--shadow-sm);
            border: 1px solid var(--border-color);
        }

        .preset-buttons {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 0.5rem;
            margin-bottom: 1.5rem;
        }

        .btn-preset {
            background: var(--bg-subtle);
            border: 1px solid transparent;
            padding: 0.6rem 0.4rem;
            border-radius: var(--radius-md);
            font-size: 0.85rem;
            font-weight: 500;
            color: var(--text-main);
            cursor: pointer;
            transition: all 0.2s ease;
            text-align: center;
        }

        .btn-preset:hover {
            background: var(--primary-light);
            color: var(--primary-hover);
        }

        .input-group {
            margin-bottom: 1.4rem;
        }

        .input-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 0.5rem;
        }

        .input-header label {
            font-size: 0.95rem;
            font-weight: 600;
            color: var(--text-main);
        }

        .number-input-wrapper {
            display: flex;
            align-items: center;
            background: var(--bg-main);
            border: 1.5px solid var(--border-color);
            border-radius: var(--radius-md);
            padding: 0.4rem 0.8rem;
            transition: border-color 0.2s ease;
        }

        .number-input-wrapper:focus-within {
            border-color: var(--primary);
        }

        .number-input-wrapper span {
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--text-muted);
            margin-right: 0.3rem;
        }

        .number-input-wrapper input {
            width: 100%;
            border: none;
            background: transparent;
            font-size: 1rem;
            font-weight: 700;
            color: var(--primary-hover);
            text-align: right;
            outline: none;
        }

        /* 奶茶色客製化 Range 滑桿 */
        input[type=range] {
            -webkit-appearance: none;
            width: 100%;
            margin-top: 0.6rem;
            background: transparent;
        }

        input[type=range]:focus {
            outline: none;
        }

        input[type=range]::-webkit-slider-runnable-track {
            width: 100%;
            height: 6px;
            cursor: pointer;
            background: var(--bg-subtle);
            border-radius: 3px;
        }

        input[type=range]::-webkit-slider-thumb {
            height: 18px;
            width: 18px;
            border-radius: 50%;
            background: var(--primary);
            cursor: pointer;
            -webkit-appearance: none;
            margin-top: -6px;
            box-shadow: 0 2px 6px rgba(181, 138, 99, 0.3);
            transition: transform 0.1s ease;
        }

        input[type=range]::-webkit-slider-thumb:hover {
            transform: scale(1.15);
            background: var(--primary-hover);
        }

        .summary-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 1rem;
            margin-bottom: 1.5rem;
        }

        .summary-card {
            background: var(--bg-subtle);
            border-radius: var(--radius-md);
            padding: 1.2rem;
            border: 1px solid rgba(230, 222, 201, 0.5);
        }

        .summary-card .year-title {
            font-size: 0.85rem;
            font-weight: 600;
            color: var(--text-muted);
            margin-bottom: 0.3rem;
        }

        .summary-card .amount {
            font-size: 1.4rem;
            font-weight: 700;
            color: var(--primary-hover);
            margin-bottom: 0.3rem;
        }

        .summary-card .profit {
            font-size: 0.85rem;
            color: var(--accent-green);
            font-weight: 600;
        }

        .chart-wrapper {
            position: relative;
            height: 340px;
            width: 100%;
            margin-top: 1rem;
        }

        .table-section {
            margin-top: 1.5rem;
        }

        .table-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1rem;
        }

        .btn-export {
            background: var(--bg-subtle);
            border: 1px solid var(--border-color);
            padding: 0.5rem 1rem;
            border-radius: var(--radius-md);
            font-size: 0.85rem;
            font-weight: 600;
            color: var(--text-main);
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .btn-export:hover {
            background: var(--primary-light);
            color: var(--primary-hover);
        }

        .table-container {
            overflow-x: auto;
            max-height: 380px;
            overflow-y: auto;
            border-radius: var(--radius-md);
            border: 1px solid var(--border-color);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: right;
            font-size: 0.9rem;
        }

        th {
            background: var(--bg-subtle);
            color: var(--text-muted);
            font-weight: 600;
            padding: 0.8rem 1rem;
            position: sticky;
            top: 0;
            z-index: 10;
        }

        td {
            padding: 0.75rem 1rem;
            border-bottom: 1px solid var(--border-color);
        }

        tr:nth-child(even) {
            background-color: #FCFBF9;
        }

        tr:hover {
            background-color: var(--primary-light);
        }

        th:first-child, td:first-child {
            text-align: center;
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>滾雪球｜複利成長計算器</h1>
        <p>規劃你的資產藍圖，感受時間與複利的累積力量</p>
    </header>

    <div class="app-layout">
        <!-- 輸入參數區域 -->
        <div class="card">
            <div class="preset-buttons">
                <button class="btn-preset" onclick="applyPreset(100000, 10000, 0, 6, 30)">穩健小資 (6%)</button>
                <button class="btn-preset" onclick="applyPreset(500000, 20000, 50000, 8, 30)">積極成長 (8%)</button>
                <button class="btn-preset" onclick="applyPreset(1000000, 30000, 100000, 10, 30)">火力全開 (10%)</button>
            </div>

            <!-- 1. 初始本金 -->
            <div class="input-group">
                <div class="input-header">
                    <label for="initialCapital">初始本金</label>
                </div>
                <div class="number-input-wrapper">
                    <span>$</span>
                    <input type="number" id="initialCapital" value="100000" step="1000">
                </div>
                <input type="range" id="initialCapitalRange" min="0" max="10000000" step="1000" value="100000">
            </div>

            <!-- 2. 每月定期定額 (步長 100) -->
            <div class="input-group">
                <div class="input-header">
                    <label for="monthlyInvestment">每月定期定額</label>
                </div>
                <div class="number-input-wrapper">
                    <span>$</span>
                    <input type="number" id="monthlyInvestment" value="10000" step="100">
                </div>
                <input type="range" id="monthlyInvestmentRange" min="0" max="500000" step="100" value="10000">
            </div>

            <!-- 3. 每年追加投入 (步長 500) -->
            <div class="input-group">
                <div class="input-header">
                    <label for="annualAddition">每年單次追加</label>
                </div>
                <div class="number-input-wrapper">
                    <span>$</span>
                    <input type="number" id="annualAddition" value="0" step="500">
                </div>
                <input type="range" id="annualAdditionRange" min="0" max="1000000" step="500" value="0">
            </div>

            <!-- 4. 預期年報酬率 (%) -->
            <div class="input-group">
                <div class="input-header">
                    <label for="annualReturn">預期年報酬率</label>
                </div>
                <div class="number-input-wrapper">
                    <span>%</span>
                    <input type="number" id="annualReturn" value="7.0" step="0.1" min="0" max="30">
                </div>
                <input type="range" id="annualReturnRange" min="0" max="30" step="0.1" value="7.0">
            </div>

            <!-- 5. 投資年限 -->
            <div class="input-group">
                <div class="input-header">
                    <label for="years">投資年限 (年)</label>
                </div>
                <div class="number-input-wrapper">
                    <span>年</span>
                    <input type="number" id="years" value="30" min="1" max="50">
                </div>
                <input type="range" id="yearsRange" min="1" max="50" step="1" value="30">
            </div>
        </div>

        <!-- 圖表與摘要區域 -->
        <div class="card">
            <div class="summary-grid">
                <div class="summary-card">
                    <div class="year-title">5 年後總資產</div>
                    <div class="amount" id="sum5">-$</div>
                    <div class="profit" id="profit5">獲利: -$</div>
                </div>
                <div class="summary-card">
                    <div class="year-title">10 年後總資產</div>
                    <div class="amount" id="sum10">-$</div>
                    <div class="profit" id="profit10">獲利: -$</div>
                </div>
                <div class="summary-card">
                    <div class="year-title">20 年後總資產</div>
                    <div class="amount" id="sum20">-$</div>
                    <div class="profit" id="profit20">獲利: -$</div>
                </div>
                <div class="summary-card">
                    <div class="year-title">30 年後總資產</div>
                    <div class="amount" id="sum30">-$</div>
                    <div class="profit" id="profit30">獲利: -$</div>
                </div>
            </div>

            <div class="chart-wrapper">
                <canvas id="growthChart"></canvas>
            </div>
        </div>
    </div>

    <!-- 數據表格區域 -->
    <div class="card table-section">
        <div class="table-header">
            <h3>逐年明細報表</h3>
            <button class="btn-export" onclick="exportCSV()">匯出 CSV 報表</button>
        </div>
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>年份</th>
                        <th>當年投入本金</th>
                        <th>累積投入本金</th>
                        <th>當年獲得利息</th>
                        <th>累積總資產</th>
                    </tr>
                </thead>
                <tbody id="resultTableBody">
                    <!-- JavaScript 動態生成 -->
                </tbody>
            </table>
        </div>
    </div>
</div>

<script>
    let chartInstance = null;
    let calculatedData = [];

    // DOM 元素繫結
    const inputs = [
        { num: 'initialCapital', range: 'initialCapitalRange' },
        { num: 'monthlyInvestment', range: 'monthlyInvestmentRange' },
        { num: 'annualAddition', range: 'annualAdditionRange' },
        { num: 'annualReturn', range: 'annualReturnRange' },
        { num: 'years', range: 'yearsRange' }
    ];

    inputs.forEach(item => {
        const numEl = document.getElementById(item.num);
        const rangeEl = document.getElementById(item.range);

        numEl.addEventListener('input', () => {
            rangeEl.value = numEl.value;
            calculate();
        });

        rangeEl.addEventListener('input', () => {
            numEl.value = rangeEl.value;
            calculate();
        });
    });

    function formatCurrency(val) {
        return new Intl.NumberFormat('zh-TW', { style: 'currency', currency: 'TWD', maximumFractionDigits: 0 }).format(val);
    }

    function applyPreset(initial, monthly, annual, rate, yrs) {
        document.getElementById('initialCapital').value = initial;
        document.getElementById('initialCapitalRange').value = initial;
        document.getElementById('monthlyInvestment').value = monthly;
        document.getElementById('monthlyInvestmentRange').value = monthly;
        document.getElementById('annualAddition').value = annual;
        document.getElementById('annualAdditionRange').value = annual;
        document.getElementById('annualReturn').value = rate;
        document.getElementById('annualReturnRange').value = rate;
        document.getElementById('years').value = yrs;
        document.getElementById('yearsRange').value = yrs;
        calculate();
    }

    function calculate() {
        const initialCapital = parseFloat(document.getElementById('initialCapital').value) || 0;
        const monthlyInvestment = parseFloat(document.getElementById('monthlyInvestment').value) || 0;
        const annualAddition = parseFloat(document.getElementById('annualAddition').value) || 0;
        const annualReturnRate = (parseFloat(document.getElementById('annualReturn').value) || 0) / 100;
        const years = parseInt(document.getElementById('years').value) || 1;

        const monthlyRate = annualReturnRate / 12;
        
        let totalBalance = initialCapital;
        let totalPrincipal = initialCapital;
        calculatedData = [];

        for (let year = 1; year <= years; year++) {
            let yearlyPrincipalAdded = (monthlyInvestment * 12) + annualAddition;
            let startBalanceYear = totalBalance;

            // 每月滾利算法
            for (let month = 1; month <= 12; month++) {
                totalBalance = (totalBalance + monthlyInvestment) * (1 + monthlyRate);
            }
            // 年底單次追加
            totalBalance += annualAddition;
            totalPrincipal += yearlyPrincipalAdded;

            let yearlyInterest = totalBalance - startBalanceYear - yearlyPrincipalAdded;
            let cumulativeInterest = totalBalance - totalPrincipal;

            calculatedData.push({
                year: year,
                yearlyContribution: yearlyPrincipalAdded,
                totalPrincipal: totalPrincipal,
                yearlyInterest: yearlyInterest,
                totalBalance: totalBalance,
                cumulativeInterest: cumulativeInterest
            });
        }

        updateSummaryCards(years);
        renderTable();
        renderChart();
    }

    function updateSummaryCards(maxYears) {
        const targets = [5, 10, 20, 30];
        targets.forEach(yr => {
            const sumEl = document.getElementById(`sum${yr}`);
            const profitEl = document.getElementById(`profit${yr}`);

            if (yr <= maxYears) {
                const data = calculatedData[yr - 1];
                sumEl.innerText = formatCurrency(data.totalBalance);
                profitEl.innerText = `獲利: ${formatCurrency(data.cumulativeInterest)}`;
            } else {
                sumEl.innerText = '-';
                profitEl.innerText = '超出設定年限';
            }
        });
    }

    function renderTable() {
        const tbody = document.getElementById('resultTableBody');
        tbody.innerHTML = '';

        calculatedData.forEach(row => {
            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td>第 ${row.year} 年</td>
                <td>${formatCurrency(row.yearlyContribution)}</td>
                <td>${formatCurrency(row.totalPrincipal)}</td>
                <td style="color: var(--accent-green); font-weight: 600;">+${formatCurrency(row.yearlyInterest)}</td>
                <td style="font-weight: 700; color: var(--primary-hover);">${formatCurrency(row.totalBalance)}</td>
            `;
            tbody.appendChild(tr);
        });
    }

    function renderChart() {
        const ctx = document.getElementById('growthChart').getContext('2d');
        const labels = calculatedData.map(d => `第 ${d.year} 年`);
        const principalData = calculatedData.map(d => Math.round(d.totalPrincipal));
        const interestData = calculatedData.map(d => Math.round(d.cumulativeInterest));

        if (chartInstance) {
            chartInstance.destroy();
        }

        chartInstance = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: labels,
                datasets: [
                    {
                        label: '累積本金',
                        data: principalData,
                        backgroundColor: '#E6DEC9',
                        borderRadius: 4
                    },
                    {
                        label: '複利獲利',
                        data: interestData,
                        backgroundColor: '#8A9A86',
                        borderRadius: 4
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    tooltip: {
                        mode: 'index',
                        intersect: false,
                        callbacks: {
                            label: function(context) {
                                return ` ${context.dataset.label}: ${formatCurrency(context.raw)}`;
                            }
                        }
                    },
                    legend: {
                        position: 'top',
                        labels: {
                            font: { family: 'Plus Jakarta Sans', size: 12 }
                        }
                    }
                },
                scales: {
                    x: {
                        stacked: true,
                        grid: { display: false }
                    },
                    y: {
                        stacked: true,
                        ticks: {
                            callback: function(value) {
                                return '$' + (value / 10000) + '萬';
                            }
                        }
                    }
                }
            }
        });
    }

    function exportCSV() {
        let csvContent = "data:text/csv;charset=utf-8,\uFEFF";
        csvContent += "年份,當年投入本金,累積投入本金,當年收益,累積總資產\n";

        calculatedData.forEach(row => {
            csvContent += `${row.year},${row.yearlyContribution},${row.totalPrincipal},${Math.round(row.yearlyInterest)},${Math.round(row.totalBalance)}\n`;
        });

        const encodedUri = encodeURI(csvContent);
        const link = document.createElement("a");
        link.setAttribute("href", encodedUri);
        link.setAttribute("download", "複利試算結果.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }

    // 初始化執行
    calculate();
</script>
</body>
</html>
