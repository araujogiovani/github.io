<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MOMENTUM - Notas Médicas</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Marked.js -->
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&family=JetBrains+Mono&family=Lora:ital@0;1&family=Roboto+Slab&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --bg-primary: #ffffff;
            --text-main: #2d3436;
            --accent: #e74c3c;
            --sidebar-bg: #f9f9f9;
            --border-color: #eee;
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg-primary);
            color: var(--text-main);
            transition: background-color 0.3s ease, color 0.3s ease;
        }

        /* Temas Dinâmicos */
        .dark {
            --bg-primary: #121212;
            --text-main: #e0e0e0;
            --sidebar-bg: #1a1a1a;
            --border-color: #2a2a2a;
        }

        /* Typewriter Mode Fix */
        .typewriter-active #editorPane {
            padding-top: 45vh !important;
            padding-bottom: 45vh !important;
        }

        .prose {
            max-width: 100%;
            color: inherit;
        }
        
        .prose h1, .prose h2, .prose h3 { color: inherit; font-weight: 700; }
        .prose blockquote { border-left: 3px solid var(--accent); padding-left: 1rem; opacity: 0.8; }
        .prose img { border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.1); }

        .modal-bg {
            background: rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(8px);
        }

        /* Sidebar Transition */
        #sidebar { transition: width 0.25s cubic-bezier(0.4, 0, 0.2, 1); }

        /* Hide scrollbar but keep functionality */
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

        .active-note {
            background-color: rgba(0,0,0,0.05);
            border-left: 3px solid var(--accent);
        }
        .dark .active-note { background-color: rgba(255,255,255,0.05); }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden">

    <!-- Header -->
    <header class="h-14 border-b flex items-center justify-between px-6 z-30 bg-inherit" style="border-color: var(--border-color)">
        <div class="flex items-center gap-6">
            <button onclick="toggleSidebar()" class="hover:opacity-60 transition">
                <i data-lucide="side-bar" class="w-5 h-5"></i>
            </button>
            <h1 class="text-xs uppercase tracking-[0.2em] font-bold opacity-80">Momentum</h1>
        </div>
        
        <div class="flex items-center gap-2">
            <button onclick="openModal('searchModal')" title="Busca (Ctrl+K)" class="p-2 hover:bg-gray-100 dark:hover:bg-zinc-800 rounded-full transition">
                <i data-lucide="search" class="w-4 h-4"></i>
            </button>
            <button onclick="toggleView()" title="Alternar Visualização" class="p-2 hover:bg-gray-100 dark:hover:bg-zinc-800 rounded-full transition">
                <i data-lucide="layout" class="w-4 h-4"></i>
            </button>
            <button onclick="toggleTheme()" title="Mudar Tema" class="p-2 hover:bg-gray-100 dark:hover:bg-zinc-800 rounded-full transition">
                <i data-lucide="sun-moon" class="w-4 h-4"></i>
            </button>
        </div>
    </header>

    <main class="flex-1 flex overflow-hidden relative">
        <!-- Sidebar -->
        <aside id="sidebar" class="w-72 border-r h-full flex flex-col overflow-hidden" style="background: var(--sidebar-bg); border-color: var(--border-color)">
            <div class="p-6">
                <button onclick="createNewNote()" class="w-full py-3 bg-zinc-900 dark:bg-white dark:text-black text-white rounded-xl text-xs font-bold uppercase tracking-widest hover:opacity-90 transition flex items-center justify-center gap-2">
                    <i data-lucide="plus" class="w-4 h-4"></i> Nova Anotação
                </button>
            </div>
            
            <div class="flex-1 overflow-y-auto no-scrollbar px-3 pb-10">
                <div class="mb-8">
                    <label class="text-[10px] uppercase tracking-widest font-bold opacity-30 px-3 mb-3 block">Tags</label>
                    <div id="tagContainer" class="flex flex-wrap gap-2 px-3">
                        <!-- Tags aqui -->
                    </div>
                </div>

                <div>
                    <label class="text-[10px] uppercase tracking-widest font-bold opacity-30 px-3 mb-3 block">Recentes</label>
                    <div id="noteList" class="space-y-1">
                        <!-- Lista de notas -->
                    </div>
                </div>
            </div>
        </aside>

        <!-- Editor/Canvas Area -->
        <div id="mainCanvas" class="flex-1 flex overflow-hidden relative transition-all duration-500">
            <!-- Editor -->
            <div id="editorPane" class="flex-1 h-full overflow-y-auto p-10 lg:p-20 transition-all duration-300 no-scrollbar">
                <div class="max-w-3xl mx-auto h-full flex flex-col">
                    <input id="noteTitle" type="text" placeholder="Título sem nome" class="w-full text-5xl font-serif bg-transparent outline-none mb-10 border-none focus:ring-0 placeholder:opacity-20">
                    <textarea id="markdownEditor" spellcheck="false" class="flex-1 w-full bg-transparent outline-none resize-none text-xl leading-relaxed border-none focus:ring-0 placeholder:opacity-20" placeholder="Comece sua jornada de estudo..."></textarea>
                </div>
            </div>

            <!-- Preview (Hidden by default) -->
            <div id="previewPane" class="hidden flex-1 h-full overflow-y-auto border-l p-10 lg:p-20 bg-inherit" style="border-color: var(--border-color)">
                <div id="previewContent" class="max-w-3xl mx-auto prose dark:prose-invert">
                    <!-- Conteúdo Renderizado -->
                </div>
            </div>
        </div>
    </main>

    <!-- Footer -->
    <footer class="h-10 border-t flex items-center justify-between px-6 text-[10px] uppercase tracking-widest opacity-40 z-30 bg-inherit" style="border-color: var(--border-color)">
        <div id="statusInfo">Pronto</div>
        <div class="flex gap-6 items-center">
            <span id="wordCounter">0 Palavras</span>
            <button onclick="toggleTypewriterMode()" id="typewriterIndicator" class="hover:opacity-100 transition">Typewriter: OFF</button>
        </div>
    </footer>

    <!-- Modal de Busca -->
    <div id="searchModal" class="hidden fixed inset-0 z-[100] modal-bg flex items-start justify-center pt-20">
        <div class="bg-white dark:bg-zinc-900 w-full max-w-2xl rounded-3xl shadow-2xl overflow-hidden border border-gray-100 dark:border-zinc-800">
            <div class="p-6 border-b dark:border-zinc-800 flex items-center gap-4">
                <i data-lucide="search" class="w-6 h-6 opacity-30"></i>
                <input id="searchInput" type="text" placeholder="Buscar em anotações ou #tags..." class="w-full bg-transparent text-xl outline-none">
            </div>
            <div id="searchResults" class="max-h-[60vh] overflow-y-auto p-4">
                <div class="text-center py-10 opacity-30 text-sm">Digite algo para pesquisar...</div>
            </div>
        </div>
    </div>

    <script>
        // Configuração Inicial do MOMENTUM
        let appState = {
            notes: JSON.parse(localStorage.getItem('momentum_notes')) || [
                { id: 'initial', title: 'Bem-vindo ao Momentum', content: '#bem-vindo #estudos\n\nEste é o seu novo espaço de estudo para a residência.\n\n- Use **negrito** para termos importantes.\n- Organize com #tags.\n- Foque no que importa.', updatedAt: Date.now() }
            ],
            currentNoteId: 'initial',
            isSidebarOpen: true,
            isDualView: false,
            isDarkMode: false,
            isTypewriter: false
        };

        const editor = document.getElementById('markdownEditor');
        const titleInput = document.getElementById('noteTitle');

        window.onload = () => {
            initApp();
            renderAll();
        };

        function initApp() {
            lucide.createIcons();
            
            // Listeners
            editor.addEventListener('input', handleEditorInput);
            titleInput.addEventListener('input', handleEditorInput);
            
            // Atalho de busca
            window.addEventListener('keydown', (e) => {
                if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
                    e.preventDefault();
                    openModal('searchModal');
                }
            });

            // Clique fora do modal
            window.onclick = (e) => {
                if (e.target.classList.contains('modal-bg')) closeModal(e.target.id);
            };

            document.getElementById('searchInput').addEventListener('input', (e) => searchNotes(e.target.value));
        }

        function handleEditorInput() {
            const note = appState.notes.find(n => n.id === appState.currentNoteId);
            if (note) {
                note.title = titleInput.value;
                note.content = editor.value;
                note.updatedAt = Date.now();
                saveState();
                updateWordCount();
                if (appState.isDualView) renderPreview();
                renderNoteList(); // Atualiza títulos na lateral
                extractTags();
            }
        }

        function renderAll() {
            renderNoteList();
            loadNote(appState.currentNoteId);
            extractTags();
        }

        function loadNote(id) {
            const note = appState.notes.find(n => n.id === id);
            if (!note) return;
            
            appState.currentNoteId = id;
            titleInput.value = note.title;
            editor.value = note.content;
            
            if (appState.isDualView) renderPreview();
            updateWordCount();

            // UI feedback
            document.querySelectorAll('.note-card').forEach(el => el.classList.remove('active-note'));
            const activeEl = document.getElementById(`note-${id}`);
            if (activeEl) activeEl.classList.add('active-note');
        }

        function createNewNote() {
            const newNote = {
                id: 'note_' + Date.now(),
                title: '',
                content: '',
                updatedAt: Date.now()
            };
            appState.notes.unshift(newNote);
            appState.currentNoteId = newNote.id;
            saveState();
            renderAll();
            titleInput.focus();
        }

        function renderNoteList() {
            const list = document.getElementById('noteList');
            list.innerHTML = appState.notes.map(n => `
                <div id="note-${n.id}" onclick="loadNote('${n.id}')" class="note-card p-4 mx-2 rounded-xl cursor-pointer hover:bg-gray-100 dark:hover:bg-zinc-800 transition group">
                    <div class="text-xs font-bold truncate opacity-80">${n.title || 'Sem título'}</div>
                    <div class="text-[10px] opacity-40 mt-1 uppercase tracking-tighter">${new Date(n.updatedAt).toLocaleDateString()}</div>
                </div>
            `).join('');
        }

        function extractTags() {
            const tags = new Set();
            appState.notes.forEach(n => {
                const matches = n.content.match(/#\w+/g);
                if (matches) matches.forEach(t => tags.add(t));
            });
            
            const container = document.getElementById('tagContainer');
            container.innerHTML = Array.from(tags).map(t => `
                <span onclick="searchByTag('${t}')" class="text-[10px] bg-zinc-100 dark:bg-zinc-800 px-2 py-1 rounded-md cursor-pointer hover:bg-red-500 hover:text-white transition">
                    ${t}
                </span>
            `).join('');
        }

        function renderPreview() {
            const content = editor.value;
            document.getElementById('previewContent').innerHTML = marked.parse(content);
        }

        // --- UI ACTIONS ---

        function toggleSidebar() {
            const sb = document.getElementById('sidebar');
            appState.isSidebarOpen = !appState.isSidebarOpen;
            sb.style.width = appState.isSidebarOpen ? '288px' : '0px';
        }

        function toggleView() {
            appState.isDualView = !appState.isDualView;
            const preview = document.getElementById('previewPane');
            const editorPane = document.getElementById('editorPane');
            
            if (appState.isDualView) {
                preview.classList.remove('hidden');
                renderPreview();
            } else {
                preview.classList.add('hidden');
            }
        }

        function toggleTheme() {
            appState.isDarkMode = !appState.isDarkMode;
            document.body.classList.toggle('dark');
        }

        function toggleTypewriterMode() {
            appState.isTypewriter = !appState.isTypewriter;
            document.body.classList.toggle('typewriter-active');
            document.getElementById('typewriterIndicator').innerText = `Typewriter: ${appState.isTypewriter ? 'ON' : 'OFF'}`;
        }

        function updateWordCount() {
            const words = editor.value.trim().split(/\s+/).filter(w => w.length > 0).length;
            document.getElementById('wordCounter').innerText = `${words} Palavras`;
        }

        function openModal(id) {
            document.getElementById(id).classList.remove('hidden');
            if (id === 'searchModal') document.getElementById('searchInput').focus();
        }

        function closeModal(id) {
            document.getElementById(id).classList.add('hidden');
        }

        function searchNotes(query) {
            const results = document.getElementById('searchResults');
            const q = query.toLowerCase();
            
            const filtered = appState.notes.filter(n => 
                n.title.toLowerCase().includes(q) || n.content.toLowerCase().includes(q)
            );

            if (filtered.length === 0) {
                results.innerHTML = `<div class="text-center py-10 opacity-30">Nada encontrado para "${query}"</div>`;
                return;
            }

            results.innerHTML = filtered.map(n => `
                <div onclick="loadNote('${n.id}'); closeModal('searchModal')" class="p-4 hover:bg-gray-50 dark:hover:bg-zinc-800 rounded-2xl cursor-pointer border-b dark:border-zinc-800 last:border-0">
                    <div class="font-bold text-sm mb-1">${n.title || 'Sem título'}</div>
                    <div class="text-xs opacity-40 truncate">${n.content.replace(/[#*`]/g, '')}</div>
                </div>
            `).join('');
        }

        function searchByTag(tag) {
            openModal('searchModal');
            document.getElementById('searchInput').value = tag;
            searchNotes(tag);
        }

        function saveState() {
            localStorage.setItem('momentum_notes', JSON.stringify(appState.notes));
        }
    </script>
</body>
</html>
