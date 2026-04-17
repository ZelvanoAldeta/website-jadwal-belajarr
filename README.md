# website-jadwal-belajarr
Web Whibie Aldyansyah S.
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jadwal Belajar | Whibie Aldyansyah S.</title>
    <style>
        :root {
            --primary: #0ea5e9; /* Biru Muda Cerah */
            --secondary: #f0f9ff;
            --white: #ffffff;
            --text-dark: #1e293b;
            --text-gray: #64748b;
            --success: #10b981;
            --danger: #ef4444;
            --border: #e2e8f0;
        }

        body {
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
            background-color: #f8fafc;
            color: var(--text-dark);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }

        .main-container {
            width: 100%;
            max-width: 1100px;
            background: var(--white);
            border-radius: 24px;
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.05);
            padding: 40px;
            border: 1px solid var(--border);
        }

        .header {
            text-align: center;
            margin-bottom: 35px;
        }

        .header h1 {
            margin: 0;
            color: var(--primary);
            font-size: 2.2rem;
            font-weight: 800;
        }

        .header p {
            margin: 8px 0 0;
            color: var(--text-gray);
            font-size: 1.1rem;
            font-weight: 500;
        }

        /* Action Buttons */
        .action-bar {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-bottom: 30px;
            flex-wrap: wrap;
        }

        .btn {
            padding: 12px 24px;
            border-radius: 12px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.2s ease;
            border: none;
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.95rem;
        }

        .btn-add { background-color: var(--primary); color: white; }
        .btn-save { background-color: var(--success); color: white; }
        .btn-reset { background-color: #f1f5f9; color: var(--danger); border: 1px solid var(--danger); }

        .btn:hover {
            transform: translateY(-2px);
            filter: brightness(1.1);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }

        /* Table Styling */
        .table-responsive {
            overflow-x: auto;
            border-radius: 16px;
            border: 1px solid var(--border);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background-color: var(--white);
        }

        th {
            background-color: var(--primary);
            color: white;
            padding: 20px;
            font-size: 0.85rem;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        td {
            padding: 16px;
            border: 1px solid var(--border);
            text-align: center;
            font-size: 0.95rem;
            min-width: 150px;
        }

        /* Editable Cells */
        td[contenteditable="true"] {
            outline-color: var(--primary);
            transition: background 0.2s;
        }

        td[contenteditable="true"]:focus {
            background-color: var(--secondary);
        }

        .time-col {
            background-color: #f8fafc;
            font-weight: 700;
            color: var(--primary);
        }

        .delete-btn {
            color: var(--danger);
            cursor: pointer;
            font-weight: 800;
            font-size: 1.2rem;
            width: 50px;
        }

        .delete-btn:hover { color: #b91c1c; }

        .footer-note {
            margin-top: 20px;
            font-size: 0.85rem;
            color: var(--text-gray);
            text-align: center;
            font-style: italic;
        }
    </style>
</head>
<body>

<div class="main-container">
    <div class="header">
        <h1>Whibie Aldyansyah S.</h1>
        <p>Dashboard Jadwal Pelajaran 11 TJKT</p>
    </div>

    <div class="action-bar">
        <button class="btn btn-add" onclick="tambahBarisOtomatis()">+ Tambah Jadwal Baru</button>
        <button class="btn btn-save" onclick="simpanKeBrowser()">💾 Simpan Perubahan</button>
        <button class="btn btn-reset" onclick="resetData()">Hapus Semua</button>
    </div>

    <div class="table-responsive">
        <table>
            <thead>
                <tr>
                    <th>Waktu</th>
                    <th>Senin</th>
                    <th>Selasa</th>
                    <th>Rabu</th>
                    <th>Kamis</th>
                    <th>Jumat</th>
                    <th style="background: #f1f5f9; color: #475569;">Opsi</th>
                </tr>
            </thead>
            <tbody id="bodyJadwal">
                </tbody>
        </table>
    </div>

    <div class="footer-note">
        * Tip: Klik pada kolom apa saja untuk mengedit. Pastikan klik "Simpan Perubahan" sebelum menutup halaman.
    </div>
</div>

<script>
    // Inisialisasi: Load data saat halaman dibuka
    window.onload = function() {
        const dataTersimpan = localStorage.getItem('jadwal_whibie_v4');
        if (dataTersimpan) {
            renderUlangTabel(JSON.parse(dataTersimpan));
        } else {
            // Tampilan awal jika masih kosong
            tambahBarisOtomatis("07:30 - 09:00");
        }
    };

    // FITUR: Tambah Jadwal Secara Otomatis
    function tambahBarisOtomatis(waktuDefault = "Klik untuk isi jam") {
        const tbody = document.getElementById('bodyJadwal');
        const tr = document.createElement('tr');
        
        // Buat baris dengan kolom yang langsung bisa diedit
        let isiBaris = `
            <td class="time-col" contenteditable="true">${waktuDefault}</td>
            <td contenteditable="true">Pelajaran</td>
            <td contenteditable="true">Pelajaran</td>
            <td contenteditable="true">Pelajaran</td>
            <td contenteditable="true">Pelajaran</td>
            <td contenteditable="true">Pelajaran</td>
            <td class="delete-btn" onclick="hapusBaris(this)">×</td>
        `;
        
        tr.innerHTML = isiBaris;
        tbody.appendChild(tr);
    }

    // Fungsi Render Ulang (Untuk loading data lama)
    function renderUlangTabel(data) {
        const tbody = document.getElementById('bodyJadwal');
        tbody.innerHTML = '';
        data.forEach(baris => {
            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td class="time-col" contenteditable="true">${baris[0]}</td>
                <td contenteditable="true">${baris[1]}</td>
                <td contenteditable="true">${baris[2]}</td>
                <td contenteditable="true">${baris[3]}</td>
                <td contenteditable="true">${baris[4]}</td>
                <td contenteditable="true">${baris[5]}</td>
                <td class="delete-btn" onclick="hapusBaris(this)">×</td>
            `;
            tbody.appendChild(tr);
        });
    }

    // FITUR: Hapus Baris
    function hapusBaris(elemen) {
        if(confirm("Hapus baris pelajaran ini?")) {
            elemen.parentElement.remove();
        }
    }

    // FITUR: Simpan Data (Edit Jadwal)
    function simpanKeBrowser() {
        const rows = document.querySelectorAll('#bodyJadwal tr');
        const dataBaru = [];

        rows.forEach(row => {
            const kolom = row.querySelectorAll('td:not(.delete-btn)');
            const isiKolom = Array.from(kolom).map(td => td.innerText);
            dataBaru.push(isiKolom);
        });

        localStorage.setItem('jadwal_whibie_v4', JSON.stringify(dataBaru));
        alert("Berhasil! Jadwal Whibie Aldyansyah S. telah disimpan.");
    }

    // FITUR: Reset
    function resetData() {
        if(confirm("Ingin menghapus semua jadwal dan memulai dari awal?")) {
            localStorage.removeItem('jadwal_whibie_v4');
            location.reload();
        }
    }
</script>

</body>
</html>
