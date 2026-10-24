<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HỆ THỐNG QUẢN LÝ & THEO DÕI ĐIỂM GIỮA HỌC KỲ I - GVBM & GVCN</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- FontAwesome CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        gvbm: {
                            50: '#eff6ff',
                            100: '#dbeafe',
                            500: '#3b82f6',
                            600: '#2563eb',
                            700: '#1d4ed8',
                            800: '#1e40af',
                            900: '#1e3a8a',
                        },
                        gvcn: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#10b981',
                            600: '#059669',
                            700: '#047857',
                            800: '#065f46',
                            900: '#064e3b',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f8fafc;
        }
        .table-responsive::-webkit-scrollbar {
            height: 6px;
        }
        .table-responsive::-webkit-scrollbar-thumb {
            background-color: #cbd5e1;
            border-radius: 4px;
        }
        @media print {
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            body {
                background: white;
                color: black;
            }
            .page-break {
                page-break-after: always;
            }
        }
        .print-only {
            display: none;
        }
    </style>
</head>
<body class="text-slate-800 antialiased min-h-screen flex flex-col justify-between">

    <!-- Header / Navbar -->
    <header id="mainHeader" class="bg-gradient-to-r from-gvbm-900 via-gvbm-800 to-gvbm-700 text-white shadow-lg no-print transition-all duration-300">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col md:flex-row justify-between items-center gap-4">
            
            <!-- Logo & Title -->
            <div class="flex items-center space-x-3">
                <div class="p-3 bg-white/10 rounded-xl backdrop-blur-sm border border-white/20">
                    <i id="roleIcon" class="fa-solid fa-graduation-cap text-3xl text-blue-200"></i>
                </div>
                <div>
                    <h1 class="text-xl sm:text-2xl font-extrabold tracking-tight">THEO DÕI KẾT QUẢ GIỮA HỌC KỲ I</h1>
                    <p class="text-xs sm:text-sm text-white/80 flex items-center gap-2 mt-0.5">
                        <span class="inline-block w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                        Đang làm việc với vai trò: <span id="roleLabel" class="font-bold underline decoration-2">GIÁO VIÊN BỘ MÔN</span>
                    </p>
                </div>
            </div>

            <!-- Role Switcher & Action Buttons -->
            <div class="flex items-center gap-3 flex-wrap justify-center">
                
                <!-- Role Toggle Button -->
                <div class="bg-black/20 p-1 rounded-xl border border-white/20 flex items-center">
                    <button id="btnRoleGVBM" onclick="switchRole('GVBM')" class="px-3.5 py-1.5 rounded-lg text-xs font-bold transition flex items-center gap-1.5 bg-white text-gvbm-900 shadow">
                        <i class="fa-solid fa-book-open"></i> GV Bộ Môn
                    </button>
                    <button id="btnRoleGVCN" onclick="switchRole('GVCN')" class="px-3.5 py-1.5 rounded-lg text-xs font-bold transition flex items-center gap-1.5 text-white/80 hover:text-white">
                        <i class="fa-solid fa-user-group"></i> GV Chủ Nhiệm
                    </button>
                </div>

                <button onclick="exportToCSV()" class="px-3 py-2 bg-emerald-600 hover:bg-emerald-700 text-white text-xs font-semibold rounded-xl shadow transition flex items-center gap-1.5">
                    <i class="fa-solid fa-file-excel"></i> Xuất Excel/CSV
                </button>
                <button onclick="window.print()" class="px-3 py-2 bg-white/10 hover:bg-white/20 text-white text-xs font-semibold rounded-xl shadow backdrop-blur-sm transition flex items-center gap-1.5 border border-white/20">
                    <i class="fa-solid fa-print"></i> In Báo Cáo
                </button>
                <button onclick="confirmResetData()" class="px-3 py-2 bg-rose-600/80 hover:bg-rose-600 text-white text-xs font-semibold rounded-xl shadow transition flex items-center gap-1.5">
                    <i class="fa-solid fa-rotate-left"></i> Đặt lại
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 w-full flex-grow">

        <!-- Header for Print View Only -->
        <div class="hidden print-only mb-6 text-center border-b pb-4">
            <h1 class="text-2xl font-bold text-slate-900" id="printHeaderTitle">BÁO CÁO KẾT QUẢ ĐIỂM GIỮA HỌC KỲ I</h1>
            <p class="text-sm text-slate-600 mt-1">Ngày xuất báo cáo: <span id="printDate"></span></p>
        </div>

        <!-- ========================================================================= -->
        <!-- SECTION 1: INTERFACE FOR SUBJECT TEACHER (GVBM)                           -->
        <!-- ========================================================================= -->
        <div id="sectionGVBM" class="space-y-6">
            
            <!-- Config Subject & Stats Row -->
            <div class="grid grid-cols-1 lg:grid-cols-4 gap-4">
                
                <!-- Subject Selector Card -->
                <div class="bg-white p-5 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">
                            <i class="fa-solid fa-book text-gvbm-600"></i> Chọn/Nhập Môn Giảng Dạy
                        </label>
                        <select id="gvbmSubjectSelect" onchange="changeSubject()" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm font-bold text-gvbm-800 focus:outline-none focus:ring-2 focus:ring-gvbm-500">
                            <option value="Toán">Môn: Toán</option>
                            <option value="Ngữ Văn">Môn: Ngữ Văn</option>
                            <option value="Tiếng Anh">Môn: Tiếng Anh</option>
                            <option value="Vật Lý">Môn: Vật Lý</option>
                            <option value="Hóa Học">Môn: Hóa Học</option>
                            <option value="Sinh Học">Môn: Sinh Học</option>
                            <option value="Lịch Sử">Môn: Lịch Sử</option>
                            <option value="Địa Lý">Môn: Địa Lý</option>
                            <option value="Tin Học">Môn: Tin Học</option>
                        </select>
                    </div>
                    <p class="text-[11px] text-slate-400 mt-3">ĐTB Môn = (TBC ĐĐGtx + ĐĐGgk × 2) ÷ 3</p>
                </div>

                <!-- Stats Overview Cards -->
                <div class="lg:col-span-3 grid grid-cols-2 sm:grid-cols-4 gap-3">
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-emerald-500">
                        <span class="text-xs font-semibold text-slate-500">Môn Tốt</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-emerald-600" id="bmStatTot">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-emerald-50 text-emerald-700" id="bmStatTotPct">0%</span>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-sky-500">
                        <span class="text-xs font-semibold text-slate-500">Môn Khá</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-sky-600" id="bmStatKha">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-sky-50 text-sky-700" id="bmStatKhaPct">0%</span>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-amber-500">
                        <span class="text-xs font-semibold text-slate-500">Môn Đạt</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-amber-600" id="bmStatDat">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-amber-50 text-amber-700" id="bmStatDatPct">0%</span>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-rose-500">
                        <span class="text-xs font-semibold text-slate-500">Chưa Đạt</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-rose-600" id="bmStatChuaDat">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-rose-50 text-rose-700" id="bmStatChuaDatPct">0%</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Input Form & Visual Chart Area -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 no-print">
                <!-- Left: Form Entry -->
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5">
                    <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
                        <h2 class="text-base font-bold text-slate-800 flex items-center gap-2" id="bmFormTitle">
                            <i class="fa-solid fa-user-plus text-gvbm-600"></i> Nhập Điểm Học Sinh
                        </h2>
                        <span id="bmEditBadge" class="hidden text-xs bg-amber-100 text-amber-800 px-2 py-0.5 rounded-full font-semibold">Đang sửa</span>
                    </div>

                    <form id="bmStudentForm" onsubmit="handleBMSubmit(event)" class="space-y-3">
                        <input type="hidden" id="bmStudentId">

                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Họ và Tên Học Sinh *</label>
                            <input type="text" id="bmFullname" required placeholder="Nhập họ và tên..." 
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-gvbm-500">
                        </div>

                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Lớp *</label>
                            <input type="text" id="bmClassName" required placeholder="Ví dụ: 9A1, 10A2..." 
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-gvbm-500">
                        </div>

                        <!-- 4 Columns for Regular Evaluation Scores -->
                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Điểm ĐG Thường Xuyên (ĐĐGtx)</label>
                            <div class="grid grid-cols-4 gap-2">
                                <input type="number" step="0.1" min="0" max="10" id="bmTx1" placeholder="TX1" class="px-2 py-1.5 bg-slate-50 border border-slate-200 rounded-lg text-xs text-center focus:ring-2 focus:ring-gvbm-500">
                                <input type="number" step="0.1" min="0" max="10" id="bmTx2" placeholder="TX2" class="px-2 py-1.5 bg-slate-50 border border-slate-200 rounded-lg text-xs text-center focus:ring-2 focus:ring-gvbm-500">
                                <input type="number" step="0.1" min="0" max="10" id="bmTx3" placeholder="TX3" class="px-2 py-1.5 bg-slate-50 border border-slate-200 rounded-lg text-xs text-center focus:ring-2 focus:ring-gvbm-500">
                                <input type="number" step="0.1" min="0" max="10" id="bmTx4" placeholder="TX4" class="px-2 py-1.5 bg-slate-50 border border-slate-200 rounded-lg text-xs text-center focus:ring-2 focus:ring-gvbm-500">
                            </div>
                        </div>

                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Điểm ĐG Giữa Kỳ (ĐĐGgk) *</label>
                            <input type="number" step="0.1" min="0" max="10" id="bmGk" required placeholder="Nhập điểm giữa kỳ (0-10)" 
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-gvbm-500">
                        </div>

                        <div class="pt-2 flex gap-2">
                            <button type="submit" id="bmBtnSubmit" class="flex-1 py-2 px-4 bg-gvbm-600 hover:bg-gvbm-700 text-white text-xs font-bold rounded-xl shadow transition">
                                <i class="fa-solid fa-check"></i> <span id="bmBtnText">Thêm Học Sinh</span>
                            </button>
                            <button type="button" id="bmBtnCancel" onclick="resetBMForm()" class="hidden py-2 px-3 bg-slate-200 text-slate-700 text-xs font-bold rounded-xl">
                                Hủy
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Right: Chart + Weak Students Alert -->
                <div class="lg:col-span-2 space-y-4 flex flex-col justify-between">
                    <!-- Warning List -->
                    <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-4">
                        <div class="flex items-center justify-between border-b pb-2 mb-2">
                            <h3 class="text-xs font-bold text-rose-600 uppercase tracking-wider flex items-center gap-1.5">
                                <i class="fa-solid fa-triangle-exclamation animate-bounce"></i> Học Sinh Cần Phụ Đạo (ĐTB Môn < 5.0)
                            </h3>
                            <span id="bmWarningCount" class="text-xs bg-rose-100 text-rose-700 px-2 py-0.5 rounded-full font-bold">0 HS</span>
                        </div>
                        <div id="bmWarningContainer" class="max-h-28 overflow-y-auto space-y-1.5 pr-1">
                            <!-- Dynamically Loaded -->
                        </div>
                    </div>

                    <!-- Chart Panel -->
                    <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-4 flex-grow flex flex-col justify-center">
                        <h3 class="text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">Phân Bổ Tỷ Lệ Xếp Loại Môn Học</h3>
                        <div class="h-36 w-full flex justify-center">
                            <canvas id="bmChart"></canvas>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Table View Section (GVBM) -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 overflow-hidden">
                <!-- Filters -->
                <div class="p-4 border-b border-slate-100 bg-slate-50/50 flex flex-col sm:flex-row justify-between items-center gap-3 no-print">
                    <div class="flex items-center gap-2">
                        <h2 class="text-base font-bold text-slate-800">Bảng Điểm Môn <span id="bmCurrentSubjectTitle" class="text-gvbm-700">Toán</span></h2>
                        <span id="bmResultCount" class="text-xs bg-slate-200 text-slate-700 px-2 py-0.5 rounded-full font-semibold">0</span>
                    </div>
                    <div class="flex items-center gap-2 w-full sm:w-auto">
                        <input type="text" id="bmSearchInput" oninput="renderBMTable()" placeholder="Tìm tên học sinh..." 
                            class="px-3 py-1.5 bg-white border border-slate-200 rounded-xl text-xs focus:ring-2 focus:ring-gvbm-500 w-full sm:w-48">
                        <select id="bmFilterClass" onchange="renderBMTable()" class="px-3 py-1.5 bg-white border border-slate-200 rounded-xl text-xs focus:ring-2 focus:ring-gvbm-500">
                            <option value="">Tất cả lớp</option>
                        </select>
                    </div>
                </div>

                <!-- Table -->
                <div class="overflow-x-auto table-responsive">
                    <table class="w-full text-left border-collapse text-xs sm:text-sm">
                        <thead>
                            <tr class="bg-slate-100 text-slate-600 uppercase text-[11px] font-bold border-b border-slate-200">
                                <th class="py-3 px-3 text-center">STT</th>
                                <th class="py-3 px-3">Họ và Tên</th>
                                <th class="py-3 px-3 text-center">Lớp</th>
                                <th class="py-3 px-3 text-center">TX1</th>
                                <th class="py-3 px-3 text-center">TX2</th>
                                <th class="py-3 px-3 text-center">TX3</th>
                                <th class="py-3 px-3 text-center">TX4</th>
                                <th class="py-3 px-3 text-center">TBC TX</th>
                                <th class="py-3 px-3 text-center">Giữa Kỳ</th>
                                <th class="py-3 px-3 text-center">ĐTB Môn</th>
                                <th class="py-3 px-3 text-center">Xếp Loại</th>
                                <th class="py-3 px-3 text-center no-print">Thao Tác</th>
                            </tr>
                        </thead>
                        <tbody id="bmTableBody" class="divide-y divide-slate-100">
                            <!-- Dynamic Content -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

        <!-- ========================================================================= -->
        <!-- SECTION 2: INTERFACE FOR HOMEROOM TEACHER (GVCN)                         -->
        <!-- ========================================================================= -->
        <div id="sectionGVCN" class="space-y-6 hidden">
            
            <!-- Class Config & General Summary -->
            <div class="grid grid-cols-1 lg:grid-cols-4 gap-4">
                
                <!-- Class Selection -->
                <div class="bg-white p-5 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between">
                    <div>
                        <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">
                            <i class="fa-solid fa-chalkboard-user text-gvcn-600"></i> Lớp Chủ Nhiệm
                        </label>
                        <input type="text" id="cnClassInput" value="9A1" onchange="renderGVCN()" placeholder="Nhập tên lớp..." 
                            class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm font-bold text-gvcn-800 focus:outline-none focus:ring-2 focus:ring-gvcn-500">
                    </div>
                    <p class="text-[11px] text-slate-400 mt-3">Tổng hợp Học lực & Rèn luyện toàn lớp</p>
                </div>

                <!-- Stats Dual Summary -->
                <div class="lg:col-span-3 grid grid-cols-2 sm:grid-cols-4 gap-3">
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-emerald-500">
                        <span class="text-xs font-semibold text-slate-500">KQ: Tốt</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-emerald-600" id="cnStatTot">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-emerald-50 text-emerald-700" id="cnStatTotPct">0%</span>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-sky-500">
                        <span class="text-xs font-semibold text-slate-500">KQ: Khá</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-sky-600" id="cnStatKha">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-sky-50 text-sky-700" id="cnStatKhaPct">0%</span>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-amber-500">
                        <span class="text-xs font-semibold text-slate-500">KQ: Đạt</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-amber-600" id="cnStatDat">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-amber-50 text-amber-700" id="cnStatDatPct">0%</span>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/80 flex flex-col justify-between border-l-4 border-l-rose-500">
                        <span class="text-xs font-semibold text-slate-500">Chưa Đạt / Cần Rèn Luyện</span>
                        <div class="flex items-baseline justify-between mt-2">
                            <span class="text-2xl font-extrabold text-rose-600" id="cnStatChuaDat">0</span>
                            <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-rose-50 text-rose-700" id="cnStatChuaDatPct">0%</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Warning & Evaluation Form Row -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 no-print">
                
                <!-- Left: Form Input for Homeroom Conduct & Comments -->
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5">
                    <div class="flex items-center justify-between pb-3 border-b border-slate-100 mb-4">
                        <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-user-check text-gvcn-600"></i> Đánh Giá Rèn Luyện & Nhận Xét
                        </h2>
                    </div>

                    <form id="cnStudentForm" onsubmit="handleCNSubmit(event)" class="space-y-3">
                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Chọn Học Sinh Trong Lớp *</label>
                            <select id="cnSelectStudent" onchange="loadCNStudentDetails()" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-gvcn-500 font-semibold">
                                <!-- Loaded dynamically -->
                            </select>
                        </div>

                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Mức Rèn Luyện (Hạnh Kiểm) *</label>
                            <select id="cnRenLuyen" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-gvcn-500">
                                <option value="Tốt">Tốt</option>
                                <option value="Khá">Khá</option>
                                <option value="Đạt">Đạt</option>
                                <option value="Chưa đạt">Chưa đạt</option>
                            </select>
                        </div>

                        <div>
                            <label class="block text-xs font-semibold text-slate-600 mb-1">Nhận Xét Của GVCN (Gửi Phụ Huynh)</label>
                            <textarea id="cnComment" rows="3" placeholder="Nhập nhận xét về ý thức học tập, nề nếp..." 
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-gvcn-500"></textarea>
                        </div>

                        <button type="submit" class="w-full py-2 px-4 bg-gvcn-600 hover:bg-gvcn-700 text-white text-xs font-bold rounded-xl shadow transition">
                            <i class="fa-solid fa-floppy-disk"></i> Lưu Đánh Giá GVCN
                        </button>
                    </form>
                </div>

                <!-- Right: Chart & Warning List -->
                <div class="lg:col-span-2 space-y-4 flex flex-col justify-between">
                    <!-- Warning List -->
                    <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-4">
                        <div class="flex items-center justify-between border-b pb-2 mb-2">
                            <h3 class="text-xs font-bold text-rose-600 uppercase tracking-wider flex items-center gap-1.5">
                                <i class="fa-solid fa-triangle-exclamation animate-bounce"></i> Học Sinh Cần Phụ Huynh Quan Tâm Đặc Biệt
                            </h3>
                            <span id="cnWarningCount" class="text-xs bg-rose-100 text-rose-700 px-2 py-0.5 rounded-full font-bold">0 HS</span>
                        </div>
                        <div id="cnWarningContainer" class="max-h-28 overflow-y-auto space-y-1.5 pr-1">
                            <!-- Dynamic Content -->
                        </div>
                    </div>

                    <!-- Chart Panel -->
                    <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-4 flex-grow flex flex-col justify-center">
                        <h3 class="text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">Phân Bổ Kết Quả Học Tập Lớp</h3>
                        <div class="h-36 w-full flex justify-center">
                            <canvas id="cnChart"></canvas>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Homeroom Table Overview -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200/80 overflow-hidden">
                <div class="p-4 border-b border-slate-100 bg-slate-50/50 flex flex-col sm:flex-row justify-between items-center gap-3 no-print">
                    <div class="flex items-center gap-2">
                        <h2 class="text-base font-bold text-slate-800">Bảng Tổng Hợp Kết Quả Lớp <span id="cnTableClassTitle" class="text-gvcn-700">9A1</span></h2>
                        <span id="cnResultCount" class="text-xs bg-slate-200 text-slate-700 px-2 py-0.5 rounded-full font-semibold">0</span>
                    </div>
                    <div class="flex items-center gap-2">
                        <input type="text" id="cnSearchInput" oninput="renderGVCNTable()" placeholder="Tìm học sinh..." 
                            class="px-3 py-1.5 bg-white border border-slate-200 rounded-xl text-xs focus:ring-2 focus:ring-gvcn-500 w-full sm:w-48">
                    </div>
                </div>

                <!-- Table Content -->
                <div class="overflow-x-auto table-responsive">
                    <table class="w-full text-left border-collapse text-xs sm:text-sm">
                        <thead>
                            <tr class="bg-slate-100 text-slate-600 uppercase text-[11px] font-bold border-b border-slate-200">
                                <th class="py-3 px-3 text-center">STT</th>
                                <th class="py-3 px-3">Họ và Tên</th>
                                <th class="py-3 px-3 text-center">Toán</th>
                                <th class="py-3 px-3 text-center">Ngữ Văn</th>
                                <th class="py-3 px-3 text-center">Tiếng Anh</th>
                                <th class="py-3 px-3 text-center">Vật Lý</th>
                                <th class="py-3 px-3 text-center">ĐTB Các Môn</th>
                                <th class="py-3 px-3 text-center">Học Lực</th>
                                <th class="py-3 px-3 text-center">Rèn Luyện</th>
                                <th class="py-3 px-3">Nhận Xét GVCN</th>
                                <th class="py-3 px-3 text-center no-print">Phiếu Điểm</th>
                            </tr>
                        </thead>
                        <tbody id="cnTableBody" class="divide-y divide-slate-100">
                            <!-- Dynamic Rows -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

        <!-- Printable Report Modal/Section for Homeroom Card -->
        <div id="printCardModal" class="hidden print-only p-8 bg-white border-2 border-slate-800 rounded-2xl my-4">
            <!-- Dynamically populated when printing individual report card -->
        </div>

    </main>

    <!-- Footer -->
    <footer class="bg-white border-t border-slate-200 mt-8 py-4 no-print">
        <div class="max-w-7xl mx-auto px-4 text-center text-xs text-slate-500 flex flex-col sm:flex-row justify-between items-center gap-2">
            <div>Phần Mềm Quản Lý Điểm Giữa HKI - Tích Hợp Chế Độ GVBM & GVCN</div>
            <div>Dữ liệu lưu an toàn trên LocalStorage thiết bị của bạn</div>
        </div>
    </footer>

    <!-- Notification Toast -->
    <div id="toast" class="fixed bottom-5 right-5 z-50 transform translate-y-20 opacity-0 transition-all duration-300 pointer-events-none">
        <div id="toastContent" class="px-4 py-3 rounded-xl shadow-lg text-white text-sm font-medium flex items-center gap-2">
            <i id="toastIcon" class="fa-solid"></i>
            <span id="toastMessage">Thông báo</span>
        </div>
    </div>

    <!-- JAVASCRIPT APP LOGIC -->
    <script>
        // LocalStorage Keys
        const STORAGE_KEY_BM = 'GVBM_Data_v3';
        const STORAGE_KEY_CN = 'GVCN_Data_v3';

        // State Variables
        let currentRole = 'GVBM'; // 'GVBM' or 'GVCN'
        let currentSubject = 'Toán';
        let bmData = {}; // Format: { "Toán": [student1, student2], "Ngữ Văn": [...] }
        let cnData = []; // Format: [{ id, fullname, className, renLuyen, comment, scores: { "Toán": 8.5, ... } }]

        // Initial Sample Data Generator
        function getSampleBMData() {
            return {
                "Toán": [
                    { id: '1', fullname: 'Nguyễn Văn Anh', className: '9A1', tx: [8, 9, 8.5, null], gk: 8.5 },
                    { id: '2', fullname: 'Trần Thị Bích', className: '9A1', tx: [9, 9.5, 10, 9], gk: 9.5 },
                    { id: '3', fullname: 'Lê Hoàng Cường', className: '9A1', tx: [6, 6.5, 7, null], gk: 6.0 },
                    { id: '4', fullname: 'Phạm Minh Đức', className: '9A1', tx: [5, 4.5, 5, null], gk: 4.0 },
                    { id: '5', fullname: 'Hoàng Thùy Dương', className: '9A2', tx: [3.5, 4, 3, null], gk: 4.5 }
                ],
                "Ngữ Văn": [
                    { id: '1', fullname: 'Nguyễn Văn Anh', className: '9A1', tx: [7.5, 8, null, null], gk: 8.0 },
                    { id: '2', fullname: 'Trần Thị Bích', className: '9A1', tx: [8.5, 9, 9.5, null], gk: 9.0 },
                    { id: '3', fullname: 'Lê Hoàng Cường', className: '9A1', tx: [5.5, 6, null, null], gk: 5.5 },
                    { id: '4', fullname: 'Phạm Minh Đức', className: '9A1', tx: [4, 5, null, null], gk: 4.5 }
                ],
                "Tiếng Anh": [
                    { id: '1', fullname: 'Nguyễn Văn Anh', className: '9A1', tx: [9, 8.5, 9, null], gk: 9.0 },
                    { id: '2', fullname: 'Trần Thị Bích', className: '9A1', tx: [9.5, 10, 9.5, null], gk: 10.0 },
                    { id: '3', fullname: 'Lê Hoàng Cường', className: '9A1', tx: [6.5, 7, null, null], gk: 6.0 },
                    { id: '4', fullname: 'Phạm Minh Đức', className: '9A1', tx: [3, 4, null, null], gk: 3.5 }
                ],
                "Vật Lý": [
                    { id: '1', fullname: 'Nguyễn Văn Anh', className: '9A1', tx: [8, 8, null, null], gk: 8.5 },
                    { id: '2', fullname: 'Trần Thị Bích', className: '9A1', tx: [9, 9, null, null], gk: 9.0 },
                    { id: '3', fullname: 'Lê Hoàng Cường', className: '9A1', tx: [6, 6.5, null, null], gk: 6.5 },
                    { id: '4', fullname: 'Phạm Minh Đức', className: '9A1', tx: [5, 5, null, null], gk: 4.0 }
                ]
            };
        }

        function getSampleCNData() {
            return [
                { id: '1', fullname: 'Nguyễn Văn Anh', className: '9A1', renLuyen: 'Tốt', comment: 'Ngoan ngoãn, hăng hái phát biểu xây dựng bài.' },
                { id: '2', fullname: 'Trần Thị Bích', className: '9A1', renLuyen: 'Tốt', comment: 'Lớp trưởng gương mẫu, học tập xuất sắc.' },
                { id: '3', fullname: 'Lê Hoàng Cường', className: '9A1', renLuyen: 'Khá', comment: 'Có tiến bộ trong học tập, cần chú ý môn Toán hơn.' },
                { id: '4', fullname: 'Phạm Minh Đức', className: '9A1', renLuyen: 'Chưa đạt', comment: 'Thường xuyên quên làm bài tập về nhà, kết quả các môn còn yếu.' }
            ];
        }

        let bmChartInstance = null;
        let cnChartInstance = null;

        // Init App
        window.onload = function() {
            loadStorage();
            initCharts();
            renderApp();
            document.getElementById('printDate').innerText = new Date().toLocaleDateString('vi-VN');
        };

        // Load LocalStorage
        function loadStorage() {
            const savedBM = localStorage.getItem(STORAGE_KEY_BM);
            if (savedBM) {
                try { bmData = JSON.parse(savedBM); } catch(e) { bmData = getSampleBMData(); }
            } else {
                bmData = getSampleBMData();
                saveBMData();
            }

            const savedCN = localStorage.getItem(STORAGE_KEY_CN);
            if (savedCN) {
                try { cnData = JSON.parse(savedCN); } catch(e) { cnData = getSampleCNData(); }
            } else {
                cnData = getSampleCNData();
                saveCNData();
            }
        }

        function saveBMData() { localStorage.setItem(STORAGE_KEY_BM, JSON.stringify(bmData)); }
        function saveCNData() { localStorage.setItem(STORAGE_KEY_CN, JSON.stringify(cnData)); }

        // Role Switcher
        function switchRole(role) {
            currentRole = role;
            const header = document.getElementById('mainHeader');
            const roleLabel = document.getElementById('roleLabel');
            const roleIcon = document.getElementById('roleIcon');
            const btnGVBM = document.getElementById('btnRoleGVBM');
            const btnGVCN = document.getElementById('btnRoleGVCN');

            if (role === 'GVBM') {
                header.className = 'bg-gradient-to-r from-gvbm-900 via-gvbm-800 to-gvbm-700 text-white shadow-lg no-print transition-all duration-300';
                roleLabel.innerText = 'GIÁO VIÊN BỘ MÔN';
                roleIcon.className = 'fa-solid fa-book-open text-3xl text-blue-200';
                btnGVBM.className = 'px-3.5 py-1.5 rounded-lg text-xs font-bold transition flex items-center gap-1.5 bg-white text-gvbm-900 shadow';
                btnGVCN.className = 'px-3.5 py-1.5 rounded-lg text-xs font-bold transition flex items-center gap-1.5 text-white/80 hover:text-white';
                
                document.getElementById('sectionGVBM').classList.remove('hidden');
                document.getElementById('sectionGVCN').classList.add('hidden');
                document.getElementById('printHeaderTitle').innerText = 'BÁO CÁO ĐIỂM BỘ MÔN GIỮA HỌC KỲ I';
            } else {
                header.className = 'bg-gradient-to-r from-gvcn-900 via-gvcn-800 to-gvcn-700 text-white shadow-lg no-print transition-all duration-300';
                roleLabel.innerText = 'GIÁO VIÊN CHỦ NHIỆM';
                roleIcon.className = 'fa-solid fa-user-group text-3xl text-emerald-200';
                btnGVCN.className = 'px-3.5 py-1.5 rounded-lg text-xs font-bold transition flex items-center gap-1.5 bg-white text-gvcn-900 shadow';
                btnGVBM.className = 'px-3.5 py-1.5 rounded-lg text-xs font-bold transition flex items-center gap-1.5 text-white/80 hover:text-white';
                
                document.getElementById('sectionGVBM').classList.add('hidden');
                document.getElementById('sectionGVCN').classList.remove('hidden');
                document.getElementById('printHeaderTitle').innerText = 'BÁO CÁO TỔNG HỢP KẾT QUẢ LỚP CHỦ NHIỆM - GIỮA HKI';
            }
            renderApp();
        }

        // Calculations & Utilities
        function calcTxAvg(txArray) {
            const valid = txArray.filter(v => v !== null && v !== undefined && !isNaN(v));
            if (valid.length === 0) return 0;
            return valid.reduce((a, b) => a + b, 0) / valid.length;
        }

        function calcMidHK1(txAvg, gk) {
            return (txAvg + gk * 2) / 3;
        }

        function getRank(dtb) {
            if (dtb >= 8.0) return { title: 'Tốt', color: 'emerald' };
            if (dtb >= 6.5) return { title: 'Khá', color: 'sky' };
            if (dtb >= 5.0) return { title: 'Đạt', color: 'amber' };
            return { title: 'Chưa đạt', color: 'rose' };
        }

        // =========================================================================
        // LOGIC FOR GVBM (SUBJECT TEACHER)
        // =========================================================================
        function changeSubject() {
            currentSubject = document.getElementById('gvbmSubjectSelect').value;
            document.getElementById('bmCurrentSubjectTitle').innerText = currentSubject;
            resetBMForm();
            renderGVBM();
        }

        function handleBMSubmit(e) {
            e.preventDefault();
            const subject = currentSubject;
            if (!bmData[subject]) bmData[subject] = [];

            const id = document.getElementById('bmStudentId').value;
            const fullname = document.getElementById('bmFullname').value.trim();
            const className = document.getElementById('bmClassName').value.trim().toUpperCase();

            const tx1 = parseFloat(document.getElementById('bmTx1').value);
            const tx2 = parseFloat(document.getElementById('bmTx2').value);
            const tx3 = parseFloat(document.getElementById('bmTx3').value);
            const tx4 = parseFloat(document.getElementById('bmTx4').value);
            const gk = parseFloat(document.getElementById('bmGk').value);

            const txArray = [
                !isNaN(tx1) ? tx1 : null,
                !isNaN(tx2) ? tx2 : null,
                !isNaN(tx3) ? tx3 : null,
                !isNaN(tx4) ? tx4 : null
            ];

            if (txArray.every(v => v === null)) {
                showToast('Vui lòng nhập ít nhất 1 cột điểm ĐĐGtx!', 'warning');
                return;
            }

            if (id) {
                const idx = bmData[subject].findIndex(s => s.id === id);
                if (idx !== -1) {
                    bmData[subject][idx] = { id, fullname, className, tx: txArray, gk };
                    showToast('Đã cập nhật điểm học sinh!', 'success');
                }
            } else {
                const newStudent = { id: Date.now().toString(), fullname, className, tx: txArray, gk };
                bmData[subject].push(newStudent);
                showToast('Thêm học sinh thành công!', 'success');
            }

            saveBMData();
            resetBMForm();
            renderGVBM();
        }

        function editBMStudent(id) {
            const list = bmData[currentSubject] || [];
            const st = list.find(s => s.id === id);
            if (!st) return;

            document.getElementById('bmStudentId').value = st.id;
            document.getElementById('bmFullname').value = st.fullname;
            document.getElementById('bmClassName').value = st.className;

            document.getElementById('bmTx1').value = st.tx[0] !== null ? st.tx[0] : '';
            document.getElementById('bmTx2').value = st.tx[1] !== null ? st.tx[1] : '';
            document.getElementById('bmTx3').value = st.tx[2] !== null ? st.tx[2] : '';
            document.getElementById('bmTx4').value = st.tx[3] !== null ? st.tx[3] : '';
            document.getElementById('bmGk').value = st.gk;

            document.getElementById('bmFormTitle').innerHTML = '<i class="fa-solid fa-pen-to-square text-amber-600"></i> Sửa Điểm Học Sinh';
            document.getElementById('bmEditBadge').classList.remove('hidden');
            document.getElementById('bmBtnText').innerText = 'Cập Nhật';
            document.getElementById('bmBtnCancel').classList.remove('hidden');
        }

        function deleteBMStudent(id) {
            if (confirm('Bạn có chắc chắn muốn xóa học sinh này khỏi môn học?')) {
                bmData[currentSubject] = (bmData[currentSubject] || []).filter(s => s.id !== id);
                saveBMData();
                renderGVBM();
                showToast('Đã xóa dữ liệu!', 'info');
            }
        }

        function resetBMForm() {
            document.getElementById('bmStudentForm').reset();
            document.getElementById('bmStudentId').value = '';
            document.getElementById('bmFormTitle').innerHTML = '<i class="fa-solid fa-user-plus text-gvbm-600"></i> Nhập Điểm Học Sinh';
            document.getElementById('bmEditBadge').classList.add('hidden');
            document.getElementById('bmBtnText').innerText = 'Thêm Học Sinh';
            document.getElementById('bmBtnCancel').classList.add('hidden');
        }

        function renderGVBM() {
            updateBMClassFilter();
            renderBMTable();
            renderBMStats();
            renderBMWarnings();
            updateBMChart();
        }

        function updateBMClassFilter() {
            const list = bmData[currentSubject] || [];
            const select = document.getElementById('bmFilterClass');
            const currentVal = select.value;
            const classes = [...new Set(list.map(s => s.className))].sort();

            select.innerHTML = '<option value="">Tất cả lớp</option>';
            classes.forEach(c => {
                const opt = document.createElement('option');
                opt.value = c;
                opt.textContent = `Lớp ${c}`;
                if (c === currentVal) opt.selected = true;
                select.appendChild(opt);
            });
        }

        function renderBMTable() {
            const list = bmData[currentSubject] || [];
            const searchVal = document.getElementById('bmSearchInput').value.toLowerCase().trim();
            const filterClass = document.getElementById('bmFilterClass').value;
            const tbody = document.getElementById('bmTableBody');
            tbody.innerHTML = '';

            const filtered = list.filter(s => {
                const matchName = s.fullname.toLowerCase().includes(searchVal);
                const matchClass = filterClass === '' || s.className === filterClass;
                return matchName && matchClass;
            });

            document.getElementById('bmResultCount').innerText = `${filtered.length} / ${list.length}`;

            filtered.forEach((s, idx) => {
                const txAvg = calcTxAvg(s.tx);
                const dtb = calcMidHK1(txAvg, s.gk);
                const rank = getRank(dtb);

                let badgeColor = '';
                if (rank.color === 'emerald') badgeColor = 'bg-emerald-100 text-emerald-800 border-emerald-200';
                else if (rank.color === 'sky') badgeColor = 'bg-sky-100 text-sky-800 border-sky-200';
                else if (rank.color === 'amber') badgeColor = 'bg-amber-100 text-amber-800 border-amber-200';
                else badgeColor = 'bg-rose-100 text-rose-800 border-rose-200 animate-pulse';

                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition';
                tr.innerHTML = `
                    <td class="py-2.5 px-3 text-center text-slate-400 font-medium">${idx + 1}</td>
                    <td class="py-2.5 px-3 font-semibold text-slate-800">${s.fullname}</td>
                    <td class="py-2.5 px-3 text-center"><span class="px-2 py-0.5 bg-slate-100 text-slate-700 rounded font-bold">${s.className}</span></td>
                    <td class="py-2.5 px-3 text-center text-slate-500 font-mono">${s.tx[0] !== null ? s.tx[0] : '-'}</td>
                    <td class="py-2.5 px-3 text-center text-slate-500 font-mono">${s.tx[1] !== null ? s.tx[1] : '-'}</td>
                    <td class="py-2.5 px-3 text-center text-slate-500 font-mono">${s.tx[2] !== null ? s.tx[2] : '-'}</td>
                    <td class="py-2.5 px-3 text-center text-slate-500 font-mono">${s.tx[3] !== null ? s.tx[3] : '-'}</td>
                    <td class="py-2.5 px-3 text-center font-mono font-medium text-slate-700">${txAvg.toFixed(1)}</td>
                    <td class="py-2.5 px-3 text-center font-mono font-medium text-slate-700">${s.gk.toFixed(1)}</td>
                    <td class="py-2.5 px-3 text-center font-mono font-extrabold ${dtb < 5.0 ? 'text-rose-600' : 'text-slate-900'}">${dtb.toFixed(1)}</td>
                    <td class="py-2.5 px-3 text-center">
                        <span class="px-2.5 py-0.5 rounded-full text-xs font-bold border ${badgeColor}">${rank.title}</span>
                    </td>
                    <td class="py-2.5 px-3 text-center no-print">
                        <div class="flex items-center justify-center space-x-1">
                            <button onclick="editBMStudent('${s.id}')" title="Sửa" class="p-1 text-slate-400 hover:text-amber-600"><i class="fa-solid fa-pen-to-square"></i></button>
                            <button onclick="deleteBMStudent('${s.id}')" title="Xóa" class="p-1 text-slate-400 hover:text-rose-600"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderBMStats() {
            const list = bmData[currentSubject] || [];
            const total = list.length;
            let tot = 0, kha = 0, dat = 0, chuaDat = 0;

            list.forEach(s => {
                const dtb = calcMidHK1(calcTxAvg(s.tx), s.gk);
                const r = getRank(dtb).title;
                if (r === 'Tốt') tot++;
                else if (r === 'Khá') kha++;
                else if (r === 'Đạt') dat++;
                else chuaDat++;
            });

            const getPct = v => total > 0 ? ((v / total) * 100).toFixed(1) + '%' : '0%';

            document.getElementById('bmStatTot').innerText = tot;
            document.getElementById('bmStatTotPct').innerText = getPct(tot);
            document.getElementById('bmStatKha').innerText = kha;
            document.getElementById('bmStatKhaPct').innerText = getPct(kha);
            document.getElementById('bmStatDat').innerText = dat;
            document.getElementById('bmStatDatPct').innerText = getPct(dat);
            document.getElementById('bmStatChuaDat').innerText = chuaDat;
            document.getElementById('bmStatChuaDatPct').innerText = getPct(chuaDat);
        }

        function renderBMWarnings() {
            const list = bmData[currentSubject] || [];
            const container = document.getElementById('bmWarningContainer');
            container.innerHTML = '';

            const weak = list.filter(s => calcMidHK1(calcTxAvg(s.tx), s.gk) < 5.0);
            document.getElementById('bmWarningCount').innerText = `${weak.length} HS`;

            if (weak.length === 0) {
                container.innerHTML = `<div class="text-xs text-emerald-600 bg-emerald-50 p-2 rounded-xl border border-emerald-100"><i class="fa-solid fa-check-circle"></i> Không có học sinh nào bị xếp loại Chưa Đạt môn này!</div>`;
                return;
            }

            weak.forEach(s => {
                const dtb = calcMidHK1(calcTxAvg(s.tx), s.gk);
                const div = document.createElement('div');
                div.className = 'flex items-center justify-between bg-rose-50 border border-rose-100 p-2 rounded-xl text-xs';
                div.innerHTML = `
                    <div class="flex items-center gap-2">
                        <span class="font-bold text-rose-800">${s.fullname}</span>
                        <span class="px-1.5 py-0.5 bg-rose-200 text-rose-800 rounded font-semibold text-[10px]">Lớp ${s.className}</span>
                    </div>
                    <span class="font-mono font-extrabold text-rose-600 bg-white px-2 py-0.5 rounded border border-rose-200">ĐTB Môn: ${dtb.toFixed(1)}</span>
                `;
                container.appendChild(div);
            });
        }

        // =========================================================================
        // LOGIC FOR GVCN (HOMEROOM TEACHER)
        // =========================================================================
        function handleCNSubmit(e) {
            e.preventDefault();
            const studentId = document.getElementById('cnSelectStudent').value;
            const renLuyen = document.getElementById('cnRenLuyen').value;
            const comment = document.getElementById('cnComment').value.trim();

            const st = cnData.find(s => s.id === studentId);
            if (st) {
                st.renLuyen = renLuyen;
                st.comment = comment;
                saveCNData();
                renderGVCN();
                showToast('Đã lưu đánh giá GVCN thành công!', 'success');
            }
        }

        function loadCNStudentDetails() {
            const studentId = document.getElementById('cnSelectStudent').value;
            const st = cnData.find(s => s.id === studentId);
            if (st) {
                document.getElementById('cnRenLuyen').value = st.renLuyen || 'Tốt';
                document.getElementById('cnComment').value = st.comment || '';
            }
        }

        function getStudentSubjectScore(fullname, className, subjectName) {
            const list = bmData[subjectName] || [];
            const st = list.find(s => s.fullname.toLowerCase() === fullname.toLowerCase() && s.className === className);
            if (!st) return null;
            return calcMidHK1(calcTxAvg(st.tx), st.gk);
        }

        function renderGVCN() {
            const className = document.getElementById('cnClassInput').value.trim().toUpperCase();
            document.getElementById('cnTableClassTitle').innerText = className;

            // Populate Student Select Dropdown for Homeroom Class
            const select = document.getElementById('cnSelectStudent');
            select.innerHTML = '';
            const classStudents = cnData.filter(s => s.className === className);

            classStudents.forEach(st => {
                const opt = document.createElement('option');
                opt.value = st.id;
                opt.textContent = st.fullname;
                select.appendChild(opt);
            });

            if (classStudents.length > 0) {
                loadCNStudentDetails();
            }

            renderGVCNTable();
            renderGVCNStats();
            renderGVCNWarnings();
            updateCNChart();
        }

        function renderGVCNTable() {
            const className = document.getElementById('cnClassInput').value.trim().toUpperCase();
            const searchVal = document.getElementById('cnSearchInput').value.toLowerCase().trim();
            const tbody = document.getElementById('cnTableBody');
            tbody.innerHTML = '';

            const list = cnData.filter(s => s.className === className && s.fullname.toLowerCase().includes(searchVal));
            document.getElementById('cnResultCount').innerText = `${list.length} Học sinh`;

            const subjects = ['Toán', 'Ngữ Văn', 'Tiếng Anh', 'Vật Lý'];

            list.forEach((s, idx) => {
                let totalScore = 0;
                let countSubjects = 0;

                let math = getStudentSubjectScore(s.fullname, s.className, 'Toán');
                let van = getStudentSubjectScore(s.fullname, s.className, 'Ngữ Văn');
                let eng = getStudentSubjectScore(s.fullname, s.className, 'Tiếng Anh');
                let phy = getStudentSubjectScore(s.fullname, s.className, 'Vật Lý');

                [math, van, eng, phy].forEach(score => {
                    if (score !== null) {
                        totalScore += score;
                        countSubjects++;
                    }
                });

                const avgOverall = countSubjects > 0 ? (totalScore / countSubjects) : 0;
                const academicRank = getRank(avgOverall);

                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition';
                tr.innerHTML = `
                    <td class="py-2.5 px-3 text-center text-slate-400 font-medium">${idx + 1}</td>
                    <td class="py-2.5 px-3 font-semibold text-slate-800">${s.fullname}</td>
                    <td class="py-2.5 px-3 text-center font-mono">${math !== null ? math.toFixed(1) : '-'}</td>
                    <td class="py-2.5 px-3 text-center font-mono">${van !== null ? van.toFixed(1) : '-'}</td>
                    <td class="py-2.5 px-3 text-center font-mono">${eng !== null ? eng.toFixed(1) : '-'}</td>
                    <td class="py-2.5 px-3 text-center font-mono">${phy !== null ? phy.toFixed(1) : '-'}</td>
                    <td class="py-2.5 px-3 text-center font-mono font-extrabold text-gvcn-800">${avgOverall > 0 ? avgOverall.toFixed(1) : '-'}</td>
                    <td class="py-2.5 px-3 text-center">
                        <span class="px-2 py-0.5 rounded-full text-xs font-bold bg-${academicRank.color}-100 text-${academicRank.color}-800 border border-${academicRank.color}-200">
                            ${academicRank.title}
                        </span>
                    </td>
                    <td class="py-2.5 px-3 text-center">
                        <span class="px-2 py-0.5 rounded-full text-xs font-bold ${s.renLuyen === 'Chưa đạt' ? 'bg-rose-100 text-rose-800' : 'bg-slate-100 text-slate-700'}">
                            ${s.renLuyen || 'Tốt'}
                        </span>
                    </td>
                    <td class="py-2.5 px-3 text-slate-600 text-xs italic">${s.comment || 'Chưa có nhận xét'}</td>
                    <td class="py-2.5 px-3 text-center no-print">
                        <button onclick="printStudentCard('${s.id}')" title="In phiếu điểm" class="px-2 py-1 bg-gvcn-100 text-gvcn-800 hover:bg-gvcn-200 rounded-lg text-xs font-bold transition">
                            <i class="fa-solid fa-print"></i> In Phiếu
                        </button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderGVCNStats() {
            const className = document.getElementById('cnClassInput').value.trim().toUpperCase();
            const list = cnData.filter(s => s.className === className);
            const total = list.length;
            let tot = 0, kha = 0, dat = 0, chuaDat = 0;

            list.forEach(s => {
                let math = getStudentSubjectScore(s.fullname, s.className, 'Toán');
                let van = getStudentSubjectScore(s.fullname, s.className, 'Ngữ Văn');
                let eng = getStudentSubjectScore(s.fullname, s.className, 'Tiếng Anh');
                let phy = getStudentSubjectScore(s.fullname, s.className, 'Vật Lý');

                let totalScore = 0, count = 0;
                [math, van, eng, phy].forEach(sc => { if (sc !== null) { totalScore += sc; count++; } });
                const avg = count > 0 ? totalScore / count : 0;
                const r = getRank(avg).title;

                if (r === 'Chưa đạt' || s.renLuyen === 'Chưa đạt') chuaDat++;
                else if (r === 'Tốt' && (s.renLuyen === 'Tốt' || !s.renLuyen)) tot++;
                else if (r === 'Khá' || s.renLuyen === 'Khá') kha++;
                else dat++;
            });

            const getPct = v => total > 0 ? ((v / total) * 100).toFixed(1) + '%' : '0%';

            document.getElementById('cnStatTot').innerText = tot;
            document.getElementById('cnStatTotPct').innerText = getPct(tot);
            document.getElementById('cnStatKha').innerText = kha;
            document.getElementById('cnStatKhaPct').innerText = getPct(kha);
            document.getElementById('cnStatDat').innerText = dat;
            document.getElementById('cnStatDatPct').innerText = getPct(dat);
            document.getElementById('cnStatChuaDat').innerText = chuaDat;
            document.getElementById('cnStatChuaDatPct').innerText = getPct(chuaDat);
        }

        function renderGVCNWarnings() {
            const className = document.getElementById('cnClassInput').value.trim().toUpperCase();
            const list = cnData.filter(s => s.className === className);
            const container = document.getElementById('cnWarningContainer');
            container.innerHTML = '';

            const weakList = list.filter(s => {
                let math = getStudentSubjectScore(s.fullname, s.className, 'Toán');
                let van = getStudentSubjectScore(s.fullname, s.className, 'Ngữ Văn');
                let eng = getStudentSubjectScore(s.fullname, s.className, 'Tiếng Anh');
                let phy = getStudentSubjectScore(s.fullname, s.className, 'Vật Lý');

                let totalScore = 0, count = 0;
                [math, van, eng, phy].forEach(sc => { if (sc !== null) { totalScore += sc; count++; } });
                const avg = count > 0 ? totalScore / count : 0;

                return avg < 5.0 || s.renLuyen === 'Chưa đạt';
            });

            document.getElementById('cnWarningCount').innerText = `${weakList.length} HS`;

            if (weakList.length === 0) {
                container.innerHTML = `<div class="text-xs text-emerald-600 bg-emerald-50 p-2 rounded-xl border border-emerald-100"><i class="fa-solid fa-check-circle"></i> Tình hình lớp ổn định! Không có học sinh nào bị cảnh báo đặc biệt.</div>`;
                return;
            }

            weakList.forEach(s => {
                const div = document.createElement('div');
                div.className = 'flex items-center justify-between bg-rose-50 border border-rose-100 p-2 rounded-xl text-xs';
                div.innerHTML = `
                    <span class="font-bold text-rose-800">${s.fullname}</span>
                    <span class="text-rose-700 italic">${s.renLuyen === 'Chưa đạt' ? 'Rèn luyện Chưa Đạt' : 'Kết quả Học lực yếu'}</span>
                `;
                container.appendChild(div);
            });
        }

        // Print Individual Student Report Card
        function printStudentCard(studentId) {
            const st = cnData.find(s => s.id === studentId);
            if (!st) return;

            let math = getStudentSubjectScore(st.fullname, st.className, 'Toán');
            let van = getStudentSubjectScore(st.fullname, st.className, 'Ngữ Văn');
            let eng = getStudentSubjectScore(st.fullname, st.className, 'Tiếng Anh');
            let phy = getStudentSubjectScore(st.fullname, st.className, 'Vật Lý');

            let total = 0, count = 0;
            [math, van, eng, phy].forEach(s => { if (s !== null) { total += s; count++; } });
            const avg = count > 0 ? (total / count).toFixed(1) : '-';

            const printModal = document.getElementById('printCardModal');
            printModal.innerHTML = `
                <div class="text-center border-b pb-4 mb-4">
                    <h2 class="text-xl font-bold uppercase">PHIẾU LIÊN LẠC GIỮA HỌC KỲ I</h2>
                    <p class="text-sm text-slate-600">Năm học 2026 - 2027</p>
                </div>
                <div class="grid grid-cols-2 gap-4 text-sm mb-6">
                    <div><b>Họ và tên:</b> ${st.fullname}</div>
                    <div><b>Lớp:</b> ${st.className}</div>
                    <div><b>Mức Rèn Luyện:</b> ${st.renLuyen || 'Tốt'}</div>
                    <div><b>ĐTB Chung Các Môn:</b> ${avg}</div>
                </div>
                <table class="w-full text-left border-collapse border border-slate-300 text-sm mb-6">
                    <thead>
                        <tr class="bg-slate-100">
                            <th class="border border-slate-300 p-2">Môn Học</th>
                            <th class="border border-slate-300 p-2 text-center">Điểm TB Giữa HKI</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr><td class="border border-slate-300 p-2">Toán</td><td class="border border-slate-300 p-2 text-center">${math !== null ? math.toFixed(1) : '-'}</td></tr>
                        <tr><td class="border border-slate-300 p-2">Ngữ Văn</td><td class="border border-slate-300 p-2 text-center">${van !== null ? van.toFixed(1) : '-'}</td></tr>
                        <tr><td class="border border-slate-300 p-2">Tiếng Anh</td><td class="border border-slate-300 p-2 text-center">${eng !== null ? eng.toFixed(1) : '-'}</td></tr>
                        <tr><td class="border border-slate-300 p-2">Vật Lý</td><td class="border border-slate-300 p-2 text-center">${phy !== null ? phy.toFixed(1) : '-'}</td></tr>
                    </tbody>
                </table>
                <div class="border p-3 rounded bg-slate-50 text-sm mb-8">
                    <b>Nhận xét của GVCN:</b> <p class="italic mt-1">${st.comment || 'Chưa có nhận xét'}</p>
                </div>
                <div class="flex justify-between text-center text-sm font-bold pt-4">
                    <div>Ý KIẾN PHỤ HUYNH</div>
                    <div>GIÁO VIÊN CHỦ NHIỆM</div>
                </div>
            `;

            window.print();
        }

        // =========================================================================
        // CHARTS LOGIC
        // =========================================================================
        function initCharts() {
            // BM Chart
            const ctxBM = document.getElementById('bmChart').getContext('2d');
            bmChartInstance = new Chart(ctxBM, {
                type: 'doughnut',
                data: {
                    labels: ['Tốt', 'Khá', 'Đạt', 'Chưa đạt'],
                    datasets: [{ data: [0,0,0,0], backgroundColor: ['#10b981', '#0284c7', '#f59e0b', '#f43f5e'] }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'right' } }, cutout: '65%' }
            });

            // CN Chart
            const ctxCN = document.getElementById('cnChart').getContext('2d');
            cnChartInstance = new Chart(ctxCN, {
                type: 'doughnut',
                data: {
                    labels: ['Tốt', 'Khá', 'Đạt', 'Chưa đạt'],
                    datasets: [{ data: [0,0,0,0], backgroundColor: ['#10b981', '#0284c7', '#f59e0b', '#f43f5e'] }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'right' } }, cutout: '65%' }
            });
        }

        function updateBMChart() {
            if (!bmChartInstance) return;
            const list = bmData[currentSubject] || [];
            let tot = 0, kha = 0, dat = 0, chuaDat = 0;
            list.forEach(s => {
                const r = getRank(calcMidHK1(calcTxAvg(s.tx), s.gk)).title;
                if (r === 'Tốt') tot++;
                else if (r === 'Khá') kha++;
                else if (r === 'Đạt') dat++;
                else chuaDat++;
            });
            bmChartInstance.data.datasets[0].data = [tot, kha, dat, chuaDat];
            bmChartInstance.update();
        }

        function updateCNChart() {
            if (!cnChartInstance) return;
            const className = document.getElementById('cnClassInput').value.trim().toUpperCase();
            const list = cnData.filter(s => s.className === className);
            let tot = 0, kha = 0, dat = 0, chuaDat = 0;

            list.forEach(s => {
                let math = getStudentSubjectScore(s.fullname, s.className, 'Toán');
                let van = getStudentSubjectScore(s.fullname, s.className, 'Ngữ Văn');
                let eng = getStudentSubjectScore(s.fullname, s.className, 'Tiếng Anh');
                let phy = getStudentSubjectScore(s.fullname, s.className, 'Vật Lý');

                let totalScore = 0, count = 0;
                [math, van, eng, phy].forEach(sc => { if (sc !== null) { totalScore += sc; count++; } });
                const avg = count > 0 ? totalScore / count : 0;
                const r = getRank(avg).title;

                if (r === 'Chưa đạt' || s.renLuyen === 'Chưa đạt') chuaDat++;
                else if (r === 'Tốt' && (s.renLuyen === 'Tốt' || !s.renLuyen)) tot++;
                else if (r === 'Khá' || s.renLuyen === 'Khá') kha++;
                else dat++;
            });

            cnChartInstance.data.datasets[0].data = [tot, kha, dat, chuaDat];
            cnChartInstance.update();
        }

        function renderApp() {
            if (currentRole === 'GVBM') {
                renderGVBM();
            } else {
                renderGVCN();
            }
        }

        // Export to CSV
        function exportToCSV() {
            let csvContent = "\uFEFF"; // UTF-8 BOM
            if (currentRole === 'GVBM') {
                csvContent += `BÁO CÁO ĐIỂM MÔN ${currentSubject.toUpperCase()}\n`;
                csvContent += "STT,Họ và Tên,Lớp,TX1,TX2,TX3,TX4,ĐTB TX,Giữa Kỳ,ĐTB Môn,Xếp Loại\n";
                const list = bmData[currentSubject] || [];
                list.forEach((s, idx) => {
                    const txAvg = calcTxAvg(s.tx);
                    const dtb = calcMidHK1(txAvg, s.gk);
                    const r = getRank(dtb).title;
                    csvContent += `${idx + 1},"${s.fullname}","${s.className}",${s.tx[0]||''},${s.tx[1]||''},${s.tx[2]||''},${s.tx[3]||''},${txAvg.toFixed(1)},${s.gk.toFixed(1)},${dtb.toFixed(1)},"${r}"\n`;
                });
            } else {
                const className = document.getElementById('cnClassInput').value.trim().toUpperCase();
                csvContent += `BÁO CÁO TỔNG HỢP LỚP CHỦ NHIỆM ${className}\n`;
                csvContent += "STT,Họ và Tên,Lớp,Toán,Ngữ Văn,Tiếng Anh,Vật Lý,ĐTB Chung,Rèn Luyện,Nhận Xét\n";
                const list = cnData.filter(s => s.className === className);
                list.forEach((s, idx) => {
                    let math = getStudentSubjectScore(s.fullname, s.className, 'Toán');
                    let van = getStudentSubjectScore(s.fullname, s.className, 'Ngữ Văn');
                    let eng = getStudentSubjectScore(s.fullname, s.className, 'Tiếng Anh');
                    let phy = getStudentSubjectScore(s.fullname, s.className, 'Vật Lý');
                    let tot = 0, c = 0;
                    [math, van, eng, phy].forEach(sc => { if (sc !== null) { tot += sc; c++; } });
                    const avg = c > 0 ? (tot / c).toFixed(1) : '-';
                    csvContent += `${idx + 1},"${s.fullname}","${s.className}",${math||'-'},${van||'-'},${eng||'-'},${phy||'-'},${avg},"${s.renLuyen||'Tốt'}","${s.comment||''}"\n`;
                });
            }

            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.setAttribute("href", url);
            link.setAttribute("download", `Bao_Cao_${currentRole}_${new Date().toISOString().slice(0,10)}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast('Đã xuất file dữ liệu thành công!', 'success');
        }

        // Reset All Application Data
        function confirmResetData() {
            if (confirm('XÁC NHẬN: Khôi phục lại dữ liệu mẫu ban đầu cho cả 2 vai trò GVBM và GVCN?')) {
                bmData = getSampleBMData();
                cnData = getSampleCNData();
                saveBMData();
                saveCNData();
                renderApp();
                showToast('Đã khôi phục dữ liệu mẫu!', 'info');
            }
        }

        // Toast Helper
        function showToast(msg, type = 'info') {
            const toast = document.getElementById('toast');
            const toastContent = document.getElementById('toastContent');
            document.getElementById('toastMessage').innerText = msg;

            if (type === 'success') toastContent.className = 'px-4 py-3 rounded-xl shadow-lg text-white text-sm font-medium flex items-center gap-2 bg-emerald-600';
            else if (type === 'warning') toastContent.className = 'px-4 py-3 rounded-xl shadow-lg text-white text-sm font-medium flex items-center gap-2 bg-amber-600';
            else toastContent.className = 'px-4 py-3 rounded-xl shadow-lg text-white text-sm font-medium flex items-center gap-2 bg-slate-800';

            toast.classList.remove('opacity-0', 'translate-y-20', 'pointer-events-none');
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-20', 'pointer-events-none');
            }, 3000);
        }
    </script>
</body>
</html># FILE-I-M-GHKI
