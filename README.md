<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Plan Takip Sistemi</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');
    body {
      margin: 0;
      font-family: 'Inter', sans-serif;
      background: linear-gradient(135deg, #202334, #181c2f);
      color: white;
      line-height: 1.5;
    }
    #loginOverlay {
      position: fixed;
      inset: 0;
      background: linear-gradient(135deg, #2c3e50, #34495e);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 9999;
    }
    #loginBox {
      background: rgba(31, 34, 53, 0.95);
      padding: 40px;
      border-radius: 16px;
      box-shadow: 0 12px 24px rgba(0, 0, 0, 0.4);
      width: 360px;
      display: flex;
      flex-direction: column;
      gap: 20px;
      animation: fadeIn 0.5s ease;
      border: 1px solid #3c415c;
    }
    #loginBox h2 {
      margin: 0;
      font-size: 26px;
      text-align: center;
    }
    #loginBox input {
      padding: 12px;
      border-radius: 8px;
      border: 1px solid #444b66;
      background: #2b2f44;
      color: white;
      font-size: 15px;
      transition: all 0.3s ease;
    }
    #loginBox input:focus {
      outline: none;
      border-color: #4f8df9;
    }
    #loginBox button {
      padding: 12px;
      border-radius: 8px;
      border: none;
      background: #4f8df9;
      color: white;
      font-size: 16px;
      font-weight: 500;
      cursor: pointer;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
      transition: background 0.3s ease;
    }
    #loginBox button:hover {
      background: #2980b9;
    }
    #loginError {
      color: #e74c3c;
      display: none;
      text-align: center;
    }
    #mainApp {
      padding: 20px;
      background: rgba(255,255,255,0.02);
      border-top: 1px solid #3c415c;
    }
    .popup {
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: #242841;
      border-radius: 12px;
      padding: 20px;
      width: 420px;
      box-shadow: 0 8px 16px rgba(0,0,0,0.5);
      z-index: 99999;
    }
    .popup h2 {
      margin-top: 0;
    }
    .popup input, .popup select, .popup textarea {
      width: 100%;
      padding: 8px;
      margin-bottom: 8px;
      border-radius: 6px;
      border: 1px solid #444b66;
      background: #2b2f44;
      color: white;
      box-sizing: border-box;
    }
    .popup input:focus, .popup select:focus, .popup textarea:focus {
      outline: none;
      border-color: #4f8df9;
    }
    .popup button {
      padding: 10px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-weight: 500;
      transition: background 0.3s ease;
    }
    .btn-primary { background: #4f8df9; color: white; }
    .btn-success { background: #28a745; color: white; }
    .btn-warning { background: #ffa500; color: white; }
    .btn-secondary { background: #333; color: white; }
    .btn-purple { background: #6c5ce7; color: white; }
    .toolbar {
      margin-bottom: 16px;
      display: flex;
      align-items: center;
      gap: 0;
      flex-wrap: wrap;
      background: #4a5568;
      border-radius: 8px;
      padding: 0;
      position: relative;
      overflow: visible;
      z-index: 10000;
    }
    .toolbar::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      height: 4px;
      background: linear-gradient(90deg, #ff7a85, #4ade80, #8b5cf6, #06b6d4, #f59e0b);
    }
    .nav-button {
      background: transparent;
      color: white;
      border: none;
      padding: 16px 24px;
      cursor: pointer;
      font-weight: 500;
      transition: all 0.3s ease;
      position: relative;
      border-radius: 0;
      font-size: 14px;
    }
    .nav-button:hover {
      background: rgba(255,255,255,0.1);
    }
    .nav-button.active {
      background: linear-gradient(135deg, #ff7a85, #a855f7);
      color: white;
    }
    .dropdown-container {
      position: relative;
      display: inline-block;
      z-index: 10002;
    }
    .dropdown-menu {
      position: absolute;
      top: 100%;
      left: 50%;
      transform: translateX(-50%);
      background: linear-gradient(135deg, #ff7a85, #a855f7);
      border-radius: 8px;
      min-width: 200px;
      box-shadow: 0 8px 25px rgba(0,0,0,0.15);
      z-index: 10001;
      overflow: hidden;
      opacity: 0;
      visibility: hidden;
      transform: translateX(-50%) translateY(-10px);
      transition: all 0.3s ease;
    }
    .dropdown-menu.show {
      opacity: 1;
      visibility: visible;
      transform: translateX(-50%) translateY(0);
    }
    .dropdown-item {
      padding: 12px 20px;
      cursor: pointer;
      color: white;
      font-weight: 500;
      transition: background 0.2s ease;
      border-bottom: 1px solid rgba(255,255,255,0.1);
    }
    .dropdown-item:last-child {
      border-bottom: none;
    }
    .dropdown-item:hover {
      background: rgba(255,255,255,0.2);
    }
    .notification-bell {
      position: relative;
      cursor: pointer;
      padding: 16px 20px;
      color: white;
      transition: background 0.3s ease;
    }
    .notification-bell:hover {
      background: rgba(255,255,255,0.1);
    }
    .notification-bell {
      position: relative;
      cursor: pointer;
    }
    .notification-badge {
      position: absolute;
      top: -10px;
      left: -10px;
      background: red;
      color: white;
      border-radius: 50%;
      font-size: 12px;
      width: 20px;
      height: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    #plan-dropzone {
      width: 100%;
      height: 600px;
      border: 2px dashed #aaa;
      position: relative;
      overflow: hidden;
      background: rgba(255,255,255,0.05);
      border-radius: 8px;
    }
    #planImage {
      position: absolute;
      top: 0;
      left: 0;
      cursor: grab;
      max-width: none;
      transform-origin: top left;
    }
    .plus-button {
      position: absolute;
      width: 30px;
      height: 30px;
      background: #007bff;
      color: white;
      text-align: center;
      line-height: 30px;
      border-radius: 50%;
      cursor: pointer;
      z-index: 10;
      font-weight: bold;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-20px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>
  <div id="loginOverlay">
    <div id="loginBox">
      <h2>🔧 Plan Takip Sistemi</h2>
      <input type="text" id="loginUser" placeholder="Kullanıcı Adı" value="ermak">
      <input type="password" id="loginPass" placeholder="Şifre" value="ermak">
      <button onclick="doLogin()">Giriş Yap</button>
      <div id="loginError">❌ Kullanıcı adı veya şifre hatalı!</div>
      <div style="font-size: 12px; color: #888; text-align: center; margin-top: 10px;">
        Demo: ermak / ermak
      </div>
    </div>
  </div>

  <div id="mainApp" style="display:none">
    <h1>🔧 Plan Takip Sistemi</h1>
    
    <div class="toolbar">
      <div class="notification-bell" onclick="toggleNotifications()">
        <span id="notifBadge" class="notification-badge" style="display:none;">0</span>
        🔔 Bildirimler
        <div id="orderNotificationPopup" style="display:none; position:absolute; top:100%; left:0; background:#1e1e2f; padding:15px; border-radius:10px; width:300px; box-shadow:0 4px 8px rgba(0,0,0,0.5); z-index:9999; max-height:300px; overflow-y:auto;">
          <h4>📦 Gelen Siparişler</h4>
          <div id="orderList"></div>
        </div>
      </div>
      
      <button class="nav-button" onclick="openUserPanel()">👥 Kullanıcı Yönetimi</button>
      
      <div class="dropdown-container">
        <button class="nav-button" onclick="togglePartsDropdown()">🔧 Yedek Parça ▼</button>
        <div id="partsDropdown" class="dropdown-menu">
          <div class="dropdown-item" onclick="openOrderPanel()">📦 Parça Siparişi</div>
          <div class="dropdown-item" onclick="openPartAddPanel()">➕ Parça Ekle</div>
          <div class="dropdown-item" onclick="openPartListPanel()">📋 Parça Listesi</div>
        </div>
      </div>
      
      <button class="nav-button" onclick="openRequestListPanel()">📋 İstek Listesi</button>
      <button class="nav-button" onclick="openDailyReportPanel()">📝 Günlük Rapor</button>
      <button class="nav-button" onclick="openDailyActivitiesPanel()">📊 Günlük Faaliyetler</button>
    </div>

    <!-- Parça Sipariş Paneli -->
    <div id="orderPanelPopup" class="popup" style="display:none;">
      <h2>📦 Parça Sipariş Formu</h2>
      <input type="file" id="partImage" accept="image/*">
      <input type="text" id="partCode" placeholder="Parça Kodu">
      <input type="text" id="partSerial" placeholder="Seri Numarası">
      <select id="machineModel">
        <option value="">Makine Modeli Seçin</option>
        <option value="CS42">CS42</option>
        <option value="CS25">CS25</option>
        <option value="MX10">MX10</option>
      </select>
      <div style="position:relative;">
        <input type="text" id="partSearch" placeholder="🔍 Parça ara (isim veya kod)">
        <div id="searchResults" style="display:none; position:absolute; top:100%; left:0; right:0; background:#1e1e2f; border:1px solid #444; border-radius:6px; max-height:200px; overflow-y:auto; z-index:1000;"></div>
      </div>
      <button onclick="submitOrder()" class="btn-success" style="width:100%;">✅ Gönder</button>
      <button onclick="closeOrderPanel()" class="btn-secondary" style="margin-top:10px; width:100%;">❌ Kapat</button>
    </div>

    <!-- Kullanıcı Yönetim Paneli -->
    <div id="userPanelPopup" class="popup" style="display:none;">
      <h2>👥 Kullanıcı Yönetimi</h2>
      <input type="text" id="newUsername" placeholder="Yeni kullanıcı adı">
      <input type="password" id="newPassword" placeholder="Şifre">
      <input type="date" id="newHireDate" placeholder="İşe Giriş Tarihi">
      <input type="file" id="newAvatar" accept="image/*">
      <div style="margin-bottom:10px;">
        <label><input type="checkbox" id="permPlus"> ➕ Artı eklesin</label><br>
        <label><input type="checkbox" id="permNote"> 📝 Not yazsın</label><br>
        <label><input type="checkbox" id="permScale"> 📏 Ölçeklendirsin</label><br>
        <label><input type="checkbox" id="permPass"> 🔑 Şifre değiştirsin</label>
      </div>
      <button onclick="addUser()" class="btn-primary" style="width:100%;">➕ Kullanıcı Ekle</button>
      <div id="userList" style="margin-top:16px; max-height:180px; overflow-y:auto; border-top:1px solid #444; padding-top:10px;"></div>
      <button onclick="closeUserPanel()" class="btn-secondary" style="margin-top:16px; width:100%;">❌ Kapat</button>
    </div>
    
    <!-- İstek Listesi -->
    <div id="requestListPopup" class="popup" style="display:none; width:460px;">
      <h3>📋 Parça İstek Listesi</h3>
      <div id="requestListContainer"></div>
      <button onclick="closeRequestListPanel()" class="btn-secondary">❌ Kapat</button>
    </div>

    <!-- Parça Ekleme Paneli -->
    <div id="partAddPopup" class="popup" style="display:none;">
      <h2>➕ Parça Ekle</h2>
      <input type="text" id="partName" placeholder="Parça Adı">
      <input type="text" id="partCodeAdd" placeholder="Parça Kodu">
      <input type="file" id="partImageAdd" accept="image/*">
      <button onclick="addPart()" class="btn-success" style="width:100%;">💾 Kaydet</button>
      <button onclick="closePartAddPanel()" class="btn-secondary" style="margin-top:10px; width:100%;">❌ Kapat</button>
    </div>

    <!-- Parça Listesi -->
    <div id="partListPopup" class="popup" style="display:none; width:600px;">
      <h2>📋 Parça Listesi</h2>
      <div id="partListContainer" style="max-height:400px; overflow-y:auto; border:1px solid #444; border-radius:6px; padding:10px; background:#2b2f44;"></div>
      <button onclick="closePartListPanel()" class="btn-secondary" style="margin-top:16px; width:100%;">❌ Kapat</button>
    </div>

    <!-- Günlük Rapor Paneli -->
    <div id="dailyReportPopup" class="popup" style="display:none; width:700px; max-height:80vh; overflow-y:auto;">
      <h2>📝 Günlük Rapor Formu</h2>
      <div style="background:#2b2f44; padding:20px; border-radius:8px; margin-bottom:15px;">
        <div style="display:grid; grid-template-columns:1fr 1fr; gap:15px; margin-bottom:15px;">
          <div>
            <label style="color:white; display:block; margin-bottom:5px;">🏢 Firma Adı</label>
            <input type="text" id="companyName" placeholder="Firma adını girin" style="width:100%; padding:10px; border:1px solid #444; border-radius:6px; background:#1e1e2f; color:white; box-sizing:border-box;">
          </div>
          <div>
            <label style="color:white; display:block; margin-bottom:5px;">📅 Tarih</label>
            <input type="date" id="reportDate" style="width:100%; padding:10px; border:1px solid #444; border-radius:6px; background:#1e1e2f; color:white; box-sizing:border-box;">
          </div>
        </div>
        
        <div style="margin-bottom:15px;">
          <label style="color:white; display:block; margin-bottom:5px;">📝 Rapor İçeriği</label>
          <div id="reportEditor" style="background:white; border:1px solid #444; border-radius:6px; min-height:300px; padding:15px; color:#333; font-family:Arial, sans-serif; line-height:1.6;">
            <div style="border-bottom:1px solid #ddd; padding-bottom:10px; margin-bottom:15px;">
              <button type="button" onclick="formatText('bold')" style="margin-right:5px; padding:5px 10px; border:1px solid #ddd; background:#f5f5f5; cursor:pointer; border-radius:3px;"><b>B</b></button>
              <button type="button" onclick="formatText('italic')" style="margin-right:5px; padding:5px 10px; border:1px solid #ddd; background:#f5f5f5; cursor:pointer; border-radius:3px;"><i>I</i></button>
              <button type="button" onclick="formatText('underline')" style="margin-right:5px; padding:5px 10px; border:1px solid #ddd; background:#f5f5f5; cursor:pointer; border-radius:3px;"><u>U</u></button>
              <button type="button" onclick="insertList()" style="margin-right:5px; padding:5px 10px; border:1px solid #ddd; background:#f5f5f5; cursor:pointer; border-radius:3px;">📋 Liste</button>
              <select onchange="changeFontSize(this.value)" style="margin-left:10px; padding:5px;">
                <option value="">Font Boyutu</option>
                <option value="12px">12px</option>
                <option value="14px">14px</option>
                <option value="16px">16px</option>
                <option value="18px">18px</option>
                <option value="20px">20px</option>
              </select>
            </div>
            <div id="editorContent" contenteditable="true" style="min-height:250px; outline:none; padding:10px; border:1px solid #eee; border-radius:4px;">
              <h3>📋 Günlük Faaliyet Raporu</h3>
              <p><strong>Tarih:</strong> <span id="dateDisplay"></span></p>
              <p><strong>Firma:</strong> <span id="companyDisplay"></span></p>
              
              <h4>🔧 Yapılan İşlemler:</h4>
              <ul>
                <li>İşlem 1</li>
                <li>İşlem 2</li>
                <li>İşlem 3</li>
              </ul>
              
              <h4>📊 Durum Bilgisi:</h4>
              <p>Bugünkü çalışmalar hakkında detaylı bilgi...</p>
              
              <h4>📌 Notlar:</h4>
              <p>Önemli notlar ve gelecek planlar...</p>
            </div>
          </div>
        </div>

        <div style="margin-bottom:15px;">
          <label style="color:white; display:block; margin-bottom:5px;">📷 Rapor Görselleri</label>
          <input type="file" id="reportImages" accept="image/*" multiple style="width:100%; padding:8px; border:1px solid #444; border-radius:6px; background:#1e1e2f; color:white;">
          <div id="reportImagePreview" style="display:flex; gap:10px; flex-wrap:wrap; margin-top:10px;"></div>
        </div>

        <div style="display:flex; gap:10px;">
          <button onclick="submitDailyReport()" class="btn-success" style="flex:1; padding:12px; font-size:16px;">📤 Raporu Gönder</button>
          <button onclick="previewReport()" style="background:#3498db; color:white; border:none; padding:12px; border-radius:6px; cursor:pointer; flex:1;">👁️ Önizleme</button>
          <button onclick="closeDailyReportPanel()" class="btn-secondary" style="padding:12px;">❌ Kapat</button>
        </div>
      </div>
    </div>

    <!-- Rapor Önizleme -->
    <div id="reportPreviewPopup" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; color:#333; padding:30px; border-radius:12px; width:80%; max-width:800px; max-height:80vh; overflow-y:auto; box-shadow:0 8px 32px rgba(0,0,0,0.3); z-index:99999;">
      <div style="text-align:right; margin-bottom:20px;">
        <button onclick="closeReportPreview()" style="background:#e74c3c; color:white; border:none; padding:8px 12px; border-radius:4px; cursor:pointer;">❌ Kapat</button>
        <button onclick="printReport()" style="background:#2ecc71; color:white; border:none; padding:8px 12px; border-radius:4px; cursor:pointer; margin-left:10px;">🖨️ Yazdır</button>
      </div>
      <div id="reportPreviewContent" style="background:white; padding:20px; border:1px solid #ddd; border-radius:8px; font-family:Arial, sans-serif; line-height:1.6;"></div>
    </div>

    <!-- Günlük Faaliyetler Paneli -->
    <div id="dailyActivitiesPopup" class="popup" style="display:none; width:900px; max-height:80vh; overflow-y:auto;">
      <h2>📊 Günlük Faaliyetler</h2>
      
      <!-- Filtre ve Arama -->
      <div style="background:#2b2f44; padding:15px; border-radius:8px; margin-bottom:15px; display:flex; gap:15px; align-items:center; flex-wrap:wrap;">
        <div>
          <label style="color:white; font-size:14px;">🔍 Firma Ara:</label>
          <input type="text" id="companyFilter" placeholder="Firma adı..." style="padding:8px; border:1px solid #444; border-radius:4px; background:#1e1e2f; color:white; margin-left:5px;">
        </div>
        <div>
          <label style="color:white; font-size:14px;">📅 Tarih:</label>
          <input type="date" id="dateFilter" style="padding:8px; border:1px solid #444; border-radius:4px; background:#1e1e2f; color:white; margin-left:5px;">
        </div>
        <button onclick="filterReports()" style="background:#3498db; color:white; border:none; padding:8px 15px; border-radius:4px; cursor:pointer;">🔍 Filtrele</button>
        <button onclick="clearFilters()" style="background:#95a5a6; color:white; border:none; padding:8px 15px; border-radius:4px; cursor:pointer;">🗑️ Temizle</button>
      </div>

      <!-- Rapor Listesi -->
      <div id="reportsContainer" style="max-height:500px; overflow-y:auto; border:1px solid #444; border-radius:8px; background:#2b2f44;">
        <div id="reportsList"></div>
      </div>

      <!-- Alt Butonlar -->
      <div style="margin-top:15px; display:flex; gap:10px; justify-content:flex-end;">
        <button onclick="exportReports()" style="background:#f39c12; color:white; border:none; padding:10px 15px; border-radius:6px; cursor:pointer;">📥 Dışa Aktar</button>
        <button onclick="closeDailyActivitiesPanel()" class="btn-secondary">❌ Kapat</button>
      </div>
    </div>

    <!-- Rapor Detay Modal -->
    <div id="reportDetailModal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:white; color:#333; padding:20px; border-radius:12px; width:80%; max-width:700px; max-height:80vh; overflow-y:auto; box-shadow:0 8px 32px rgba(0,0,0,0.3); z-index:100000;">
      <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px; border-bottom:1px solid #ddd; padding-bottom:15px;">
        <h3 style="margin:0;">📋 Rapor Detayı</h3>
        <div>
          <button onclick="printDetailReport()" style="background:#2ecc71; color:white; border:none; padding:8px 12px; border-radius:4px; cursor:pointer; margin-right:10px;">🖨️ Yazdır</button>
          <button onclick="closeReportDetail()" style="background:#e74c3c; color:white; border:none; padding:8px 12px; border-radius:4px; cursor:pointer;">❌ Kapat</button>
        </div>
      </div>
      <div id="reportDetailContent"></div>
    </div>

    <!-- Plan Kontrolleri -->
    <div style="margin: 20px 0; padding: 15px; background: rgba(255,255,255,0.05); border-radius: 8px;">
      <h3>🗺️ Plan Yönetimi</h3>
      <input type="file" id="planInput" accept="image/*" multiple style="margin-right: 10px;">
      <button id="addPlusMode" class="btn-primary">➕ Artı Nokta Ekle</button>
      <button id="toggleMove" class="btn-secondary">👆 Konumlandırma</button>
      <button id="toggleScale" class="btn-secondary">📏 Ölçeklendirme</button>
      <div id="scaleControls" style="margin-top:10px; display:none;">
        Genişlik(px): <input type="number" id="imgWidth" value="1000" style="width: 100px;">
        Yükseklik(px): <input type="number" id="imgHeight" value="600" style="width: 100px;">
        <button id="applySize" class="btn-primary">✅ Uygula</button>
      </div>
    </div>

    <!-- Plan Alanı -->
    <div id="plan-dropzone">
      <img id="planImage" src="">
      <div style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: #666; font-size: 18px; text-align: center;">
        📁 Plan resmi yüklemek için yukarıdaki dosya seçici kullanın
      </div>
    </div>

    <!-- Modal -->
    <div id="modal" style="display:none; position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:#1e1e2f; padding:20px; border-radius:10px; z-index:9999; width:400px;">
      <h4>📝 Parça Bilgisi</h4>
      <textarea id="noteArea" maxlength="5000" placeholder="Notunuzu buraya yazın..." style="width:100%; height:80px; margin-bottom:10px; background:#2b2f44; color:white; border:1px solid #444b66; border-radius:6px; padding:8px; box-sizing:border-box;"></textarea>
      <button id="saveNote" class="btn-primary" style="width:100%; margin-bottom:10px;">💾 Notu Kaydet</button>
      <div id="noteList" style="border:1px solid #444; padding:10px; margin-bottom:10px; max-height:150px; overflow-y:auto; background:#2b2f44; border-radius:6px;"></div>
      <label style="color:white;">🖼️ Not Görseli Ekle (En fazla 4)</label>
      <input type="file" id="noteImageInput" accept="image/*" multiple style="margin-bottom:10px;">
      <div id="noteImagePreview" style="display:flex; gap:10px; flex-wrap:wrap; margin-bottom:10px;"></div>
      <label style="color:white;">📎 Alt Resim Ekle</label>
      <input type="file" id="subImageInput" accept="image/*">
      <div id="preview-container" style="display:flex; gap:10px; flex-wrap:wrap; margin:10px 0;"></div>
      <button id="saveSubImage" class="btn-success">💾 Kaydet</button>
      <button onclick="closeModal()" class="btn-secondary" style="margin-left:10px;">❌ Kapat</button>
    </div>
  </div>

<script>
// Global değişkenler
var addMode = false;
var currentButton = null;
var subImageMap = new Map();
var isDragging = false;
var dragStartX, dragStartY, imageStartX = 0, imageStartY = 0;
var orderCount = 0;
var partsList = [];
var dailyReports = []; // Günlük raporları saklamak için

// Login fonksiyonu
function doLogin() {
  var user = document.getElementById('loginUser').value.trim();
  var pass = document.getElementById('loginPass').value.trim();
  if (user === 'ermak' && pass === 'ermak') {
    document.getElementById('loginOverlay').style.display = 'none';
    document.getElementById('mainApp').style.display = 'block';
  } else {
    document.getElementById('loginError').style.display = 'block';
  }
}

// Panel fonksiyonları
function openUserPanel() {
  document.getElementById('userPanelPopup').style.display = 'block';
}
function closeUserPanel() {
  document.getElementById('userPanelPopup').style.display = 'none';
}
function openOrderPanel() {
  document.getElementById('orderPanelPopup').style.display = 'block';
  document.getElementById('partsDropdown').style.display = 'none';
  setupPartSearch();
}
function closeOrderPanel() {
  document.getElementById('orderPanelPopup').style.display = 'none';
}
function openRequestListPanel() {
  document.getElementById('requestListPopup').style.display = 'block';
}
function closeRequestListPanel() {
  document.getElementById('requestListPopup').style.display = 'none';
}
function openPartAddPanel() {
  document.getElementById('partAddPopup').style.display = 'block';
  document.getElementById('partsDropdown').style.display = 'none';
}
function closePartAddPanel() {
  document.getElementById('partAddPopup').style.display = 'none';
}
function openPartListPanel() {
  renderPartsList();
  document.getElementById('partListPopup').style.display = 'block';
  document.getElementById('partsDropdown').style.display = 'none';
}
function closePartListPanel() {
  document.getElementById('partListPopup').style.display = 'none';
}
function closeModal() {
  document.getElementById('modal').style.display = 'none';
}

// Günlük rapor fonksiyonları
function openDailyReportPanel() {
  document.getElementById('dailyReportPopup').style.display = 'block';
  document.getElementById('reportDate').value = new Date().toISOString().split('T')[0];
  updateReportDisplay();
}

function closeDailyReportPanel() {
  document.getElementById('dailyReportPopup').style.display = 'none';
}

function updateReportDisplay() {
  var companyName = document.getElementById('companyName').value || '[Firma Adı]';
  var reportDate = document.getElementById('reportDate').value || '[Tarih]';
  
  document.getElementById('companyDisplay').textContent = companyName;
  document.getElementById('dateDisplay').textContent = new Date(reportDate).toLocaleDateString('tr-TR');
}

function formatText(command) {
  document.execCommand(command, false, null);
  document.getElementById('editorContent').focus();
}

function insertList() {
  document.execCommand('insertUnorderedList', false, null);
  document.getElementById('editorContent').focus();
}

function changeFontSize(size) {
  if (size) {
    document.execCommand('fontSize', false, '7');
    var fontElements = document.getElementsByTagName('font');
    for (var i = 0; i < fontElements.length; i++) {
      if (fontElements[i].size === '7') {
        fontElements[i].removeAttribute('size');
        fontElements[i].style.fontSize = size;
      }
    }
  }
  document.getElementById('editorContent').focus();
}

function previewReport() {
  var content = document.getElementById('editorContent').innerHTML;
  var images = document.getElementById('reportImages').files;
  
  var previewContent = '<div style="text-align:center; margin-bottom:30px;"><h2>📋 Günlük Faaliyet Raporu</h2></div>';
  previewContent += content;
  
  if (images.length > 0) {
    previewContent += '<div style="margin-top:30px;"><h4>📷 Ekli Görseller:</h4><div style="display:flex; flex-wrap:wrap; gap:10px;">';
    for (var i = 0; i < images.length; i++) {
      var reader = new FileReader();
      reader.onload = function(e) {
        var imgDiv = '<img src="' + e.target.result + '" style="max-width:200px; max-height:150px; border:1px solid #ddd; border-radius:4px; object-fit:cover;">';
        document.getElementById('reportPreviewContent').innerHTML += imgDiv;
      };
      reader.readAsDataURL(images[i]);
    }
    previewContent += '</div></div>';
  }
  
  document.getElementById('reportPreviewContent').innerHTML = previewContent;
  document.getElementById('reportPreviewPopup').style.display = 'block';
}

function closeReportPreview() {
  document.getElementById('reportPreviewPopup').style.display = 'none';
}

function printReport() {
  var printContent = document.getElementById('reportPreviewContent').innerHTML;
  var printWindow = window.open('', '_blank');
  printWindow.document.write('<html><head><title>Günlük Rapor</title></head><body style="font-family:Arial,sans-serif; line-height:1.6; padding:20px;">');
  printWindow.document.write(printContent);
  printWindow.document.write('</body></html>');
  printWindow.document.close();
  printWindow.print();
}

function submitDailyReport() {
  var companyName = document.getElementById('companyName').value.trim();
  var reportDate = document.getElementById('reportDate').value;
  var content = document.getElementById('editorContent').innerHTML;
  var images = document.getElementById('reportImages').files;
  
  if (!companyName || !reportDate) {
    alert('❌ Lütfen firma adı ve tarih alanlarını doldurun');
    return;
  }
  
  if (!content.trim()) {
    alert('❌ Lütfen rapor içeriği yazın');
    return;
  }
  
  // Raporu kaydet
  var report = {
    id: Date.now(),
    company: companyName,
    date: reportDate,
    content: content,
    images: Array.from(images).map(function(file) {
      return {
        name: file.name,
        size: file.size,
        type: file.type
      };
    }),
    imageCount: images.length,
    createdAt: new Date().toLocaleString('tr-TR'),
    submittedBy: 'ermak', // Mevcut kullanıcı
    status: 'Gönderildi'
  };
  
  // Raporları listeye ekle
  dailyReports.push(report);
  
  alert('✅ Günlük rapor başarıyla gönderildi!\n\n📋 Rapor Özeti:\n' + 
        '🏢 Firma: ' + companyName + '\n' +
        '📅 Tarih: ' + new Date(reportDate).toLocaleDateString('tr-TR') + '\n' +
        '📷 Görsel Sayısı: ' + images.length);
  
  // Formu temizle
  document.getElementById('companyName').value = '';
  document.getElementById('reportDate').value = new Date().toISOString().split('T')[0];
  document.getElementById('editorContent').innerHTML = '<h3>📋 Günlük Faaliyet Raporu</h3><p><strong>Tarih:</strong> <span id="dateDisplay"></span></p><p><strong>Firma:</strong> <span id="companyDisplay"></span></p><h4>🔧 Yapılan İşlemler:</h4><ul><li>İşlem 1</li><li>İşlem 2</li><li>İşlem 3</li></ul><h4>📊 Durum Bilgisi:</h4><p>Bugünkü çalışmalar hakkında detaylı bilgi...</p><h4>📌 Notlar:</h4><p>Önemli notlar ve gelecek planlar...</p>';
  document.getElementById('reportImages').value = '';
  document.getElementById('reportImagePreview').innerHTML = '';
  
  closeDailyReportPanel();
}

// Günlük faaliyetler fonksiyonları
function openDailyActivitiesPanel() {
  document.getElementById('dailyActivitiesPopup').style.display = 'block';
  loadDailyReports();
}

function closeDailyActivitiesPanel() {
  document.getElementById('dailyActivitiesPopup').style.display = 'none';
}

function loadDailyReports() {
  // Demo raporları ekle (eğer liste boşsa)
  if (dailyReports.length === 0) {
    var demoReports = [
      {
        id: 1,
        company: 'ABC Makina Ltd.',
        date: '2025-05-23',
        content: '<h3>📋 Günlük Faaliyet Raporu</h3><p><strong>Tarih:</strong> 23/05/2025</p><p><strong>Firma:</strong> ABC Makina Ltd.</p><h4>🔧 Yapılan İşlemler:</h4><ul><li>CS42 makinesinde bakım yapıldı</li><li>Yedek parça değişimi gerçekleştirildi</li><li>Sistem testleri tamamlandı</li></ul><h4>📊 Durum Bilgisi:</h4><p>Tüm sistemler normale döndü. Performans %95 seviyesinde.</p><h4>📌 Notlar:</h4><p>Gelecek hafta rutin kontrol planlanıyor.</p>',
        imageCount: 3,
        createdAt: '23/05/2025 09:15:32',
        submittedBy: 'ahmet.yilmaz',
        status: 'Tamamlandı'
      },
      {
        id: 2,
        company: 'XYZ Sanayi A.Ş.',
        date: '2025-05-23',
        content: '<h3>📋 Günlük Faaliyet Raporu</h3><p><strong>Tarih:</strong> 23/05/2025</p><p><strong>Firma:</strong> XYZ Sanayi A.Ş.</p><h4>🔧 Yapılan İşlemler:</h4><ul><li>MX10 serisi kurulum</li><li>Personel eğitimi verildi</li><li>Kalibrasyon ayarları yapıldı</li></ul><h4>📊 Durum Bilgisi:</h4><p>Kurulum başarıyla tamamlandı. Sistem çalışır durumda.</p><h4>📌 Notlar:</h4><p>2 hafta sonra kontrol ziyareti planlandı.</p>',
        imageCount: 2,
        createdAt: '23/05/2025 14:30:18',
        submittedBy: 'mehmet.can',
        status: 'Beklemede'
      },
      {
        id: 3,
        company: 'DEF Teknoloji',
        date: '2025-05-22',
        content: '<h3>📋 Günlük Faaliyet Raporu</h3><p><strong>Tarih:</strong> 22/05/2025</p><p><strong>Firma:</strong> DEF Teknoloji</p><h4>🔧 Yapılan İşlemler:</h4><ul><li>CS25 arıza analizi</li><li>Elektronik kart değişimi</li><li>Yazılım güncellemesi</li></ul><h4>📊 Durum Bilgisi:</h4><p>Arıza giderildi. Sistem stabil çalışıyor.</p><h4>📌 Notlar:</h4><p>Müşteri memnuniyeti yüksek.</p>',
        imageCount: 1,
        createdAt: '22/05/2025 16:45:55',
        submittedBy: 'ermak',
        status: 'Tamamlandı'
      }
    ];
    dailyReports = dailyReports.concat(demoReports);
  }
  
  displayReports(dailyReports);
}

function displayReports(reports) {
  var container = document.getElementById('reportsList');
  container.innerHTML = '';
  
  if (reports.length === 0) {
    container.innerHTML = '<div style="text-align:center; padding:40px; color:#aaa;"><h3>📋 Henüz rapor bulunmuyor</h3><p>İlk günlük raporu oluşturmak için "Günlük Rapor" butonunu kullanın.</p></div>';
    return;
  }
  
  reports.forEach(function(report) {
    var reportDiv = document.createElement('div');
    reportDiv.style.cssText = 'padding:15px; border-bottom:1px solid #444; cursor:pointer; transition:background 0.2s; position:relative;';
    reportDiv.onmouseover = function() { this.style.background = 'rgba(255,255,255,0.05)'; };
    reportDiv.onmouseout = function() { this.style.background = 'transparent'; };
    
    var statusColor = report.status === 'Tamamlandı' ? '#2ecc71' : report.status === 'Beklemede' ? '#f39c12' : '#3498db';
    
    reportDiv.innerHTML = 
      '<div style="display:flex; justify-content:space-between; align-items:start; margin-bottom:10px;">' +
        '<div style="flex:1;">' +
          '<h4 style="margin:0 0 5px 0; color:white;">🏢 ' + report.company + '</h4>' +
          '<div style="font-size:14px; color:#aaa; margin-bottom:8px;">📅 ' + new Date(report.date).toLocaleDateString('tr-TR') + ' • 👤 ' + report.submittedBy + ' • 🕒 ' + report.createdAt + '</div>' +
          '<div style="display:flex; gap:15px; font-size:13px; color:#bbb;">' +
            '<span>📷 ' + report.imageCount + ' görsel</span>' +
            '<span style="color:' + statusColor + '; font-weight:500;">● ' + report.status + '</span>' +
          '</div>' +
        '</div>' +
        '<div style="display:flex; gap:5px;">' +
          '<button onclick="viewReportDetail(' + report.id + '); event.stopPropagation();" style="background:#3498db; color:white; border:none; padding:5px 10px; border-radius:4px; cursor:pointer; font-size:12px;">👁️ Görüntüle</button>' +
          '<button onclick="deleteReport(' + report.id + '); event.stopPropagation();" style="background:#e74c3c; color:white; border:none; padding:5px 10px; border-radius:4px; cursor:pointer; font-size:12px;">🗑️ Sil</button>' +
        '</div>' +
      '</div>';
    
    reportDiv.onclick = function() {
      viewReportDetail(report.id);
    };
    
    container.appendChild(reportDiv);
  });
}

function viewReportDetail(reportId) {
  var report = dailyReports.find(function(r) { return r.id == reportId; });
  if (!report) return;
  
  var content = 
    '<div style="margin-bottom:20px; padding:15px; background:#f8f9fa; border-radius:8px;">' +
      '<div style="display:grid; grid-template-columns:1fr 1fr; gap:15px; margin-bottom:15px;">' +
        '<div><strong>🏢 Firma:</strong> ' + report.company + '</div>' +
        '<div><strong>📅 Tarih:</strong> ' + new Date(report.date).toLocaleDateString('tr-TR') + '</div>' +
        '<div><strong>👤 Gönderen:</strong> ' + report.submittedBy + '</div>' +
        '<div><strong>🕒 Gönderim Zamanı:</strong> ' + report.createdAt + '</div>' +
        '<div><strong>📷 Görsel Sayısı:</strong> ' + report.imageCount + '</div>' +
        '<div><strong>📊 Durum:</strong> <span style="color:#2ecc71; font-weight:500;">' + report.status + '</span></div>' +
      '</div>' +
    '</div>' +
    '<div style="border:1px solid #ddd; border-radius:8px; padding:20px; background:white;">' +
      report.content +
    '</div>';
  
  document.getElementById('reportDetailContent').innerHTML = content;
  document.getElementById('reportDetailModal').style.display = 'block';
}

function closeReportDetail() {
  document.getElementById('reportDetailModal').style.display = 'none';
}

function printDetailReport() {
  var content = document.getElementById('reportDetailContent').innerHTML;
  var printWindow = window.open('', '_blank');
  printWindow.document.write('<html><head><title>Günlük Rapor Detayı</title></head><body style="font-family:Arial,sans-serif; line-height:1.6; padding:20px;">');
  printWindow.document.write(content);
  printWindow.document.write('</body></html>');
  printWindow.document.close();
  printWindow.print();
}

function deleteReport(reportId) {
  if (confirm('🗑️ Bu raporu silmek istediğinizden emin misiniz?')) {
    dailyReports = dailyReports.filter(function(r) { return r.id != reportId; });
    displayReports(dailyReports);
    alert('✅ Rapor başarıyla silindi');
  }
}

function filterReports() {
  var companyFilter = document.getElementById('companyFilter').value.toLowerCase();
  var dateFilter = document.getElementById('dateFilter').value;
  
  var filteredReports = dailyReports.filter(function(report) {
    var matchCompany = !companyFilter || report.company.toLowerCase().includes(companyFilter);
    var matchDate = !dateFilter || report.date === dateFilter;
    return matchCompany && matchDate;
  });
  
  displayReports(filteredReports);
}

function clearFilters() {
  document.getElementById('companyFilter').value = '';
  document.getElementById('dateFilter').value = '';
  displayReports(dailyReports);
}

function exportReports() {
  if (dailyReports.length === 0) {
    alert('❌ Dışa aktarılacak rapor bulunmuyor');
    return;
  }
  
  var exportData = 'Günlük Raporlar Listesi\n\n';
  dailyReports.forEach(function(report) {
    exportData += '🏢 Firma: ' + report.company + '\n';
    exportData += '📅 Tarih: ' + new Date(report.date).toLocaleDateString('tr-TR') + '\n';
    exportData += '👤 Gönderen: ' + report.submittedBy + '\n';
    exportData += '🕒 Gönderim: ' + report.createdAt + '\n';
    exportData += '📷 Görsel: ' + report.imageCount + '\n';
    exportData += '📊 Durum: ' + report.status + '\n';
    exportData += '------------------------\n\n';
  });
  
  var blob = new Blob([exportData], { type: 'text/plain' });
  var url = window.URL.createObjectURL(blob);
  var a = document.createElement('a');
  a.href = url;
  a.download = 'gunluk_raporlar_' + new Date().toISOString().split('T')[0] + '.txt';
  a.click();
  window.URL.revokeObjectURL(url);
  
  alert('✅ Raporlar başarıyla dışa aktarıldı');
}

// Dropdown fonksiyonları
function toggleNotifications() {
  var popup = document.getElementById('orderNotificationPopup');
  popup.style.display = popup.style.display === 'none' ? 'block' : 'none';
}

function togglePartsDropdown() {
  var dropdown = document.getElementById('partsDropdown');
  dropdown.classList.toggle('show');
}

function setActiveButton(buttonElement) {
  // Tüm butonlardan active class'ını kaldır
  var buttons = document.querySelectorAll('.nav-button');
  buttons.forEach(function(btn) {
    btn.classList.remove('active');
  });
  
  // Tıklanan butona active class'ı ekle
  if (buttonElement) {
    buttonElement.classList.add('active');
  }
}

// Sipariş fonksiyonları
function submitOrder() {
  var model = document.getElementById('machineModel').value;
  var code = document.getElementById('partCode').value;
  var serial = document.getElementById('partSerial').value;
  
  if (!model || !code || !serial) {
    alert('❌ Lütfen tüm alanları doldurun');
    return;
  }

  orderCount++;
  var now = new Date().toLocaleString('tr-TR');
  var username = 'ermak';

  var entry = document.createElement('div');
  entry.style.padding = '8px';
  entry.style.borderBottom = '1px solid #444';
  entry.innerHTML = '<strong>' + username + '</strong> - ' + now + '<br><small>Model: ' + model + ', Kod: ' + code + ', Seri: ' + serial + '</small>';
  
  var orderList = document.getElementById('orderList');
  if (orderList.firstChild) {
    orderList.insertBefore(entry, orderList.firstChild);
  } else {
    orderList.appendChild(entry);
  }
  
  var badge = document.getElementById('notifBadge');
  badge.style.display = 'flex';
  badge.textContent = orderCount;

  var reqList = document.getElementById('requestListContainer');
  var reqEntry = document.createElement('div');
  reqEntry.style.padding = '10px';
  reqEntry.style.borderBottom = '1px solid #555';
  reqEntry.style.marginBottom = '6px';
  reqEntry.innerHTML = '<div><strong>' + username + '</strong> • ' + now + '</div><div style="font-size:13px; color:#aaa;">Model: ' + model + ', Kod: ' + code + ', Seri: ' + serial + '</div><div style="margin-top:6px;"><button onclick="approveRequest(this, \'' + username + '\')" style="padding:5px 10px; font-size:13px; border:none; background:#00b894; color:white; border-radius:4px; cursor:pointer;">✅ Onayla</button></div>';
  
  if (reqList.firstChild) {
    reqList.insertBefore(reqEntry, reqList.firstChild);
  } else {
    reqList.appendChild(reqEntry);
  }

  alert('✅ Sipariş başarıyla gönderildi!');
  
  document.getElementById('partCode').value = '';
  document.getElementById('partSerial').value = '';
  document.getElementById('machineModel').value = '';
  document.getElementById('partSearch').value = '';
  
  closeOrderPanel();
}

function approveRequest(btn, approvedBy) {
  var now = new Date().toLocaleTimeString('tr-TR');
  btn.parentElement.innerHTML = '<em style="color: #00b894;">✅ Hazırlanıyor (Onaylayan: ' + approvedBy + ', ' + now + ')</em>';
}

// Kullanıcı yönetimi
function addUser() {
  var username = document.getElementById('newUsername').value.trim();
  var password = document.getElementById('newPassword').value.trim();
  var permissions = {
    plus: document.getElementById('permPlus').checked,
    note: document.getElementById('permNote').checked,
    scale: document.getElementById('permScale').checked,
    changePass: document.getElementById('permPass').checked
  };
  if (!username || !password) return alert('❌ Kullanıcı adı ve şifre boş olamaz');
  
  var userList = document.getElementById('userList');
  var rawDate = document.getElementById('newHireDate').value;
  var hireDate = rawDate ? new Date(rawDate).toLocaleDateString('tr-TR') : '';
  var avatarInput = document.getElementById('newAvatar');
  var avatarURL = avatarInput.files[0] ? URL.createObjectURL(avatarInput.files[0]) : null;

  var entry = document.createElement('div');
  entry.style.padding = '10px';
  entry.style.borderBottom = '1px solid #444';
  entry.style.display = 'flex';
  entry.style.alignItems = 'center';
  entry.style.gap = '10px';

  var avatar = document.createElement('img');
  avatar.src = avatarURL || 'https://via.placeholder.com/40x40/4f8df9/ffffff?text=👤';
  avatar.style.width = '40px';
  avatar.style.height = '40px';
  avatar.style.borderRadius = '50%';
  avatar.style.objectFit = 'cover';

  var userInfo = document.createElement('div');
  var permList = Object.entries(permissions).filter(function(item) { return item[1]; }).map(function(item) { return item[0]; }).join(', ') || 'Yok';
  userInfo.innerHTML = '<strong>👤 ' + username + '</strong><br><span style="font-size:12px; color:#aaa;">📅 Tarih: ' + (hireDate || 'Belirtilmemiş') + '<br>🔐 Yetkiler: ' + permList + '</span>';

  entry.appendChild(avatar);
  entry.appendChild(userInfo);
  userList.appendChild(entry);
  
  document.getElementById('newUsername').value = '';
  document.getElementById('newPassword').value = '';
  document.getElementById('permPlus').checked = false;
  document.getElementById('permNote').checked = false;
  document.getElementById('permScale').checked = false;
  document.getElementById('permPass').checked = false;
  document.getElementById('newHireDate').value = '';
  document.getElementById('newAvatar').value = '';
  
  alert('✅ Kullanıcı başarıyla eklendi!');
}

// Parça yönetimi
function addPart() {
  var name = document.getElementById('partName').value.trim();
  var code = document.getElementById('partCodeAdd').value.trim();
  var imageFile = document.getElementById('partImageAdd').files[0];
  
  if (!name || !code) {
    alert('❌ Lütfen parça adı ve kodu girin');
    return;
  }

  var part = {
    id: Date.now(),
    name: name,
    code: code,
    image: null,
    addedDate: new Date().toLocaleString('tr-TR')
  };

  if (imageFile) {
    var reader = new FileReader();
    reader.onload = function(e) {
      part.image = e.target.result;
      partsList.push(part);
      alert('✅ Parça başarıyla eklendi!');
      clearPartForm();
      closePartAddPanel();
    };
    reader.readAsDataURL(imageFile);
  } else {
    partsList.push(part);
    alert('✅ Parça başarıyla eklendi!');
    clearPartForm();
    closePartAddPanel();
  }
}

function clearPartForm() {
  document.getElementById('partName').value = '';
  document.getElementById('partCodeAdd').value = '';
  document.getElementById('partImageAdd').value = '';
}

function renderPartsList() {
  var container = document.getElementById('partListContainer');
  container.innerHTML = '';
  
  if (partsList.length === 0) {
    container.innerHTML = '<div style="text-align:center; color:#aaa; padding:20px;">📦 Henüz parça eklenmemiş</div>';
    return;
  }

  for (var i = 0; i < partsList.length; i++) {
    var part = partsList[i];
    var partDiv = document.createElement('div');
    partDiv.style.cssText = 'display:flex; align-items:center; gap:15px; padding:15px; border-bottom:1px solid #555; background:rgba(255,255,255,0.02); margin-bottom:10px; border-radius:6px;';
    
    var imageDiv = document.createElement('div');
    if (part.image) {
      var img = document.createElement('img');
      img.src = part.image;
      img.style.cssText = 'width:60px; height:60px; object-fit:cover; border-radius:6px; border:1px solid #666;';
      imageDiv.appendChild(img);
    } else {
      imageDiv.innerHTML = '<div style="width:60px; height:60px; background:#444; border-radius:6px; display:flex; align-items:center; justify-content:center; color:#aaa; font-size:12px;">🖼️ Resim Yok</div>';
    }
    
    var infoDiv = document.createElement('div');
    infoDiv.style.flex = '1';
    infoDiv.innerHTML = '<div style="font-weight:600; font-size:16px; margin-bottom:4px;">🔧 ' + part.name + '</div><div style="color:#aaa; font-size:14px; margin-bottom:2px;">📋 Kod: ' + part.code + '</div><div style="color:#888; font-size:12px;">📅 Eklenme: ' + part.addedDate + '</div>';
    
    var deleteBtn = document.createElement('button');
    deleteBtn.textContent = '🗑️ Sil';
    deleteBtn.style.cssText = 'padding:6px 12px; background:#e74c3c; color:white; border:none; border-radius:4px; cursor:pointer; font-size:12px;';
    deleteBtn.onclick = (function(partId) {
      return function() {
        if (confirm('🗑️ Bu parçayı silmek istediğinizden emin misiniz?')) {
          partsList = partsList.filter(function(p) { return p.id !== partId; });
          renderPartsList();
        }
      };
    })(part.id);
    
    partDiv.appendChild(imageDiv);
    partDiv.appendChild(infoDiv);
    partDiv.appendChild(deleteBtn);
    container.appendChild(partDiv);
  }
}

// Parça arama fonksiyonları
function setupPartSearch() {
  var searchInput = document.getElementById('partSearch');
  var resultsDiv = document.getElementById('searchResults');
  
  searchInput.oninput = function() {
    var query = this.value.trim().toLowerCase();
    
    if (query.length < 2) {
      resultsDiv.style.display = 'none';
      return;
    }

    var filteredParts = partsList.filter(function(part) {
      return part.name.toLowerCase().indexOf(query) !== -1 || part.code.toLowerCase().indexOf(query) !== -1;
    });

    if (filteredParts.length === 0) {
      resultsDiv.innerHTML = '<div style="padding:10px; color:#aaa; text-align:center;">🔍 Parça bulunamadı</div>';
      resultsDiv.style.display = 'block';
      return;
    }

    resultsDiv.innerHTML = '';
    for (var i = 0; i < filteredParts.length; i++) {
      var part = filteredParts[i];
      var item = document.createElement('div');
      item.style.cssText = 'padding:10px; cursor:pointer; border-bottom:1px solid #333; display:flex; align-items:center; gap:10px; transition:background 0.2s;';
      item.onmouseover = function() { this.style.background = '#333'; };
      item.onmouseout = function() { this.style.background = 'transparent'; };
      
      var img = document.createElement('div');
      if (part.image) {
        img.innerHTML = '<img src="' + part.image + '" style="width:30px; height:30px; object-fit:cover; border-radius:4px;">';
      } else {
        img.innerHTML = '<div style="width:30px; height:30px; background:#444; border-radius:4px; display:flex; align-items:center; justify-content:center; font-size:10px; color:#aaa;">🔧</div>';
      }
      
      var info = document.createElement('div');
      info.innerHTML = '<div style="font-weight:500;">🔧 ' + part.name + '</div><div style="font-size:12px; color:#aaa;">📋 Kod: ' + part.code + '</div>';
      
      item.appendChild(img);
      item.appendChild(info);
      
      item.onclick = (function(partData) {
        return function() {
          selectPart(partData);
          resultsDiv.style.display = 'none';
          searchInput.value = partData.name + ' (' + partData.code + ')';
        };
      })(part);
      
      resultsDiv.appendChild(item);
    }
    
    resultsDiv.style.display = 'block';
  };
}

function selectPart(part) {
  document.getElementById('partCode').value = part.code;
}

// Sayfa yüklendiğinde event listener'ları başlat
window.onload = function() {
  // Plan düzenleme event listener'ları
  document.getElementById('addPlusMode').onclick = function() {
    addMode = true;
    document.getElementById('plan-dropzone').style.cursor = 'crosshair';
    document.getElementById('addPlusMode').textContent = '✅ Tıklayın (Aktif)';
    document.getElementById('addPlusMode').style.background = '#28a745';
  };

  document.getElementById('plan-dropzone').onclick = function(e) {
    if (!addMode) return;
    var rect = e.currentTarget.getBoundingClientRect();
    var x = e.clientX - rect.left;
    var y = e.clientY - rect.top;
    var plus = document.createElement('div');
    plus.className = 'plus-button';
    plus.textContent = '+';
    plus.style.left = (x - 15) + 'px';
    plus.style.top = (y - 15) + 'px';
    plus.onclick = function(ev) {
      ev.stopPropagation();
      currentButton = plus;
      document.getElementById('subImageInput').value = '';
      var previewContainer = document.getElementById('preview-container');
      previewContainer.innerHTML = '';
      var images = subImageMap.get(currentButton) || [];
      for (var i = 0; i < images.length; i++) {
        addPreviewImage(images[i]);
      }
      document.getElementById('modal').style.display = 'block';
    };
    document.getElementById('plan-dropzone').appendChild(plus);
    addMode = false;
    document.getElementById('plan-dropzone').style.cursor = 'default';
    document.getElementById('addPlusMode').textContent = '➕ Artı Nokta Ekle';
    document.getElementById('addPlusMode').style.background = '#4f8df9';
  };

  // Not kaydetme
  document.getElementById('saveNote').onclick = function() {
    var noteText = document.getElementById('noteArea').value.trim();
    var noteImagePreview = document.getElementById('noteImagePreview');
    if (!noteText && noteImagePreview.childNodes.length === 0) return;
    var noteList = document.getElementById('noteList');
    var noteItem = document.createElement('div');
    noteItem.style.padding = '5px';
    noteItem.style.borderBottom = '1px solid #555';
    var now = new Date();
    var timestamp = now.toLocaleString('tr-TR');
    noteItem.innerHTML = '<div style="font-size:12px;color:#999;">📅 ' + timestamp + '</div><div>📝 ' + noteText + '</div>';
    noteList.appendChild(noteItem);
    document.getElementById('noteArea').value = '';
    document.getElementById('noteImagePreview').innerHTML = '';
    alert('✅ Not başarıyla kaydedildi!');
  };

  // Not resmi ekleme
  document.getElementById('noteImageInput').onchange = function(e) {
    var files = Array.from(e.target.files);
    var preview = document.getElementById('noteImagePreview');
    for (var i = 0; i < files.length && preview.childNodes.length < 4; i++) {
      var file = files[i];
      var reader = new FileReader();
      reader.onload = function(ev) {
        var img = document.createElement('img');
        img.src = ev.target.result;
        img.style.maxWidth = '60px';
        img.style.marginRight = '5px';
        img.style.border = '1px solid #666';
        img.style.borderRadius = '4px';
        preview.appendChild(img);
      };
      reader.readAsDataURL(file);
    }
  };

  // Alt resim kaydetme
  document.getElementById('saveSubImage').onclick = function() {
    var input = document.getElementById('subImageInput');
    var file = input.files[0];
    if (!file) return alert('❌ Lütfen bir resim seçin');
    var reader = new FileReader();
    reader.onload = function(e) {
      var images = subImageMap.get(currentButton) || [];
      if (images.length >= 4) return alert('❌ En fazla 4 resim ekleyebilirsiniz');
      images.push(e.target.result);
      subImageMap.set(currentButton, images);
      addPreviewImage(e.target.result);
      alert('✅ Resim başarıyla eklendi!');
    };
    reader.readAsDataURL(file);
    document.getElementById('modal').style.display = 'none';
  };

  // Plan resmi yükleme
  document.getElementById('planInput').onchange = function(e) {
    var file = e.target.files[0];
    if (!file) return;
    var reader = new FileReader();
    reader.onload = function(ev) {
      var img = document.getElementById('planImage');
      img.src = ev.target.result;
      img.style.display = 'block';
    };
    reader.readAsDataURL(file);
  };

  // Resim hareket ettirme
  document.getElementById('toggleMove').onclick = function() {
    var img = document.getElementById('planImage');
    var btn = document.getElementById('toggleMove');
    if (btn.textContent === '👆 Konumlandırma') {
      btn.textContent = '✅ Taşıma Aktif';
      btn.style.background = '#28a745';
      img.onmousedown = function(e) {
        isDragging = true;
        dragStartX = e.clientX;
        dragStartY = e.clientY;
        imageStartX = parseInt(img.style.left) || 0;
        imageStartY = parseInt(img.style.top) || 0;
        img.style.cursor = 'grabbing';
      };
      document.onmouseup = function() { 
        isDragging = false; 
        img.style.cursor = 'grab'; 
      };
      document.onmousemove = function(e) {
        if (!isDragging) return;
        var dx = e.clientX - dragStartX;
        var dy = e.clientY - dragStartY;
        img.style.left = (imageStartX + dx) + 'px';
        img.style.top = (imageStartY + dy) + 'px';
      };
    } else {
      btn.textContent = '👆 Konumlandırma';
      btn.style.background = '#333';
      img.onmousedown = null;
      document.onmouseup = null;
      document.onmousemove = null;
      img.style.cursor = 'grab';
    }
  };

  // Ölçeklendirme kontrolleri
  document.getElementById('toggleScale').onclick = function() {
    var controls = document.getElementById('scaleControls');
    controls.style.display = controls.style.display === 'none' ? 'block' : 'none';
  };

  document.getElementById('applySize').onclick = function() {
    var w = parseInt(document.getElementById('imgWidth').value);
    var h = parseInt(document.getElementById('imgHeight').value);
    var img = document.getElementById('planImage');
    if (!isNaN(w)) img.style.width = w + 'px';
    if (!isNaN(h)) img.style.height = h + 'px';
    alert('✅ Boyut ayarları uygulandı!');
  };

  // Günlük rapor event listener'ları
  document.getElementById('companyName').oninput = updateReportDisplay;
  document.getElementById('reportDate').onchange = updateReportDisplay;
  
  // Rapor görsel önizleme
  document.getElementById('reportImages').onchange = function(e) {
    var files = Array.from(e.target.files);
    var preview = document.getElementById('reportImagePreview');
    preview.innerHTML = '';
    
    for (var i = 0; i < files.length && i < 5; i++) {
      var file = files[i];
      var reader = new FileReader();
      reader.onload = function(ev) {
        var imgDiv = document.createElement('div');
        imgDiv.style.position = 'relative';
        imgDiv.innerHTML = '<img src="' + ev.target.result + '" style="width:80px; height:80px; object-fit:cover; border:1px solid #666; border-radius:4px;"><div style="position:absolute; top:-5px; right:-5px; background:#e74c3c; color:white; border-radius:50%; width:20px; height:20px; display:flex; align-items:center; justify-content:center; font-size:12px; cursor:pointer;" onclick="this.parentElement.remove()">×</div>';
        preview.appendChild(imgDiv);
      };
      reader.readAsDataURL(file);
    }
  };

  // Dışarıya tıklayınca popup'ları kapat
  document.onclick = function(e) {
    var popup = document.getElementById('orderNotificationPopup');
    if (popup.style.display === 'block' && !popup.contains(e.target) && !e.target.closest('.notification-bell')) {
      popup.style.display = 'none';
    }
    
    var dropdown = document.getElementById('partsDropdown');
    if (dropdown && dropdown.classList.contains('show') && !e.target.closest('.dropdown-container')) {
      dropdown.classList.remove('show');
    }
    
    var searchResults = document.getElementById('searchResults');
    var searchInput = document.getElementById('partSearch');
    if (searchResults && searchInput && !searchInput.contains(e.target) && !searchResults.contains(e.target)) {
      searchResults.style.display = 'none';
    }
  };
};

function addPreviewImage(src) {
  var previewContainer = document.getElementById('preview-container');
  var box = document.createElement('div');
  box.style.position = 'relative';
  var img = document.createElement('img');
  img.src = src;
  img.style.maxWidth = '60px';
  img.style.border = '1px solid #666';
  img.style.borderRadius = '4px';
  var del = document.createElement('button');
  del.textContent = '❌';
  del.style.cssText = 'position:absolute; top:-5px; right:-5px; background:red; color:white; border:none; border-radius:50%; cursor:pointer; width:18px; height:18px; font-size:10px;';
  del.onclick = function() {
    var images = subImageMap.get(currentButton) || [];
    var index = images.indexOf(src);
    if (index > -1) images.splice(index, 1);
    subImageMap.set(currentButton, images);
    previewContainer.innerHTML = '';
    var updatedImages = subImageMap.get(currentButton) || [];
    for (var i = 0; i < updatedImages.length; i++) {
      addPreviewImage(updatedImages[i]);
    }
  };
  box.appendChild(img);
  box.appendChild(del);
  previewContainer.appendChild(box);
}
</script>
</body>
</html>
