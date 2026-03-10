<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منظومة الهلال الأحمر - جرابلس</title>
    
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.0/xlsx.full.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">

    <style>
        :root { --sarc-red: #d32f2f; --dark: #1a1a1a; --gray: #f4f4f4; --gold: #fbc02d; --green: #2e7d32; }
        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: var(--gray); margin: 0; }
        
        #loginScreen { height: 100vh; display: flex; align-items: center; justify-content: center; background: #eceff1; }
        .login-card { background: white; padding: 40px; border-radius: 20px; box-shadow: 0 15px 35px rgba(0,0,0,0.1); text-align: center; width: 380px; border-top: 8px solid var(--sarc-red); }
        .login-card img { width: 100px; margin-bottom: 20px; }

        .app-header { background: white; padding: 15px; text-align: center; border-bottom: 4px solid var(--sarc-red); display: none; align-items: center; justify-content: center; gap: 20px; }
        .app-header img { width: 50px; }

        .container { max-width: 950px; margin: 30px auto; background: white; border-radius: 15px; box-shadow: 0 5px 25px rgba(0,0,0,0.08); padding: 30px; display: none; }
        
        input, select, textarea { width: 100%; padding: 14px; margin: 10px 0; border: 2px solid #e0e0e0; border-radius: 10px; font-size: 16px; box-sizing: border-box; }
        button { width: 100%; padding: 15px; border: none; border-radius: 10px; cursor: pointer; font-weight: bold; background: var(--sarc-red); color: white; font-size: 18px; margin-top: 10px; transition: 0.3s; }
        button:hover { opacity: 0.9; transform: translateY(-2px); }

        .exemption-box { background: #fff3e0; border: 2px dashed #ff9800; padding: 15px; border-radius: 10px; margin: 15px 0; display: flex; align-items: center; gap: 15px; cursor: pointer; }
        .fee-panel { background: #fffde7; border: 2px solid var(--gold); padding: 15px; border-radius: 8px; text-align: center; margin: 15px 0; font-size: 24px; color: var(--sarc-red); font-weight: bold; }

        .logout-btn { position: fixed; top: 15px; left: 15px; background: #333; color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; z-index: 1000; display: none; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: center; }
        th { background: var(--dark); color: white; }

        @media print { .no-print { display: none; } .print-area { display: block !important; } }
        .print-area { display: none; text-align: center; padding: 30px; border: 2px dashed black; }
    </style>
</head>
<body>

<button class="logout-btn no-print" id="logoutBtn" onclick="location.reload()">خروج <i class="fas fa-sign-out-alt"></i></button>

<div id="loginScreen" class="no-print">
    <div class="login-card">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/Red_Crescent_logo.svg/1200px-Red_Crescent_logo.svg.png">
        <h3>منظومة شعبة جرابلس</h3>
        <select id="userRole">
            <option value="recep">بوابة الاستقبال</option>
            <option value="doc">بوابة الأطباء</option>
            <option value="pharm">بوابة الصيدلية</option>
            <option value="admin">بوابة المدير</option>
        </select>
        <input type="password" id="userPass" placeholder="أدخل كلمة السر">
        <button onclick="attemptLogin()">دخول للمنظومة</button>
    </div>
</div>

<div class="app-header no-print" id="appHead">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/Red_Crescent_logo.svg/1200px-Red_Crescent_logo.svg.png">
    <h2>الهلال الأحمر العربي السوري - شعبة جرابلس</h2>
</div>

<div id="recepPanel" class="container no-print">
    <h3><i class="fas fa-id-card"></i> تسجيل مراجع</h3>
    <input type="number" id="pNid" placeholder="الرقم الوطني للمراجع" oninput="calcFee()">
    <input type="text" id="pName" placeholder="الاسم الثلاثي">
    <select id="pDoc" onchange="calcFee()">
        <option value="">-- اختر العيادة --</option>
        <optgroup label="الأطباء">
            <option value="د. فيصل البكار">د. فيصل البكار</option>
            <option value="د. دهوم الأمين">د. دهوم الأمين</option>
            <option value="د. محمد عليوي">د. محمد عليوي</option>
            <option value="د. أحمد شيخ أحمد">د. أحمد شيخ أحمد</option>
            <option value="د. علي الأحمد">د. علي الأحمد</option>
            <option value="د. حسن الحفني">د. حسن الحفني</option>
            <option value="🦷 عيادة الأسنان">عيادة الأسنان</option>
        </optgroup>
        <optgroup label="القابلات">
            <option value="القابلة كوكب المسطو">القابلة كوكب المسطو</option>
            <option value="القابلة ثريا عبد السلام">القابلة ثريا عبد السلام</option>
            <option value="القابلة فضيلة المصطفى">القابلة فضيلة المصطفى</option>
        </optgroup>
    </select>
    <label class="exemption-box"><input type="checkbox" id="isVol" onchange="calcFee()"> <span>إعفاء (متطوع / حالة إنسانية)</span></label>
    <div class="fee-panel">الرسم: <span id="feeVal">0</span> ل.ت</div>
    <button onclick="saveAndPrint()">حفظ وطباعة التذكرة</button>
</div>

<div id="docPanel" class="container no-print">
    <h3><i class="fas fa-user-md"></i> معاينات الطبيب</h3>
    <select id="currentDoc" onchange="loadQueue()">
        <option value="">-- اختر عيادتك لعرض المرضى --</option>
        <option value="د. فيصل البكار">د. فيصل البكار</option>
        <option value="د. دهوم الأمين">د. دهوم الأمين</option>
        <option value="🦷 عيادة الأسنان">عيادة الأسنان</option>
    </select>
    <div id="docTableArea"></div>
</div>

<div id="pharmPanel" class="container no-print">
    <h3 style="color:var(--green);"><i class="fas fa-pills"></i> الصيدلية</h3>
    <input type="number" id="phScan" placeholder="امسح باركود التذكرة..." onchange="phSearch()">
    <div id="phResult" style="display:none; padding:20px; background:#f1f8e9; border-radius:12px; margin-top:20px;">
        <h4 id="phPName"></h4>
        <p>التشخيص: <mark id="phPDiag" style="font-weight:bold;"></mark></p>
        <hr>
        <input type="text" id="medBarcode" placeholder="امسح باركود الدواء..." onchange="phAddMed()">
        <ul id="medList"></ul>
        <button style="background:var(--green);" onclick="location.reload()">تأكيد الصرف ✅</button>
    </div>
</div>

<div id="adminPanel" class="container no-print">
    <h3><i class="fas fa-chart-line"></i> الإدارة والجرد</h3>
    <button style="background:var(--dark);" onclick="exportExcel()">تصدير جرد اليوم (Excel) <i class="fas fa-file-excel"></i></button>
    <div style="margin-top:80px; padding:20px; border: 2px solid red; border-radius:10px;">
        <h4 style="color:red;">منطقة التصفير ⚠️</h4>
        <button style="background:#666;" onclick="systemReset()">تصفير شامل لنهاية الدوام</button>
    </div>
</div>

<div id="ticket" class="print-area">
    <h2 style="color:red; margin:0;">الهلال الأحمر العربي السوري</h2>
    <p>شعبة جرابلس</p>
    <hr>
    <div style="font-size:100px; font-weight:bold;" id="prQ"></div>
    <p style="font-size:24px;" id="prName"></p>
    <p>العيادة: <b id="prDoc"></b></p>
    <p>الرسم: <b id="prFee"></b> ل.ت</p>
    <svg id="barcode"></svg>
</div>

<script>
    const PWD = { recep: "111", doc: "222", pharm: "333", admin: "444" };

    function attemptLogin() {
        const role = document.getElementById('userRole').value;
        const pass = document.getElementById('userPass').value;
        if(pass === PWD[role]) {
            document.getElementById('loginScreen').style.display = 'none';
            document.getElementById('appHead').style.display = 'flex';
            document.getElementById('logoutBtn').style.display = 'block';
            document.getElementById(role + 'Panel').style.display = 'block';
        } else { alert("كلمة السر غير صحيحة!"); }
    }

    function calcFee() {
        let nid = document.getElementById('pNid').value;
        let doc = document.getElementById('pDoc').value;
        let isVol = document.getElementById('isVol').checked;
        if (isVol) { document.getElementById('feeVal').innerText = "0"; return; }
        let db = JSON.parse(localStorage.getItem('SARC_DB')) || [];
        let visited = db.some(x => x.nid === nid && x.doc === doc);
        let fee = visited ? 50 : 100;
        if(doc.includes("فيصل")) fee = visited ? 100 : 200;
        document.getElementById('feeVal').innerText = fee;
    }

    function saveAndPrint() {
        let nid = document.getElementById('pNid').value;
        let name = document.getElementById('pName').value;
        let doc = document.getElementById('pDoc').value;
        let fee = document.getElementById('feeVal').innerText;
        if(!nid || !name || !doc) return alert("البيانات ناقصة!");

        let db = JSON.parse(localStorage.getItem('SARC_DB')) || [];
        let q = db.filter(x => x.doc === doc).length + 1;
        db.push({ nid, name, doc, fee, q, diag: "في انتظار المعاينة", date: new Date().toLocaleString() });
        localStorage.setItem('SARC_DB', JSON.stringify(db));
        
        document.getElementById('prQ').innerText = q;
        document.getElementById('prName').innerText = name;
        document.getElementById('prDoc').innerText = doc;
        document.getElementById('prFee').innerText = fee;
        JsBarcode("#barcode", nid, {height: 40});
        window.print();
        location.reload();
    }

    function loadQueue() {
        let doc = document.getElementById('currentDoc').value;
        let db = JSON.parse(localStorage.getItem('SARC_DB')) || [];
        let list = db.filter(x => x.doc === doc);
        let html = `<table><tr><th>الدور</th><th>الاسم</th><th>التشخيص</th><th>حفظ</th></tr>`;
        list.forEach((p, idx) => {
            html += `<tr><td>${p.q}</td><td>${p.name}</td><td><textarea id="diag_${idx}">${p.diag}</textarea></td>
            <td><button onclick="saveDiag('${doc}', ${idx})" style="padding:5px; font-size:12px;">💾</button></td></tr>`;
        });
        document.getElementById('docTableArea').innerHTML = html + "</table>";
    }

    function saveDiag(doc, idx) {
        let db = JSON.parse(localStorage.getItem('SARC_DB')) || [];
        let list = db.filter(x => x.doc === doc);
        list[idx].diag = document.getElementById(`diag_${idx}`).value;
        localStorage.setItem('SARC_DB', JSON.stringify(db));
        alert("تم حفظ التشخيص");
    }

    function phSearch() {
        let nid = document.getElementById('phScan').value;
        let p = (JSON.parse(localStorage.getItem('SARC_DB')) || []).find(x => x.nid === nid);
        if(p) {
            document.getElementById('phResult').style.display = "block";
            document.getElementById('phPName').innerText = "المراجع: " + p.name;
            document.getElementById('phPDiag').innerText = p.diag;
        }
    }

    function phAddMed() {
        let m = document.getElementById('medBarcode').value;
        let li = document.createElement('li');
        li.innerText = "💊 دواء: " + m;
        document.getElementById('medList').appendChild(li);
        document.getElementById('medBarcode').value = "";
    }

    function exportExcel() {
        let db = JSON.parse(localStorage.getItem('SARC_DB')) || [];
        let ws = XLSX.utils.json_to_sheet(db);
        let wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "جرد جرابلس");
        XLSX.writeFile(wb, "SARC_Jarabulus_Report.xlsx");
    }

    function systemReset() {
        if(confirm("سيتم حذف جميع البيانات! هل أنت متأكد؟")) {
            localStorage.clear();
            location.reload();
        }
    }
</script>
</body>
</html>
