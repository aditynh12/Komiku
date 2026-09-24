<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mykomiku</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: { 50: '#fdf4ff', 100: '#fae8ff', 500: '#d946ef', 600: '#c026d3', 700: '#a21caf' }
                    }
                }
            }
        }
    </script>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen font-sans antialiased selection:bg-purple-500 selection:text-white">

    <!-- Header / Navbar -->
    <header class="sticky top-0 z-40 backdrop-blur-md bg-slate-900/80 border-b border-slate-800">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-purple-500 to-indigo-600 flex items-center justify-center shadow-lg shadow-purple-500/30">
                    <i class="fa-solid fa-book-open text-white"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg sm:text-xl tracking-tight bg-gradient-to-r from-purple-400 to-indigo-400 bg-clip-text text-transparent">Comic Tracker</h1>
                    <p class="text-xs text-slate-400">Offline Manhwa & Manga Vault</p>
                </div>
            </div>

            <!-- Aksi Header -->
            <div class="flex items-center space-x-2 sm:space-x-3">
                <button onclick="openModal()" class="bg-purple-600 hover:bg-purple-500 text-white px-3.5 py-2 rounded-xl text-sm font-medium shadow-lg shadow-purple-600/20 transition-all flex items-center space-x-2">
                    <i class="fa-solid fa-plus"></i>
                    <span class="hidden sm:inline">Tambah Komik</span>
                </button>
                <button onclick="exportData()" title="Backup Data (JSON)" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 transition-colors">
                    <i class="fa-solid fa-download text-sm"></i>
                </button>
                <label title="Restore Data (JSON)" class="p-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-300 cursor-pointer transition-colors">
                    <i class="fa-solid fa-upload text-sm"></i>
                    <input type="file" id="importFile" accept=".json" class="hidden" onchange="importData(event)">
                </label>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 space-y-6">

        <!-- Bar Kontrol & Statistik -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex items-center space-x-4">
                <div class="w-12 h-12 rounded-xl bg-purple-500/10 text-purple-400 flex items-center justify-center text-xl">
                    <i class="fa-solid fa-book"></i>
                </div>
                <div>
                    <p class="text-xs text-slate-400 font-medium">Total Komik</p>
                    <h3 id="statTotal" class="text-2xl font-bold text-slate-100">0</h3>
                </div>
            </div>
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex items-center space-x-4">
                <div class="w-12 h-12 rounded-xl bg-emerald-500/10 text-emerald-400 flex items-center justify-center text-xl">
                    <i class="fa-solid fa-fire"></i>
                </div>
                <div>
                    <p class="text-xs text-slate-400 font-medium">Sedang Dibaca</p>
                    <h3 id="statReading" class="text-2xl font-bold text-slate-100">0</h3>
                </div>
            </div>
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex items-center space-x-4">
                <div class="w-12 h-12 rounded-xl bg-blue-500/10 text-blue-400 flex items-center justify-center text-xl">
                    <i class="fa-solid fa-circle-check"></i>
                </div>
                <div>
                    <p class="text-xs text-slate-400 font-medium">Selesai (Completed)</p>
                    <h3 id="statCompleted" class="text-2xl font-bold text-slate-100">0</h3>
                </div>
            </div>
        </div>

        <!-- Filter & Pencarian -->
        <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex flex-col md:flex-row items-center justify-between gap-4">
            <!-- Pencarian Judul -->
            <div class="relative w-full md:w-96">
                <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center pointer-events-none text-slate-400">
                    <i class="fa-solid fa-magnifying-glass text-sm"></i>
                </span>
                <input type="text" id="searchInput" oninput="renderComics()" placeholder="Cari judul komik..." class="w-full pl-10 pr-4 py-2.5 bg-slate-950 border border-slate-800 rounded-xl text-sm focus:outline-none focus:border-purple-500 text-slate-200 placeholder-slate-500">
            </div>

            <div class="flex items-center space-x-3 w-full md:w-auto justify-end">
                <!-- Filter Status -->
                <select id="statusFilter" onchange="renderComics()" class="bg-slate-950 border border-slate-800 rounded-xl px-3.5 py-2.5 text-sm text-slate-300 focus:outline-none focus:border-purple-500">
                    <option value="all">Semua Status</option>
                    <option value="Reading">Reading</option>
                    <option value="Completed">Completed</option>
                    <option value="Plan to Read">Plan to Read</option>
                </select>

                <!-- Tombol Ganti View (Grid / Table) -->
                <div class="flex bg-slate-950 border border-slate-800 rounded-xl p-1">
                    <button onclick="setViewMode('grid')" id="btnGridView" class="p-2 rounded-lg text-xs transition-colors text-purple-400 bg-purple-500/10">
                        <i class="fa-solid fa-grip"></i>
                    </button>
                    <button onclick="setViewMode('table')" id="btnTableView" class="p-2 rounded-lg text-xs transition-colors text-slate-400 hover:text-slate-200">
                        <i class="fa-solid fa-list"></i>
                    </button>
                </div>
            </div>
        </div>

        <!-- Daftar Komik Container -->
        <div id="comicContainer">
            <!-- Render dinamis via JS -->
        </div>

    </main>

    <!-- Modal Form Tambah / Edit -->
    <div id="comicModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/70 backdrop-blur-sm hidden">
        <div class="bg-slate-900 border border-slate-800 w-full max-w-lg rounded-2xl shadow-2xl overflow-hidden transform transition-all">
            <div class="flex items-center justify-between px-6 py-4 border-b border-slate-800">
                <h3 id="modalTitle" class="font-bold text-lg text-slate-100">Tambah Komik Baru</h3>
                <button onclick="closeModal()" class="text-slate-400 hover:text-slate-200 p-1">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>
            
            <form id="comicForm" onsubmit="saveComic(event)" class="p-6 space-y-4">
                <input type="hidden" id="comicId">
                
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">Judul Komik *</label>
                    <input type="text" id="formTitle" required placeholder="Contoh: Solo Leveling" class="w-full px-3.5 py-2.5 bg-slate-950 border border-slate-800 rounded-xl text-sm focus:outline-none focus:border-purple-500 text-slate-200">
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1">Chapter Terakhir</label>
                        <input type="number" id="formChapter" min="0" value="0" class="w-full px-3.5 py-2.5 bg-slate-950 border border-slate-800 rounded-xl text-sm focus:outline-none focus:border-purple-500 text-slate-200">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1">Status Baca</label>
                        <select id="formStatus" class="w-full px-3.5 py-2.5 bg-slate-950 border border-slate-800 rounded-xl text-sm focus:outline-none focus:border-purple-500 text-slate-200">
                            <option value="Reading">Reading</option>
                            <option value="Completed">Completed</option>
                            <option value="Plan to Read">Plan to Read</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">Link Website Baca</label>
                    <input type="url" id="formLink" placeholder="https://domainkomik.com/manga/..." class="w-full px-3.5 py-2.5 bg-slate-950 border border-slate-800 rounded-xl text-sm focus:outline-none focus:border-purple-500 text-slate-200">
                </div>

                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">Link Cover Gambar (URL)</label>
                    <input type="url" id="formImage" placeholder="https://i.imgur.com/contoh.jpg" class="w-full px-3.5 py-2.5 bg-slate-950 border border-slate-800 rounded-xl text-sm focus:outline-none focus:border-purple-500 text-slate-200">
                    <p class="text-[11px] text-slate-500 mt-1">Catatan: Link share/google akan otomatis disesuaikan oleh sistem agar aman.</p>
                </div>

                <div class="flex items-center justify-end space-x-3 pt-4 border-t border-slate-800">
                    <button type="button" onclick="closeModal()" class="px-4 py-2 rounded-xl text-sm font-medium bg-slate-800 hover:bg-slate-700 text-slate-300 transition-colors">Batal</button>
                    <button type="submit" class="px-4 py-2 rounded-xl text-sm font-medium bg-purple-600 hover:bg-purple-500 text-white shadow-lg shadow-purple-600/20 transition-all">Simpan Komik</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Script Utama Aplikasi -->
    <script>
        let comics = JSON.parse(localStorage.getItem('offline_comics_data')) || [
            {
                id: '1',
                title: 'Solo Leveling',
                chapter: 179,
                status: 'Completed',
                link: 'https://komikcast.ch',
                image: 'https://images.unsplash.com/photo-1612036782180-6f0b6cd846fe?q=80&w=600&auto=format&fit=crop',
                updatedAt: '24 Sep 2026, 12:00'
            }
        ];

        let viewMode = 'grid'; // grid atau table

        function saveToLocalStorage() {
            localStorage.setItem('offline_comics_data', JSON.stringify(comics));
            updateStats();
        }

        function updateStats() {
            const total = comics.length;
            const reading = comics.filter(c => c.status === 'Reading').length;
            const completed = comics.filter(c => c.status === 'Completed').length;

            document.getElementById('statTotal').innerText = total;
            document.getElementById('statReading').innerText = reading;
            document.getElementById('statCompleted').innerText = completed;
        }

        function setViewMode(mode) {
            viewMode = mode;
            if (mode === 'grid') {
                document.getElementById('btnGridView').className = "p-2 rounded-lg text-xs transition-colors text-purple-400 bg-purple-500/10";
                document.getElementById('btnTableView').className = "p-2 rounded-lg text-xs transition-colors text-slate-400 hover:text-slate-200";
            } else {
                document.getElementById('btnTableView').className = "p-2 rounded-lg text-xs transition-colors text-purple-400 bg-purple-500/10";
                document.getElementById('btnGridView').className = "p-2 rounded-lg text-xs transition-colors text-slate-400 hover:text-slate-200";
            }
            renderComics();
        }

        function getFormattedDate() {
            const now = new Date();
            const options = { day: 'numeric', month: 'short', year: 'numeric', hour: '2-digit', minute: '2-digit' };
            return now.toLocaleDateString('id-ID', options);
        }

        function cleanImageUrl(url) {
            if (!url) return 'https://images.unsplash.com/photo-1534447677768-be436bb09401?q=80&w=600&auto=format&fit=crop';
            if (url.includes('drive.google.com/file/d/')) {
                const match = url.match(/\/d\/(.*?)\//);
                if (match && match[1]) {
                    return `https://lh3.googleusercontent.com/d/${match[1]}`;
                }
            }
            return url;
        }

        function renderComics() {
            const keyword = document.getElementById('searchInput').value.toLowerCase();
            const statusFilter = document.getElementById('statusFilter').value;

            const filtered = comics.filter(c => {
                const matchTitle = c.title.toLowerCase().includes(keyword);
                const matchStatus = statusFilter === 'all' || c.status === statusFilter;
                return matchTitle && matchStatus;
            });

            const container = document.getElementById('comicContainer');

            if (filtered.length === 0) {
                container.innerHTML = `
                    <div class="bg-slate-900 border border-slate-800 rounded-2xl p-12 text-center space-y-3">
                        <div class="w-16 h-16 bg-slate-800 text-slate-500 rounded-full flex items-center justify-center mx-auto text-2xl">
                            <i class="fa-solid fa-ghost"></i>
                        </div>
                        <h3 class="text-lg font-medium text-slate-300">Tidak ada komik ditemukan</h3>
                        <p class="text-xs text-slate-500">Coba ubah kata kunci pencarian atau tambahkan komik baru.</p>
                    </div>
                `;
                return;
            }

            if (viewMode === 'grid') {
                let html = '<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">';
                filtered.forEach(c => {
                    const coverImg = cleanImageUrl(c.image);
                    let badgeColor = 'bg-amber-500/10 text-amber-400 border-amber-500/20';
                    if (c.status === 'Completed') badgeColor = 'bg-blue-500/10 text-blue-400 border-blue-500/20';
                    if (c.status === 'Reading') badgeColor = 'bg-emerald-500/10 text-emerald-400 border-emerald-500/20';

                    html += `
                        <div class="bg-slate-900 border border-slate-800 rounded-2xl overflow-hidden flex flex-col justify-between transition-all hover:border-slate-700 shadow-lg">
                            <div>
                                <div class="relative h-48 bg-slate-950 overflow-hidden group">
                                    <img src="${coverImg}" alt="${c.title}" onerror="this.src='https://images.unsplash.com/photo-1534447677768-be436bb09401?q=80&w=600&auto=format&fit=crop'" class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300">
                                    <div class="absolute inset-0 bg-gradient-to-t from-slate-950 via-transparent opacity-80"></div>
                                    <span class="absolute top-3 left-3 text-[10px] font-semibold px-2.5 py-1 rounded-full border backdrop-blur-md ${badgeColor}">${c.status}</span>
                                </div>
                                <div class="p-4 space-y-2">
                                    <h4 class="font-bold text-slate-100 text-base line-clamp-1" title="${c.title}">${c.title}</h4>
                                    
                                    <div class="flex items-center justify-between text-xs text-slate-400">
                                        <span>Chapter Terakhir:</span>
                                        <div class="flex items-center space-x-2 bg-slate-950 border border-slate-800 rounded-lg px-2 py-1">
                                            <button onclick="adjustChapter('${c.id}', -1)" class="text-slate-400 hover:text-white px-1"><i class="fa-solid fa-minus text-[10px]"></i></button>
                                            <span class="font-bold text-purple-400">${c.chapter}</span>
                                            <button onclick="adjustChapter('${c.id}', 1)" class="text-slate-400 hover:text-white px-1"><i class="fa-solid fa-plus text-[10px]"></i></button>
                                        </div>
                                    </div>
                                    <p class="text-[11px] text-slate-500 flex items-center space-x-1 pt-1">
                                        <i class="fa-regular fa-clock text-[10px]"></i>
                                        <span>Diperbarui: ${c.updatedAt || '-'}</span>
                                    </p>
                                </div>
                            </div>
                            <div class="p-4 pt-0 grid grid-cols-3 gap-2 border-t border-slate-800/60 mt-2">
                                <a href="${c.link || '#'}" target="_blank" class="col-span-1 bg-purple-600 hover:bg-purple-500 text-white rounded-xl py-2 text-xs font-medium flex items-center justify-center space-x-1 transition-colors">
                                    <i class="fa-solid fa-globe"></i>
                                    <span>Baca</span>
                                </a>
                                <button onclick="openEditModal('${c.id}')" class="bg-slate-800 hover:bg-slate-700 text-slate-300 rounded-xl py-2 text-xs font-medium transition-colors">
                                    <i class="fa-solid fa-pen"></i> Edit
                                </button>
                                <button onclick="deleteComic('${c.id}')" class="bg-rose-500/10 hover:bg-rose-500/20 text-rose-400 rounded-xl py-2 text-xs font-medium transition-colors">
                                    <i class="fa-solid fa-trash"></i>
                                </button>
                            </div>
                        </div>
                    `;
                });
                html += '</div>';
                container.innerHTML = html;
            } else {
                let html = `
                    <div class="bg-slate-900 border border-slate-800 rounded-2xl overflow-x-auto shadow-lg">
                        <table class="w-full text-left border-collapse text-sm">
                            <thead>
                                <tr class="border-b border-slate-800 text-slate-400 text-xs bg-slate-950/50">
                                    <th class="p-4">Komik</th>
                                    <th class="p-4">Status</th>
                                    <th class="p-4 text-center">Chapter</th>
                                    <th class="p-4">Terakhir Dibaca</th>
                                    <th class="p-4 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-800/60">
                `;
                filtered.forEach(c => {
                    const coverImg = cleanImageUrl(c.image);
                    html += `
                        <tr class="hover:bg-slate-800/40 transition-colors">
                            <td class="p-4 flex items-center space-x-3">
                                <img src="${coverImg}" onerror="this.src='https://images.unsplash.com/photo-1534447677768-be436bb09401?q=80&w=600&auto=format&fit=crop'" class="w-10 h-10 rounded-lg object-cover bg-slate-950">
                                <span class="font-bold text-slate-200 line-clamp-1">${c.title}</span>
                            </td>
                            <td class="p-4">
                                <span class="text-xs px-2.5 py-1 rounded-full border bg-slate-950 border-slate-800 text-slate-300">${c.status}</span>
                            </td>
                            <td class="p-4 text-center">
                                <div class="inline-flex items-center space-x-2 bg-slate-950 border border-slate-800 rounded-lg px-2 py-1">
                                    <button onclick="adjustChapter('${c.id}', -1)" class="text-slate-400 hover:text-white px-1"><i class="fa-solid fa-minus text-[10px]"></i></button>
                                    <span class="font-bold text-purple-400">${c.chapter}</span>
                                    <button onclick="adjustChapter('${c.id}', 1)" class="text-slate-400 hover:text-white px-1"><i class="fa-solid fa-plus text-[10px]"></i></button>
                                </div>
                            </td>
                            <td class="p-4 text-xs text-slate-400">${c.updatedAt || '-'}</td>
                            <td class="p-4 text-center space-x-2">
                                <a href="${c.link || '#'}" target="_blank" title="Baca" class="p-2 bg-purple-600/20 text-purple-400 hover:bg-purple-600 hover:text-white rounded-lg inline-block transition-colors"><i class="fa-solid fa-globe"></i></a>
                                <button onclick="openEditModal('${c.id}')" title="Edit" class="p-2 bg-slate-800 hover:bg-slate-700 text-slate-300 rounded-lg inline-block transition-colors"><i class="fa-solid fa-pen"></i></button>
                                <button onclick="deleteComic('${c.id}')" title="Hapus" class="p-2 bg-rose-500/10 hover:bg-rose-500/20 text-rose-400 rounded-lg inline-block transition-colors"><i class="fa-solid fa-trash"></i></button>
                            </td>
                        </tr>
                    `;
                });
                html += '</tbody></table></div>';
                container.innerHTML = html;
            }
        }

        function openModal() {
            document.getElementById('modalTitle').innerText = 'Tambah Komik Baru';
            document.getElementById('comicForm').reset();
            document.getElementById('comicId').value = '';
            document.getElementById('comicModal').classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('comicModal').classList.add('hidden');
        }

        function openEditModal(id) {
            const comic = comics.find(c => c.id === id);
            if (!comic) return;

            document.getElementById('modalTitle').innerText = 'Edit Data Komik';
            document.getElementById('comicId').value = comic.id;
            document.getElementById('formTitle').value = comic.title;
            document.getElementById('formChapter').value = comic.chapter;
            document.getElementById('formStatus').value = comic.status;
            document.getElementById('formLink').value = comic.link || '';
            document.getElementById('formImage').value = comic.image || '';

            document.getElementById('comicModal').classList.remove('hidden');
        }

        function saveComic(event) {
            event.preventDefault();
            const id = document.getElementById('comicId').value;
            const title = document.getElementById('formTitle').value;
            const chapter = parseInt(document.getElementById('formChapter').value) || 0;
            const status = document.getElementById('formStatus').value;
            const link = document.getElementById('formLink').value;
            const image = document.getElementById('formImage').value;
            const nowStr = getFormattedDate();

            if (id) {
                const index = comics.findIndex(c => c.id === id);
                if (index !== -1) {
                    comics[index] = { ...comics[index], title, chapter, status, link, image, updatedAt: nowStr };
                }
            } else {
                const newComic = {
                    id: Date.now().toString(),
                    title,
                    chapter,
                    status,
                    link,
                    image,
                    updatedAt: nowStr
                };
                comics.unshift(newComic);
            }

            saveToLocalStorage();
            closeModal();
            renderComics();
        }

        function adjustChapter(id, amount) {
            const comic = comics.find(c => c.id === id);
            if (!comic) return;

            comic.chapter = Math.max(0, comic.chapter + amount);
            comic.updatedAt = getFormattedDate();
            saveToLocalStorage();
            renderComics();
        }

        function deleteComic(id) {
            if (confirm('Apakah Anda yakin ingin menghapus komik ini dari daftar?')) {
                comics = comics.filter(c => c.id !== id);
                saveToLocalStorage();
                renderComics();
            }
        }

        function exportData() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(comics, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", "comic_tracker_backup.json");
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
        }

        function importData(event) {
            const fileReader = new FileReader();
            if (event.target.files[0]) {
                fileReader.readAsText(event.target.files[0], "UTF-8");
                fileReader.onload = function (e) {
                    try {
                        const parsed = JSON.parse(e.target.result);
                        if (Array.isArray(parsed)) {
                            comics = parsed;
                            saveToLocalStorage();
                            renderComics();
                            alert('Data berhasil dipulihkan!');
                        } else {
                            alert('Format file JSON tidak valid.');
                        }
                    } catch (err) {
                        alert('Gagal membaca file JSON.');
                    }
                };
            }
        }

        updateStats();
        renderComics();
    </script>
</body>
</html>
