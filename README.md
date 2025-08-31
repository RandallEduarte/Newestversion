<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Bookkeeping Pro — Login & App</title>
<style>
:root{
  --bg:#071026;--card:#0d1530;--muted:#0f2346;--text:#e6eefc;--sub:#9fb0d8;--line:#10223d;
  --accent1:#4f9cfb;--accent2:#22c55e;--pill:#0b1a35;--danger:#ef4444;
}
*{box-sizing:border-box}
body{margin:0;background:linear-gradient(180deg,#071026,#041425);color:var(--text);font-family:Inter,system-ui,Roboto,Arial;min-height:100vh}
.container{max-width:960px;margin:0 auto;padding:14px}
.header{display:flex;justify-content:space-between;align-items:center;margin-bottom:12px}
.brand{display:flex;align-items:center;gap:12px}
.logo{width:42px;height:42px;border-radius:8px;background:linear-gradient(135deg,var(--accent1),var(--accent2))}
.title{font-size:18px;font-weight:600}
.small{color:var(--sub);font-size:13px}
.card{background:var(--card);border-radius:12px;padding:12px;border:1px solid var(--line);box-shadow:0 8px 30px rgba(0,0,0,.4)}
.row{display:flex;gap:8px;align-items:center}
.btn{padding:8px 12px;border-radius:10px;border:1px solid var(--line);background:transparent;color:var(--text);cursor:pointer}
.btn.primary{background:linear-gradient(135deg,var(--accent1),var(--accent2));border:none;color:#021014}
.btn.ghost{background:transparent}
.btn.danger{background:rgba(239,68,68,.12);color:#ffdede;border-color:#7f1d1d}
.input,select{padding:8px;border-radius:10px;border:1px solid var(--line);background:#081428;color:var(--text);width:100%}
.hide{display:none}
nav{position:fixed;left:0;right:0;bottom:0;padding:8px;background:rgba(2,6,12,.6);border-top:1px solid var(--line);display:flex;justify-content:center;gap:8px}
nav a{display:flex;flex-direction:column;align-items:center;gap:4px;padding:8px;border-radius:10px;color:var(--sub);text-decoration:none;font-size:13px}
nav a.active{background:var(--muted);color:var(--text)}
table{width:100%;border-collapse:collapse;margin-top:8px}
th,td{padding:8px;border-bottom:1px solid var(--line);text-align:left}
.notice{padding:8px;border-radius:8px;margin-top:8px;background:rgba(255,255,255,0.02);color:var(--sub);font-size:13px}
.err{color:var(--danger);font-size:13px;margin-top:6px}
@media(min-width:900px){ .container{padding:24px} }
</style>
</head>
<body>
  <div class="container">
    <div class="header">
      <div class="brand">
        <div class="logo"></div>
        <div>
          <div class="title">Bookkeeping Pro</div>
          <div class="small">Secure — Cloud synced</div>
        </div>
      </div>

      <div class="row">
        <div id="companyBadge" class="small" style="display:none">Company</div>
        <div id="syncStatus" class="small">Offline</div>
        <button id="btnLogout" class="btn ghost hide">Logout</button>
      </div>
    </div>

    <!-- LOGIN / SIGNUP (visible when signed out) -->
    <section id="view-auth" class="card" style="max-width:720px;margin:0 auto;">
      <h3 style="margin:0 0 8px">Sign in or create an account</h3>
      <div class="small">Use Email / Password</div>

      <div style="margin-top:12px;display:grid;gap:8px">
        <input id="authEmail" class="input" type="email" placeholder="you@example.com" />
        <input id="authPass" class="input" type="password" placeholder="Password (min 6 chars)" />
        <div style="display:flex;gap:8px">
          <button id="btnLogin" class="btn primary">Log in</button>
          <button id="btnSignup" class="btn">Sign up</button>
        </div>
        <div id="authMessage" class="err hide"></div>
        <div class="notice">Important: do not open this file using <code>file://</code>. Serve it over HTTP(S) (eg. `python -m http.server` or Firebase Hosting).</div>
      </div>
    </section>

    <!-- APP (visible when signed in) -->
    <section id="view-app" class="hide">
      <!-- companies modal inline -->
      <div id="companyModal" class="card hide" style="max-width:820px;margin:0 auto 12px auto">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <strong>Companies</strong>
          <div><button id="btnCloseCompanies" class="btn">Close</button></div>
        </div>
        <div id="companyList" style="margin-top:10px"></div>
        <!-- create company panel appended by JS -->
      </div>

      <!-- Main app UI -->
      <main id="appMain">
        <div id="dashboard" class="card" style="margin-bottom:12px">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <div><strong>Dashboard</strong><div class="small">Quick actions</div></div>
            <div class="small">AR: <span id="kpiAR">0.00</span> • Revenue: <span id="kpiREV">0.00</span></div>
          </div>
          <div style="margin-top:12px;display:flex;gap:8px;flex-wrap:wrap">
            <button id="qaNewClient" class="btn">New Client</button>
            <button id="qaNewInvoice" class="btn">New Invoice</button>
            <button id="qaNewJE" class="btn">New Journal</button>
            <button id="qaOpenCompanies" class="btn">Companies</button>
          </div>
        </div>

        <!-- Clients -->
        <div id="panel-clients" class="card hide" style="margin-bottom:12px">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <strong>Clients</strong>
            <div class="small">Manage customers</div>
          </div>
          <div style="margin-top:10px;display:flex;gap:8px;flex-wrap:wrap">
            <input id="clientName" class="input" placeholder="Client name"/>
            <input id="clientEmail" class="input" placeholder="Email"/>
            <input id="clientPhone" class="input" placeholder="Phone"/>
            <button id="btnAddClient" class="btn primary">Add</button>
          </div>
          <div style="margin-top:10px;overflow:auto;max-height:40vh">
            <table id="clientTable"><thead><tr><th>Name</th><th>Email</th><th>Phone</th><th>Balance</th><th></th></tr></thead><tbody></tbody></table>
          </div>
        </div>

        <!-- Invoices -->
        <div id="panel-invoices" class="card hide" style="margin-bottom:12px">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <strong>Invoices</strong><div class="small">Create & manage</div>
          </div>
          <div style="margin-top:10px;display:flex;gap:8px;flex-wrap:wrap">
            <input id="invDate" class="input" type="date" style="max-width:160px"/>
            <input id="invNo" class="input" placeholder="Invoice No" style="max-width:220px"/>
            <select id="invClient" class="input" style="min-width:200px"></select>
            <input id="invQty" class="input" type="number" placeholder="Qty" style="max-width:100px"/>
            <input id="invRate" class="input" type="number" placeholder="Rate" style="max-width:120px"/>
            <button id="btnSaveInvoice" class="btn primary">Save Invoice</button>
          </div>
          <div style="margin-top:10px;overflow:auto;max-height:40vh">
            <table id="invoiceTable"><thead><tr><th>Date</th><th>No</th><th>Client</th><th>Amount</th><th>Status</th><th></th></tr></thead><tbody></tbody></table>
          </div>
        </div>

        <!-- Journal -->
        <div id="panel-journal" class="card hide" style="margin-bottom:12px">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <strong>Journal</strong><div class="small">Post entries</div>
          </div>
          <div style="margin-top:10px;display:flex;gap:8px;flex-wrap:wrap">
            <input id="jeDate" class="input" type="date" style="max-width:160px"/>
            <input id="jeRef" class="input" placeholder="Ref" style="max-width:220px"/>
            <input id="jeMemo" class="input" placeholder="Memo"/>
            <button id="btnAddLine" class="btn">Add Line</button>
            <button id="btnPostJE" class="btn primary">Post</button>
          </div>
          <div id="jeLines" class="card" style="margin-top:10px"></div>
          <div style="margin-top:10px;overflow:auto;max-height:36vh">
            <table id="journalTable"><thead><tr><th>Date</th><th>Ref</th><th>Memo</th><th></th></tr></thead><tbody></tbody></table>
          </div>
        </div>

        <!-- Accounts -->
        <div id="panel-accounts" class="card hide" style="margin-bottom:12px">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <strong>Accounts</strong><div class="small">Chart of accounts</div>
          </div>
          <div style="margin-top:8px;display:flex;gap:8px">
            <input id="accName" class="input" placeholder="Account name"/>
            <select id="accType" class="input" style="max-width:160px"><option>Asset</option><option>Liability</option><option>Equity</option><option>Income</option><option>Expense</option></select>
            <button id="btnAddAccount" class="btn primary">Add</button>
          </div>
          <div style="margin-top:10px;overflow:auto;max-height:36vh">
            <table id="accTable"><thead><tr><th>Name</th><th>Type</th><th></th></tr></thead><tbody></tbody></table>
          </div>
        </div>

        <!-- Settings -->
        <div id="panel-settings" class="card hide">
          <strong>Settings</strong>
          <div class="small" style="margin-top:6px">Currency & default accounts</div>
          <div style="display:flex;gap:8px;margin-top:8px">
            <input id="setCurrency" class="input" placeholder="Currency (USD)"/>
            <input id="setAR" class="input" placeholder="AR Account (Accounts Receivable)"/>
            <input id="setREV" class="input" placeholder="Revenue Account (Sales Revenue)"/>
            <button id="btnSaveSettings" class="btn primary">Save</button>
          </div>
        </div>
      </main>
    </section>

    <div style="height:94px"></div>
  </div>

  <nav>
    <a data-panel="home" class="active">🏠<span class="small">Home</span></a>
    <a data-panel="invoices">🧾<span class="small">Invoices</span></a>
    <a data-panel="journal">📒<span class="small">Journal</span></a>
    <a data-panel="clients">👤<span class="small">Clients</span></a>
    <a data-panel="accounts">🏷️<span class="small">Accounts</span></a>
    <a data-panel="settings">⚙️<span class="small">Settings</span></a>
  </nav>

<script type="module">
/* ===========================
   Firebase (your project)
   =========================== */
import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.12.3/firebase-app.js';
import { getAuth, onAuthStateChanged, signInWithEmailAndPassword, createUserWithEmailAndPassword, signOut } from 'https://www.gstatic.com/firebasejs/10.12.3/firebase-auth.js';
import { getDatabase, ref, set, push, onValue, get, update, serverTimestamp } from 'https://www.gstatic.com/firebasejs/10.12.3/firebase-database.js';

// <-- your firebase config (lizardo-sea-oil) -->
const firebaseConfig = {
  apiKey: "AIzaSyB0GBOkpFyLIp8RJDARHEgb_W8q1W40v40",
  authDomain: "lizardo-sea-oil.firebaseapp.com",
  databaseURL: "https://lizardo-sea-oil-default-rtdb.firebaseio.com",
  projectId: "lizardo-sea-oil",
  storageBucket: "lizardo-sea-oil.firebasestorage.app",
  messagingSenderId: "594882681824",
  appId: "1:594882681824:web:6d70cc3c27be2946632985",
  measurementId: "G-QW08V2PW52"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getDatabase(app);

/* ===========================
   Local app state & defaults
   =========================== */
const State = { clients:[], invoices:[], journal:[], accounts:[], settings:{ arAccount:'Accounts Receivable', revAccount:'Sales Revenue', cashAccount:'Cash' }, currency:'USD' };
let currentUser = null;
let currentCompany = localStorage.getItem('bkpro.company') || null;

const DEFAULT_ACCOUNTS = [
  { id: crypto.randomUUID(), name:'Cash', type:'Asset' },
  { id: crypto.randomUUID(), name:'Accounts Receivable', type:'Asset' },
  { id: crypto.randomUUID(), name:'Sales Revenue', type:'Income' },
  { id: crypto.randomUUID(), name:'Service Revenue', type:'Income' },
  { id: crypto.randomUUID(), name:"Owner's Capital", type:'Equity' },
  { id: crypto.randomUUID(), name:'Salaries Expense', type:'Expense' },
  { id: crypto.randomUUID(), name:'Rent Expense', type:'Expense' }
];

/* ===========================
   Small UI helpers
   =========================== */
const $ = s=>document.querySelector(s), $$=s=>Array.from(document.querySelectorAll(s));
const fmt = n=>Number(n||0).toFixed(2);
function show(selector){ $(selector)?.classList.remove('hide'); }
function hide(selector){ $(selector)?.classList.add('hide'); }
function setSyncStatus(t){ $('#syncStatus').textContent = t; }

/* ===========================
   Auth UI behavior (separation)
   =========================== */
function showAuthView(){ show('#view-auth'); hide('#view-app'); hide('#btnLogout'); hide('#companyBadge'); setSyncStatus('Signed out'); }
function showAppView(){ hide('#view-auth'); show('#view-app'); show('#btnLogout'); setSyncStatus('Online'); $('#companyBadge').style.display='inline-block'; }

$('#btnLogin').addEventListener('click', async ()=>{
  const email = $('#authEmail').value.trim(), pass = $('#authPass').value;
  if(!email||!pass){ showAuthError('Enter email and password'); return; }
  try{ $('#authMessage').classList.add('hide'); await signInWithEmailAndPassword(auth,email,pass); }catch(e){ showAuthError(e.message||e); }
});
$('#btnSignup').addEventListener('click', async ()=>{
  const email = $('#authEmail').value.trim(), pass = $('#authPass').value;
  if(!email||!pass){ showAuthError('Enter email and password'); return; }
  try{ $('#authMessage').classList.add('hide'); await createUserWithEmailAndPassword(auth,email,pass); }catch(e){ showAuthError(e.message||e); }
});
$('#btnLogout').addEventListener('click', async ()=> {
  try{ await signOut(auth); currentCompany=null; localStorage.removeItem('bkpro.company'); showAuthView(); }catch(e){ alert('Logout failed: '+(e.message||e)); }
});
function showAuthError(msg){ $('#authMessage').textContent = msg; $('#authMessage').classList.remove('hide'); }

/* ===========================
   Nav (bottom) show panels
   =========================== */
function openPanel(name){
  // hide panels and show correct one
  ['#dashboard','#panel-clients','#panel-invoices','#panel-journal','#panel-accounts','#panel-settings'].forEach(id=> hide(id));
  if(name==='home') show('#dashboard');
  if(name==='clients') show('#panel-clients');
  if(name==='invoices') show('#panel-invoices');
  if(name==='journal') show('#panel-journal');
  if(name==='accounts') show('#panel-accounts');
  if(name==='settings') show('#panel-settings');
  // nav active
  $$('nav a').forEach(a=> a.classList.toggle('active', a.dataset.panel===name));
}
$$('nav a').forEach(a=> a.addEventListener('click', ()=> openPanel(a.dataset.panel)));
openPanel('home');

/* ===========================
   Company list + Create Company (fixed)
   =========================== */
function renderCompanyList(list){
  const wrap = $('#companyList'); if(!wrap) return;
  const items = list || {}; const keys = Object.keys(items || {});
  wrap.innerHTML = keys.length ? keys.map(id=> {
    const c = items[id];
    const selected = (id===currentCompany) ? ' (selected)' : '';
    return `<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
      <div><strong>${c.name||'Company'}</strong><div class="small">${selected}</div></div>
      <div style="display:flex;gap:6px">
        <button class="btn" data-id="${id}" data-act="open">Open</button>
        <button class="btn" data-id="${id}" data-act="rename">Rename</button>
      </div></div>`;
  }).join('') : `<div class="small">No companies yet. Use the panel below to create one.</div>`;
}

async function initCreateCompanyPanel(){
  const wrap = $('#companyList'); if(!wrap) return;
  if(document.getElementById('btnCreateCompany')) return; // already inited
  const panel = document.createElement('div');
  panel.style.marginTop='12px';
  panel.innerHTML = `
    <div style="border-top:1px solid var(--line);padding-top:10px">
      <div class="small">Create a new company</div>
      <div style="display:flex;gap:8px;margin-top:8px">
        <input id="newCompanyName" class="input" placeholder="Company name"/>
        <button id="btnCreateCompany" class="btn primary">Create</button>
      </div>
      <div id="createCompanyErr" class="err hide"></div>
    </div>`;
  wrap.appendChild(panel);

  panel.querySelector('#btnCreateCompany').addEventListener('click', async ()=>{
    const nameEl = panel.querySelector('#newCompanyName');
    const name = (nameEl.value||'').trim();
    const errEl = panel.querySelector('#createCompanyErr');
    errEl.classList.add('hide');
    if(!name){ errEl.textContent = 'Enter a company name'; errEl.classList.remove('hide'); return; }
    if(!currentUser){ errEl.textContent = 'Not signed in'; errEl.classList.remove('hide'); return; }
    try{
      setSyncStatus('Creating company…');
      const uid = currentUser.uid;
      // push company under users/{uid}/companies
      const companiesRef = ref(db, `users/${uid}/companies`);
      const newRef = push(companiesRef);
      const newId = newRef.key;
      await set(ref(db, `users/${uid}/companies/${newId}`), { name });
      // seed default data for the new company
      await set(ref(db, `users/${uid}/data/${newId}/accounts`), DEFAULT_ACCOUNTS);
      await set(ref(db, `users/${uid}/data/${newId}/clients`), []);
      await set(ref(db, `users/${uid}/data/${newId}/invoices`), []);
      await set(ref(db, `users/${uid}/data/${newId}/journal`), []);
      await set(ref(db, `users/${uid}/data/${newId}/settings`), { createdAt: serverTimestamp() });
      // auto-select the company
      await selectCompany(newId);
      nameEl.value='';
      $('#companyModal').classList.add('hide');
      setSyncStatus('Online');
    }catch(err){
      console.error('create company error', err);
      errEl.textContent = err.message || String(err);
      errEl.classList.remove('hide');
      setSyncStatus('Error');
    }
  });

  // handle open/rename
  wrap.addEventListener('click', async (e)=>{
    const btn = e.target.closest('button[data-act]');
    if(!btn) return;
    const id = btn.dataset.id;
    if(btn.dataset.act==='open'){ await selectCompany(id); $('#companyModal').classList.add('hide'); }
    if(btn.dataset.act==='rename'){
      const newName = prompt('New company name');
      if(!newName) return;
      try{ await update(ref(db, `users/${currentUser.uid}/companies/${id}`), { name:newName }); }catch(err){ alert('Rename failed: '+(err.message||err)); }
    }
  });
}

/* ===========================
   Data binding for selected company
   =========================== */
let unsub = [];
async function selectCompany(companyId){
  if(!currentUser) { alert('Sign in first'); return; }
  currentCompany = companyId;
  localStorage.setItem('bkpro.company', companyId);
  // remove previous listeners
  unsub.forEach(f=> typeof f === 'function' && f());
  unsub = [];
  // show company name
  const nameRef = ref(db, `users/${currentUser.uid}/companies/${companyId}`);
  const unName = onValue(nameRef, snap=> {
    const v = snap.val(); $('#companyBadge').textContent = v?.name || 'Company'; $('#companyBadge').style.display='inline-block';
  });
  unsub.push(()=> unName && unName());
  // attach data listeners
  const base = `users/${currentUser.uid}/data/${companyId}`;
  unsub.push(onValue(ref(db, `${base}/clients`), snap=> { State.clients = normalize(snap); renderClients(); }));
  unsub.push(onValue(ref(db, `${base}/invoices`), snap=> { State.invoices = normalize(snap); renderInvoices(); renderKPIs(); }));
  unsub.push(onValue(ref(db, `${base}/journal`), snap=> { State.journal = normalize(snap); renderJournal(); }));
  unsub.push(onValue(ref(db, `${base}/accounts`), snap=> { State.accounts = normalize(snap); renderAccounts(); }));
  unsub.push(onValue(ref(db, `${base}/settings`), snap=> { const v=snap.val(); if(v) Object.assign(State.settings, v); }));

  // ensure accounts seeded if empty
  try{
    const accSnap = await get(ref(db, `${base}/accounts`));
    const accVal = accSnap.val();
    const isEmpty = !accVal || (Array.isArray(accVal) && accVal.length===0) || (typeof accVal==='object' && Object.keys(accVal).length===0);
    if(isEmpty) await set(ref(db, `${base}/accounts`), DEFAULT_ACCOUNTS);
  }catch(e){ console.warn('seed accounts check failed', e); }
}

function normalize(snap){
  const v = snap.val();
  if(!v) return [];
  if(Array.isArray(v)) return v.filter(Boolean);
  return Object.keys(v).map(k=> Object.assign({ id:k }, v[k]));
}

/* ===========================
   Renderers
   =========================== */
function renderClients(){
  $('#invClient').innerHTML = `<option value="">Select client</option>` + State.clients.map(c=>`<option value="${c.id}">${c.name}</option>`).join('');
  const tbody = $('#clientTable tbody');
  tbody.innerHTML = State.clients.length ? State.clients.map(c=>`<tr><td>${c.name}</td><td>${c.email||''}</td><td>${c.phone||''}</td><td>${fmt(calcClientBalance(c.id))}</td><td><button class="btn" data-act="ledger" data-id="${c.id}">Ledger</button></td></tr>`).join('') : `<tr><td colspan="5" class="small">No clients yet</td></tr>`;
}
function calcClientBalance(clientId){ return State.invoices.filter(i=>i.clientId===clientId).reduce((s,i)=> s + (i.amount||0) - (i.paid||0), 0); }

function renderInvoices(){
  const tbody = $('#invoiceTable tbody');
  tbody.innerHTML = State.invoices.length ? State.invoices.map(i=>{
    const client = State.clients.find(c=>c.id===i.clientId) || {};
    return `<tr><td>${i.date||''}</td><td>${i.number||''}</td><td>${client.name||'—'}</td><td>${fmt(i.amount||0)}</td><td>${i.status||'Unpaid'}</td><td><button class="btn" data-act="pay" data-id="${i.id}">Pay</button> <button class="btn ghost" data-act="del" data-id="${i.id}">Delete</button></td></tr>`;
  }).join('') : `<tr><td colspan="6" class="small">No invoices yet</td></tr>`;
}

function renderJournal(){
  $('#journalTable tbody').innerHTML = State.journal.length ? State.journal.map(j=>`<tr><td>${j.date||''}</td><td>${j.ref||''}</td><td>${j.memo||''}</td><td><button class="btn danger" data-id="${j.id}">Delete</button></td></tr>`).join('') : `<tr><td colspan="4" class="small">No journal entries</td></tr>`;
  $('#jeLines').innerHTML=''; addJournalLineRow($('#jeLines'));
}

function renderAccounts(){
  $('#accTable tbody').innerHTML = State.accounts.length ? State.accounts.map(a=>`<tr><td>${a.name}</td><td>${a.type}</td><td><button class="btn ghost" data-act="del-acc" data-id="${a.id}">Delete</button></td></tr>`).join('') : `<tr><td colspan="3" class="small">No accounts</td></tr>`;
  $$('#jeLines select.accSel').forEach(sel=>{
    const val = sel.value;
    sel.innerHTML = `<option value="">Select account</option>` + State.accounts.map(a=>`<option value="${a.name}">${a.name} (${a.type})</option>`).join('');
    if(val) sel.value = val;
  });
}

function renderKPIs(){
  const ar = State.invoices.reduce((s,i)=> s + (i.amount||0) - (i.paid||0), 0);
  const rev = State.invoices.reduce((s,i)=> s + (i.paid||0), 0);
  $('#kpiAR').textContent = fmt(ar);
  $('#kpiREV').textContent = fmt(rev);
}

/* ===========================
   Interactions (clients/invoices/journal/accounts)
   =========================== */
$('#qaNewClient').addEventListener('click', ()=> { openPanel('clients'); show('#panel-clients'); });
$('#qaNewInvoice').addEventListener('click', ()=> { openPanel('invoices'); show('#panel-invoices'); });
$('#qaNewJE').addEventListener('click', ()=> { openPanel('journal'); show('#panel-journal'); });
$('#qaOpenCompanies').addEventListener('click', ()=> $('#companyModal').classList.toggle('hide'));

$('#btnAddClient').addEventListener('click', async ()=>{
  if(!currentUser||!currentCompany) return alert('Select a company first');
  const name = $('#clientName').value.trim(); if(!name) return alert('Name required');
  const obj = { id:crypto.randomUUID(), name, email:$('#clientEmail').value.trim(), phone:$('#clientPhone').value.trim() };
  State.clients.push(obj);
  try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/clients`), State.clients); $('#clientName').value=''; $('#clientEmail').value=''; $('#clientPhone').value=''; }catch(e){ alert('Save failed: '+(e.message||e)); }
});

$('#btnSaveInvoice').addEventListener('click', async ()=>{
  if(!currentUser||!currentCompany) return alert('Select a company first');
  const clientId = $('#invClient').value; if(!clientId) return alert('Choose client');
  const qty = Number($('#invQty').value||0); const rate = Number($('#invRate').value||0); const amount = qty*rate;
  const id = crypto.randomUUID(); const date = $('#invDate').value || new Date().toISOString().slice(0,10);
  const number = $('#invNo').value || ('INV-'+String(State.invoices.length+1).padStart(4,'0'));
  const obj = { id, date, number, clientId, qty, rate, amount, status:'Unpaid', paid:0 };
  State.invoices.push(obj);
  // autopost journal (AR DR, Revenue CR)
  State.journal.push({ id:crypto.randomUUID(), date, ref:number, memo:`Invoice ${number}`, lines:[ { accountName: State.settings.arAccount, debit: amount, credit: 0, clientId }, { accountName: State.settings.revAccount, debit:0, credit:amount } ] });
  try{
    await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/invoices`), State.invoices);
    await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/journal`), State.journal);
    $('#invQty').value=''; $('#invRate').value=''; $('#invNo').value=''; $('#invDate').value='';
  }catch(e){ alert('Save invoice failed: '+(e.message||e)); }
});

$('#invoiceTable').addEventListener('click', async (e)=>{
  const btn = e.target.closest('button[data-act]'); if(!btn) return;
  const id = btn.dataset.id; const inv = State.invoices.find(x=>x.id===id); if(!inv) return;
  if(btn.dataset.act==='del'){ State.invoices = State.invoices.filter(x=>x.id!==id); try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/invoices`), State.invoices); }catch(e){ alert(e.message||e); } }
  if(btn.dataset.act==='pay'){
    const due = (inv.amount||0)-(inv.paid||0);
    const amt = Number(prompt('Payment amount', String(due))||0); if(!amt) return;
    inv.paid = (inv.paid||0) + amt; inv.status = inv.paid>=inv.amount ? 'Paid' : 'Partial';
    State.journal.push({ id:crypto.randomUUID(), date:new Date().toISOString().slice(0,10), ref:inv.number, memo:`Payment ${inv.number}`, lines:[ { accountName: State.settings.cashAccount, debit: amt, credit: 0 }, { accountName: State.settings.arAccount, debit:0, credit:amt, clientId: inv.clientId } ] });
    try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/invoices`), State.invoices); await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/journal`), State.journal); }catch(e){ alert(e.message||e); }
  }
});

