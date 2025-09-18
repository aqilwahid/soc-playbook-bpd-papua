<!DOCTYPE html>

<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Form Absensi dengan Tanda Tangan</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

```
    body {
        font-family: 'Arial', sans-serif;
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        min-height: 100vh;
        padding: 20px;
    }

    .container {
        max-width: 600px;
        margin: 0 auto;
        background: white;
        border-radius: 15px;
        box-shadow: 0 15px 35px rgba(0,0,0,0.1);
        padding: 30px;
    }

    h1 {
        text-align: center;
        color: #333;
        margin-bottom: 30px;
        font-size: 28px;
    }

    .form-group {
        margin-bottom: 20px;
    }

    label {
        display: block;
        margin-bottom: 8px;
        font-weight: bold;
        color: #555;
    }

    input[type="text"], input[type="email"], select {
        width: 100%;
        padding: 12px;
        border: 2px solid #e0e0e0;
        border-radius: 8px;
        font-size: 16px;
        transition: border-color 0.3s;
    }

    input[type="text"]:focus, input[type="email"]:focus, select:focus {
        outline: none;
        border-color: #667eea;
    }

    .signature-section {
        margin: 30px 0;
        text-align: center;
    }

    .signature-pad {
        border: 3px solid #667eea;
        border-radius: 10px;
        background: #fafafa;
        cursor: crosshair;
        display: block;
        margin: 15px auto;
        box-shadow: 0 5px 15px rgba(0,0,0,0.1);
    }

    .signature-controls {
        margin: 15px 0;
    }

    .btn {
        background: linear-gradient(45deg, #667eea, #764ba2);
        color: white;
        border: none;
        padding: 12px 25px;
        border-radius: 25px;
        cursor: pointer;
        font-size: 16px;
        margin: 5px;
        transition: transform 0.2s, box-shadow 0.2s;
    }

    .btn:hover {
        transform: translateY(-2px);
        box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
    }

    .btn-clear {
        background: linear-gradient(45deg, #ff6b6b, #ee5a24);
    }

    .btn-clear:hover {
        box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4);
    }

    .btn-submit {
        background: linear-gradient(45deg, #00d2d3, #54a0ff);
        padding: 15px 40px;
        font-size: 18px;
        width: 100%;
        margin-top: 20px;
    }

    .signature-preview {
        margin-top: 20px;
        text-align: center;
    }

    .signature-image {
        border: 2px solid #e0e0e0;
        border-radius: 8px;
        max-width: 100%;
    }

    .attendance-list {
        margin-top: 30px;
        padding-top: 20px;
        border-top: 2px solid #e0e0e0;
    }

    .attendance-item {
        background: #f8f9fa;
        padding: 15px;
        margin: 10px 0;
        border-radius: 8px;
        border-left: 4px solid #667eea;
    }

    .attendance-signature {
        width: 200px;
        height: 60px;
        border: 1px solid #ddd;
        border-radius: 4px;
        margin-top: 10px;
    }

    @media (max-width: 768px) {
        .signature-pad {
            width: 100% !important;
            height: 200px !important;
        }
    }
</style>
```

</head>
<body>
    <div class="container">
        <h1>📝 Form Absensi Digital</h1>

