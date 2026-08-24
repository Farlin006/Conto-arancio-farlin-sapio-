<!doctype html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Scarica Conto Personale — Packager</title>
  <style>
    body{font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial;display:flex;align-items:center;justify-content:center;height:100vh;margin:0;background:#090909;color:#fff}
    .card{background:#0f0f10;padding:28px;border-radius:12px;max-width:720px;width:95%;box-shadow:0 8px 30px rgba(0,0,0,.6);text-align:center}
    button{background:#0A84FF;border:none;color:#fff;padding:12px 18px;border-radius:10px;font-weight:700;cursor:pointer}
    pre{background:#0b0b0c;padding:12px;border-radius:8px;margin-top:18px;overflow:auto;text-align:left}
    a.link{color:#0A84FF}
  </style>
</head>
<body>
  <div class="card" role="main">
    <h1>Conto Personale — Download ZIP</h1>
    <p>Premi il pulsante per generare e scaricare il file <strong>conto-personale.zip</strong> contenente l'app completa.</p>
    <p><button id="downloadBtn">Scarica l'app (ZIP)</button></p>
    <p style="color:#9aa0a6;font-size:13px">Dopo il download, estrai lo ZIP e apri <code>index.html</code>.</p>
    <details style="margin-top:12px;color:#9aa0a6">
      <summary>Files inclusi</summary>
      <pre>
- index.html
- styles.css
- script.js
- README.md
      </pre>
    </details>
  </div>

  <script>
  // --- ZIP builder (minimal, no compression / STORED method) ---
  (function(){
    // Files to include (strings)
    const files = {
      "index.html": `<!doctype html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Conto Personale</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="app" id="app">
    <header class="header" role="banner">
      <div class="profile" aria-hidden="true">MC</div>
      <button id="detailsBtn" class="details" aria-label="Dettagli conto">Dettagli</button>
    </header>

    <main class="main" role="main">
      <section class="balance-section" aria-labelledby="saldoTitle">
        <div id="saldoTitle" class="currency">Saldo disponibile</div>
        <div id="balance" class="balance" aria-live="polite">€ 0,00</div>
      </section>

      <section class="actions" aria-label="Azioni conto">
        <button id="sendBtn" class="btn" type="button">Invia</button>
        <button id="addBtn" class="btn primary" type="button">Aggiungi fondi</button>
      </section>

      <section class="transactions" aria-labelledby="txTitle">
        <div class="tx-header">
          <h2 id="txTitle" class="tx-title">Oggi</h2>
          <div id="txCount" class="tx-count" aria-hidden="true"></div>
        </div>

        <ul id="txList" class="tx-list" role="list" aria-live="polite"></ul>

        <div id="emptyState" class="empty" hidden>
          Nessuna transazione.
        </div>
      </section>
    </main>

    <div id="toast" class="toast" role="status" aria-live="assertive" aria-atomic="true"></div>
  </div>

  <script type="module" src="script.js"></script>
</body>
</html>`,

      "styles.css": `:root{
  --bg:#000000;
  --surface:#1C1C1E;
  --muted:#8E8E93;
  --accent:#0A84FF;
  --positive:#32D74B;
  --text:#FFFFFF;
  --card-radius:16px;
  --pad:16px;
  --gap:12px;
  --header-height:72px;
}

*{box-sizing:border-box}
html,body,#app{height:100%;margin:0}
body{
  font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
  background:var(--bg);
  color:var(--text);
  -webkit-font-smoothing:antialiased;
  -moz-osx-font-smoothing:grayscale;
}

.app{display:flex;flex-direction:column;min-height:100vh}

/* Header */
.header{
  height:var(--header-height);
  display:flex;
  align-items:center;
  justify-content:space-between;
  padding:0 var(--pad);
}
.profile{
  width:48px;height:48px;background:#333;border-radius:50%;
  display:flex;align-items:center;justify-content:center;font-weight:700;font-size:18px;
}
.details{
  background:transparent;border:none;color:var(--accent);font-weight:600;font-size:16px;cursor:pointer;
}

/* Main content */
.main{display:flex;flex-direction:column;padding:0 var(--pad) 24px;gap:var(--gap);flex:1}

/* Balance */
.balance-section{display:flex;flex-direction:column;align-items:center;margin-top:8px}
.currency{font-size:14px;color:var(--muted);text-transform:uppercase;letter-spacing:1px}
.balance{font-size:44px;font-weight:700;margin:6px 0;letter-spacing:-1px}

/* Actions */
.actions{display:flex;gap:12px;justify-content:center;margin-top:8px}
.btn{background:#1C1C1E;color:var(--accent);border:none;padding:12px 18px;border-radius:12px;font-size:15px;font-weight:600;min-width:120px;cursor:pointer}
.btn.primary{background:var(--accent);color:var(--text)}
.btn:focus{outline:3px solid rgba(10,132,255,0.22);outline-offset:2px}

/* Transactions card */
.transactions{
  background:var(--surface);
  border-top-left-radius:22px;
  border-top-right-radius:22px;
  padding:18px;
  display:flex;
  flex-direction:column;
  gap:12px;
  /* make transactions area scrollable */
  flex:1;
  min-height:180px;
  max-height:calc(100vh - var(--header-height) - 220px);
  overflow:auto;
}

/* Header inside transactions */
.tx-header{display:flex;justify-content:space-between;align-items:center}
.tx-title{font-size:18px;font-weight:600;margin:0}
.tx-count{color:var(--muted);font-size:13px}

/* Transaction list */
.tx-list{list-style:none;padding:0;margin:0;display:flex;flex-direction:column;gap:8px}
.tx-item{display:flex;justify-content:space-between;align-items:center;padding:12px 0;border-bottom:1px solid rgba(255,255,255,0.04)}
.tx-left{display:flex;flex-direction:column}
.tx-name{font-weight:600;font-size:15px}
.tx-date{font-size:13px;color:var(--muted);margin-top:4px}
.tx-amount{font-weight:700;font-size:15px;text-align:right}
.tx-amount.positive{color:var(--positive)}
.tx-amount.negative{color:#FF453A}

/* Empty state */
.empty{text-align:center;color:var(--muted);padding:28px}

/* Toast */
.toast{position:fixed;left:50%;transform:translateX(-50%);bottom:24px;background:#111;padding:10px 14px;border-radius:10px;color:var(--text);box-shadow:0 6px 18px rgba(0,0,0,0.6);opacity:0;pointer-events:none;transition:opacity .25s}

/* Responsive tweaks */
@media (max-width:420px){
  .balance{font-size:34px}
  .btn{min-width:110px;padding:10px 12px}
}`,

      "script.js": `// Minimal single-file app logic
const locale = 'it-IT';
const currency = 'EUR';
const fmt = new Intl.NumberFormat(locale, { style: 'currency', currency });

const state = {
  balance: 17000000.00,
  transactions: [
    { id: genId(), name: 'Bonifico in entrata', date: new Date(), origin: "Fondo d'investimento", amount: 17000000.00 },
    { id: genId(), name: 'Starbucks', date: new Date(Date.now() - 1000*60*60*2), origin: "Carta fisica", amount: -4.50 },
  ]
};

// DOM refs
const balanceEl = document.getElementById('balance');
const txList = document.getElementById('txList');
const txCount = document.getElementById('txCount');
const emptyState = document.getElementById('emptyState');
const toast = document.getElementById('toast');

document.getElementById('addBtn').addEventListener('click', onAddFunds);
document.getElementById('sendBtn').addEventListener('click', onSend);

// Initial render
render();

function render(){
  renderBalance();
  renderTransactions();
}

function renderBalance(){
  balanceEl.textContent = fmt.format(state.balance);
}

function renderTransactions(){
  txList.innerHTML = '';
  if(state.transactions.length === 0){
    emptyState.hidden = false;
    txCount.textContent = '';
    return;
  }
  emptyState.hidden = true;
  txCount.textContent = \`\${state.transactions.length} transazione\${state.transactions.length>1?'i':''}\`;

  // Sort by date desc
  const sorted = [...state.transactions].sort((a,b)=>b.date - a.date);
  for(const tx of sorted){
    const li = document.createElement('li');
    li.className = 'tx-item';
    li.setAttribute('role','listitem');

    const left = document.createElement('div');
    left.className = 'tx-left';
    const name = document.createElement('div');
    name.className = 'tx-name';
    name.textContent = tx.name;
    const date = document.createElement('div');
    date.className = 'tx-date';
    date.textContent = \`\${timeOnly(tx.date)} • \${tx.origin || ''}\`;

    left.appendChild(name);
    left.appendChild(date);

    const amount = document.createElement('div');
    amount.className = 'tx-amount';
    amount.textContent = (tx.amount >= 0 ? '+ ' : '- ') + fmt.format(Math.abs(tx.amount));
    amount.classList.add(tx.amount >= 0 ? 'positive' : 'negative');

    li.appendChild(left);
    li.appendChild(amount);
    txList.appendChild(li);
  }
  // ensure newest visible
  txList.scrollTop = 0;
}

function onAddFunds(){
  const raw = prompt('Importo da aggiungere (es: 100.50):', '');
  if (!raw) return;
  const amount = parseFloat(raw.replace(',', '.'));
  if (Number.isNaN(amount) || amount <= 0) return showToast('Importo non valido');
  state.balance += amount;
  state.transactions.push({ id: genId(), name: 'Aggiunta fondi', date: new Date(), origin: 'Deposito', amount });
  render();
  showToast(\`Aggiunti \${fmt.format(amount)}\`);
}

function onSend(){
  const raw = prompt('Importo da inviare (es: 12.50):', '');
  if (!raw) return;
  const amount = parseFloat(raw.replace(',', '.'));
  if (Number.isNaN(amount) || amount <= 0) return showToast('Importo non valido');
  if (amount > state.balance) return showToast('Saldo insufficiente');
  const to = prompt('Destinatario (nome o conto):', 'Persona');
  state.balance -= amount;
  state.transactions.push({ id: genId(), name: \`Pagamento a \${to || 'destinatario'}\`, date: new Date(), origin: 'Carta/Bonifico', amount: -amount });
  render();
  showToast(\`Inviati \${fmt.format(amount)} a \${to || 'destinatario'}\`);
}

function showToast(msg, ms = 2500){
  toast.textContent = msg;
  toast.style.opacity = '1';
  toast.style.pointerEvents = 'auto';
  setTimeout(()=>{ toast.style.opacity = '0'; toast.style.pointerEvents = 'none'; }, ms);
}

function timeOnly(d){
  return new Date(d).toLocaleTimeString(locale, { hour: '2-digit', minute: '2-digit' });
}

function genId(){ return Math.random().toString(36).slice(2,9); }`,

      "README.md": `# Conto Personale — Web App (standalone)

Cosa contiene:
- index.html — struttura semantica e markup accessibile
- styles.css — stile dark, responsive e scroll interno per le transazioni
- script.js — logica minima: dati di esempio, formattazione valuta locale, azioni "Invia" e "Aggiungi fondi", toast

Come usare:
1. Metti tutti i file nella stessa cartella.
2. Apri \`index.html\` con un browser moderno (Chrome / Firefox / Safari).
3. Usa i pulsanti "Invia" e "Aggiungi fondi" per simulare transazioni.

Miglioramenti inclusi:
- Semantica (header/main/section)
- Accessibilità: aria-live per aggiornamenti, focus visibile, role=list/listitem
- Formattazione valuta con Intl.NumberFormat('it-IT', 'EUR')
- Lista transazioni scrollabile, ordinata per data
- Messaggi utente (prompt/toast) per feedback immediato

Possibili estensioni future:
- Convertire in SPA React/Vue o backend per persistenza (localStorage/Server)
- Modal e form custom al posto di prompt
- Autenticazione, mock API e test automatici
- Localizzazione multi-lingua dinamica`
    };

    // Utility: UTF-8 encoder
    function toUTF8Bytes(str){
      return new TextEncoder().encode(str);
    }

    // CRC32 function
    const CRC32_TABLE = (function(){
      let c, table = new Uint32Array(256);
      for(let n=0;n<256;n++){
        c = n;
        for(let k=0;k<8;k++){
          c = (c & 1) ? (0xEDB88320 ^ (c >>> 1)) : (c >>> 1);
        }
        table[n] = c >>> 0;
      }
      return table;
    })();
    function crc32(bytes){
      let crc = 0xFFFFFFFF;
      for(let i=0;i<bytes.length;i++){
        crc = (crc >>> 8) ^ CRC32_TABLE[(crc ^ bytes[i]) & 0xFF];
      }
      return (crc ^ 0xFFFFFFFF) >>> 0;
    }

    // Little-endian write helpers
    function u32(n){ return [n & 0xFF, (n>>>8)&0xFF, (n>>>16)&0xFF, (n>>>24)&0xFF]; }
    function u16(n){ return [n & 0xFF, (n>>>8)&0xFF]; }

    function buildZip(fileMap){
      const fileEntries = [];
      let localDirSize = 0;

      // Build local file headers and collect central directory data
      for(const name of Object.keys(fileMap)){
        const contentBytes = toUTF8Bytes(fileMap[name]);
        const crc = crc32(contentBytes);
        const compressedSize = contentBytes.length;
        const uncompressedSize = contentBytes.length;
        const fileNameBytes = toUTF8Bytes(name);

        // Local file header
        const localHeader = [
          0x50,0x4b,0x03,0x04,         // local file header signature
          ...u16(20),                  // version needed to extract
          ...u16(0),                   // general purpose bit flag
          ...u16(0),                   // compression method (0 = stored)
          ...u16(0),                   // last mod file time
          ...u16(0),                   // last mod file date
          ...u32(crc),                 // crc-32
          ...u32(compressedSize),      // compressed size
          ...u32(uncompressedSize),    // uncompressed size
          ...u16(fileNameBytes.length) // file name length
        ];

        const localHeaderBytes = new Uint8Array(localHeader.length + fileNameBytes.length + contentBytes.length);
        let offset = 0;
        localHeaderBytes.set(new Uint8Array(localHeader), offset); offset += localHeader.length;
        localHeaderBytes.set(fileNameBytes, offset); offset += fileNameBytes.length;
        localHeaderBytes.set(contentBytes, offset);

        fileEntries.push({
          name,
          localHeaderBytes,
          crc,
          compressedSize,
          uncompressedSize,
          fileNameBytes,
          localHeaderOffset: localDirSize
        });

        localDirSize += localHeaderBytes.length;
      }

      // Central directory
      let centralSize = 0;
      const centralParts = [];
      for(const e of fileEntries){
        const hdr = [
          0x50,0x4b,0x01,0x02,     // central file header signature
          ...u16(20),              // version made by
          ...u16(20),              // version needed to extract
          ...u16(0),               // general purpose bit flag
          ...u16(0),               // compression method
          ...u16(0),               // last mod file time
          ...u16(0),               // last mod file date
          ...u32(e.crc),           // crc-32
          ...u32(e.compressedSize),// compressed size
          ...u32(e.uncompressedSize),// uncompressed size
          ...u16(e.fileNameBytes.length), // file name length
          ...u16(0),               // extra field length
          ...u16(0),               // file comment length
          ...u16(0),               // disk number start
          ...u16(0),               // internal file attrs
          ...u32(0),               // external file attrs
          ...u32(e.localHeaderOffset) // relative offset of local header
        ];
        const part = new Uint8Array(hdr.length + e.fileNameBytes.length);
        part.set(new Uint8Array(hdr), 0);
        part.set(e.fileNameBytes, hdr.length);
        centralParts.push(part);
        centralSize += part.length;
      }

      // End of central directory
      const centralDirOffset = localDirSize;
      const eocd = [
        0x50,0x4b,0x05,0x06,       // end of central dir signature
        ...u16(0),                 // number of this disk
        ...u16(0),                 // number of the disk with the start of the central directory
        ...u16(fileEntries.length),// total number of entries in the central dir on this disk
        ...u16(fileEntries.length),// total number of entries in the central dir
        ...u32(centralSize),       // size of the central directory
        ...u32(centralDirOffset),  // offset of start of central directory with respect to the starting disk number
        ...u16(0)                  // .ZIP file comment length
      ];

      // Assemble full zip
      const totalSize = localDirSize + centralSize + eocd.length;
      const zipBytes = new Uint8Array(totalSize);
      let p = 0;
      // local files
      for(const e of fileEntries){
        zipBytes.set(e.localHeaderBytes, p);
        p += e.localHeaderBytes.length;
      }
      // central dir
      for(const part of centralParts){
        zipBytes.set(part, p);
        p += part.length;
      }
      // eocd
      zipBytes.set(new Uint8Array(eocd), p);
      p += eocd.length;

      return zipBytes;
    }

    // Download helper
    function downloadBlob(bytes, filename){
      const blob = new Blob([bytes], { type: 'application/zip' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = filename;
      document.body.appendChild(a);
      a.click();
      a.remove();
      setTimeout(()=>URL.revokeObjectURL(url), 5000);
    }

    // Attach click handler
    document.getElementById('downloadBtn').addEventListener('click', () => {
      try{
        const zipBytes = buildZip(files);
        downloadBlob(zipBytes, 'conto-personale.zip');
      }catch(err){
        alert('Errore durante la creazione dello ZIP: ' + err);
      }
    });
  })();
  </script>
</body>
</html>
