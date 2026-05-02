<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MedNotes</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/marked/9.1.6/marked.min.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#f9f8f6;--sidebar-bg:#f2f0ec;--note-bg:#ffffff;--text:#1a1a1a;--text-muted:#888;
  --border:#e0ddd8;--accent:#c8533a;--accent-light:#fdf0ee;--toolbar-bg:#fafaf8;
  --tag-bg:#e8e4de;--tag-text:#555;--hover:#ede9e3;--sel:#e2ddd6;
  --hl-yellow:#fff9c4;--hl-blue:#e3f0ff;--hl-green:#e6f9ed;--hl-pink:#ffe6f2;
}
[data-theme=dark]{
  --bg:#1c1b19;--sidebar-bg:#232220;--note-bg:#2a2926;--text:#e8e5df;--text-muted:#888;
  --border:#383632;--accent:#e07055;--accent-light:#3a2420;--toolbar-bg:#252422;
  --tag-bg:#333128;--tag-text:#aaa;--hover:#2e2c28;--sel:#35332e;
  --hl-yellow:#4a4200;--hl-blue:#0d2a4a;--hl-green:#0a3a1c;--hl-pink:#4a0d2a;
}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;background:var(--bg);color:var(--text);height:100vh;display:flex;overflow:hidden;font-size:15px}
/* Sidebar */
#sidebar{width:260px;min-width:200px;max-width:320px;background:var(--sidebar-bg);border-right:1px solid var(--border);display:flex;flex-direction:column;overflow:hidden;resize:none;transition:width .2s}
#sidebar.collapsed{width:0;min-width:0;border:none}
#sidebar-header{padding:14px 14px 10px;border-bottom:1px solid var(--border);flex-shrink:0}
#app-title{font-size:17px;font-weight:600;letter-spacing:-.3px;color:var(--accent);margin-bottom:10px}
#search-wrap{position:relative}
#search{width:100%;padding:7px 10px 7px 30px;border:1px solid var(--border);border-radius:8px;background:var(--note-bg);color:var(--text);font-size:13px;outline:none}
#search:focus{border-color:var(--accent)}
#search-wrap::before{content:"⌕";position:absolute;left:8px;top:50%;transform:translateY(-50%);color:var(--text-muted);font-size:16px;pointer-events:none}
#tags-bar{padding:8px 14px;border-bottom:1px solid var(--border);display:flex;flex-wrap:wrap;gap:4px;flex-shrink:0}
.tag-filter{padding:2px 8px;border-radius:12px;background:var(--tag-bg);color:var(--tag-text);font-size:11px;cursor:pointer;border:1px solid transparent;user-select:none}
.tag-filter.active{background:var(--accent);color:#fff;border-color:var(--accent)}
#notes-list{flex:1;overflow-y:auto;padding:8px 0}
.note-item{padding:10px 14px;cursor:pointer;border-bottom:1px solid var(--border);transition:background .1s}
.note-item:hover{background:var(--hover)}
.note-item.active{background:var(--sel)}
.ni-title{font-size:13px;font-weight:500;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.ni-preview{font-size:11px;color:var(--text-muted);margin-top:2px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.ni-tags{display:flex;flex-wrap:wrap;gap:3px;margin-top:4px}
.ni-tag{font-size:10px;padding:1px 6px;border-radius:8px;background:var(--tag-bg);color:var(--tag-text)}
.ni-date{font-size:10px;color:var(--text-muted);margin-top:3px}
/* Main */
#main{flex:1;display:flex;flex-direction:column;overflow:hidden}
/* Toolbar */
#toolbar{padding:8px 14px;border-bottom:1px solid var(--border);background:var(--toolbar-bg);display:flex;align-items:center;gap:6px;flex-wrap:wrap;flex-shrink:0}
.tb-btn{padding:4px 8px;border:1px solid var(--border);border-radius:5px;background:var(--note-bg);color:var(--text);cursor:pointer;font-size:12px;display:flex;align-items:center;gap:3px;white-space:nowrap;transition:background .1s}
.tb-btn:hover{background:var(--hover)}
.tb-btn.active{background:var(--accent);color:#fff;border-color:var(--accent)}
.tb-sep{width:1px;height:20px;background:var(--border);margin:0 2px}
.tb-select{padding:4px 6px;border:1px solid var(--border);border-radius:5px;background:var(--note-bg);color:var(--text);font-size:12px;cursor:pointer;outline:none}
input[type=color].tb-color{width:28px;height:28px;border:1px solid var(--border);border-radius:5px;padding:0;cursor:pointer;background:none}
/* Editor area */
#editor-area{flex:1;display:flex;overflow:hidden}
#editor-wrap{flex:1;display:flex;flex-direction:column;overflow:hidden}
#note-title-input{width:100%;padding:16px 20px 8px;font-size:22px;font-weight:700;border:none;outline:none;background:var(--note-bg);color:var(--text);border-bottom:1px solid var(--border);flex-shrink:0}
#tags-input-wrap{padding:6px 20px;border-bottom:1px solid var(--border);background:var(--note-bg);display:flex;align-items:center;gap:6px;flex-wrap:wrap;flex-shrink:0}
#tags-input-wrap span{font-size:12px;color:var(--text-muted)}
.edit-tag{font-size:11px;padding:2px 8px;border-radius:10px;background:var(--accent-light);color:var(--accent);display:flex;align-items:center;gap:4px}
.edit-tag .rm-tag{cursor:pointer;font-size:13px;line-height:1;color:var(--accent)}
#new-tag-input{border:none;outline:none;background:transparent;color:var(--text);font-size:12px;min-width:80px}
#editor-cols{flex:1;display:flex;overflow:hidden;background:var(--note-bg)}
#col1,#col2{flex:1;display:flex;flex-direction:column;overflow:hidden}
#col2{border-left:1px solid var(--border)}
.col-label{font-size:10px;color:var(--text-muted);padding:4px 20px;border-bottom:1px solid var(--border);background:var(--toolbar-bg);flex-shrink:0}
#editor{flex:1;padding:16px 20px;border:none;outline:none;resize:none;font-family:'SF Mono',Monaco,Consolas,monospace;font-size:14px;line-height:1.7;background:transparent;color:var(--text);tab-size:2;overflow-y:auto}
#preview{flex:1;padding:16px 20px;overflow-y:auto;line-height:1.7}
#preview img{max-width:100%;border-radius:6px;margin:8px 0}
#preview h1{font-size:1.7em;font-weight:700;margin:1em 0 .4em;border-bottom:2px solid var(--border);padding-bottom:.3em}
#preview h2{font-size:1.35em;font-weight:600;margin:.9em 0 .3em}
#preview h3{font-size:1.1em;font-weight:600;margin:.8em 0 .3em;color:var(--accent)}
#preview p{margin:.5em 0}
#preview ul,#preview ol{padding-left:1.4em;margin:.4em 0}
#preview li{margin:.2em 0}
#preview code{background:var(--tag-bg);border-radius:4px;padding:1px 5px;font-size:.88em;font-family:Monaco,monospace}
#preview pre{background:var(--tag-bg);border-radius:8px;padding:12px;overflow-x:auto;margin:.6em 0}
#preview pre code{background:none;padding:0}
#preview blockquote{border-left:3px solid var(--accent);padding:.3em 1em;color:var(--text-muted);margin:.6em 0;background:var(--accent-light);border-radius:0 6px 6px 0}
#preview table{border-collapse:collapse;width:100%;margin:.6em 0}
#preview th,#preview td{border:1px solid var(--border);padding:6px 10px;font-size:13px}
#preview th{background:var(--tag-bg);font-weight:600}
#preview .hl-y{background:var(--hl-yellow)}
#preview .hl-b{background:var(--hl-blue)}
#preview .hl-g{background:var(--hl-green)}
#preview .hl-p{background:var(--hl-pink)}
.two-col #editor-cols{flex-direction:row}
.one-col #col2{border-left:1px solid var(--border)}
/* Image upload area */
#img-drop{display:none;padding:12px 20px;border-top:1px solid var(--border);background:var(--toolbar-bg);flex-shrink:0}
#img-drop.show{display:flex;align-items:center;gap:10px}
#img-drop input{display:none}
#img-drop label{padding:5px 12px;border:1px solid var(--border);border-radius:6px;cursor:pointer;font-size:12px;background:var(--note-bg)}
#img-drop label:hover{background:var(--hover)}
/* Empty state */
#empty-state{flex:1;display:flex;align-items:center;justify-content:center;flex-direction:column;gap:10px;color:var(--text-muted);background:var(--note-bg)}
#empty-state svg{opacity:.3}
/* Scrollbar */
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:var(--border);border-radius:3px}
/* Search highlight */
.search-hl{background:#ffeb3b;border-radius:2px;padding:0 1px}
/* Sidebar toggle btn */
#toggle-sidebar{font-size:16px;cursor:pointer;background:none;border:none;color:var(--text-muted);padding:4px 6px;border-radius:5px}
#toggle-sidebar:hover{color:var(--text);background:var(--hover)}
#new-note-btn{margin-left:auto;padding:5px 12px;background:var(--accent);color:#fff;border:none;border-radius:7px;cursor:pointer;font-size:13px;font-weight:500;white-space:nowrap}
#new-note-btn:hover{opacity:.88}
</style>
</head>
<body data-theme="light">

<div id="sidebar">
  <div id="sidebar-header">
    <div id="app-title">MedNotes</div>
    <div id="search-wrap"><input id="search" type="text" placeholder="Pesquisar notas, tags..."></div>
  </div>
  <div id="tags-bar" title="Filtrar por tag"></div>
  <div id="notes-list"></div>
</div>

<div id="main">
  <!-- Toolbar -->
  <div id="toolbar">
    <button id="toggle-sidebar" title="Toggle sidebar">☰</button>
    <button id="new-note-btn">+ Nova nota</button>
    <div class="tb-sep"></div>
    <!-- Text formatting -->
    <button class="tb-btn" data-cmd="bold" title="Negrito (Ctrl+B)"><b>B</b></button>
    <button class="tb-btn" data-cmd="italic" title="Itálico (Ctrl+I)"><i>I</i></button>
    <button class="tb-btn" data-cmd="strike" title="Tachado">S̶</button>
    <button class="tb-btn" data-cmd="code" title="Código">&lt;/&gt;</button>
    <div class="tb-sep"></div>
    <!-- Color text -->
    <input type="color" class="tb-color" id="color-picker" title="Cor do texto" value="#c8533a">
    <button class="tb-btn" id="apply-color" title="Aplicar cor">A</button>
    <select class="tb-select" id="hl-select" title="Destacar texto">
      <option value="">Destacar</option>
      <option value="y">🟡 Amarelo</option>
      <option value="b">🔵 Azul</option>
      <option value="g">🟢 Verde</option>
      <option value="p">🩷 Rosa</option>
    </select>
    <div class="tb-sep"></div>
    <!-- Headings & blocks -->
    <select class="tb-select" id="heading-sel" title="Cabeçalho">
      <option value="">Título...</option>
      <option value="# ">H1</option>
      <option value="## ">H2</option>
      <option value="### ">H3</option>
    </select>
    <button class="tb-btn" data-cmd="ul" title="Lista">• Lista</button>
    <button class="tb-btn" data-cmd="ol" title="Numerada">1. Lista</button>
    <button class="tb-btn" data-cmd="quote" title="Citação">❝</button>
    <button class="tb-btn" data-cmd="table" title="Tabela">⊞</button>
    <div class="tb-sep"></div>
    <!-- Image -->
    <button class="tb-btn" id="img-btn" title="Inserir imagem">🖼</button>
    <div class="tb-sep"></div>
    <!-- Layout -->
    <button class="tb-btn active" id="btn-1col" title="Coluna simples">◻</button>
    <button class="tb-btn" id="btn-2col" title="Duas colunas">▣</button>
    <div class="tb-sep"></div>
    <!-- Theme -->
    <button class="tb-btn" id="theme-btn" title="Tema">◑</button>
  </div>

  <!-- Editor area -->
  <div id="editor-area">
    <div id="empty-state">
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14,2 14,8 20,8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/><polyline points="10,9 9,9 8,9"/></svg>
      <p style="font-size:14px">Selecione uma nota ou crie uma nova</p>
    </div>
    <div id="editor-wrap" style="display:none;flex:1;flex-direction:column;overflow:hidden">
      <input id="note-title-input" type="text" placeholder="Título da nota...">
      <div id="tags-input-wrap">
        <span>#</span>
        <div id="current-tags"></div>
        <input id="new-tag-input" type="text" placeholder="adicionar tag...">
      </div>
      <div id="editor-cols" class="one-col">
        <div id="col1">
          <div class="col-label">MARKDOWN</div>
          <textarea id="editor" spellcheck="false" placeholder="Escreva em Markdown...&#10;&#10;## Cardiologia Nuclear&#10;**ECG** em repouso..."></textarea>
        </div>
        <div id="col2">
          <div class="col-label">PRÉVIA</div>
          <div id="preview"></div>
        </div>
      </div>
      <div id="img-drop">
        <span style="font-size:12px;color:var(--text-muted)">Inserir imagem:</span>
        <label for="img-file">📁 Escolher arquivo</label>
        <input type="file" id="img-file" accept="image/*" multiple>
        <span style="font-size:11px;color:var(--text-muted)">ou cole (Ctrl+V) no editor</span>
        <button class="tb-btn" id="img-cancel" style="margin-left:auto">✕ Fechar</button>
      </div>
    </div>
  </div>
</div>

<script>
// ---- State ----
let notes = JSON.parse(localStorage.getItem('mednotes_v1') || '[]');
let activeId = null;
let filterTags = [];
let searchQ = '';
let twoCol = false;

// ---- Defaults ----
if(!notes.length) {
  notes = [{
    id: Date.now(),
    title: 'Bem-vindo ao MedNotes',
    content: `## Visão geral\nEste é seu caderno de residência médica. Escreva em **Markdown**.\n\n### Formatação especial\n- Cores: \`<span style="color:#c8533a">texto colorido</span>\`\n- Destaques via barra de ferramentas\n- Imagens: inseridas diretamente no texto\n\n### Atalhos\n- \`Ctrl+B\` → negrito\n- \`Ctrl+I\` → itálico\n- \`Ctrl+S\` → salvar\n\n> **Dica:** Use tags para organizar por especialidade. Ex: \`cardiologia\`, \`nuclear\`, \`ecg\``,
    tags: ['tutorial','cardiologia'],
    created: Date.now(),
    updated: Date.now()
  }];
  save();
}

function save(){localStorage.setItem('mednotes_v1', JSON.stringify(notes))}

// ---- Helpers ----
function genId(){return Date.now() + Math.random()}
function getNote(id){return notes.find(n=>n.id===id)}
function allTags(){
  const s = new Set();
  notes.forEach(n=>(n.tags||[]).forEach(t=>s.add(t)));
  return [...s].sort();
}
function filteredNotes(){
  let r = [...notes];
  if(filterTags.length) r = r.filter(n=>filterTags.every(t=>(n.tags||[]).includes(t)));
  if(searchQ){
    const q = searchQ.toLowerCase();
    r = r.filter(n=>(n.title||'').toLowerCase().includes(q)||(n.content||'').toLowerCase().includes(q)||(n.tags||[]).some(t=>t.toLowerCase().includes(q)));
  }
  return r.sort((a,b)=>b.updated-a.updated);
}

// ---- Render sidebar ----
function renderSidebar(){
  // Tags bar
  const tb = document.getElementById('tags-bar');
  const tags = allTags();
  tb.innerHTML = tags.map(t=>`<span class="tag-filter${filterTags.includes(t)?' active':''}" data-tag="${t}">#${t}</span>`).join('');
  tb.querySelectorAll('.tag-filter').forEach(el=>el.addEventListener('click',()=>{
    const t = el.dataset.tag;
    if(filterTags.includes(t)) filterTags = filterTags.filter(x=>x!==t);
    else filterTags.push(t);
    renderSidebar();
  }));
  // Notes list
  const nl = document.getElementById('notes-list');
  const fn = filteredNotes();
  if(!fn.length){nl.innerHTML='<div style="padding:20px 14px;font-size:12px;color:var(--text-muted)">Nenhuma nota encontrada</div>';return}
  nl.innerHTML = fn.map(n=>{
    const preview = (n.content||'').replace(/[#*`_>]/g,'').slice(0,80);
    const dateStr = new Date(n.updated).toLocaleDateString('pt-BR',{day:'2-digit',month:'short'});
    return `<div class="note-item${n.id===activeId?' active':''}" data-id="${n.id}">
      <div class="ni-title">${hl(n.title||'Sem título')}</div>
      <div class="ni-preview">${hl(preview)}</div>
      ${(n.tags||[]).length?`<div class="ni-tags">${(n.tags).map(t=>`<span class="ni-tag">#${t}</span>`).join('')}</div>`:''}
      <div class="ni-date">${dateStr}</div>
    </div>`;
  }).join('');
  nl.querySelectorAll('.note-item').forEach(el=>el.addEventListener('click',()=>openNote(+el.dataset.id)));
}

function hl(text){
  if(!searchQ) return text;
  const re = new RegExp('('+searchQ.replace(/[.*+?^${}()|[\]\\]/g,'\\$&')+')','gi');
  return text.replace(re,'<mark class="search-hl">$1</mark>');
}

// ---- Open note ----
function openNote(id){
  activeId = id;
  const n = getNote(id);
  if(!n) return;
  document.getElementById('empty-state').style.display='none';
  const ew = document.getElementById('editor-wrap');
  ew.style.display='flex';
  document.getElementById('note-title-input').value = n.title||'';
  document.getElementById('editor').value = n.content||'';
  renderCurrentTags(n.tags||[]);
  renderPreview();
  renderSidebar();
}

function renderCurrentTags(tags){
  const ct = document.getElementById('current-tags');
  ct.innerHTML = tags.map(t=>`<span class="edit-tag">#${t}<span class="rm-tag" data-tag="${t}">×</span></span>`).join('');
  ct.querySelectorAll('.rm-tag').forEach(el=>el.addEventListener('click',()=>{
    const n = getNote(activeId);
    if(!n) return;
    n.tags = (n.tags||[]).filter(x=>x!==el.dataset.tag);
    save();
    renderCurrentTags(n.tags);
    renderSidebar();
  }));
}

// ---- Render preview ----
function renderPreview(){
  const content = document.getElementById('editor').value;
  let html = marked.parse(content, {breaks:true});
  // highlight spans
  html = html.replace(/==y:(.*?)==/g,'<span class="hl-y">$1</span>');
  html = html.replace(/==b:(.*?)==/g,'<span class="hl-b">$1</span>');
  html = html.replace(/==g:(.*?)==/g,'<span class="hl-g">$1</span>');
  html = html.replace(/==p:(.*?)==/g,'<span class="hl-p">$1</span>');
  document.getElementById('preview').innerHTML = html;
}

// ---- Save note ----
function saveActive(){
  if(!activeId) return;
  const n = getNote(activeId);
  if(!n) return;
  n.title = document.getElementById('note-title-input').value;
  n.content = document.getElementById('editor').value;
  n.updated = Date.now();
  save();
  renderSidebar();
}

// ---- New note ----
document.getElementById('new-note-btn').addEventListener('click',()=>{
  const n = {id:genId(),title:'Nova nota',content:'',tags:[],created:Date.now(),updated:Date.now()};
  notes.unshift(n);
  save();
  openNote(n.id);
});

// ---- Toolbar formatting ----
function wrapSel(before,after=''){
  const ta = document.getElementById('editor');
  const s=ta.selectionStart, e=ta.selectionEnd;
  const sel=ta.value.substring(s,e)||'texto';
  ta.value=ta.value.substring(0,s)+before+sel+after+ta.value.substring(e);
  ta.selectionStart=s+before.length; ta.selectionEnd=s+before.length+sel.length;
  ta.focus(); renderPreview(); saveActive();
}
function insertLine(prefix){
  const ta = document.getElementById('editor');
  const s=ta.selectionStart;
  const before=ta.value.lastIndexOf('\n',s-1)+1;
  ta.value=ta.value.substring(0,before)+prefix+ta.value.substring(before);
  ta.selectionStart=ta.selectionEnd=before+prefix.length;
  ta.focus(); renderPreview(); saveActive();
}

document.querySelectorAll('.tb-btn[data-cmd]').forEach(btn=>{
  btn.addEventListener('click',()=>{
    const cmd=btn.dataset.cmd;
    if(cmd==='bold') wrapSel('**','**');
    else if(cmd==='italic') wrapSel('*','*');
    else if(cmd==='strike') wrapSel('~~','~~');
    else if(cmd==='code') wrapSel('`','`');
    else if(cmd==='ul') insertLine('- ');
    else if(cmd==='ol') insertLine('1. ');
    else if(cmd==='quote') insertLine('> ');
    else if(cmd==='table') wrapSel('\n| Col 1 | Col 2 | Col 3 |\n|-------|-------|-------|\n| dado  | dado  | dado  |\n','');
  });
});

// Heading
document.getElementById('heading-sel').addEventListener('change',function(){
  if(!this.value) return;
  insertLine(this.value);
  this.value='';
});

// Color text
document.getElementById('apply-color').addEventListener('click',()=>{
  const c = document.getElementById('color-picker').value;
  wrapSel(`<span style="color:${c}">`, '</span>');
});

// Highlight
document.getElementById('hl-select').addEventListener('change',function(){
  if(!this.value) return;
  const v=this.value;
  wrapSel(`==${v}:`,`==`);
  this.value='';
});

// Keyboard shortcuts
document.getElementById('editor').addEventListener('keydown',e=>{
  if(e.ctrlKey||e.metaKey){
    if(e.key==='b'){e.preventDefault();wrapSel('**','**')}
    if(e.key==='i'){e.preventDefault();wrapSel('*','*')}
    if(e.key==='s'){e.preventDefault();saveActive()}
  }
  // Tab
  if(e.key==='Tab'){e.preventDefault();const ta=e.target;const s=ta.selectionStart;ta.value=ta.value.substring(0,s)+'  '+ta.value.substring(s);ta.selectionStart=ta.selectionEnd=s+2}
});

// Live preview
document.getElementById('editor').addEventListener('input',()=>{renderPreview();saveActive()});
document.getElementById('note-title-input').addEventListener('input',saveActive);

// Tag input
document.getElementById('new-tag-input').addEventListener('keydown',e=>{
  if(e.key==='Enter'||e.key===','||e.key===' '){
    e.preventDefault();
    const val=e.target.value.trim().replace(/[^a-z0-9\-_áéíóúâêîôûãõçàü]/gi,'').toLowerCase();
    if(!val) return;
    const n=getNote(activeId);
    if(!n) return;
    if(!(n.tags||[]).includes(val)){n.tags=[...(n.tags||[]),val];save();renderCurrentTags(n.tags);renderSidebar();}
    e.target.value='';
  }
  if(e.key==='Backspace'&&!e.target.value){
    const n=getNote(activeId);
    if(!n||!n.tags.length) return;
    n.tags=n.tags.slice(0,-1);save();renderCurrentTags(n.tags);renderSidebar();
  }
});

// Search
document.getElementById('search').addEventListener('input',function(){
  searchQ=this.value.trim();
  renderSidebar();
});

// Layout toggle
document.getElementById('btn-1col').addEventListener('click',()=>{
  twoCol=false;
  document.getElementById('editor-cols').className='one-col';
  document.getElementById('btn-1col').classList.add('active');
  document.getElementById('btn-2col').classList.remove('active');
});
document.getElementById('btn-2col').addEventListener('click',()=>{
  twoCol=true;
  document.getElementById('editor-cols').className='two-col';
  document.getElementById('btn-2col').classList.add('active');
  document.getElementById('btn-1col').classList.remove('active');
});

// Theme
document.getElementById('theme-btn').addEventListener('click',()=>{
  const d=document.body;
  d.dataset.theme=d.dataset.theme==='dark'?'light':'dark';
});

// Sidebar toggle
document.getElementById('toggle-sidebar').addEventListener('click',()=>{
  document.getElementById('sidebar').classList.toggle('collapsed');
});

// Image
document.getElementById('img-btn').addEventListener('click',()=>{
  document.getElementById('img-drop').classList.toggle('show');
});
document.getElementById('img-cancel').addEventListener('click',()=>{
  document.getElementById('img-drop').classList.remove('show');
});
document.getElementById('img-file').addEventListener('change',function(){
  [...this.files].forEach(insertImage);
  this.value='';
});

function insertImage(file){
  const r=new FileReader();
  r.onload=e=>{
    const md=`\n![${file.name}](${e.target.result})\n`;
    const ta=document.getElementById('editor');
    const pos=ta.selectionStart;
    ta.value=ta.value.substring(0,pos)+md+ta.value.substring(pos);
    ta.selectionStart=ta.selectionEnd=pos+md.length;
    renderPreview();saveActive();
  };
  r.readAsDataURL(file);
}

// Paste image
document.getElementById('editor').addEventListener('paste',e=>{
  const items=[...(e.clipboardData?.items||[])];
  const imgItem=items.find(i=>i.type.startsWith('image/'));
  if(imgItem){e.preventDefault();insertImage(imgItem.getAsFile());}
});

// Init
renderSidebar();
if(notes.length) openNote(notes[0].id);
</script>
</body>
</html>