```
    <form id="attendanceForm">
        <div class="form-group">
            <label for="nama">Nama Lengkap:</label>
            <input type="text" id="nama" name="nama" required>
        </div>

        <div class="form-group">
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
        </div>

        <div class="form-group">
            <label for="jabatan">Jabatan/Posisi:</label>
            <select id="jabatan" name="jabatan" required>
                <option value="">Pilih Jabatan</option>
                <option value="Manager">Manager</option>
                <option value="Supervisor">Supervisor</option>
                <option value="Staff">Staff</option>
                <option value="Intern">Intern</option>
                <option value="Freelancer">Freelancer</option>
            </select>
        </div>

        <div class="signature-section">
            <label>Tanda Tangan Digital:</label>
            <p style="color: #666; font-size: 14px; margin: 10px 0;">
                Silakan buat tanda tangan Anda di area di bawah ini
            </p>
            
            <canvas id="signaturePad" class="signature-pad" width="500" height="200"></canvas>
            
            <div class="signature-controls">
                <button type="button" class="btn btn-clear" onclick="clearSignature()">
                    🗑️ Hapus Tanda Tangan
                </button>
            </div>
        </div>

        <button type="submit" class="btn btn-submit">
            ✅ Submit Absensi
        </button>
    </form>

    <div class="attendance-list" id="attendanceList">
        <h2>📊 Daftar Absensi Hari Ini</h2>
        <div id="attendanceItems"></div>
    </div>
</div>

<script>
    // Signature Pad Implementation
    const canvas = document.getElementById('signaturePad');
    const ctx = canvas.getContext('2d');
    let isDrawing = false;
    let attendanceData = [];

    // Set canvas background
    ctx.fillStyle = '#ffffff';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Drawing functions
    function startDrawing(e) {
        isDrawing = true;
        const rect = canvas.getBoundingClientRect();
        const x = (e.clientX || e.touches[0].clientX) - rect.left;
        const y = (e.clientY || e.touches[0].clientY) - rect.top;
        
        ctx.beginPath();
        ctx.moveTo(x, y);
    }

    function draw(e) {
        if (!isDrawing) return;
        
        const rect = canvas.getBoundingClientRect();
        const x = (e.clientX || e.touches[0].clientX) - rect.left;
        const y = (e.clientY || e.touches[0].clientY) - rect.top;
        
        ctx.lineWidth = 2;
        ctx.lineCap = 'round';
        ctx.strokeStyle = '#333';
        ctx.lineTo(x, y);
        ctx.stroke();
        ctx.beginPath();
        ctx.moveTo(x, y);
    }

    function stopDrawing() {
        isDrawing = false;
        ctx.beginPath();
    }

    // Mouse events
    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mousemove', draw);
    canvas.addEventListener('mouseup', stopDrawing);

    // Touch events for mobile
    canvas.addEventListener('touchstart', (e) => {
        e.preventDefault();
        startDrawing(e);
    });
    canvas.addEventListener('touchmove', (e) => {
        e.preventDefault();
        draw(e);
    });
    canvas.addEventListener('touchend', stopDrawing);

    // Clear signature function
    function clearSignature() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#ffffff';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    }

    // Check if signature pad is empty
    function isSignaturePadEmpty() {
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const pixels = imageData.data;
        
        for (let i = 0; i < pixels.length; i += 4) {
            // Check if pixel is not white
            if (pixels[i] !== 255 || pixels[i + 1] !== 255 || pixels[i + 2] !== 255) {
                return false;
            }
        }
        return true;
    }

    // Form submission
    document.getElementById('attendanceForm').addEventListener('submit', function(e) {
        e.preventDefault();
        
        const nama = document.getElementById('nama').value;
        const email = document.getElementById('email').value;
        const jabatan = document.getElementById('jabatan').value;
        
        if (isSignaturePadEmpty()) {
            alert('Silakan buat tanda tangan terlebih dahulu!');
            return;
        }

        // Get signature as base64 image
        const signatureDataURL = canvas.toDataURL();
        
        // Create attendance record
        const attendanceRecord = {
            nama: nama,
            email: email,
            jabatan: jabatan,
            signature: signatureDataURL,
            timestamp: new Date().toLocaleString('id-ID')
        };

        // Add to attendance list
        attendanceData.push(attendanceRecord);
        
        // Display success message
        alert(`Absensi berhasil! Terima kasih ${nama}.`);
        
        // Update attendance display
        updateAttendanceDisplay();
        
        // Reset form
        document.getElementById('attendanceForm').reset();
        clearSignature();
    });

    // Update attendance display
    function updateAttendanceDisplay() {
        const attendanceItems = document.getElementById('attendanceItems');
        attendanceItems.innerHTML = '';

        if (attendanceData.length === 0) {
            attendanceItems.innerHTML = '<p style="text-align: center; color: #666;">Belum ada data absensi</p>';
            return;
        }

        attendanceData.forEach((record, index) => {
            const item = document.createElement('div');
            item.className = 'attendance-item';
            item.innerHTML = `
                <div style="display: flex; justify-content: space-between; align-items: flex-start;">
                    <div>
                        <strong>${record.nama}</strong><br>
                        <span style="color: #666;">${record.email}</span><br>
                        <span style="color: #667eea;">${record.jabatan}</span><br>
                        <small style="color: #999;">${record.timestamp}</small>
                    </div>
                    <div>
                        <img src="${record.signature}" class="attendance-signature" alt="Tanda Tangan ${record.nama}">
                    </div>
                </div>
            `;
            attendanceItems.appendChild(item);
        });
    }

    // Initialize display
    updateAttendanceDisplay();
</script>
```

</body>
</html>
