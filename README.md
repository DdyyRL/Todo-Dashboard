<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard To-Do List</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #4361ee;
            --secondary: #3f37c9;
            --success: #4cc9f0;
            --danger: #f72585;
            --warning: #f8961e;
            --info: #4895ef;
            --light: #f8f9fa;
            --dark: #212529;
            --gray: #6c757d;
            --border: #dee2e6;
            --card-bg: #ffffff;
            --sidebar-bg: #ffffff;
            --header-bg: #ffffff;
            --text-primary: #212529;
            --text-secondary: #6c757d;
            --shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: #f5f7fb;
            color: var(--text-primary);
            display: flex;
            min-height: 100vh;
        }

        /* Sidebar */
        .sidebar {
            width: 250px;
            background: var(--sidebar-bg);
            padding: 20px;
            box-shadow: var(--shadow);
            position: fixed;
            height: 100vh;
            overflow-y: auto;
            z-index: 1000;
        }

        .sidebar-header {
            padding-bottom: 20px;
            border-bottom: 1px solid var(--border);
            margin-bottom: 20px;
        }

        .sidebar-header h2 {
            color: var(--primary);
            font-size: 1.5rem;
        }

        .nav-menu {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .nav-item {
            display: flex;
            align-items: center;
            padding: 12px 15px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
            color: var(--text-secondary);
            text-decoration: none;
        }

        .nav-item:hover, .nav-item.active {
            background-color: rgba(67, 97, 238, 0.1);
            color: var(--primary);
        }

        .nav-item i {
            margin-right: 10px;
            font-size: 1.2rem;
        }

        /* Main Content */
        .main-content {
            flex: 1;
            margin-left: 250px;
            padding: 20px;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px 0;
            border-bottom: 1px solid var(--border);
            margin-bottom: 20px;
        }

        .header h1 {
            color: var(--text-primary);
        }

        .view-selector {
            display: flex;
            gap: 10px;
        }

        .view-btn {
            padding: 8px 16px;
            border: 1px solid var(--border);
            background: white;
            border-radius: 6px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .view-btn.active {
            background: var(--primary);
            color: white;
            border-color: var(--primary);
        }

        /* Cards */
        .cards-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .card {
            background: var(--card-bg);
            border-radius: 10px;
            padding: 20px;
            box-shadow: var(--shadow);
        }

        .card-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .card-title {
            font-size: 1rem;
            color: var(--text-secondary);
        }

        .card-icon {
            width: 40px;
            height: 40px;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
        }

        .primary-bg { background: rgba(67, 97, 238, 0.1); color: var(--primary); }
        .success-bg { background: rgba(76, 201, 240, 0.1); color: var(--success); }
        .warning-bg { background: rgba(248, 150, 30, 0.1); color: var(--warning); }
        .danger-bg { background: rgba(247, 37, 133, 0.1); color: var(--danger); }

        .card-value {
            font-size: 1.8rem;
            font-weight: bold;
            margin-bottom: 5px;
        }

        .card-desc {
            font-size: 0.9rem;
            color: var(--text-secondary);
        }

        /* Activity Lists */
        .section-title {
            font-size: 1.3rem;
            margin: 20px 0 15px;
            color: var(--text-primary);
        }

        .activity-list {
            display: grid;
            gap: 15px;
        }

        .activity-item {
            background: var(--card-bg);
            border-radius: 10px;
            padding: 15px;
            box-shadow: var(--shadow);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .activity-info {
            flex: 1;
        }

        .activity-title {
            font-weight: 600;
            margin-bottom: 5px;
        }

        .activity-meta {
            display: flex;
            gap: 15px;
            font-size: 0.9rem;
            color: var(--text-secondary);
        }

        .activity-status {
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 500;
        }

        .status-pending { background: rgba(248, 150, 30, 0.1); color: var(--warning); }
        .status-progress { background: rgba(67, 97, 238, 0.1); color: var(--primary); }
        .status-completed { background: rgba(76, 201, 240, 0.1); color: var(--success); }

        .activity-actions {
            display: flex;
            gap: 10px;
        }

        .action-btn {
            background: none;
            border: none;
            cursor: pointer;
            color: var(--text-secondary);
            font-size: 1.1rem;
        }

        /* Forms */
        .form-container {
            background: var(--card-bg);
            border-radius: 10px;
            padding: 25px;
            box-shadow: var(--shadow);
            margin-bottom: 20px;
        }

        .form-title {
            font-size: 1.3rem;
            margin-bottom: 20px;
            color: var(--text-primary);
        }

        .form-group {
            margin-bottom: 15px;
        }

        .form-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: 500;
        }

        .form-control {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--border);
            border-radius: 6px;
            font-size: 1rem;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        .btn {
            padding: 10px 20px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 1rem;
            transition: all 0.3s;
        }

        .btn-primary {
            background: var(--primary);
            color: white;
        }

        .btn-primary:hover {
            background: var(--secondary);
        }

        .btn-outline {
            background: transparent;
            border: 1px solid var(--border);
            color: var(--text-primary);
        }

        /* Tabs */
        .tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            border-bottom: 1px solid var(--border);
        }

        .tab {
            padding: 10px 20px;
            cursor: pointer;
            border-bottom: 3px solid transparent;
        }

        .tab.active {
            border-bottom: 3px solid var(--primary);
            color: var(--primary);
            font-weight: 500;
        }

        /* Calendar */
        .calendar-container {
            background: var(--card-bg);
            border-radius: 10px;
            padding: 20px;
            box-shadow: var(--shadow);
            margin-top: 20px;
        }

        .calendar-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .calendar-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
        }

        .calendar-day-header {
            text-align: center;
            padding: 10px;
            font-weight: 600;
            color: var(--text-secondary);
        }

        .calendar-day {
            min-height: 80px;
            border: 1px solid var(--border);
            border-radius: 5px;
            padding: 5px;
            position: relative;
        }

        .calendar-date {
            font-size: 0.9rem;
            margin-bottom: 5px;
        }

        .calendar-event {
            font-size: 0.8rem;
            padding: 2px 4px;
            border-radius: 3px;
            margin-bottom: 2px;
            color: white;
            cursor: pointer;
        }

        .event-work { background: var(--primary); }
        .event-college { background: var(--success); }
        .event-assignment { background: var(--warning); }

        /* KPI Styles */
        .kpi-container {
            background: var(--card-bg);
            border-radius: 10px;
            padding: 25px;
            box-shadow: var(--shadow);
            margin-bottom: 20px;
        }

        .kpi-metrics {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 25px;
        }

        .kpi-metric {
            text-align: center;
            padding: 15px;
        }

        .kpi-number {
            font-size: 2rem;
            font-weight: bold;
            color: var(--primary);
        }

        .kpi-label {
            color: var(--text-secondary);
            margin-top: 5px;
        }

        .chart-container {
            margin: 20px 0;
            height: 300px;
        }

        .performance-badge {
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.9rem;
            font-weight: 500;
        }

        .excellent { background: rgba(40, 167, 69, 0.1); color: #28a745; }
        .good { background: rgba(255, 193, 7, 0.1); color: #ffc107; }
        .fair { background: rgba(255, 133, 27, 0.1); color: #ff851b; }
        .needs-improvement { background: rgba(220, 53, 69, 0.1); color: #dc3545; }

        /* Footer */
        .footer {
            text-align: center;
            padding: 20px;
            color: var(--text-secondary);
            font-size: 0.9rem;
            border-top: 1px solid var(--border);
            margin-top: 30px;
        }

        /* Responsive */
        @media (max-width: 768px) {
            .sidebar {
                width: 70px;
                padding: 10px;
            }
            
            .sidebar .nav-text {
                display: none;
            }
            
            .main-content {
                margin-left: 70px;
            }
            
            .form-row {
                grid-template-columns: 1fr;
            }
            
            .cards-container {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <!-- Sidebar -->
    <div class="sidebar">
        <div class="sidebar-header">
            <h2><i class="fas fa-tasks"></i> To-Do Dashboard</h2>
        </div>
        <div class="nav-menu">
            <div class="nav-item active" data-page="dashboard">
                <i class="fas fa-home"></i>
                <span class="nav-text">Dashboard</span>
            </div>
            <div class="nav-item" data-page="work">
                <i class="fas fa-briefcase"></i>
                <span class="nav-text">Pekerjaan</span>
            </div>
            <div class="nav-item" data-page="college">
                <i class="fas fa-graduation-cap"></i>
                <span class="nav-text">Kuliah</span>
            </div>
            <div class="nav-item" data-page="kpi">
                <i class="fas fa-bullseye"></i>
                <span class="nav-text">KPI</span>
            </div>
        </div>
    </div>

    <!-- Main Content -->
    <div class="main-content">
        <div class="header">
            <h1 id="page-title">Dashboard</h1>
            <div class="view-selector">
                <button class="view-btn active" data-view="week">Minggu Ini</button>
                <button class="view-btn" data-view="month">Bulan Ini</button>
            </div>
        </div>

        <!-- Dashboard Page -->
        <div id="dashboard-page" class="page">
            <!-- Overview Cards -->
            <div class="cards-container">
                <div class="card">
                    <div class="card-header">
                        <div class="card-title">Total Aktivitas</div>
                        <div class="card-icon primary-bg">
                            <i class="fas fa-tasks"></i>
                        </div>
                    </div>
                    <div class="card-value" id="total-activities">0</div>
                    <div class="card-desc">Semua tugas dan pekerjaan</div>
                </div>
                <div class="card">
                    <div class="card-header">
                        <div class="card-title">Selesai</div>
                        <div class="card-icon success-bg">
                            <i class="fas fa-check-circle"></i>
                        </div>
                    </div>
                    <div class="card-value" id="completed-tasks">0</div>
                    <div class="card-desc">Tugas yang sudah selesai</div>
                </div>
                <div class="card">
                    <div class="card-header">
                        <div class="card-title">Dalam Proses</div>
                        <div class="card-icon warning-bg">
                            <i class="fas fa-sync-alt"></i>
                        </div>
                    </div>
                    <div class="card-value" id="in-progress">0</div>
                    <div class="card-desc">Tugas sedang dikerjakan</div>
                </div>
                <div class="card">
                    <div class="card-header">
                        <div class="card-title">KPI Score</div>
                        <div class="card-icon danger-bg">
                            <i class="fas fa-bullseye"></i>
                        </div>
                    </div>
                    <div class="card-value" id="kpi-score">0%</div>
                    <div class="card-desc">Tingkat performa keseluruhan</div>
                </div>
            </div>

            <!-- KPI Summary -->
            <div class="kpi-container">
                <h3 class="section-title">Ringkasan KPI</h3>
                <div class="kpi-metrics">
                    <div class="kpi-metric">
                        <div class="kpi-number" id="kpi-completion-rate">0%</div>
                        <div class="kpi-label">Tingkat Penyelesaian</div>
                    </div>
                    <div class="kpi-metric">
                        <div class="kpi-number" id="kpi-performance-score">0%</div>
                        <div class="kpi-label">Skor Performa</div>
                    </div>
                    <div class="kpi-metric">
                        <div class="kpi-number" id="kpi-overdue">0</div>
                        <div class="kpi-label">Tugas Terlambat</div>
                    </div>
                </div>
            </div>

            <!-- This Week Activities -->
            <h3 class="section-title">Aktivitas Minggu Ini</h3>
            <div class="activity-list" id="weekly-activities">
                <!-- Activities will be populated here -->
            </div>
        </div>

        <!-- Work Page -->
        <div id="work-page" class="page" style="display: none;">
            <div class="form-container">
                <h3 class="form-title">Tambah Pekerjaan Baru</h3>
                <form id="work-form">
                    <div class="form-group">
                        <label>Nama Pekerjaan</label>
                        <input type="text" class="form-control" id="work-name" required>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Tanggal Mulai</label>
                            <input type="date" class="form-control" id="work-start-date" required>
                        </div>
                        <div class="form-group">
                            <label>Tanggal Selesai</label>
                            <input type="date" class="form-control" id="work-end-date" required>
                        </div>
                    </div>
                    <div class="form-group">
                        <label>Status</label>
                        <select class="form-control" id="work-status" required>
                            <option value="pending">Belum Mulai</option>
                            <option value="progress">Sedang Berlangsung</option>
                            <option value="completed">Selesai</option>
                        </select>
                    </div>
                    <button type="submit" class="btn btn-primary">Simpan Pekerjaan</button>
                </form>
            </div>

            <div class="calendar-container">
                <div class="calendar-header">
                    <h3>Kalender Pekerjaan</h3>
                </div>
                <div class="calendar-grid" id="work-calendar">
                    <!-- Calendar will be populated here -->
                </div>
            </div>

            <h3 class="section-title">Daftar Pekerjaan</h3>
            <div class="activity-list" id="work-list">
                <!-- Work items will be populated here -->
            </div>
        </div>

        <!-- College Page -->
        <div id="college-page" class="page" style="display: none;">
            <div class="tabs">
                <div class="tab active" data-tab="assignments">Tugas</div>
                <div class="tab" data-tab="schedule">Jadwal Kuliah</div>
            </div>

            <!-- Assignments Tab -->
            <div id="assignments-tab">
                <div class="form-container">
                    <h3 class="form-title">Tambah Tugas Baru</h3>
                    <form id="assignment-form">
                        <div class="form-group">
                            <label>Nama Tugas</label>
                            <input type="text" class="form-control" id="assignment-name" required>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Tanggal Tenggat</label>
                                <input type="date" class="form-control" id="assignment-due-date" required>
                            </div>
                            <div class="form-group">
                                <label>Mata Kuliah</label>
                                <select class="form-control" id="assignment-course">
                                    <option value="">Pilih Mata Kuliah</option>
                                    <option value="">Tidak ada mata kuliah</option>
                                </select>
                            </div>
                        </div>
                        <div class="form-group">
                            <label>Kategori</label>
                            <select class="form-control" id="assignment-category" required>
                                <option value="individual">Individu</option>
                                <option value="group">Kelompok</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label>Status</label>
                            <select class="form-control" id="assignment-status" required>
                                <option value="pending">Belum Mulai</option>
                                <option value="progress">Sedang Berlangsung</option>
                                <option value="completed">Selesai</option>
                            </select>
                        </div>
                        <button type="submit" class="btn btn-primary">Simpan Tugas</button>
                    </form>
                </div>

                <h3 class="section-title">Daftar Tugas</h3>
                <div class="activity-list" id="assignment-list">
                    <!-- Assignments will be populated here -->
                </div>
            </div>

            <!-- Schedule Tab -->
            <div id="schedule-tab" style="display: none;">
                <div class="form-container">
                    <h3 class="form-title">Tambah Jadwal Kuliah</h3>
                    <form id="schedule-form">
                        <div class="form-group">
                            <label>Nama Mata Kuliah</label>
                            <input type="text" class="form-control" id="schedule-course" required>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Hari</label>
                                <select class="form-control" id="schedule-day" required>
                                    <option value="Senin">Senin</option>
                                    <option value="Selasa">Selasa</option>
                                    <option value="Rabu">Rabu</option>
                                    <option value="Kamis">Kamis</option>
                                    <option value="Jumat">Jumat</option>
                                    <option value="Sabtu">Sabtu</option>
                                    <option value="Minggu">Minggu</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label>Waktu</label>
                                <input type="time" class="form-control" id="schedule-time" required>
                            </div>
                        </div>
                        <div class="form-group">
                            <label>Dosen</label>
                            <input type="text" class="form-control" id="schedule-instructor">
                        </div>
                        <div class="form-group">
                            <label>Ruangan</label>
                            <input type="text" class="form-control" id="schedule-room">
                        </div>
                        <button type="submit" class="btn btn-primary">Simpan Jadwal</button>
                    </form>
                </div>

                <h3 class="section-title">Jadwal Kuliah Mingguan</h3>
                <div class="activity-list" id="schedule-list">
                    <!-- Schedule will be populated here -->
                </div>
            </div>
        </div>

        <!-- KPI Page -->
        <div id="kpi-page" class="page" style="display: none;">
            <div class="tabs">
                <div class="tab active" data-tab="kpi-work">KPI Pekerjaan</div>
                <div class="tab" data-tab="kpi-college">KPI Kuliah</div>
            </div>

            <!-- KPI Work Tab -->
            <div id="kpi-work-tab">
                <div class="kpi-container">
                    <div class="kpi-metrics">
                        <div class="kpi-metric">
                            <div class="kpi-number" id="work-total">0</div>
                            <div class="kpi-label">Total Pekerjaan</div>
                        </div>
                        <div class="kpi-metric">
                            <div class="kpi-number" id="work-completion">0%</div>
                            <div class="kpi-label">Tingkat Penyelesaian</div>
                        </div>
                        <div class="kpi-metric">
                            <div class="kpi-number" id="work-performance">0%</div>
                            <div class="kpi-label">Skor Performa</div>
                        </div>
                        <div class="kpi-metric">
                            <div class="kpi-number" id="work-overdue">0</div>
                            <div class="kpi-label">Pekerjaan Terlambat</div>
                        </div>
                    </div>
                    
                    <div class="chart-container">
                        <canvas id="work-status-chart"></canvas>
                    </div>
                    
                    <div class="chart-container">
                        <canvas id="work-trend-chart"></canvas>
                    </div>
                </div>
            </div>

            <!-- KPI College Tab -->
            <div id="kpi-college-tab" style="display: none;">
                <div class="kpi-container">
                    <div class="kpi-metrics">
                        <div class="kpi-metric">
                            <div class="kpi-number" id="college-total">0</div>
                            <div class="kpi-label">Total Tugas</div>
                        </div>
                        <div class="kpi-metric">
                            <div class="kpi-number" id="college-completion">0%</div>
                            <div class="kpi-label">Tingkat Penyelesaian</div>
                        </div>
                        <div class="kpi-metric">
                            <div class="kpi-number" id="college-performance">0%</div>
                            <div class="kpi-label">Skor Performa</div>
                        </div>
                        <div class="kpi-metric">
                            <div class="kpi-number" id="college-overdue">0</div>
                            <div class="kpi-label">Tugas Terlambat</div>
                        </div>
                    </div>
                    
                    <div class="chart-container">
                        <canvas id="college-status-chart"></canvas>
                    </div>
                    
                    <div class="chart-container">
                        <canvas id="college-category-chart"></canvas>
                    </div>
                    
                    <div class="chart-container">
                        <canvas id="college-trend-chart"></canvas>
                    </div>
                </div>
            </div>
        </div>

        <!-- Footer -->
        <div class="footer">
            <p>Created by Dedi Ruslaidi L</p>
        </div>
    </div>

    <script>
        // Data storage
        let workItems = JSON.parse(localStorage.getItem('workItems')) || [];
        let assignments = JSON.parse(localStorage.getItem('assignments')) || [];
        let classSchedules = JSON.parse(localStorage.getItem('classSchedules')) || [];

        // DOM Elements
        const navItems = document.querySelectorAll('.nav-item');
        const pages = document.querySelectorAll('.page');
        const viewButtons = document.querySelectorAll('.view-btn');
        const tabs = document.querySelectorAll('.tab');
        
        // Navigation
        navItems.forEach(item => {
            item.addEventListener('click', () => {
                const page = item.getAttribute('data-page');
                
                navItems.forEach(nav => nav.classList.remove('active'));
                item.classList.add('active');
                
                pages.forEach(p => p.style.display = 'none');
                document.getElementById(`${page}-page`).style.display = 'block';
                document.getElementById('page-title').textContent = 
                    page === 'dashboard' ? 'Dashboard' :
                    page === 'work' ? 'Pekerjaan' :
                    page === 'college' ? 'Kuliah' : 'KPI';
                
                if(page === 'college') {
                    updateCourseOptions();
                }
                renderPage(page);
            });
        });

        // View selector
        viewButtons.forEach(btn => {
            btn.addEventListener('click', () => {
                viewButtons.forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                renderDashboard();
            });
        });

        // Tab switching
        tabs.forEach(tab => {
            tab.addEventListener('click', () => {
                const tabName = tab.getAttribute('data-tab');
                const tabContainer = tab.closest('.tabs').nextElementSibling;
                
                tabs.forEach(t => t.classList.remove('active'));
                tab.classList.add('active');
                
                if(tabName.includes('kpi-')) {
                    document.querySelectorAll('#kpi-work-tab, #kpi-college-tab').forEach(t => t.style.display = 'none');
                    document.getElementById(`${tabName}-tab`).style.display = 'block';
                } else {
                    document.querySelectorAll('#assignments-tab, #schedule-tab').forEach(t => t.style.display = 'none');
                    document.getElementById(`${tabName}-tab`).style.display = 'block';
                }
            });
        });

        // Forms
        document.getElementById('work-form').addEventListener('submit', addWork);
        document.getElementById('assignment-form').addEventListener('submit', addAssignment);
        document.getElementById('schedule-form').addEventListener('submit', addSchedule);

        // Initialize
        renderDashboard();

        function saveToStorage() {
            localStorage.setItem('workItems', JSON.stringify(workItems));
            localStorage.setItem('assignments', JSON.stringify(assignments));
            localStorage.setItem('classSchedules', JSON.stringify(classSchedules));
        }

        function addWork(e) {
            e.preventDefault();
            const work = {
                id: Date.now(),
                name: document.getElementById('work-name').value,
                startDate: document.getElementById('work-start-date').value,
                endDate: document.getElementById('work-end-date').value,
                status: document.getElementById('work-status').value,
                createdAt: new Date().toISOString()
            };
            workItems.push(work);
            saveToStorage();
            document.getElementById('work-form').reset();
            renderPage('work');
        }

        function addAssignment(e) {
            e.preventDefault();
            const selectedCourse = document.getElementById('assignment-course').value;
            const courseData = classSchedules.find(c => c.id == selectedCourse);
            
            const assignment = {
                id: Date.now(),
                name: document.getElementById('assignment-name').value,
                dueDate: document.getElementById('assignment-due-date').value,
                category: document.getElementById('assignment-category').value,
                status: document.getElementById('assignment-status').value,
                courseId: selectedCourse || null,
                courseName: courseData ? courseData.course : null,
                createdAt: new Date().toISOString()
            };
            assignments.push(assignment);
            saveToStorage();
            document.getElementById('assignment-form').reset();
            renderPage('college');
        }

        function addSchedule(e) {
            e.preventDefault();
            const schedule = {
                id: Date.now(),
                course: document.getElementById('schedule-course').value,
                day: document.getElementById('schedule-day').value,
                time: document.getElementById('schedule-time').value,
                instructor: document.getElementById('schedule-instructor').value,
                room: document.getElementById('schedule-room').value,
                createdAt: new Date().toISOString()
            };
            classSchedules.push(schedule);
            saveToStorage();
            document.getElementById('schedule-form').reset();
            updateCourseOptions();
            renderPage('college');
        }

        function updateCourseOptions() {
            const select = document.getElementById('assignment-course');
            select.innerHTML = '<option value="">Pilih Mata Kuliah</option>';
            
            if(classSchedules.length === 0) {
                select.innerHTML += '<option value="">Tidak ada mata kuliah (tambahkan jadwal dulu)</option>';
            } else {
                classSchedules.forEach(course => {
                    const option = document.createElement('option');
                    option.value = course.id;
                    option.textContent = course.course;
                    select.appendChild(option);
                });
            }
        }

        function deleteItem(type, id) {
            if(type === 'work') {
                workItems = workItems.filter(item => item.id != id);
            } else if(type === 'assignment') {
                assignments = assignments.filter(item => item.id != id);
            } else if(type === 'schedule') {
                classSchedules = classSchedules.filter(item => item.id != id);
            }
            saveToStorage();
            renderPage(document.querySelector('.nav-item.active').getAttribute('data-page'));
        }

        function editItem(type, id) {
            if(type === 'work') {
                const item = workItems.find(w => w.id == id);
                document.getElementById('work-name').value = item.name;
                document.getElementById('work-start-date').value = item.startDate;
                document.getElementById('work-end-date').value = item.endDate;
                document.getElementById('work-status').value = item.status;
                
                // Remove item and update form to edit mode
                workItems = workItems.filter(w => w.id != id);
                document.getElementById('work-form').onsubmit = function(e) {
                    e.preventDefault();
                    item.name = document.getElementById('work-name').value;
                    item.startDate = document.getElementById('work-start-date').value;
                    item.endDate = document.getElementById('work-end-date').value;
                    item.status = document.getElementById('work-status').value;
                    workItems.push(item);
                    saveToStorage();
                    document.getElementById('work-form').reset();
                    this.onsubmit = addWork;
                    renderPage('work');
                };
            } else if(type === 'assignment') {
                const item = assignments.find(a => a.id == id);
                document.getElementById('assignment-name').value = item.name;
                document.getElementById('assignment-due-date').value = item.dueDate;
                document.getElementById('assignment-category').value = item.category;
                document.getElementById('assignment-status').value = item.status;
                if(item.courseId) {
                    document.getElementById('assignment-course').value = item.courseId;
                }
                
                assignments = assignments.filter(a => a.id != id);
                document.getElementById('assignment-form').onsubmit = function(e) {
                    e.preventDefault();
                    const selectedCourse = document.getElementById('assignment-course').value;
                    const courseData = classSchedules.find(c => c.id == selectedCourse);
                    
                    item.name = document.getElementById('assignment-name').value;
                    item.dueDate = document.getElementById('assignment-due-date').value;
                    item.category = document.getElementById('assignment-category').value;
                    item.status = document.getElementById('assignment-status').value;
                    item.courseId = selectedCourse || null;
                    item.courseName = courseData ? courseData.course : null;
                    
                    assignments.push(item);
                    saveToStorage();
                    document.getElementById('assignment-form').reset();
                    this.onsubmit = addAssignment;
                    renderPage('college');
                };
            }
        }

        function renderPage(page) {
            switch(page) {
                case 'dashboard':
                    renderDashboard();
                    break;
                case 'work':
                    renderWork();
                    renderCalendar('work');
                    break;
                case 'college':
                    renderCollege();
                    break;
                case 'kpi':
                    renderKPI();
                    break;
            }
        }

        function renderDashboard() {
            const view = document.querySelector('.view-btn.active').getAttribute('data-view');
            const now = new Date();
            const startOfWeek = new Date(now.setDate(now.getDate() - now.getDay()));
            const startOfMonth = new Date(new Date().getFullYear(), new Date().getMonth(), 1);
            
            const allItems = [
                ...workItems.map(w => ({...w, type: 'work'})),
                ...assignments.map(a => ({...a, type: 'assignment'}))
            ];
            
            const filteredItems = allItems.filter(item => {
                const itemDate = new Date(item.endDate || item.dueDate);
                if(view === 'week') {
                    return itemDate >= startOfWeek && itemDate <= new Date(startOfWeek.getTime() + 7*24*60*60*1000);
                } else {
                    return itemDate >= startOfMonth && itemDate <= new Date(startOfMonth.getFullYear(), startOfMonth.getMonth() + 1, 0);
                }
            });
            
            document.getElementById('total-activities').textContent = allItems.length;
            document.getElementById('completed-tasks').textContent = allItems.filter(i => i.status === 'completed').length;
            document.getElementById('in-progress').textContent = allItems.filter(i => i.status === 'progress').length;
            
            // Calculate KPI score
            const completed = allItems.filter(i => i.status === 'completed').length;
            const overdue = allItems.filter(i => {
                const dueDate = new Date(i.endDate || i.dueDate);
                return dueDate < new Date() && i.status !== 'completed';
            }).length;
            
            const kpiScore = allItems.length > 0 ? Math.max(0, Math.round(((completed - overdue) / allItems.length) * 100)) : 0;
            document.getElementById('kpi-score').textContent = `${kpiScore}%`;
            
            // Update KPI summary
            const completionRate = allItems.length > 0 ? Math.round((completed / allItems.length) * 100) : 0;
            document.getElementById('kpi-completion-rate').textContent = `${completionRate}%`;
            document.getElementById('kpi-performance-score').textContent = `${kpiScore}%`;
            document.getElementById('kpi-overdue').textContent = overdue;
            
            // Render weekly activities
            const weeklyList = document.getElementById('weekly-activities');
            weeklyList.innerHTML = '';
            
            filteredItems.forEach(item => {
                const div = document.createElement('div');
                div.className = 'activity-item';
                div.innerHTML = `
                    <div class="activity-info">
                        <div class="activity-title">${item.name}</div>
                        <div class="activity-meta">
                            <span><i class="far fa-calendar"></i> ${formatDate(item.endDate || item.dueDate)}</span>
                            ${item.courseName ? `<span><i class="fas fa-graduation-cap"></i> ${item.courseName}</span>` : ''}
                            <span class="activity-status status-${item.status}">
                                ${getStatusText(item.status)}
                            </span>
                        </div>
                    </div>
                    <div class="activity-actions">
                        <button class="action-btn" onclick="editItem('${item.type}', ${item.id})"><i class="fas fa-edit"></i></button>
                        <button class="action-btn" onclick="deleteItem('${item.type}', ${item.id})"><i class="fas fa-trash"></i></button>
                    </div>
                `;
                weeklyList.appendChild(div);
            });
        }

        function renderWork() {
            const workList = document.getElementById('work-list');
            workList.innerHTML = '';
            
            workItems.forEach(work => {
                const div = document.createElement('div');
                div.className = 'activity-item';
                div.innerHTML = `
                    <div class="activity-info">
                        <div class="activity-title">${work.name}</div>
                        <div class="activity-meta">
                            <span><i class="far fa-calendar"></i> ${formatDate(work.startDate)} - ${formatDate(work.endDate)}</span>
                            <span class="activity-status status-${work.status}">
                                ${getStatusText(work.status)}
                            </span>
                        </div>
                    </div>
                    <div class="activity-actions">
                        <button class="action-btn" onclick="editItem('work', ${work.id})"><i class="fas fa-edit"></i></button>
                        <button class="action-btn" onclick="deleteItem('work', ${work.id})"><i class="fas fa-trash"></i></button>
                    </div>
                `;
                workList.appendChild(div);
            });
        }

        function renderCollege() {
            renderAssignments();
            renderSchedule();
        }

        function renderAssignments() {
            const assignmentList = document.getElementById('assignment-list');
            assignmentList.innerHTML = '';
            
            assignments.forEach(assignment => {
                const div = document.createElement('div');
                div.className = 'activity-item';
                div.innerHTML = `
                    <div class="activity-info">
                        <div class="activity-title">${assignment.name}</div>
                        <div class="activity-meta">
                            <span><i class="far fa-calendar"></i> Tenggat: ${formatDate(assignment.dueDate)}</span>
                            ${assignment.courseName ? `<span><i class="fas fa-graduation-cap"></i> ${assignment.courseName}</span>` : ''}
                            <span><i class="fas fa-tag"></i> ${assignment.category === 'individual' ? 'Individu' : 'Kelompok'}</span>
                            <span class="activity-status status-${assignment.status}">
                                ${getStatusText(assignment.status)}
                            </span>
                        </div>
                    </div>
                    <div class="activity-actions">
                        <button class="action-btn" onclick="editItem('assignment', ${assignment.id})"><i class="fas fa-edit"></i></button>
                        <button class="action-btn" onclick="deleteItem('assignment', ${assignment.id})"><i class="fas fa-trash"></i></button>
                    </div>
                `;
                assignmentList.appendChild(div);
            });
        }

        function renderSchedule() {
            const scheduleList = document.getElementById('schedule-list');
            scheduleList.innerHTML = '';
            
            classSchedules.forEach(schedule => {
                const div = document.createElement('div');
                div.className = 'activity-item';
                div.innerHTML = `
                    <div class="activity-info">
                        <div class="activity-title">${schedule.course}</div>
                        <div class="activity-meta">
                            <span><i class="far fa-clock"></i> ${schedule.day}, ${schedule.time}</span>
                            <span><i class="fas fa-chalkboard-teacher"></i> ${schedule.instructor || 'Belum ditentukan'}</span>
                            <span><i class="fas fa-door-open"></i> ${schedule.room || 'Belum ditentukan'}</span>
                        </div>
                    </div>
                    <div class="activity-actions">
                        <button class="action-btn" onclick="deleteItem('schedule', ${schedule.id})"><i class="fas fa-trash"></i></button>
                    </div>
                `;
                scheduleList.appendChild(div);
            });
        }

        function renderCalendar(type) {
            const calendar = document.getElementById('work-calendar');
            calendar.innerHTML = '';
            
            // Add day headers
            ['Min', 'Sen', 'Sel', 'Rab', 'Kam', 'Jum', 'Sab'].forEach(day => {
                const header = document.createElement('div');
                header.className = 'calendar-day-header';
                header.textContent = day;
                calendar.appendChild(header);
            });
            
            // Generate calendar days
            const today = new Date();
            const year = today.getFullYear();
            const month = today.getMonth();
            const firstDay = new Date(year, month, 1).getDay();
            const daysInMonth = new Date(year, month + 1, 0).getDate();
            
            // Empty days before first day
            for(let i = 0; i < firstDay; i++) {
                const emptyDay = document.createElement('div');
                emptyDay.className = 'calendar-day';
                calendar.appendChild(emptyDay);
            }
            
            // Days of the month
            for(let day = 1; day <= daysInMonth; day++) {
                const date = new Date(year, month, day);
                const dayDiv = document.createElement('div');
                dayDiv.className = 'calendar-day';
                
                const dateDiv = document.createElement('div');
                dateDiv.className = 'calendar-date';
                dateDiv.textContent = day;
                dayDiv.appendChild(dateDiv);
                
                // Add events for this day
                const dayEvents = workItems.filter(w => {
                    const workDate = new Date(w.startDate);
                    return workDate.getDate() === day && workDate.getMonth() === month && workDate.getFullYear() === year;
                });
                
                dayEvents.forEach(event => {
                    const eventDiv = document.createElement('div');
                    eventDiv.className = 'calendar-event event-work';
                    eventDiv.textContent = event.name.substring(0, 15) + '...';
                    dayDiv.appendChild(eventDiv);
                });
                
                calendar.appendChild(dayDiv);
            }
        }

        function renderKPI() {
            renderKPIWork();
            renderKPICollege();
        }

        function renderKPIWork() {
            const completed = workItems.filter(w => w.status === 'completed').length;
            const total = workItems.length;
            const overdue = workItems.filter(w => {
                const endDate = new Date(w.endDate);
                return endDate < new Date() && w.status !== 'completed';
            }).length;
            
            const completionRate = total > 0 ? Math.round((completed / total) * 100) : 0;
            const performanceScore = total > 0 ? Math.max(0, Math.round(((completed - overdue) / total) * 100)) : 0;
            
            document.getElementById('work-total').textContent = total;
            document.getElementById('work-completion').textContent = `${completionRate}%`;
            document.getElementById('work-performance').textContent = `${performanceScore}%`;
            document.getElementById('work-overdue').textContent = overdue;
            
            // Update status chart
            const statusCtx = document.getElementById('work-status-chart').getContext('2d');
            new Chart(statusCtx, {
                type: 'pie',
                data: {
                    labels: ['Selesai', 'Dalam Proses', 'Belum Mulai'],
                    datasets: [{
                        data: [
                            workItems.filter(w => w.status === 'completed').length,
                            workItems.filter(w => w.status === 'progress').length,
                            workItems.filter(w => w.status === 'pending').length
                        ],
                        backgroundColor: ['#28a745', '#ffc107', '#dc3545']
                    }]
                }
            });
            
            // Update trend chart
            const trendCtx = document.getElementById('work-trend-chart').getContext('2d');
            new Chart(trendCtx, {
                type: 'bar',
                data: {
                    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
                    datasets: [{
                        label: 'Pekerjaan Bulanan',
                        data: [5, 8, 6, 9, 7, 10],
                        backgroundColor: 'rgba(67, 97, 238, 0.6)'
                    }]
                }
            });
        }

        function renderKPICollege() {
            const completed = assignments.filter(a => a.status === 'completed').length;
            const total = assignments.length;
            const overdue = assignments.filter(a => {
                const dueDate = new Date(a.dueDate);
                return dueDate < new Date() && a.status !== 'completed';
            }).length;
            
            const completionRate = total > 0 ? Math.round((completed / total) * 100) : 0;
            const performanceScore = total > 0 ? Math.max(0, Math.round(((completed - overdue) / total) * 100)) : 0;
            
            document.getElementById('college-total').textContent = total;
            document.getElementById('college-completion').textContent = `${completionRate}%`;
            document.getElementById('college-performance').textContent = `${performanceScore}%`;
            document.getElementById('college-overdue').textContent = overdue;
            
            // Update status chart
            const statusCtx = document.getElementById('college-status-chart').getContext('2d');
            new Chart(statusCtx, {
                type: 'pie',
                data: {
                    labels: ['Selesai', 'Dalam Proses', 'Belum Mulai'],
                    datasets: [{
                        data: [
                            assignments.filter(a => a.status === 'completed').length,
                            assignments.filter(a => a.status === 'progress').length,
                            assignments.filter(a => a.status === 'pending').length
                        ],
                        backgroundColor: ['#28a745', '#ffc107', '#dc3545']
                    }]
                }
            });
            
            // Update category chart
            const categoryCtx = document.getElementById('college-category-chart').getContext('2d');
            new Chart(categoryCtx, {
                type: 'pie',
                data: {
                    labels: ['Individu', 'Kelompok'],
                    datasets: [{
                        data: [
                            assignments.filter(a => a.category === 'individual').length,
                            assignments.filter(a => a.category === 'group').length
                        ],
                        backgroundColor: ['#4361ee', '#f72585']
                    }]
                }
            });
            
            // Update trend chart
            const trendCtx = document.getElementById('college-trend-chart').getContext('2d');
            new Chart(trendCtx, {
                type: 'bar',
                data: {
                    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
                    datasets: [{
                        label: 'Tugas Bulanan',
                        data: [12, 8, 15, 10, 14, 9],
                        backgroundColor: 'rgba(76, 201, 240, 0.6)'
                    }]
                }
            });
        }

        function getStatusText(status) {
            switch(status) {
                case 'pending': return 'Belum Mulai';
                case 'progress': return 'Sedang Berlangsung';
                case 'completed': return 'Selesai';
                default: return status;
            }
        }

        function formatDate(dateString) {
            const options = { year: 'numeric', month: 'long', day: 'numeric' };
            return new Date(dateString).toLocaleDateString('id-ID', options);
        }
    </script>
</body>
</html>
