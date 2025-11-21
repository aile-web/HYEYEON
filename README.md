<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="default">
    <meta name="apple-mobile-web-app-title" content="복약지도">
    <title>복약 지도 앱</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background-color: #f5f5f5;
            padding-bottom: 70px;
        }

        .header {
            background-color: #4CAF50;
            color: white;
            padding: 15px;
            text-align: center;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        .header h1 {
            font-size: 20px;
            font-weight: 600;
        }

        .tab-container {
            display: flex;
            background-color: white;
            position: sticky;
            top: 50px;
            z-index: 99;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
        }

        .tab {
            flex: 1;
            padding: 15px;
            text-align: center;
            background-color: white;
            border: none;
            cursor: pointer;
            font-size: 15px;
            color: #666;
            border-bottom: 3px solid transparent;
            transition: all 0.3s;
        }

        .tab.active {
            color: #4CAF50;
            border-bottom-color: #4CAF50;
            font-weight: 600;
        }

        .content {
            display: none;
            padding: 20px;
        }

        .content.active {
            display: block;
        }

        .card {
            background: white;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 15px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.08);
        }

        .form-group {
            margin-bottom: 15px;
        }

        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #333;
            font-size: 14px;
        }

        input, select {
            width: 100%;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 15px;
            transition: border-color 0.3s;
        }

        input:focus, select:focus {
            outline: none;
            border-color: #4CAF50;
        }

        .weight-ranges {
            margin-top: 10px;
        }

        .weight-range-item {
            display: flex;
            gap: 10px;
            margin-bottom: 10px;
            align-items: center;
        }

        .weight-range-item input {
            flex: 1;
        }

        .weight-range-item button {
            padding: 8px 12px;
            background-color: #f44336;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 13px;
        }

        .btn {
            width: 100%;
            padding: 14px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s;
            margin-top: 10px;
        }

        .btn-primary {
            background-color: #4CAF50;
            color: white;
        }

        .btn-primary:active {
            background-color: #45a049;
        }

        .btn-secondary {
            background-color: #2196F3;
            color: white;
        }

        .btn-secondary:active {
            background-color: #0b7dda;
        }

        .medication-list {
            margin-top: 20px;
        }

        .medication-item {
            background: white;
            border-radius: 10px;
            padding: 15px;
            margin-bottom: 10px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.08);
        }

        .medication-item h3 {
            color: #4CAF50;
            margin-bottom: 10px;
            font-size: 17px;
        }

        .medication-item .ranges {
            font-size: 14px;
            color: #666;
            margin-bottom: 10px;
        }

        .medication-item .ranges div {
            padding: 5px 0;
            border-bottom: 1px solid #f0f0f0;
        }

        .medication-item .ranges div:last-child {
            border-bottom: none;
        }

        .medication-item .actions {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }

        .medication-item .actions button {
            flex: 1;
            padding: 10px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
            font-weight: 600;
        }

        .btn-edit {
            background-color: #FF9800;
            color: white;
        }

        .btn-delete {
            background-color: #f44336;
            color: white;
        }

        .result-card {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border-radius: 12px;
            padding: 25px;
            margin-top: 20px;
            text-align: center;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }

        .result-card h3 {
            font-size: 16px;
            margin-bottom: 10px;
            opacity: 0.9;
        }

        .result-card .dose {
            font-size: 36px;
            font-weight: bold;
            margin: 10px 0;
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: #999;
        }

        .empty-state svg {
            width: 80px;
            height: 80px;
            margin-bottom: 15px;
            opacity: 0.3;
        }

        @media (max-width: 600px) {
            .header h1 {
                font-size: 18px;
            }
            
            .tab {
                font-size: 14px;
                padding: 12px;
            }
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>💊 복약 지도</h1>
    </div>

    <div class="tab-container">
        <button class="tab active" onclick="switchTab('calculate')">복용량 계산</button>
        <button class="tab" onclick="switchTab('manage')">약 정보 관리</button>
    </div>

    <!-- 복용량 계산 탭 -->
    <div id="calculate" class="content active">
        <div class="card">
            <h2 style="margin-bottom: 20px; color: #333; font-size: 18px;">환자 정보 입력</h2>
            
            <div class="form-group">
                <label>나이 (세)</label>
                <input type="number" id="patientAge" placeholder="예: 7" min="0">
            </div>

            <div class="form-group">
                <label>체중 (kg)</label>
                <input type="number" id="patientWeight" placeholder="예: 25.5" step="0.1" min="0">
            </div>

            <div class="form-group">
                <label>약 선택</label>
                <select id="medicationSelect">
                    <option value="">약을 선택하세요</option>
                </select>
            </div>

            <button class="btn btn-primary" onclick="calculateDose()">복용량 계산</button>
        </div>

        <div id="resultContainer"></div>
    </div>

    <!-- 약 정보 관리 탭 -->
    <div id="manage" class="content">
        <div class="card">
            <h2 style="margin-bottom: 20px; color: #333; font-size: 18px;">약 정보 입력</h2>
            
            <div class="form-group">
                <label>약 이름</label>
                <input type="text" id="medicationName" placeholder="예: 타이레놀 시럽">
            </div>

            <div class="form-group">
                <label>체중 구간별 복용량</label>
                <div class="weight-ranges" id="weightRanges">
                    <div class="weight-range-item">
                        <input type="number" placeholder="최소 체중 (kg)" step="0.1" class="min-weight">
                        <input type="number" placeholder="최대 체중 (kg)" step="0.1" class="max-weight">
                        <input type="text" placeholder="복용량 (예: 5ml)" class="dose">
                        <button onclick="removeWeightRange(this)">삭제</button>
                    </div>
                </div>
                <button class="btn btn-secondary" onclick="addWeightRange()">+ 체중 구간 추가</button>
            </div>

            <button class="btn btn-primary" onclick="saveMedication()">저장</button>
        </div>

        <div class="medication-list" id="medicationList"></div>
    </div>

    <script>
        // IndexedDB 초기화
        let db;
        const dbName = 'MedicationDB';
        const storeName = 'medications';

        function initDB() {
            const request = indexedDB.open(dbName, 1);

            request.onerror = function() {
                alert('데이터베이스를 열 수 없습니다.');
            };

            request.onsuccess = function(event) {
                db = event.target.result;
                loadMedications();
                updateMedicationSelect();
            };

            request.onupgradeneeded = function(event) {
                db = event.target.result;
                const objectStore = db.createObjectStore(storeName, { keyPath: 'id', autoIncrement: true });
                objectStore.createIndex('name', 'name', { unique: false });
            };
        }

        // 탭 전환
        function switchTab(tabName) {
            const tabs = document.querySelectorAll('.tab');
            const contents = document.querySelectorAll('.content');
            
            tabs.forEach(tab => tab.classList.remove('active'));
            contents.forEach(content => content.classList.remove('active'));
            
            event.target.classList.add('active');
            document.getElementById(tabName).classList.add('active');
            
            if (tabName === 'manage') {
                loadMedications();
            } else {
                updateMedicationSelect();
            }
        }

        // 체중 구간 추가
        function addWeightRange() {
            const container = document.getElementById('weightRanges');
            const div = document.createElement('div');
            div.className = 'weight-range-item';
            div.innerHTML = `
                <input type="number" placeholder="최소 체중 (kg)" step="0.1" class="min-weight">
                <input type="number" placeholder="최대 체중 (kg)" step="0.1" class="max-weight">
                <input type="text" placeholder="복용량 (예: 5ml)" class="dose">
                <button onclick="removeWeightRange(this)">삭제</button>
            `;
            container.appendChild(div);
        }

        // 체중 구간 삭제
        function removeWeightRange(button) {
            const container = document.getElementById('weightRanges');
            if (container.children.length > 1) {
                button.parentElement.remove();
            } else {
                alert('최소 1개의 체중 구간이 필요합니다.');
            }
        }

        // 약 정보 저장
        function saveMedication() {
            const name = document.getElementById('medicationName').value.trim();
            if (!name) {
                alert('약 이름을 입력해주세요.');
                return;
            }

            const ranges = [];
            const rangeItems = document.querySelectorAll('.weight-range-item');
            
            for (let item of rangeItems) {
                const minWeight = parseFloat(item.querySelector('.min-weight').value);
                const maxWeight = parseFloat(item.querySelector('.max-weight').value);
                const dose = item.querySelector('.dose').value.trim();

                if (isNaN(minWeight) || isNaN(maxWeight) || !dose) {
                    alert('모든 체중 구간 정보를 입력해주세요.');
                    return;
                }

                if (minWeight >= maxWeight) {
                    alert('최소 체중은 최대 체중보다 작아야 합니다.');
                    return;
                }

                ranges.push({ minWeight, maxWeight, dose });
            }

            // 체중 구간 중복 확인
            ranges.sort((a, b) => a.minWeight - b.minWeight);
            for (let i = 0; i < ranges.length - 1; i++) {
                if (ranges[i].maxWeight > ranges[i + 1].minWeight) {
                    alert('체중 구간이 겹칩니다. 다시 확인해주세요.');
                    return;
                }
            }

            const medication = { name, ranges };

            const transaction = db.transaction([storeName], 'readwrite');
            const objectStore = transaction.objectStore(storeName);
            const request = objectStore.add(medication);

            request.onsuccess = function() {
                alert('약 정보가 저장되었습니다.');
                document.getElementById('medicationName').value = '';
                document.getElementById('weightRanges').innerHTML = `
                    <div class="weight-range-item">
                        <input type="number" placeholder="최소 체중 (kg)" step="0.1" class="min-weight">
                        <input type="number" placeholder="최대 체중 (kg)" step="0.1" class="max-weight">
                        <input type="text" placeholder="복용량 (예: 5ml)" class="dose">
                        <button onclick="removeWeightRange(this)">삭제</button>
                    </div>
                `;
                loadMedications();
                updateMedicationSelect();
            };

            request.onerror = function() {
                alert('저장 중 오류가 발생했습니다.');
            };
        }

        // 약 목록 불러오기
        function loadMedications() {
            const transaction = db.transaction([storeName], 'readonly');
            const objectStore = transaction.objectStore(storeName);
            const request = objectStore.getAll();

            request.onsuccess = function(event) {
                const medications = event.target.result;
                const listContainer = document.getElementById('medicationList');
                
                if (medications.length === 0) {
                    listContainer.innerHTML = `
                        <div class="empty-state">
                            <svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                            </svg>
                            <p>등록된 약이 없습니다.</p>
                        </div>
                    `;
                    return;
                }

                listContainer.innerHTML = '';
                medications.forEach(med => {
                    const div = document.createElement('div');
                    div.className = 'medication-item';
                    
                    let rangesHTML = '';
                    med.ranges.forEach(range => {
                        rangesHTML += `<div>${range.minWeight}kg ~ ${range.maxWeight}kg: ${range.dose}</div>`;
                    });

                    div.innerHTML = `
                        <h3>${med.name}</h3>
                        <div class="ranges">${rangesHTML}</div>
                        <div class="actions">
                            <button class="btn-delete" onclick="deleteMedication(${med.id})">삭제</button>
                        </div>
                    `;
                    listContainer.appendChild(div);
                });
            };
        }

        // 약 삭제
        function deleteMedication(id) {
            if (!confirm('이 약 정보를 삭제하시겠습니까?')) {
                return;
            }

            const transaction = db.transaction([storeName], 'readwrite');
            const objectStore = transaction.objectStore(storeName);
            const request = objectStore.delete(id);

            request.onsuccess = function() {
                alert('삭제되었습니다.');
                loadMedications();
                updateMedicationSelect();
            };
        }

        // 약 선택 드롭다운 업데이트
        function updateMedicationSelect() {
            const transaction = db.transaction([storeName], 'readonly');
            const objectStore = transaction.objectStore(storeName);
            const request = objectStore.getAll();

            request.onsuccess = function(event) {
                const medications = event.target.result;
                const select = document.getElementById('medicationSelect');
                
                select.innerHTML = '<option value="">약을 선택하세요</option>';
                medications.forEach(med => {
                    const option = document.createElement('option');
                    option.value = med.id;
                    option.textContent = med.name;
                    select.appendChild(option);
                });
            };
        }

        // 복용량 계산
        function calculateDose() {
            const age = parseFloat(document.getElementById('patientAge').value);
            const weight = parseFloat(document.getElementById('patientWeight').value);
            const medId = parseInt(document.getElementById('medicationSelect').value);

            if (isNaN(age) || age < 0) {
                alert('나이를 올바르게 입력해주세요.');
                return;
            }

            if (isNaN(weight) || weight <= 0) {
                alert('체중을 올바르게 입력해주세요.');
                return;
            }

            if (!medId) {
                alert('약을 선택해주세요.');
                return;
            }

            const transaction = db.transaction([storeName], 'readonly');
            const objectStore = transaction.objectStore(storeName);
            const request = objectStore.get(medId);

            request.onsuccess = function(event) {
                const medication = event.target.result;
                let dose = null;

                for (let range of medication.ranges) {
                    if (weight >= range.minWeight && weight <= range.maxWeight) {
                        dose = range.dose;
                        break;
                    }
                }

                const resultContainer = document.getElementById('resultContainer');
                
                if (dose) {
                    resultContainer.innerHTML = `
                        <div class="result-card">
                            <h3>계산 결과</h3>
                            <div style="font-size: 14px; opacity: 0.9; margin: 10px 0;">
                                ${medication.name}<br>
                                나이: ${age}세 / 체중: ${weight}kg
                            </div>
                            <div class="dose">${dose}</div>
                        </div>
                    `;
                } else {
                    resultContainer.innerHTML = `
                        <div class="card" style="background-color: #fff3cd; border-left: 4px solid #ff9800;">
                            <h3 style="color: #ff9800; margin-bottom: 10px;">⚠️ 해당 체중 구간 없음</h3>
                            <p style="color: #666;">체중 ${weight}kg에 해당하는 복용량 정보가 없습니다.<br>약 정보를 확인하거나 전문가와 상담하세요.</p>
                        </div>
                    `;
                }
            };
        }

        // 페이지 로드 시 DB 초기화
        window.onload = function() {
            initDB();
        };
    </script>
</body>
</html>
