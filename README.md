تمام، نقدر نعدل البنود ونغير التصميم كله براحتك. 

هشرحلك تعملها إزاي، وهبعتلك كود جاهز فيه تعديلين:
1. *إضافة/حذف بنود الخدمات*: تغير الأسعار والأسماء براحتك
2. *تغيير التصميم*: غيرتلك الألوان والشكل يبقى أهدى وأشيك

#### 1. الكود الجديد - انسخه كله في `index.html`

<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>نظام فواتير المستشفى</title>
<style>
    :root {
        --main-color: #27ae60;
        --main-hover: #219150;
        --bg-color: #f0f7f4;
        --text-color: #2c3e50;
    }
    
    body {
        font-family: 'Tahoma', Arial, sans-serif;
        background: var(--bg-color);
        margin: 0;
        padding: 20px;
        direction: rtl;
    }
    .container {
        max-width: 850px;
        margin: auto;
        background: white;
        padding: 35px;
        border-radius: 15px;
        box-shadow: 0 4px 15px rgba(0,0,0,0.08);
    }
    h1 {
        text-align: center;
        color: var(--text-color);
        margin-bottom: 30px;
        border-bottom: 3px solid var(--main-color);
        padding-bottom: 15px;
    }
    .form-group {
        margin-bottom: 18px;
    }
    label {
        display: block;
        margin-bottom: 8px;
        color: var(--text-color);
        font-weight: bold;
        font-size: 15px;
    }
    input, select {
        width: 100%;
        padding: 12px;
        border: 2px solid #ddd;
        border-radius: 8px;
        box-sizing: border-box;
        font-size: 16px;
        transition: 0.3s;
    }
    input:focus, select:focus {
        border-color: var(--main-color);
        outline: none;
    }
    button {
        background: var(--main-color);
        color: white;
        padding: 14px 30px;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        font-size: 17px;
        width: 100%;
        margin-top: 20px;
        font-weight: bold;
        transition: 0.3s;
    }
    button:hover {
        background: var(--main-hover);
        transform: translateY(-2px);
    }
    .bill {
        margin-top: 30px;
        padding: 25px;
        border: 2px solid var(--main-color);
        border-radius: 10px;
        display: none;
        background: #fafafa;
    }
    .bill h2 {
        text-align: center;
        color: var(--text-color);
    }
    .bill-item {
        display: flex;
        justify-content: space-between;
        padding: 12px 0;
        border-bottom: 1px dashed #ddd;
        font-size: 16px;
    }
    .total {
        font-size: 22px;
        font-weight: bold;
        color: #e74c3c;
        text-align: center;
        margin-top: 20px;
        padding-top: 15px;
        border-top: 2px solid var(--main-color);
    }
    @media print {
        button, form { display: none; }
        .bill { display: block !important; box-shadow: none; }
    }
</style>
</head>
<body>
<div class="container">
    <h1>نظام فواتير المستشفى</h1>
    
    <form id="billForm">
        <div class="form-group">
            <label>اسم المريض:</label>
            <input type="text" id="patientName" required>
        </div>
        
        <div class="form-group">
            <label>نوع الخدمة:</label>
            <select id="service" required>
                <option value="">اختر الخدمة</option>
                <option value="100">كشف عادي - 100 جنيه</option>
                <option value="250">كشف استشاري - 250 جنيه</option>
                <option value="500">أشعة عادية - 500 جنيه</option>
                <option value="1200">أشعة مقطعية - 1200 جنيه</option>
                <option value="800">تحاليل شاملة - 800 جنيه</option>
                <option value="1500">عملية صغرى - 1500 جنيه</option>
                <option value="5000">عملية كبرى - 5000 جنيه</option>
                <option value="300">جلسة علاج طبيعي - 300 جنيه</option>
                <option value="50">حقنة أو محلول - 50 جنيه</option>
                <option value="200">كشف طوارئ - 200 جنيه</option>
                <option value="0">خدمة أخرى - حدد السعر يدوي</option>
            </select>
        </div>

        <div class="form-group" id="customServiceBox" style="display:none;">
            <label>اسم الخدمة الأخرى:</label>
            <input type="text" id="customServiceName">
            <label style="margin-top:10px;">سعر الخدمة:</label>
            <input type="number" id="customServicePrice" min="0">
        </div>
        
        <div class="form-group">
            <label>عدد الأيام في المستشفى:</label>
            <input type="number" id="days" value="0" min="0">
        </div>
        
        <div class="form-group">
            <label>سعر اليوم:</label>
            <input type="number" id="dayPrice" value="200" min="0">
        </div>
        
        <button type="submit">حساب الفاتورة</button>
    </form>

    <div class="bill" id="billResult">
        <h2>الفاتورة</h2>
        <div id="billDetails"></div>
        <div class="total" id="totalAmount"></div>
        <button onclick="window.print()">طباعة الفاتورة</button>
    </div>
