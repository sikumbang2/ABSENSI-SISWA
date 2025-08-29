# ABSENSI-SISWA
MTs MA'ARIF NU KEMIRI
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Absensi Digital - MTs MA'ARIF NU KEMIRI</title>
  <!-- Bootstrap CSS -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" />
  <style>
    :root{
      --brand:#0d6efd;
    }
    body{font-family:'Segoe UI',system-ui,-apple-system,Roboto,Arial,sans-serif;background:#f8f9fa}
    .navbar{position:sticky;top:0;z-index:1000}
    .layout{min-height:100vh}
    .sidebar{min-height:calc(100vh - 56px);background:var(--brand);color:#fff}
    .sidebar a{color:#fff;text-decoration:none;display:block;padding:10px 14px;border-radius:.5rem;margin:4px 8px}
    .sidebar a.active,.sidebar a:hover{background:#0b5ed7}
    .content{padding:20px}
    .hidden{display:none!important}
    .card{border-radius:1rem}
    .foto-siswa{width:40px;height:40px;object-fit:cover;border-radius:50%}
    .table thead th{white-space:nowrap}
    @media (max-width: 991px){.sidebar{min-height:auto}}
    /* Print styles for QR & Laporan */
    @media print{
      .no-print{display:none!important}
      .print-card{display:inline-block;width:180px;height:220px;margin:10px;border:1px solid #ccc;text-align:center;padding:10px}
    }
  </style>
</head>
<body>
  <!-- Navbar -->
  <nav class="navbar navbar-dark bg-primary shadow no-print">
    <div class="container-fluid">
      <span class="navbar-brand mb-0 h1">📚 Absensi Digital - MTs MA'ARIF NU KEMIRI</span>
      <div class="d-flex gap-2">
        <button class="btn btn-light btn-sm" onclick="location.reload()">🔄 Refresh</button>
        <button class="btn btn-light btn-sm">👤 Admin</button>
      </div>
    </div>
  </nav>

  <div class="container-fluid layout">
    <div class="row">
      <!-- Sidebar -->
      <aside class="col-lg-2 p-0 sidebar no-print">
        <h5 class="text-center py-3 mb-0">Menu</h5>
        <nav id="sideNav">
          <a data-target="dashboard" class="active">📊 Dashboard</a>
          <a data-target="siswa">👨‍🎓 Manajemen Siswa</a>
          <a data-target="absensi">✅ Absensi</a>
          <a data-target="import">📁 Import Data</a>
          <a data-target="qr">🖨️ Cetak QR</a>
          <a data-target="laporan">📈 Laporan</a>
        </nav>
      </aside>

      <!-- Main Content -->
      <main class="col-lg-10 content">
        <!-- DASHBOARD -->
        <section id="dashboard">
          <h3 class="mb-4">📊 Dashboard</h3>
          <div class="row g-4">
            <div class="col-md-4">
              <div class="card shadow text-center p-3">
                <h6>Total Siswa</h6>
                <h2 class="text-primary" id="statTotalSiswa">0</h2>
              </div>
            </div>
            <div class="col-md-4">
              <div class="card shadow text-center p-3">
                <h6>Hadir Hari Ini</h6>
                <h2 class="text-success" id="statHadirHariIni">0</h2>
              </div>
            </div>
            <div class="col-md-4">
              <div class="card shadow text-center p-3">
                <h6>Alpha Hari Ini</h6>
                <h2 class="text-danger" id="statAlphaHariIni">0</h2>
              </div>
            </div>
          </div>
        </section>

        <!-- MANAJEMEN SISWA -->
        <section id="siswa" class="hidden">
          <div class="d-flex justify-content-between align-items-center mb-3">
            <h3>👨‍🎓 Manajemen Siswa</h3>
            <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#modalSiswa" onclick="openTambahSiswa()">➕ Tambah Siswa</button>
          </div>
          <div class="row g-2 mb-3">
            <div class="col-md-6">
              <input type="text" id="cariSiswa" class="form-control" placeholder="🔍 Cari siswa (nama/NISN)" oninput="renderSiswaTable()" />
            </div>
            <div class="col-md-3">
              <select id="filterKelas" class="form-select" onchange="renderSiswaTable()">
                <option value="">Semua Kelas</option>
                <option>7</option><option>8</option><option>9</option>
              </select>
            </div>
            <div class="col-md-3">
              <select id="filterRombel" class="form-select" onchange="renderSiswaTable()">
                <option value="">Semua Rombel</option>
                <option>A</option><option>B</option><option>C</option><option>D</option>
                <option>E</option><option>F</option><option>G</option><option>H</option>
              </select>
            </div>
          </div>
          <div class="card shadow p-3">
            <div class="table-responsive">
              <table class="table table-hover align-middle">
                <thead class="table-primary">
                  <tr>
                    <th>Foto</th>
                    <th>NISN</th>
                    <th>Nama</th>
                    <th>Kelas</th>
                    <th>Rombel</th>
                    <th style="width:140px">Aksi</th>
                  </tr>
                </thead>
                <tbody id="siswaTableBody"></tbody>
              </table>
            </div>
          </div>
        </section>

        <!-- ABSENSI -->
        <section id="absensi" class="hidden">
          <h3 class="mb-4">✅ Form Absensi</h3>
          <div class="card shadow p-4">
            <form id="formAbsensi" onsubmit="simpanAbsensi(event)">
              <div class="mb-3">
                <label class="form-label">Scan QR Code / Masukkan NISN</label>
                <input type="text" id="nisn_absen" class="form-control" placeholder="Contoh: 1234567890" oninput="isiNamaOtomatis()" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Nama Siswa</label>
                <input type="text" id="nama_absen" class="form-control" readonly />
              </div>
              <div class="mb-3">
                <label class="form-label">Status Kehadiran</label>
                <select id="status_absen" class="form-select" required>
                  <option value="">-- Pilih Status --</option>
                  <option>Hadir</option>
                  <option>Sakit</option>
                  <option>Izin</option>
                  <option>Alpha</option>
                </select>
              </div>
              <div class="mb-3">
                <label class="form-label">Waktu</label>
                <input type="text" id="waktu_absen" class="form-control" readonly />
              </div>
              <div class="mb-3">
                <label class="form-label">Keterangan</label>
                <textarea id="ket_absen" class="form-control" placeholder="Opsional..."></textarea>
              </div>
              <button type="submit" class="btn btn-primary">💾 Simpan Absensi</button>
            </form>
          </div>
        </section>

        <!-- IMPORT DATA -->
        <section id="import" class="hidden">
          <h3 class="mb-4">📁 Import Data Siswa</h3>
          <div class="card shadow p-4">
            <div class="mb-3">
              <label class="form-label">Pilih File CSV</label>
              <input type="file" id="fileInput" accept=".csv" class="form-control" />
              <small class="text-muted">Format kolom:<th></th> NISN<th></th> Nama<th></th> Kelas<th></th> Rombel<th></th></small>
            </div>
            <div class="d-flex gap-2 mb-3">
              <button class="btn btn-primary" onclick="previewCSV()">👀 Preview Data</button>
              <button class="btn btn-success" onclick="simpanImport()">💾 Simpan ke Daftar Siswa</button>
              <button class="btn btn-outline-secondary" onclick="downloadTemplate()">⬇️ Download Template CSV</button>
            </div>
            <div id="previewTable" class="table-responsive hidden">
              <h6>Preview Data:</h6>
              <table class="table table-bordered table-hover">
                <thead class="table-primary">
                  <tr><th>NISN</th><th>Nama</th><th>Kelas</th><th>Rombel</th></tr>
                </thead>
                <tbody id="previewBody"></tbody>
              </table>
            </div>
          </div>
        </section>

        <!-- CETAK QR -->
        <section id="qr" class="hidden">
          <h3 class="mb-4">🖨️ Cetak QR Code Siswa</h3>
          <div class="card shadow p-4">
            <div class="d-flex justify-content-between align-items-center mb-3">
              <div class="d-flex gap-2">
                <button class="btn btn-success" onclick="printAllQR()">🖨️ Cetak Semua QR</button>
                <button class="btn btn-outline-primary" onclick="renderQRTable()">🔁 Refresh QR</button>
              </div>
              <div class="d-flex gap-2">
                <select id="qrKelas" class="form-select" onchange="renderQRTable()">
                  <option value="">Semua Kelas</option>
                  <option>7</option><option>8</option><option>9</option>
                </select>
                <select id="qrRombel" class="form-select" onchange="renderQRTable()">
                  <option value="">Semua Rombel</option>
                  <option>A</option><option>B</option><option>C</option><option>D</option>
                  <option>E</option><option>F</option><option>G</option><option>H</option>
                </select>
              </div>
            </div>
            <div class="table-responsive">
              <table class="table table-bordered table-hover">
                <thead class="table-primary">
                  <tr>
                    <th>NISN</th><th>Nama</th><th>Kelas</th><th>Rombel</th><th>QR Code</th><th>Aksi</th>
                  </tr>
                </thead>
                <tbody id="qrTableBody"></tbody>
              </table>
            </div>
          </div>

          <!-- Printable area (built dynamically) -->
          <div id="printArea" class="d-none"></div>
        </section>

        <!-- LAPORAN -->
        <section id="laporan" class="hidden">
          <h3 class="mb-4">📈 Laporan Absensi</h3>
          <div class="card shadow p-4 mb-4">
            <form id="filterForm" class="row g-3">
              <div class="col-md-3">
                <label class="form-label">Tanggal Mulai</label>
                <input type="date" class="form-control" id="tglMulai" />
              </div>
              <div class="col-md-3">
                <label class="form-label">Tanggal Akhir</label>
                <input type="date" class="form-control" id="tglAkhir" />
              </div>
              <div class="col-md-2">
                <label class="form-label">Kelas</label>
                <select class="form-select" id="kelasFilterLap">
                  <option value="">Semua</option><option>7</option><option>8</option><option>9</option>
                </select>
              </div>
              <div class="col-md-2">
                <label class="form-label">Rombel</label>
                <select class="form-select" id="rombelFilterLap">
                  <option value="">Semua</option>
                  <option>A</option><option>B</option><option>C</option><option>D</option>
                  <option>E</option><option>F</option><option>G</option><option>H</option>
                </select>
              </div>
              <div class="col-md-2 d-flex align-items-end">
                <button type="button" class="btn btn-primary w-100" onclick="filterLaporan()">Terapkan</button>
              </div>
            </form>
          </div>

          <div class="row text-center mb-4">
            <div class="col-md-3"><div class="card shadow p-3 bg-success text-white"><h6>Hadir</h6><h3 id="statHadir">0</h3></div></div>
            <div class="col-md-3"><div class="card shadow p-3 bg-warning text-dark"><h6>Sakit</h6><h3 id="statSakit">0</h3></div></div>
            <div class="col-md-3"><div class="card shadow p-3 bg-info text-dark"><h6>Izin</h6><h3 id="statIzin">0</h3></div></div>
            <div class="col-md-3"><div class="card shadow p-3 bg-danger text-white"><h6>Alpha</h6><h3 id="statAlpha">0</h3></div></div>
          </div>

          <div class="card shadow p-4" id="laporanCard">
            <div class="d-flex justify-content-between align-items-center mb-3">
              <h5>Rekap Absensi</h5>
              <button class="btn btn-success" onclick="printLaporan()">🖨️ Cetak Laporan</button>
            </div>
            <div class="table-responsive">
              <table class="table table-bordered table-hover">
                <thead class="table-secondary">
                  <tr>
                    <th>NISN</th><th>Nama</th><th>Kelas</th><th>Rombel</th>
                    <th>Hadir</th><th>Sakit</th><th>Izin</th><th>Alpha</th><th>% Kehadiran</th>
                  </tr>
                </thead>
                <tbody id="laporanTableBody"></tbody>
              </table>
            </div>
          </div>
        </section>
      </main>
    </div>
  </div>

  <!-- Modal Tambah/Edit Siswa -->
  <div class="modal fade" id="modalSiswa" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog">
      <form class="modal-content" onsubmit="submitSiswa(event)">
        <div class="modal-header bg-primary text-white">
          <h5 class="modal-title" id="modalTitle">➕ Tambah Siswa</h5>
          <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
        </div>
        <div class="modal-body">
          <input type="hidden" id="modeEdit" value="0" />
          <div class="mb-3">
            <label class="form-label">NISN</label>
            <input type="text" id="nisn" class="form-control" required />
          </div>
          <div class="mb-3">
            <label class="form-label">Nama</label>
            <input type="text" id="nama" class="form-control" required />
          </div>
          <div class="row mb-3">
            <div class="col">
              <label class="form-label">Kelas</label>
              <select id="kelas" class="form-select">
                <option>7</option><option>8</option><option>9</option>
              </select>
            </div>
            <div class="col">
              <label class="form-label">Rombel</label>
              <select id="rombel" class="form-select">
                <option>A</option><option>B</option><option>C</option><option>D</option>
                <option>E</option><option>F</option><option>G</option><option>H</option>
              </select>
            </div>
          </div>
          <div class="mb-3">
            <label class="form-label">Foto</label>
            <input type="file" id="foto" class="form-control" accept="image/*" onchange="previewFoto(event)" />
          </div>
          <div class="text-center">
            <img id="previewImg" src="https://via.placeholder.com/80" class="rounded-circle" width="80" height="80" alt="Preview" />
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Batal</button>
          <button type="submit" class="btn btn-primary">Simpan</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Dependencies -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/qrcodejs/qrcode.min.js"></script>

  <script>
    /*********************************
     * DATA DUMMY & STATE SEMENTARA *
     *********************************/
    let siswaList = [
      { nisn: "1234567890", nama: "Ahmad Fauzi", kelas: "7", rombel: "A", foto: "https://via.placeholder.com/40" },
      { nisn: "9876543210", nama: "Siti Aisyah", kelas: "8", rombel: "B", foto: "https://via.placeholder.com/40" },
      { nisn: "1122334455", nama: "Budi Santoso", kelas: "9", rombel: "C", foto: "https://via.placeholder.com/40" }
    ];

    // Rekap absensi per siswa (dummy). Key = NISN
    const rekapMap = new Map([
      ["1234567890", { hadir: 15, sakit: 1, izin: 2, alpha: 0 }],
      ["9876543210", { hadir: 14, sakit: 0, izin: 1, alpha: 3 }],
      ["1122334455", { hadir: 16, sakit: 2, izin: 0, alpha: 0 }]
    ]);

    // Buffer hasil preview import
    let importPreview = [];

    /********************
     * NAV & SECTIONS   *
     ********************/
    const nav = document.getElementById('sideNav');
    nav.addEventListener('click', (e)=>{
      if(e.target.matches('a[data-target]')){
        document.querySelectorAll('#sideNav a').forEach(a=>a.classList.remove('active'));
        e.target.classList.add('active');
        const target = e.target.getAttribute('data-target');
        showPage(target);
        if(target==='qr') renderQRTable();
        if(target==='laporan') renderLaporanTable(buildLaporanArray());
      }
    });

    function showPage(page){
      ['dashboard','siswa','absensi','import','qr','laporan'].forEach(id=>{
        document.getElementById(id).classList.add('hidden');
      });
      document.getElementById(page).classList.remove('hidden');
      updateDashboard();
    }

    /********************
     * DASHBOARD         *
     ********************/
    function updateDashboard(){
      document.getElementById('statTotalSiswa').textContent = siswaList.length;
      // Simulasi statistik hari ini dari rekapMap (acak kecil)
      let hadir=0, alpha=0;
      rekapMap.forEach(v=>{hadir += v.hadir % 2; alpha += v.alpha % 2});
      document.getElementById('statHadirHariIni').textContent = hadir;
      document.getElementById('statAlphaHariIni').textContent = alpha;
    }

    /********************
     * MANAJEMEN SISWA  *
     ********************/
    function renderSiswaTable(){
      const kw = document.getElementById('cariSiswa').value.toLowerCase();
      const fk = document.getElementById('filterKelas').value;
      const fr = document.getElementById('filterRombel').value;
      const tbody = document.getElementById('siswaTableBody');
      tbody.innerHTML = '';

      siswaList
        .filter(s=>!fk || s.kelas===fk)
        .filter(s=>!fr || s.rombel===fr)
        .filter(s=> !kw || s.nama.toLowerCase().includes(kw) || s.nisn.includes(kw))
        .forEach(s=>{
          const tr = document.createElement('tr');
          tr.innerHTML = `
            <td><img src="${s.foto}" class="foto-siswa" alt="${s.nama}"/></td>
            <td>${s.nisn}</td>
            <td>${s.nama}</td>
            <td>${s.kelas}</td>
            <td>${s.rombel}</td>
            <td>
              <div class="btn-group btn-group-sm">
                <button class="btn btn-warning" onclick="openEditSiswa('${s.nisn}')">✏️ Edit</button>
                <button class="btn btn-danger" onclick="hapusSiswa('${s.nisn}')">🗑️ Hapus</button>
              </div>
            </td>`;
          tbody.appendChild(tr);
        });
    }

    function openTambahSiswa(){
      document.getElementById('modalTitle').textContent = '➕ Tambah Siswa';
      document.getElementById('modeEdit').value = '0';
      document.getElementById('nisn').readOnly = false;
      document.getElementById('nisn').value = '';
      document.getElementById('nama').value = '';
      document.getElementById('kelas').value = '7';
      document.getElementById('rombel').value = 'A';
      document.getElementById('foto').value = '';
      document.getElementById('previewImg').src = 'https://via.placeholder.com/80';
    }

    function openEditSiswa(nisn){
      const s = siswaList.find(x=>x.nisn===nisn);
      if(!s) return;
      const modal = new bootstrap.Modal(document.getElementById('modalSiswa'));
      document.getElementById('modalTitle').textContent = '✏️ Edit Siswa';
      document.getElementById('modeEdit').value = '1';
      document.getElementById('nisn').readOnly = true;
      document.getElementById('nisn').value = s.nisn;
      document.getElementById('nama').value = s.nama;
      document.getElementById('kelas').value = s.kelas;
      document.getElementById('rombel').value = s.rombel;
      document.getElementById('previewImg').src = s.foto || 'https://via.placeholder.com/80';
      modal.show();
    }

    function previewFoto(e){
      const file = e.target.files[0];
      if(!file) return;
      const reader = new FileReader();
      reader.onload = ev => {
        document.getElementById('previewImg').src = ev.target.result;
        document.getElementById('previewImg').dataset.dataurl = ev.target.result; // simpan sementara
      };
      reader.readAsDataURL(file);
    }

    function submitSiswa(ev){
      ev.preventDefault();
      const isEdit = document.getElementById('modeEdit').value==='1';
      const data = {
        nisn: document.getElementById('nisn').value.trim(),
        nama: document.getElementById('nama').value.trim(),
        kelas: document.getElementById('kelas').value,
        rombel: document.getElementById('rombel').value,
        foto: document.getElementById('previewImg').dataset.dataurl || document.getElementById('previewImg').src
      };
      if(isEdit){
        const idx = siswaList.findIndex(x=>x.nisn===data.nisn);
        if(idx>-1) siswaList[idx] = {...siswaList[idx], ...data};
      } else {
        if(siswaList.some(x=>x.nisn===data.nisn)){
          alert('NISN sudah ada!');
          return;
        }
        siswaList.push(data);
        if(!rekapMap.has(data.nisn)) rekapMap.set(data.nisn,{hadir:0,sakit:0,izin:0,alpha:0});
      }
      bootstrap.Modal.getInstance(document.getElementById('modalSiswa')).hide();
      renderSiswaTable();
      updateDashboard();
    }

    function hapusSiswa(nisn){
      if(!confirm('Yakin hapus siswa ini?')) return;
      siswaList = siswaList.filter(s=>s.nisn!==nisn);
      rekapMap.delete(nisn);
      renderSiswaTable();
      updateDashboard();
    }

    /********************
     * ABSENSI           *
     ********************/
    function isiNamaOtomatis(){
      const nisn = document.getElementById('nisn_absen').value.trim();
      const s = siswaList.find(x=>x.nisn===nisn);
      document.getElementById('nama_absen').value = s ? s.nama : '';
    }
    function tickWaktu(){
      const now = new Date();
      document.getElementById('waktu_absen').value = now.toLocaleString('id-ID',{dateStyle:'short',timeStyle:'medium'});
    }
    setInterval(()=>{ if(document.getElementById('waktu_absen')) tickWaktu(); }, 1000);

    function simpanAbsensi(e){
      e.preventDefault();
      const nisn = document.getElementById('nisn_absen').value.trim();
      const status = document.getElementById('status_absen').value;
      if(!rekapMap.has(nisn)) rekapMap.set(nisn,{hadir:0,sakit:0,izin:0,alpha:0});
      const r = rekapMap.get(nisn);
      if(status==='Hadir') r.hadir++;
      if(status==='Sakit') r.sakit++;
      if(status==='Izin')  r.izin++;
      if(status==='Alpha') r.alpha++;
      rekapMap.set(nisn,r);
      alert('Absensi tersimpan (simulasi).');
      document.getElementById('formAbsensi').reset();
      updateDashboard();
      renderLaporanTable(buildLaporanArray());
    }

    /********************
     * IMPORT CSV        *
     ********************/
    function previewCSV(){
      importPreview = [];
      const fileInput = document.getElementById('fileInput');
      const previewTable = document.getElementById('previewTable');
      const previewBody = document.getElementById('previewBody');
      previewBody.innerHTML='';
      if(!fileInput.files.length){alert('⚠️ Pilih file CSV terlebih dahulu!');return;}
      const file = fileInput.files[0];
      const reader = new FileReader();
      reader.onload = (e)=>{
        const rows = e.target.result.split(/\r?\n/).map(r=>r.trim()).filter(Boolean);
        rows.forEach(row=>{
          const cols = row.split(',').map(x=>x.trim());
          if(cols.length>=4){
            const [nisn,nama,kelas,rombel] = cols;
            importPreview.push({nisn,nama,kelas,rombel,foto:'https://via.placeholder.com/40'});
            const tr = document.createElement('tr');
            tr.innerHTML = `<td>${nisn}</td><td>${nama}</td><td>${kelas}</td><td>${rombel}</td>`;
            previewBody.appendChild(tr);
          }
        });
        previewTable.classList.remove('hidden');
      };
      reader.readAsText(file);
    }

    function simpanImport(){
      if(importPreview.length===0){alert('Tidak ada data untuk disimpan.');return;}
      let inserted=0, skipped=0;
      importPreview.forEach(s=>{
        if(siswaList.some(x=>x.nisn===s.nisn)) { skipped++; return; }
        siswaList.push(s);
        if(!rekapMap.has(s.nisn)) rekapMap.set(s.nisn,{hadir:0,sakit:0,izin:0,alpha:0});
        inserted++;
      });
      alert(`Import selesai. Ditambahkan: ${inserted}, duplikat dilewati: ${skipped}.`);
      renderSiswaTable();
      updateDashboard();
      document.getElementById('previewTable').classList.add('hidden');
      importPreview=[];
    }

    function downloadTemplate(){
      const csv = 'NISN,Nama,Kelas,Rombel\n1234567890,Ahmad Fauzi,7,A\n9876543210,Siti Aisyah,8,B';
      const blob = new Blob([csv],{type:'text/csv'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href=url; a.download='template_siswa.csv'; a.click();
      URL.revokeObjectURL(url);
    }

    /********************
     * CETAK QR          *
     ********************/
    function renderQRTable(){
      const tbody = document.getElementById('qrTableBody');
      tbody.innerHTML='';
      const fk = document.getElementById('qrKelas').value;
      const fr = document.getElementById('qrRombel').value;
      const list = siswaList.filter(s=>!fk||s.kelas===fk).filter(s=>!fr||s.rombel===fr);
      list.forEach((s,i)=>{
        const tr = document.createElement('tr');
        const qrid = `qrcode-${i}-${s.nisn}`;
        tr.innerHTML = `
          <td>${s.nisn}</td>
          <td>${s.nama}</td>
          <td>${s.kelas}</td>
          <td>${s.rombel}</td>
          <td><div id="${qrid}"></div></td>
          <td><button class="btn btn-primary btn-sm" onclick="printSingleQR('${s.nisn}')">🖨️ Cetak</button></td>`;
        tbody.appendChild(tr);
        new QRCode(document.getElementById(qrid), { text:`${s.nisn} - ${s.nama}`, width:80, height:80 });
      });
    }

    function printAllQR(){
      const fk = document.getElementById('qrKelas').value;
      const fr = document.getElementById('qrRombel').value;
      const list = siswaList.filter(s=>!fk||s.kelas===fk).filter(s=>!fr||s.rombel===fr);
      const printWin = window.open('', '', 'width=1000,height=700');
      printWin.document.write('<html><head><title>Cetak QR</title></head><body>');
      list.forEach((s,idx)=>{
        const containerId = `printQR-${idx}`;
        printWin.document.write(`<div class="print-card"><h6>${s.nama}</h6><div id="${containerId}"></div><p class="mt-2">NISN: ${s.nisn}<br/>Kelas: ${s.kelas} - ${s.rombel}</p></div>`);
      });
      printWin.document.write('<script src="https://cdn.jsdelivr.net/npm/qrcodejs/qrcode.min.js"><\/script>');
      printWin.document.write('<script>('+function(list){
        list.forEach((s,idx)=>{ new QRCode(document.getElementById('printQR-'+idx), {text: s.nisn+' - '+s.nama, width:100,height:100}); });
        window.onload = function(){ setTimeout(()=>window.print(), 300); };
      }+')('+JSON.stringify(list)+');<\/script>');
      printWin.document.write('</body></html>');
      printWin.document.close();
    }

    function printSingleQR(nisn){
      const s = siswaList.find(x=>x.nisn===nisn);
      if(!s) return;
      const w = window.open('', '', 'width=420,height=520');
      w.document.write('<html><head><title>Cetak QR</title></head><body>');
      w.document.write(`<div style="text-align:center;border:1px solid #ccc;padding:15px;margin:10px"><h4>${s.nama}</h4><div id="qrPrint"></div><p class="mt-2">NISN: ${s.nisn}<br/>Kelas: ${s.kelas} - ${s.rombel}</p></div>`);
      w.document.write('<script src="https://cdn.jsdelivr.net/npm/qrcodejs/qrcode.min.js"><\/script>');
      w.document.write('<script>('+function(s){ new QRCode(document.getElementById('qrPrint'), {text: s.nisn+' - '+s.nama, width:120,height:120}); window.onload=function(){ setTimeout(()=>window.print(),300); }; }+')('+JSON.stringify(s)+');<\/script>');
      w.document.write('</body></html>');
      w.document.close();
    }

    /********************
     * LAPORAN           *
     ********************/
    function buildLaporanArray(){
      // gabungkan siswaList + rekapMap menjadi array untuk tabel laporan
      return siswaList.map(s=>{
        const r = rekapMap.get(s.nisn) || {hadir:0,sakit:0,izin:0,alpha:0};
        return { ...s, ...r };
      });
    }

    function renderLaporanTable(data){
      const tbody = document.getElementById('laporanTableBody');
      tbody.innerHTML='';
      let totalHadir=0,totalSakit=0,totalIzin=0,totalAlpha=0;
      data.forEach(s=>{
        const totalPertemuan = s.hadir + s.sakit + s.izin + s.alpha;
        const persen = totalPertemuan ? Math.round((s.hadir/totalPertemuan)*100) : 0;
        totalHadir+=s.hadir; totalSakit+=s.sakit; totalIzin+=s.izin; totalAlpha+=s.alpha;
        const tr = document.createElement('tr');
        tr.innerHTML = `<td>${s.nisn}</td><td>${s.nama}</td><td>${s.kelas}</td><td>${s.rombel}</td><td>${s.hadir}</td><td>${s.sakit}</td><td>${s.izin}</td><td>${s.alpha}</td><td>${persen}%</td>`;
        tbody.appendChild(tr);
      });
      document.getElementById('statHadir').textContent = totalHadir;
      document.getElementById('statSakit').textContent = totalSakit;
      document.getElementById('statIzin').textContent = totalIzin;
      document.getElementById('statAlpha').textContent = totalAlpha;
    }

    function filterLaporan(){
      const k = document.getElementById('kelasFilterLap').value;
      const r = document.getElementById('rombelFilterLap').value;
      let arr = buildLaporanArray();
      if(k) arr = arr.filter(s=>s.kelas===k);
      if(r) arr = arr.filter(s=>s.rombel===r);
      renderLaporanTable(arr);
    }

    function printLaporan(){
      const html = document.getElementById('laporanCard').innerHTML;
      const w = window.open('', '', 'width=1000,height=700');
      w.document.write('<html><head><title>Laporan Absensi</title>');
      w.document.write('<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" />');
      w.document.write('</head><body>');
      w.document.write('<div class="container py-3">'+html+'</div>');
      w.document.write('</body></html>');
      w.document.close();
      w.print();
    }

    /********************
     * INIT ON LOAD      *
     ********************/
    document.addEventListener('DOMContentLoaded', ()=>{
      renderSiswaTable();
      updateDashboard();
      tickWaktu();
    });
  </script>
</body>
</html>
