
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Absen Siswa - XI TSM-3</title>
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, sans-serif;
      margin: 0; padding: 24px;
      background: #0f172a; color: #e2e8f0;
    }
    .app {
      max-width: 900px; margin: 0 auto;
      display: grid; gap: 16px;
    }
    .card {
      background: #111827; border: 1px solid #1f2937;
      border-radius: 16px; padding: 18px; box-shadow: 0 8px 16px rgba(0,0,0,.2);
    }
    .row { display: flex; gap: 12px; align-items: center; }
    .nowrap { flex-wrap: nowrap; }
    .between { justify-content: space-between; }
    h1 { font-size: 24px; margin: 0; color: #f8fafc; }
    .name {
      font-size: 28px; font-weight: 700; letter-spacing: .3px; color: #f8fafc;
      user-select: none;
    }
    .badge {
      background: #1f2937; border: 1px solid #374151; padding: 6px 10px;
      border-radius: 999px; font-size: 12px; color: #cbd5e1; white-space: nowrap;
    }
    button {
      border: 0; border-radius: 12px; padding: 12px 14px;
      font-size: 16px; font-weight: 700; cursor: pointer;
      background: #334155; color: #e2e8f0; transition: transform .05s ease;
      user-select: none;
    }
    button:active { transform: translateY(1px); }
    button[disabled] { opacity: .5; cursor: not-allowed; }
    .btn-nav { padding: 12px 14px; }
    .btn-h { background: #22c55e; }
    .btn-a { background: #ef4444; }
    .btn-i { background: #f59e0b; }
    .btn-s { background: #10b981; }
    .btn-reset { background: #475569; }
    .btn-export { background: #3b82f6; }
    .grid {
      width: 100%; overflow: auto; border-radius: 12px; border: 1px solid #1f2937;
    }
    table { width: 100%; border-collapse: collapse; }
    th, td { padding: 10px 12px; border-bottom: 1px solid #1f2937; text-align: left; }
    th { position: sticky; top: 0; background: #0b1220; z-index: 1; }
    tr.marked td { background: #0b3a2a33; }
    .status-dot { width: 10px; height: 10px; display: inline-block; border-radius: 50%; margin-right: 6px; }
    .dot-h { background: #22c55e; }
    .dot-a { background: #ef4444; }
    .dot-i { background: #f59e0b; }
    .dot-s { background: #10b981; }
    .muted { color: #94a3b8; font-size: 12px; }
    .spacer { flex: 1 1 auto; }
    .group { display: flex; gap: 12px; align-items: center; }
    .lists { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; margin-top: 12px; }
    .list-card { background: #0b1220; border: 1px solid #1f2937; border-radius: 12px; padding: 12px; }
    .list-title { font-weight: 800; margin-bottom: 8px; font-size: 14px; }
    .t-alpha { color: #fca5a5; }
    .t-izin { color: #fcd34d; }
    .t-sakit { color: #86efac; }
    .list-item { font-size: 13px; padding: 6px 0; border-bottom: 1px dashed #1f2937; }
    .list-item:last-child { border-bottom: 0; }
    @media (max-width: 720px) {
      .lists { grid-template-columns: 1fr; }
    }
    @media (max-width: 560px) {
      h1 { font-size: 20px; }
      .name { font-size: 22px; }
      button { padding: 10px 12px; font-size: 15px; }
    }
  </style>
</head>
<body>
  <div class="app">
    <div class="card">
      <div class="row nowrap between">
        <h1>Absen Siswa XI TSM-3</h1>
        <span class="badge" id="tanggal"></span>
      </div>

      <div class="row nowrap between" style="margin-top:8px">
        <div class="group">
          <span class="badge" id="progress"></span>
          <span class="badge" id="posisi"></span>
        </div>
        <div class="group">
          <button class="btn-reset" id="resetBtn" title="Reset semua status (Ctrl+R)">Reset</button>
          <button class="btn-export" id="exportBtn" title="Ekspor CSV">Ekspor CSV</button>
        </div>
      </div>

      <div style="margin-top:12px" class="row">
        <div class="name" id="currentName">-</div>
      </div>

      <div class="row nowrap between">
        <div class="group">
          <button class="btn-h" id="btnH" title="Tandai Hadir (Enter/Space)">✓ = Hadir</button>
          <button class="btn-a" id="btnA" title="Tandai Alpha (A)">A = Alpha</button>
          <button class="btn-i" id="btnI" title="Tandai Izin (I)">I = Izin</button>
          <button class="btn-s" id="btnS" title="Tandai Sakit (S)">S = Sakit</button>
        </div>
        <div class="group">
          <button class="btn-nav" id="upBtn" title="Sebelumnya (Arrow Up)">▲</button>
          <button class="btn-nav" id="downBtn" title="Berikutnya (Arrow Down)">▼</button>
          <span class="badge" id="summary"></span>
        </div>
      </div>

      <!-- New lists section -->
      <div class="lists">
        <div class="list-card">
          <div class="list-title t-alpha">Alpha</div>
          <div id="listAlpha" class="list-body muted">Belum ada.</div>
        </div>
        <div class="list-card">
          <div class="list-title t-izin">Izin</div>
          <div id="listIzin" class="list-body muted">Belum ada.</div>
        </div>
        <div class="list-card">
          <div class="list-title t-sakit">Sakit</div>
          <div id="listSakit" class="list-body muted">Belum ada.</div>
        </div>
      </div>

      <div class="muted" style="margin-top:8px">
        Pintasan: ✓(Enter/Space) untuk Hadir, A/I/S untuk status lain, Arrow Up/Down untuk pindah siswa.
      </div>
    </div>

    <div class="card grid">
      <table id="table">
        <thead>
          <tr>
            <th style="width:64px">No</th>
            <th>Nama Siswa</th>
            <th style="width:160px">Status</th>
          </tr>
        </thead>
        <tbody></tbody>
      </table>
    </div>
  </div>

  <script>
    const NAMA = [
      "ACHMAD NOER IKHSAN",
      "ABDUL RASYID",
      "ARDIAN",
      "ARYA YUDISTIRA",
      "AZAM DWI ARIF DHARMA KUSU",
      "AZI FADILLAH FAUZANSYAH",
      "BIRLI MUHAIR ARRABY",
      "DAUD NABABAN",
      "DIKA NANDA PRAPTAMA",
      "DIWI ANGARA",
      "FEBRY FRANS VALENTINO",
      "GALIH TUA PANGGIHOTAN GUL",
      "GERRY BRANIVO PEHULISA SE",
      "HILDAN MAULANA SAPUTRA",
      "IMAM SAKTI WIGUNA AGUSTIA",
      "MUHAMAD AFRIZAL",
      "MUHAMAD RIZKY RAMADHAN",
      "MUHAMMAD HAFIZ ALFUQRON",
      "MUHAMMAD SYAM DIZKIRILLAH",
      "MUHAMMAD ZIKRI",
      "MUZAKKI MEAZZA",
      "NAJWA PAURI",
      "NOVAL FAUZI SAPUTRA",
      "PANDU WINATA",
      "RAFLI HABIBI",
      "REFAN SEPTIAN",
      "REPA BUDI PRATAMA",
      "REYHAN ABIAN",
      "RIFAL IKHLAS KARIM",
      "RIZKY MULLA JAYA",
      "YOHANES PRISCO SIANTURI"
    ];

    const tbody = document.querySelector("#table tbody");
    const currentName = document.getElementById("currentName");
    const progress = document.getElementById("progress");
    const posisi = document.getElementById("posisi");
    const summary = document.getElementById("summary");
    const tanggal = document.getElementById("tanggal");

    const listAlpha = document.getElementById("listAlpha");
    const listIzin = document.getElementById("listIzin");
    const listSakit = document.getElementById("listSakit");

    const btnH = document.getElementById("btnH");
    const btnA = document.getElementById("btnA");
    const btnI = document.getElementById("btnI");
    const btnS = document.getElementById("btnS");
    const upBtn = document.getElementById("upBtn");
    const downBtn = document.getElementById("downBtn");
    const resetBtn = document.getElementById("resetBtn");
    const exportBtn = document.getElementById("exportBtn");

    let idx = 0;
    let status = Array(NAMA.length).fill(""); // "", "H", "A", "I", "S"

    function fmtDate(d) {
      return d.toLocaleDateString("id-ID", { weekday: "long", year: "numeric", month: "long", day: "numeric" });
    }

    function renderTable() {
      tbody.innerHTML = "";
      NAMA.forEach((n, i) => {
        const tr = document.createElement("tr");
        if (status[i]) tr.classList.add("marked");
        const tdNo = document.createElement("td");
        const tdNm = document.createElement("td");
        const tdSt = document.createElement("td");
        tdNo.textContent = i + 1;
        tdNm.textContent = n;
        const lab = status[i] || "";
        const cls = lab ? "dot-" + lab.toLowerCase() : "";
        const labelText = lab === "H" ? "Hadir" :
                          lab === "A" ? "Alpha" :
                          lab === "I" ? "Izin" :
                          lab === "S" ? "Sakit" : "";
        tdSt.innerHTML = lab
          ? `<span class="status-dot ${cls}"></span>${labelText} (${lab})`
          : `<span class="muted">Belum diisi</span>`;
        tr.appendChild(tdNo); tr.appendChild(tdNm); tr.appendChild(tdSt);
        tbody.appendChild(tr);
      });
      updateProgress();
      renderLists();
    }

    function renderLists() {
      const alpha = [], izin = [], sakit = [];
      status.forEach((st, i) => {
        if (st === "A") alpha.push(`${i+1}. ${NAMA[i]}`);
        else if (st === "I") izin.push(`${i+1}. ${NAMA[i]}`);
        else if (st === "S") sakit.push(`${i+1}. ${NAMA[i]}`);
      });
      listAlpha.innerHTML = alpha.length ? alpha.map(n => `<div class="list-item">${n}</div>`).join("") : "<span class='muted'>Belum ada.</span>";
      listIzin.innerHTML  = izin.length  ? izin.map(n => `<div class="list-item">${n}</div>`).join("")  : "<span class='muted'>Belum ada.</span>";
      listSakit.innerHTML = sakit.length ? sakit.map(n => `<div class="list-item">${n}</div>`).join("") : "<span class='muted'>Belum ada.</span>";
    }

    function updateCurrent() {
      currentName.textContent = NAMA[idx];
      posisi.textContent = (idx + 1) + " / " + NAMA.length;
    }

    function move(delta) {
      idx = Math.min(Math.max(idx + delta, 0), NAMA.length - 1);
      updateCurrent();
      // no auto scroll
    }

    function setStatus(s) {
      status[idx] = s;
      renderTable();
      move(1); // auto next
      save();
    }

    function updateProgress() {
      const filled = status.filter(Boolean).length;
      progress.textContent = `Progress: ${filled}/${NAMA.length}`;
      const alpha = status.filter(x => x === "A").length;
      const izin = status.filter(x => x === "I").length;
      const sakit = status.filter(x => x === "S").length;
      const hadir = status.filter(x => x === "H").length;
      summary.textContent = `H:${hadir} A:${alpha} I:${izin} S:${sakit}`;
    }

    function resetAll() {
      if (!confirm("Yakin reset semua status?")) return;
      status = Array(NAMA.length).fill("");
      idx = 0;
      renderTable();
      updateCurrent();
      save();
    }

    function save() {
      const key = "absen_xitsm3_" + new Date().toISOString().slice(0,10);
      localStorage.setItem(key, JSON.stringify(status));
    }

    function load() {
      const key = "absen_xitsm3_" + new Date().toISOString().slice(0,10);
      const raw = localStorage.getItem(key);
      if (raw) {
        try { status = JSON.parse(raw) || status; } catch {}
      }
    }

    function exportCSV() {
      const rows = [["No","Nama Siswa","Status","Tanggal"]];
      const tgl = new Date().toISOString().slice(0,10);
      NAMA.forEach((n,i)=> rows.push([i+1, n, status[i] || "", tgl]));
      const csv = rows.map(r => r.map(v => `"${String(v).replace(/"/g,'""')}"`).join(",")).join("\n");
      const blob = new Blob([csv], {type: "text/csv;charset=utf-8;"});
      const url = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url; a.download = "absen_siswa_"+tgl+".csv";
      a.click(); URL.revokeObjectURL(url);
    }

    // Events
    btnH.addEventListener("click", () => setStatus("H"));
    btnA.addEventListener("click", () => setStatus("A"));
    btnI.addEventListener("click", () => setStatus("I"));
    btnS.addEventListener("click", () => setStatus("S"));
    upBtn.addEventListener("click", () => move(-1));
    downBtn.addEventListener("click", () => move(1));
    resetBtn.addEventListener("click", resetAll);
    exportBtn.addEventListener("click", exportCSV);

    document.addEventListener("keydown", (e) => {
      if (e.key === "ArrowUp") move(-1);
      else if (e.key === "ArrowDown") move(1);
      else if (e.key === " " || e.key === "Enter") { e.preventDefault(); setStatus("H"); }
      else if (e.key.toLowerCase() === "a") setStatus("A");
      else if (e.key.toLowerCase() === "i") setStatus("I");
      else if (e.key.toLowerCase() === "s") setStatus("S");
      else if (e.ctrlKey && e.key.toLowerCase() === "r") { e.preventDefault(); resetAll(); }
    });

    // Init
    tanggal.textContent = fmtDate(new Date());
    load();
    renderTable();
    updateCurrent();
  </script>
</body>
</html>