$('#btnAddLine').addEventListener('click', ()=> addJournalLineRow($('#jeLines')));
$('#btnPostJE').addEventListener('click', async ()=>{
  if(!currentUser||!currentCompany) return alert('Select a company first');
  const accs = $$('#jeLines select.accSel'), clients = $$('#jeLines select.clientSel'), debits = $$('#jeLines .debit'), credits = $$('#jeLines .credit');
  const lines = [];
  for(let i=0;i<accs.length;i++){
    const a = accs[i].value; if(!a) continue;
    lines.push({ accountName: a, clientId: clients[i]?.value || undefined, debit: Number(debits[i]?.value||0), credit: Number(credits[i]?.value||0) });
  }
  const totalD = lines.reduce((s,l)=> s + (l.debit||0), 0);
  const totalC = lines.reduce((s,l)=> s + (l.credit||0), 0);
  if(totalD<=0 || totalD !== totalC) return alert('Debits and credits must balance');
  if(lines.some(l=>!l.accountName)) return alert('Pick accounts for all lines');
  const entry = { id:crypto.randomUUID(), date:$('#jeDate').value || new Date().toISOString().slice(0,10), ref:$('#jeRef').value, memo:$('#jeMemo').value, lines };
  State.journal.push(entry);
  try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/journal`), State.journal); renderJournal(); }catch(e){ alert('Post failed: '+(e.message||e)); }
});

$('#accTable').addEventListener('click', async (e)=>{ const btn = e.target.closest('button[data-act="del-acc"]'); if(!btn) return; const id=btn.dataset.id; State.accounts = State.accounts.filter(a=>a.id!==id); try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/accounts`), State.accounts); }catch(e){ alert(e.message||e); } });
$('#btnAddAccount').addEventListener('click', async ()=>{ const name=$('#accName').value.trim(); const type=$('#accType').value; if(!name) return alert('Account name required'); State.accounts.push({ id:crypto.randomUUID(), name, type }); try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/accounts`), State.accounts); $('#accName').value=''; }catch(e){ alert(e.message||e); } });

$('#clientTable').addEventListener('click', (e)=>{ const btn = e.target.closest('button[data-act="ledger"]'); if(!btn) return; const id = btn.dataset.id; const c = State.clients.find(x=>x.id===id); if(!c) return alert(`${c.name} ledger — invoices & journal entries available.`); });

$('#btnSaveSettings').addEventListener('click', async ()=>{ State.currency = $('#setCurrency').value || State.currency; State.settings.arAccount = $('#setAR').value || State.settings.arAccount; State.settings.revAccount = $('#setREV').value || State.settings.revAccount; try{ await set(ref(db, `users/${currentUser.uid}/data/${currentCompany}/settings`), State.settings); alert('Saved'); }catch(e){ alert(e.message||e); } });

/* ===========================
   addJournalLineRow helper
   =========================== */
function addJournalLineRow(container){
  const div = document.createElement('div'); div.style.display='flex'; div.style.gap='8px'; div.style.marginBottom='8px';
  div.innerHTML = `<select class="input accSel" style="min-width:200px"><option value="">Select account</option></select>
    <select class="input clientSel" style="max-width:180px"><option value="">No client</option>${State.clients.map(c=>`<option value="${c.id}">${c.name}</option>`).join('')}</select>
    <input class="input debit" placeholder="Debit" style="max-width:120px"/>
    <input class="input credit" placeholder="Credit" style="max-width:120px"/>
    <button class="btn danger btnDelLine">✕</button>`;
  container.appendChild(div);
  renderAccounts();
  div.querySelector('.btnDelLine').addEventListener('click', ()=> div.remove());
}

/* ===========================
   Auth state listener — separate views
   =========================== */
onAuthStateChanged(auth, async (user)=>{
  currentUser = user || null;
  if(!currentUser){
    // show login
    showAuthView();
    return;
  }
  // signed in — show app
  showAppView();
  // show companies list and create panel
  onValue(ref(db, `users/${currentUser.uid}/companies`), snap=>{
    renderCompanyList(snap.val()||{});
    initCreateCompanyPanel();
  });
  // select stored or first company
  try{
    const csnap = await get(ref(db, `users/${currentUser.uid}/companies`));
    const companies = csnap.val() || {};
    if(!currentCompany){
      const first = Object.keys(companies)[0]; if(first) await selectCompany(first);
    } else {
      if(companies[currentCompany]) await selectCompany(currentCompany); else { const first = Object.keys(companies)[0]; if(first) await selectCompany(first); }
    }
  }catch(e){ console.error('init companies error', e); }
});

/* ===========================
   utility: initial UI & refresh
   =========================== */
function initUI(){ openPanel('home'); }
initUI();
setInterval(()=>{ try{ renderKPIs(); }catch(e){} }, 2000);

/* ===========================
   Helpful comments for Firebase rules + deploy
   ===========================
   Realtime Database rules (paste in Firebase Console -> Realtime Database -> Rules):

   {
     "rules": {
       "users": {
         "$uid": {
           ".read": "$uid === auth.uid",
           ".write": "$uid === auth.uid"
         }
       }
     }
   }

   Make sure Authentication -> Sign-in method -> Email/Password is enabled.

   To deploy:
   1) npm install -g firebase-tools
   2) firebase login
   3) firebase init hosting (select project 'lizardo-sea-oil'; public directory: . ; single-page app: y)
   4) firebase deploy --only hosting

   If you hit PERMISSION_DENIED when creating a company:
   - Confirm you're signed in (app shows your email in UI).
   - Confirm the rules above are published.
   - Copy/paste the console error here and I'll decode it.

*/
</script>
</body>
</html>
