<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>看護職・介護職の退職金ダッシュボード</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Chosen Palette: Calm Stone/Emerald -->
    <!-- Application Structure Plan: A dashboard-style, tabbed interface. This is more effective for this dense, data-heavy report than a long scrolling page. Tabs: 1. 概要 (Overview): Key stats (75.5% adoption), Doughnut chart for Table 1 (Adoption types), and key takeaways. 2. 勤続年数別・相場 (Pay Scale by Service Length): The core interactive element. A dropdown filter will allow users to select a dataset ("業界全体", "公的病院", "自己都合") which dynamically updates a Chart.js bar chart showing pay scales by service length. This combines three static tables (2A, 2B, 2C) into one dynamic, explorable tool, making comparison far easier for the user. 3. 考察・戦略 (Analysis & Strategy): Presents the qualitative insights from Sections 3 & 4 as clean, readable cards, now including specific compensation data (originally in the external file). This structure separates the high-level summary, the deep-dive data, and the strategic analysis into logical, user-selectable sections. -->
    <!-- Visualization & Content Choices: Table 1 (Adoption %) -> Goal: Inform proportion -> Viz: Doughnut Chart (Chart.js/Canvas) -> Interaction: Tooltips on hover -> Justification: Better than a table for showing parts of a whole (75.5% vs 24.5%). Tables 2A, 2B, 2C (Pay Scales) -> Goal: Compare pay across sectors and tenure -> Viz: Interactive Bar Chart (Chart.js/Canvas) -> Interaction: Dropdown <select> element updates chart data -> Justification: Combines 3 static tables into one dynamic, explorable tool, making comparison far easier for the user. Sections 3 & 4 (Text Analysis) -> Goal: Inform strategic takeaways -> Viz: Styled cards (HTML/Tailwind) -> Justification: Clear, scannable presentation of key findings. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        html { scroll-behavior: smooth; }
        body { font-family: 'Inter', 'Noto Sans JP', sans-serif; }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            height: 40vh;
            max-height: 450px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 350px;
            }
        }
        .tab-button.active {
            border-bottom-color: #059669; /* emerald-600 */
            color: #047857; /* emerald-700 */
            font-weight: 600;
        }
        .tab-button {
            border-bottom-width: 3px;
            border-bottom-color: transparent;
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
    </style>
</head>
<body class="bg-stone-100 text-stone-800">

    <!-- Header -->
    <header class="bg-white shadow-md">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8 py-4">
            <h1 class="text-3xl font-bold text-emerald-700">医療・福祉業界 退職金ダッシュボード</h1>
            <p class="mt-1 text-lg text-stone-600">看護職・介護職の退職金制度導入実態と平均相場</p>
        </div>
    </header>

    <!-- Main Content -->
    <main class="container mx-auto p-4 sm:p-6 lg:p-8">

        <!-- Tab Navigation -->
        <div class="mb-6 border-b border-stone-300">
            <nav class="flex -mb-px space-x-6" aria-label="Tabs">
                <button class="tab-button active py-4 px-1 text-center text-base font-medium text-stone-600 hover:text-emerald-700" data-tab="tab-overview">
                    概要・導入状況
                </button>
                <button class="tab-button py-4 px-1 text-center text-base font-medium text-stone-600 hover:text-emerald-700" data-tab="tab-comparison">
                    退職金相場 比較
                </button>
                <button class="tab-button py-4 px-1 text-center text-base font-medium text-stone-600 hover:text-emerald-700" data-tab="tab-strategy">
                    考察・戦略
                </button>
            </nav>
        </div>

        <!-- Tab Content: Overview -->
        <div id="tab-overview" class="tab-content active">
            <h2 class="text-2xl font-semibold text-stone-800 mb-4">1. 医療・福祉業界の退職金制度導入状況</h2>
            <p class="text-base text-stone-700 mb-6">厚生労働省の統計（令和5年調査）に基づくと、医療・福祉業界の退職給付制度の導入率は全産業平均と同水準です。しかし、その内訳を見ると「退職一時金制度のみ」の割合が非常に高いことが特徴です。</p>
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Doughnut Chart: Adoption Rate -->
                <div class="bg-white p-6 rounded-lg shadow-md border border-stone-200">
                    <h3 class="text-xl font-bold text-stone-800 mb-4">制度導入率（医療・福祉）</h3>
                    <div class="chart-container" style="max-height: 300px;">
                        <canvas id="adoptionChart"></canvas>
                    </div>
                </div>

                <!-- Doughnut Chart: System Type -->
                <div class="bg-white p-6 rounded-lg shadow-md border border-stone-200">
                    <h3 class="text-xl font-bold text-stone-800 mb-4">導入制度の形態（導入企業内訳）</h3>
                    <div class="chart-container" style="max-height: 300px;">
                        <canvas id="systemTypeChart"></canvas>
                    </div>
                </div>
            </div>
            
            <!-- Key Points -->
            <div class="mt-6 grid grid-cols-1 md:grid-cols-2 gap-6">
                <div class="bg-white p-6 rounded-lg shadow-md border border-emerald-300">
                    <h3 class="text-lg font-semibold text-emerald-800 mb-2">ポイント 1: 「一時金のみ」が主流</h3>
                    <p class="text-stone-700">導入企業の約87%が「退職一時金制度のみ」を採用しており、これは全産業平均（約69%）と比べて高い割合です。企業年金を併用している法人はまだ少数派であることがわかります。</p>
                </div>
                <div class="bg-white p-6 rounded-lg shadow-md border border-amber-300">
                    <h3 class="text-lg font-semibold text-amber-800 mb-2">ポイント 2: 人材採用における格差</h3>
                    <p class="text-stone-700">裏を返せば、約4分の1の医療機関・施設では退職金制度自体が導入されていません。制度の有無が、人材採用における福利厚生の大きな差となっている可能性があります。</p>
                </div>
            </div>
        </div>

        <!-- Tab Content: Comparison -->
        <div id="tab-comparison" class="tab-content">
            <h2 class="text-2xl font-semibold text-stone-800 mb-4">2. 職種・勤続年数別の退職金相場</h2>
            <p class="text-base text-stone-700 mb-6">公的機関のデータやモデルケースを基に、退職金相場を比較します。下のドロップダウンから比較したいデータセットを選択してください。金額はあくまで目安です。</p>

            <!-- Filter -->
            <div class="mb-6 max-w-xs">
                <label for="dataset-filter" class="block text-sm font-medium text-stone-700 mb-2">表示するデータを選択:</label>
                <select id="dataset-filter" class="w-full bg-white border border-stone-300 rounded-md shadow-sm p-2 focus:ring-emerald-500 focus:border-emerald-500">
                    <option value="self">自己都合退職（業界平均）</option>
                    <option value="industry">業界全体（大卒・定年等）</option>
                    <option value="public">公的病院モデル（定年等）</option>
                </select>
            </div>

            <!-- Bar Chart -->
            <div class="bg-white p-6 rounded-lg shadow-md border border-stone-200">
                <h3 id="chart-title" class="text-xl font-bold text-stone-800 mb-4">退職金相場（自己都合退職）</h3>
                <div class="chart-container">
                    <canvas id="comparisonChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Tab Content: Strategy -->
        <div id="tab-strategy" class="tab-content">
            <h2 class="text-2xl font-semibold text-stone-800 mb-4">3. 考察・見直しに向けた視点</h2>
            <p class="text-base text-stone-700 mb-6">これらのデータを踏まえ、自院の制度を見直す際の戦略的な視点を整理します。</p>
            
            <div class="space-y-6">
                <!-- NEW SECTION: Specific Compensation Data -->
                <div class="bg-white p-6 rounded-lg shadow-xl border border-red-400">
                    <h3 class="text-xl font-bold text-red-700 mb-3">【参考】公的病院の具体的な退職金水準（看護職など）</h3>
                    <p class="text-sm text-stone-600 mb-4">公的病院（国立病院機構、公立病院など）は、一般的に退職金水準が高く、目標設定の参考になります。（金額はモデルケースに基づく概算です）</p>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="space-y-2">
                            <h4 class="font-semibold text-base text-stone-800 border-b pb-1">国立病院機構（NHO）モデル</h4>
                            <ul class="list-disc list-inside ml-4 text-sm text-stone-700">
                                <li>**勤続10年（自己都合）**: 約160万円</li>
                                <li>**勤続20年（自己都合）**: 約400万円</li>
                                <li>**定年退職（勤続30年）**: 約2,000万円</li>
                            </ul>
                        </div>
                        <div class="space-y-2">
                            <h4 class="font-semibold text-base text-stone-800 border-b pb-1">公立病院モデル（地方公務員準拠）</h4>
                            <ul class="list-disc list-inside ml-4 text-sm text-stone-700">
                                <li>**定年退職（勤続30年）**: 約1,400万円～1,900万円</li>
                                <li>**公立病院（10年勤続）**: 約280万円</li>
                                <li>**日本赤十字病院**：勤続1年から支給、役職により25年で2,250万円超のケースあり</li>
                            </ul>
                        </div>
                    </div>
                </div>

                <!-- Original Strategy Cards -->
                <div class="bg-white p-6 rounded-lg shadow-md border border-stone-200">
                    <h3 class="text-xl font-bold text-stone-800 mb-3">制度の有無と規模による影響</h3>
                    <ul class="list-disc list-inside space-y-2 text-stone-700">
                        <li><strong>大規模病院・公的病院</strong>：退職金水準が高く、DB（確定給付）や企業年金基金を併用するケースも多いため、**ベテラン層の定着**に強い。</li>
                        <li><strong>クリニック・小規模法人</strong>：制度がない、または水準が低い場合があり、**職員の流動性が高まる**一因となり得る。</li>
                        <li><strong>介護職員特有の共済</strong>：社会福祉法人では「社会福祉施設職員等退職手当共済制度」の加入が、**介護職員の採用・定着**における重要な要素となる。</li>
                    </ul>
                </div>

                <!-- Strategy Cards -->
                <div class="bg-white p-6 rounded-lg shadow-md border border-emerald-300">
                    <h3 class="text-xl font-bold text-emerald-800 mb-3">見直しに向けた視点</h3>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div class="border-l-4 border-emerald-500 pl-4">
                            <h4 class="font-semibold text-lg">1. 若手層への対応</h4>
                            <p class="text-stone-700">勤続年数が短い層には、ポータビリティ（持ち運び）が可能な**DC（確定拠出年金）**を導入し、転職時も資産が持てる安心感を提供することで、入職のハードルを下げる。</p>
                        </div>
                        <div class="border-l-4 border-emerald-500 pl-4">
                            <h4 class="font-semibold text-lg">2. ベテラン層の定着</h4>
                            <p class="text-stone-700">**DB（確定給B）**の導入や、既存の**退職一時金制度の計算式を見直し**、勤続10年～20年での退職金水準を競合並みに引き上げ、中核人材の定着を図る。</p>
                        </div>
                        <div class="border-l-4 border-emerald-500 pl-4">
                            <h4 class="font-semibold text-lg">3. 財務リスクの管理</h4>
                            <p class="text-stone-700">将来の退職給付債務（一時金）の支払いを、DB、DC、中退共といった**外部積立制度**へ計画的に移行・分散することで、法人の財務リスクを軽減する。</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </main>

    <footer class="bg-stone-800 text-stone-300 mt-12">
        <div class="container mx-auto px-6 py-8 text-center">
            <p>&copy; 2025 医療・福祉業界 退職金ダッシュボード. データは公的統計およびモデルケースに基づく目安です。</p>
        </div>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            // --- Tab Switching Logic ---
            const tabButtons = document.querySelectorAll('.tab-button');
            const tabContents = document.querySelectorAll('.tab-content');

            tabButtons.forEach(button => {
                button.addEventListener('click', () => {
                    const tabId = button.getAttribute('data-tab');

                    tabButtons.forEach(btn => btn.classList.remove('active'));
                    button.classList.add('active');

                    tabContents.forEach(content => {
                        if (content.id === tabId) {
                            content.classList.add('active');
                        } else {
                            content.classList.remove('active');
                        }
                    });
                });
            });

            // --- Chart.js Logic ---
            
            // 1. Overview Chart 1: Adoption Rate
            const adoptionCtx = document.getElementById('adoptionChart').getContext('2d');
            new Chart(adoptionCtx, {
                type: 'doughnut',
                data: {
                    labels: ['制度あり', '制度なし'],
                    datasets: [{
                        label: '制度導入率',
                        data: [75.5, 24.5],
                        backgroundColor: [
                            'rgba(5, 150, 105, 0.7)', // emerald-600
                            'rgba(231, 229, 228, 0.7)' // stone-200
                        ],
                        borderColor: [
                            'rgba(4, 120, 87, 1)',
                            'rgba(168, 162, 158, 1)'
                        ],
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom' },
                        tooltip: { callbacks: { label: (c) => `${c.label}: ${c.raw}%` } }
                    }
                }
            });

            // 2. Overview Chart 2: System Type
            const systemTypeCtx = document.getElementById('systemTypeChart').getContext('2d');
            new Chart(systemTypeCtx, {
                type: 'doughnut',
                data: {
                    labels: ['一時金のみ', '両制度併用', '年金のみ'],
                    datasets: [{
                        label: '制度の形態',
                        data: [86.9, 11.4, 1.7],
                        backgroundColor: [
                            'rgba(5, 150, 105, 0.7)', // emerald-600
                            'rgba(59, 130, 246, 0.7)', // blue-500
                            'rgba(245, 158, 11, 0.7)' // amber-500
                        ],
                        borderColor: [
                            'rgba(4, 120, 87, 1)',
                            'rgba(37, 99, 235, 1)',
                            'rgba(217, 119, 6, 1)'
                        ],
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom' },
                        tooltip: { callbacks: { label: (c) => `${c.label}: ${c.raw}%` } }
                    }
                }
            });

            // 3. Comparison Bar Chart
            const comparisonCtx = document.getElementById('comparisonChart').getContext('2d');
            const datasets = {
                self: {
                    title: '退職金相場（自己都合退職）',
                    labels: ['5年', '10年', '20年'],
                    data: [106, 220, 488]
                },
                industry: {
                    title: '退職金相場（業界全体・大卒・定年等）',
                    labels: ['10年', '20年+', '20年+ (高卒)'],
                    data: [191, 1235, 994]
                },
                public: {
                    title: '退職金相場（公的病院モデル・定年等）',
                    labels: ['10年 (NHO自己)', '10年 (公立)', '20年 (公立)', '20年 (NHO定年)', '25年+ (日赤)'],
                    data: [160, 280, 800, 1000, 1750] // 日赤は1500-2000のため中値1750
                }
            };
            
            let comparisonChart = new Chart(comparisonCtx, {
                type: 'bar',
                data: {
                    labels: datasets.self.labels,
                    datasets: [{
                        label: '退職金相場 (万円)',
                        data: datasets.self.data,
                        backgroundColor: 'rgba(5, 150, 105, 0.7)',
                        borderColor: 'rgba(4, 120, 87, 1)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: '退職金額 (万円)'
                            }
                        }
                    },
                    plugins: {
                        legend: { display: false },
                        tooltip: {
                            callbacks: {
                                label: (c) => ` ${c.label}: ${c.raw} 万円`
                            }
                        }
                    }
                }
            });

            // Filter logic
            const filter = document.getElementById('dataset-filter');
            const chartTitle = document.getElementById('chart-title');
            
            filter.addEventListener('change', (e) => {
                const selectedValue = e.target.value;
                const newData = datasets[selectedValue];
                
                chartTitle.textContent = newData.title;
                comparisonChart.data.labels = newData.labels;
                comparisonChart.data.datasets[0].data = newData.data;
                comparisonChart.update();
            });
        });
    </script>
</body>
</html>