</div>

<script>
document.getElementById('service').addEventListener('change', function() {
    if(this.value === "0") {
        document.getElementById('customServiceBox').style.display = 'block';
    } else {
        document.getElementById('customServiceBox').style.display = 'none';
    }
});

document.getElementById('billForm').addEventListener('submit', function(e) {
    e.preventDefault();
    
    const name = document.getElementById('patientName').value;
    let servicePrice = parseFloat(document.getElementById('service').value);
    let serviceText = document.getElementById('service').options[document.getElementById('service').selectedIndex].text;
    
    if(servicePrice === 0) {
        servicePrice = parseFloat(document.getElementById('customServicePrice').value) || 0;
        serviceText = document.getElementById('customServiceName').value || "خدمة أخرى";
    }
    
    const days = parseFloat(document.getElementById('days').value);
    const dayPrice = parseFloat(document.getElementById('dayPrice').value);
    
    const hospitalStay = days * dayPrice;
    const total = servicePrice + hospitalStay;
    
    let details = `
        <div class="bill-item">
            <span>اسم المريض:</span>
            <span>${name}</span>
        </div>
        <div class="bill-item">
            <span>الخدمة:</span>
            <span>${serviceText}</span>
        </div>
    `;
    
    if (days > 0) {
        details += `
        <div class="bill-item">
            <span>الإقامة: ${days} يوم × ${dayPrice} جنيه</span>
            <span>${hospitalStay} جنيه</span>
        </div>
        `;
    }
    
    document.getElementById('billDetails').innerHTML = details;
    document.getElementById('totalAmount').innerHTML = `الإجمالي: ${total} جنيه`;
    document.getElementById('billResult').style.display = 'block';
});
</script>
</body>
</html>
#### 2. إزاي تعدل البنود والأسعار بنفسك؟

افتح الكود ودور على الجزء ده:
<select id="service" required>
    <option value="100">كشف عادي - 100 جنيه</option>
    <option value="250">كشف استشاري - 250 جنيه</option>
- *value="100"*: ده السعر اللي هيتحسب في الفاتورة. غيره للرقم اللي انت عايزه.
- *كشف عادي - 100 جنيه*: ده النص اللي بيظهر للمستخدم. غيره زي ما تحب.

عايز تضيف بند جديد؟ انسخ السطر ده وحطه تحت أي بند:
<option value="السعر">اسم الخدمة - السعر جنيه</option>
عايز تغير اللون الأخضر للون تاني؟ غير القيم دي فوق في أول الكود:
:root {
    --main-color: #27ae60;  /* ده اللون الأساسي */
    --main-hover: #219150;  /* ده لون الزرار لما تدوس عليه */
}
ممكن تجرب ألوان زي: `#3498db` أزرق، `#e74c3c` أحمر، `#8e44ad` موف

#### 3. طريقة الرفع على جيت هب
1. روح `https://github.com/mkmal3307-boop/-`
2. اضغط `index.html` > علامة القلم *Edit*
3. امسح القديم وحط الجديد > *Commit changes*

استنى دقيقة وحدث الصفحة `https://mkmal3307-boop.github.io/-/` هتلاقي التغييرات ظهرت.

قولي عايز تغير اللون لإيه، أو إيه البنود اللي عايز تضيفها بالأسعار، وأنا أعدلهولك وأبعته جاهز على طول.
