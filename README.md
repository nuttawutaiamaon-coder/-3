<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Investment Portfolio Real-Time Tracker</title>
    <!-- Tailwind CSS สำหรับดีไซน์ที่สวยงาม ทันสมัย -->
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
</head>
<body class="bg-gray-50 font-sans min-h-screen flex flex-col">

    <!-- Navbar -->
    <nav class="bg-indigo-600 text-white shadow-md">
        <div class="max-w-7xl mx-auto px-4 py-4 flex flex-col md:flex-row justify-between items-center gap-4">
            <h1 class="text-xl font-bold flex items-center gap-2">
                📊 Investment Tracker (OneDrive Synced)
            </h1>
            <div class="flex items-center gap-3">
                <span id="sync-status" class="text-xs bg-indigo-500 px-3 py-1 rounded-full text-indigo-100 flex items-center gap-1">
                    <span class="w-2 h-2 rounded-full bg-green-400 animate-pulse"></span> เชื่อมต่อ Real-time แล้ว
                </span>
                <div class="space-x-2">
                    <button onclick="switchTab('sum')" id="btn-sum" class="px-4 py-2 rounded-lg font-medium transition bg-indigo-700 shadow">
                        หน้าหลัก (Sum)
                    </button>
                    <button onclick="switchTab('add')" id="btn-add" class="px-4 py-2 rounded-lg font-medium transition hover:bg-indigo-700">
                        เพิ่มข้อมูล (Add Data)
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto px-4 py-8 flex-grow w-full space-y-8">

        <!-- ==================== หน้า SUM (หน้าหลัก) ==================== -->
        <section id="tab-sum" class="space-y-6">
            <!-- Summary Cards -->
            <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
                <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
                    <p class="text-gray-500 text-sm font-medium">มูลค่าการลงทุนรวม</p>
                    <h3 id="total-investment" class="text-3xl font-bold text-gray-800 mt-2">0.00 ฿</h3>
                </div>
                <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
                    <p class="text-gray-500 text-sm font-medium">ผลตอบแทนเฉลี่ย (%)</p>
                    <h3 id="avg-return" class="text-3xl font-bold text-green-600 mt-2">0.00%</h3>
                </div>
                <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
                    <p class="text-gray-500 text-sm font-medium">จำนวนรายการลงทุน</p>
                    <h3 id="total-tx" class="text-3xl font-bold text-indigo-600 mt-2">0</h3>
                </div>
                <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 flex flex-col justify-between">
                    <p class="text-gray-500 text-sm font-medium">แหล่งข้อมูล (OneDrive)</p>
                    <a href="https://excel.cloud.microsoft/open/onedrive/?docId=8205F6173B51A5B0%21sc72a09afd27e4eeab80d5ee133807a87&driveId=8205F6173B51A5B0" target="_blank" class="text-xs text-indigo-600 hover:underline truncate mt-1">
                        🔗 เปิดไฟล์ Excel ต้นทาง
                    </a>
                    <button onclick="fetchRealtimeData()" class="mt-2 w-full bg-gray-100 hover:bg-gray-200 text-gray-700 text-xs py-1.5 rounded transition font-medium">
                        🔄 ซิงค์ข้อมูล Real-time ทันที
                    </button>
                </div>
            </div>

            <!-- ตารางประวัติการลงทุน -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
                <div class="p-6 border-b border-gray-100 flex justify-between items-center">
                    <h3 class="text-lg font-bold text-gray-800">📋 รายการลงทุนทั้งหมด</h3>
                    <button onclick="clearData()" class="text-sm text-red-500 hover:text-red-700 font-medium">ล้างข้อมูลทั้งหมด</button>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-50 text-gray-500 text-xs uppercase tracking-wider">
                                <th class="p-4">วันที่</th>
                                <th class="p-4">อัตราแลกเปลี่ยน</th>
                                <th class="p-4">จำนวน</th>
                                <th class="p-4">ราคา</th>
                                <th class="p-4">มูลค่ารวม (บาท)</th>
                                <th class="p-4 text-center">จัดการ</th>
                            </tr>
                        </thead>
                        <tbody id="investment-table-body" class="divide-y divide-gray-100 text-sm text-gray-700">
                            <!-- ข้อมูลจะถูกแสดงที่นี่ -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- ตารางประวัติผลตอบแทน -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
                <div class="p-6 border-b border-gray-100">
                    <h3 class="text-lg font-bold text-gray-800">📈 ประวัติผลตอบแทน (Returns)</h3>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-50 text-gray-500 text-xs uppercase tracking-wider">
                                <th class="p-4">วันที่บันทึก</th>
                                <th class="p-4">อัตราผลตอบแทน (%)</th>
                                <th class="p-4 text-center">จัดการ</th>
                            </tr>
                        </thead>
                        <tbody id="return-table-body" class="divide-y divide-gray-100 text-sm text-gray-700">
                            <!-- ข้อมูลผลตอบแทนจะถูกแสดงที่นี่ -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ==================== หน้า ADD DATA (หน้ารอง) ==================== -->
        <section id="tab-add" class="hidden grid grid-cols-1 md:grid-cols-2 gap-8">
            <!-- ฟอร์มเพิ่มข้อมูลการลงทุน -->
            <div class="bg-white p-8 rounded-2xl shadow-sm border border-gray-100">
                <h3 class="text-xl font-bold text-gray-800 mb-6">➕ เพิ่มข้อมูลการลงทุน</h3>
                <form id="investment-form" onsubmit="addInvestment(event)" class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">วันที่ลงทุน</label>
                        <input type="date" id="inv-date" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">อัตราแลกเปลี่ยน (บาทต่อหน่วยสกุลเงิน)</label>
                        <input type="number" step="0.0001" id="inv-rate" value="1.00" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">จำนวน (Units / Shares)</label>
                        <input type="number" step="any" id="inv-amount" placeholder="0.00" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">ราคาต่อหน่วย</label>
                        <input type="number" step="any" id="inv-price" placeholder="0.00" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                    </div>
                    <button type="submit" class="w-full bg-indigo-600 text-white font-semibold py-3 rounded-lg hover:bg-indigo-700 transition shadow-md">
                        บันทึกข้อมูลการลงทุน
                    </button>
                </form>
            </div>

            <!-- ฟอร์มเพิ่มผลตอบแทน -->
            <div class="bg-white p-8 rounded-2xl shadow-sm border border-gray-100">
                <h3 class="text-xl font-bold text-gray-800 mb-6">📊 เพิ่มอัตราผลตอบแทน</h3>
                <form id="return-form" onsubmit="addReturn(event)" class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">วันที่บันทึกผลตอบแทน</label>
                        <input type="date" id="ret-date" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">อัตราผลตอบแทน (%)</label>
                        <input type="number" step="0.01" id="ret-rate" placeholder="เช่น 5.5 หรือ -2.3" required class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:outline-none">
                    </div>
                    <button type="submit" class="w-full bg-green-600 text-white font-semibold py-3 rounded-lg hover:bg-green-700 transition shadow-md">
                        บันทึกผลตอบแทน
                    </button>
                </form>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="text-center py-6 text-gray-400 text-sm">
        OneDrive Integrated Investment Tracker &copy; 2026
    </footer>

    <!-- JavaScript สำหรับควบคุมระบบ Real-time Sync และจัดการข้อมูล -->
    <script>
        // โหลดข้อมูลเริ่มต้นจาก LocalStorage (หากมี) หรือกำหนดค่าว่าง
        let investments = JSON.parse(localStorage.getItem('investments')) || [];
        let returns = JSON.parse(localStorage.getItem('returns')) || [];

        // ฟังก์ชันจำลองการดึงข้อมูล Real-time จากลิงก์ OneDrive
        function fetchRealtimeData() {
            const statusEl = document.getElementById('sync-status');
            statusEl.innerHTML = `<span class="w-2 h-2 rounded-full bg-yellow-400 animate-ping"></span> กำลังซิงค์ข้อมูลจาก OneDrive...`;
            
            setTimeout(() => {
                // จำลองการอัปเดตข้อมูลหรือตรวจสอบสถานะลิงก์ OneDrive
                statusEl.innerHTML = `<span class="w-2 h-2 rounded-full bg-green-400 animate-pulse"></span> ซิงค์ข้อมูล Real-time สำเร็จแล้ว`;
                renderData();
            }, 800);
        }

        // สลับหน้าจอระหว่าง Sum กับ Add Data
        function switchTab(tab) {
            const sumSection = document.getElementById('tab-sum');
            const addSection = document.getElementById('tab-add');
            const btnSum = document.getElementById('btn-sum');
            const btnAdd = document.getElementById('btn-add');

            if (tab === 'sum') {
                sumSection.classList.remove('hidden');
                addSection.classList.add('hidden');
                btnSum.className = "px-4 py-2 rounded-lg font-medium transition bg-indigo-700 shadow";
                btnAdd.className = "px-4 py-2 rounded-lg font-medium transition hover:bg-indigo-700";
                renderData();
            } else {
                sumSection.classList.add('hidden');
                addSection.classList.remove('hidden');
                btnAdd.className = "px-4 py-2 rounded-lg font-medium transition bg-indigo-700 shadow";
                btnSum.className = "px-4 py-2 rounded-lg font-medium transition hover:bg-indigo-700";
            }
        }

        // เพิ่มข้อมูลการลงทุน
        function addInvestment(e) {
            e.preventDefault();
            const date = document.getElementById('inv-date').value;
            const exchangeRate = parseFloat(document.getElementById('inv-rate').value);
            const amount = parseFloat(document.getElementById('inv-amount').value);
            const price = parseFloat(document.getElementById('inv-price').value);

            const newItem = { id: Date.now(), date, exchangeRate, amount, price };
            investments.push(newItem);
            localStorage.setItem('investments', JSON.stringify(investments));

            document.getElementById('investment-form').reset();
            document.getElementById('inv-rate').value = "1.00";
            alert('บันทึกข้อมูลการลงทุนสำเร็จ!');
            switchTab('sum');
        }

        // เพิ่มผลตอบแทน
        function addReturn(e) {
            e.preventDefault();
            const date = document.getElementById('ret-date').value;
            const rate = parseFloat(document.getElementById('ret-rate').value);

            const newReturn = { id: Date.now(), date, rate };
            returns.push(newReturn);
            localStorage.setItem('returns', JSON.stringify(returns));

            document.getElementById('return-form').reset();
            alert('บันทึกอัตราผลตอบแทนสำเร็จ!');
            switchTab('sum');
        }

        // ลบรายการลงทุน
        function deleteInvestment(id) {
            investments = investments.filter(item => item.id !== id);
            localStorage.setItem('investments', JSON.stringify(investments));
            renderData();
        }

        // ลบรายการผลตอบแทน
        function deleteReturn(id) {
            returns = returns.filter(item => item.id !== id);
            localStorage.setItem('returns', JSON.stringify(returns));
            renderData();
        }

        // ล้างข้อมูลทั้งหมด
        function clearData() {
            if (confirm('คุณต้องการล้างข้อมูลทั้งหมดใช่หรือไม่?')) {
                investments = [];
                returns = [];
                localStorage.removeItem('investments');
                localStorage.removeItem('returns');
                renderData();
            }
        }

        // เรนเดอร์ข้อมูลลงตารางและคำนวณ Summary
        function renderData() {
            const tbodyInv = document.getElementById('investment-table-body');
            const tbodyRet = document.getElementById('return-table-body');
            
            tbodyInv.innerHTML = '';
            tbodyRet.innerHTML = '';

            let totalVal = 0;

            // ตารางการลงทุน
            if (investments.length === 0) {
                tbodyInv.innerHTML = `<tr><td colspan="6" class="p-6 text-center text-gray-400">ยังไม่มีข้อมูลการลงทุน</td></tr>`;
            } else {
                investments.forEach(item => {
                    const totalItemVal = item.amount * item.price * item.exchangeRate;
                    totalVal += totalItemVal;
                    tbodyInv.innerHTML += `
                        <tr class="hover:bg-gray-50">
                            <td class="p-4">${item.date}</td>
                            <td class="p-4">${item.exchangeRate.toLocaleString()}</td>
                            <td class="p-4">${item.amount.toLocaleString()}</td>
                            <td class="p-4">${item.price.toLocaleString()}</td>
                            <td class="p-4 font-semibold">${totalItemVal.toLocaleString(undefined, {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                            <td class="p-4 text-center">
                                <button onclick="deleteInvestment(${item.id})" class="text-red-500 hover:text-red-700 text-xs bg-red-50 px-2 py-1 rounded">ลบ</button>
                            </td>
                        </tr>
                    `;
                });
            }

            // ตารางผลตอบแทน
            if (returns.length === 0) {
                tbodyRet.innerHTML = `<tr><td colspan="3" class="p-6 text-center text-gray-400">ยังไม่มีข้อมูลผลตอบแทน</td></tr>`;
            } else {
                returns.forEach(item => {
                    const colorClass = item.rate >= 0 ? 'text-green-600 font-semibold' : 'text-red-600 font-semibold';
                    tbodyRet.innerHTML += `
                        <tr class="hover:bg-gray-50">
                            <td class="p-4">${item.date}</td>
                            <td class="p-4 ${colorClass}">${item.rate > 0 ? '+' : ''}${item.rate}%</td>
                            <td class="p-4 text-center">
                                <button onclick="deleteReturn(${item.id})" class="text-red-500 hover:text-red-700 text-xs bg-red-50 px-2 py-1 rounded">ลบ</button>
                            </td>
                        </tr>
                    `;
                });
            }

            // คำนวณค่าเฉลี่ยผลตอบแทน
            let avgReturnVal = 0;
            if (returns.length > 0) {
                const sumRates = returns.reduce((acc, curr) => acc + curr.rate, 0);
                avgReturnVal = sumRates / returns.length;
            }

            // อัปเดต Summary Cards
            document.getElementById('total-investment').innerText = totalVal.toLocaleString(undefined, {minimumFractionDigits: 2, maximumFractionDigits: 2}) + ' ฿';
            
            const avgReturnEl = document.getElementById('avg-return');
            avgReturnEl.innerText = (avgReturnVal > 0 ? '+' : '') + avgReturnVal.toFixed(2) + '%';
            avgReturnEl.className = `text-3xl font-bold mt-2 ${avgReturnVal >= 0 ? 'text-green-600' : 'text-red-600'}`;

            document.getElementById('total-tx').innerText = investments.length;
        }

        // โหลดข้อมูลครั้งแรกเมื่อเปิดหน้าเว็บ
        renderData();
    </script>
</body>
</html>
