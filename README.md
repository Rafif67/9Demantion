```html
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nusantara Edu 9D</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- React dan ReactDOM (Development/Production CDN) -->
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  
  <!-- Babel Standalone untuk compile JSX langsung di browser -->
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <style>
    .custom-scrollbar::-webkit-scrollbar {
      width: 4px;
    }
    .custom-scrollbar::-webkit-scrollbar-track {
      background: transparent;
    }
    .custom-scrollbar::-webkit-scrollbar-thumb {
      background: rgba(255, 255, 255, 0.15);
      border-radius: 10px;
    }
    
    @keyframes ios-pop {
      0% { opacity: 0; transform: scale(0.96) translateY(10px); }
      100% { opacity: 1; transform: scale(1) translateY(0); }
    }
    @keyframes ios-slide {
      0% { opacity: 0; transform: translateX(20px); }
      100% { opacity: 1; transform: translateX(0); }
    }

    .animate-ios-pop {
      animation: ios-pop 0.5s cubic-bezier(0.32, 0.72, 0, 1) forwards;
    }
    .animate-ios-slide {
      animation: ios-slide 0.4s cubic-bezier(0.32, 0.72, 0, 1) forwards;
    }
  </style>
</head>
<body class="bg-slate-900 text-white overflow-x-hidden">

  <div id="root"></div>

  <!-- Script Aplikasi React ditulis menggunakan Babel -->
  <script type="text/babel">
    const { useState, useMemo, useEffect } = React;

    const studentList = [
      "Agung Ramaindra Syahputra", "Ainun Tiyas martiningsih", "Alessia Alzena Riangga",
      "Almaira Faiqha", "Alya Faliha", "Amirah Julveni Maulana", "Andi Aqil",
      "Aurora Felicia Angelie", "Bilqis Melani", "Chaya muja syatira",
      "Fadhil oktavian nabil", "fahmi firjatullah", "faiz Alfarizi",
      "farhan umaydillah radjiansyah", "fathir pradipta alfarizi", "fazril nizart",
      "januari", "jihaan kaltsum khairunnisa", "juan edra abiya",
      "Khanza Rista Bhanuwati", "Kristiana Berta", "Marselino Prasetyo wijaya",
      "M. Fadil Putra pratama", "M. Kafka adri Al-Maliq 67 sigmo", "M. Luthfi faturrahman",
      "Natalis Fedora Purba", "Rafif Rakha Arkana Dih 🥀", "Rayhan Qolbu",
      "Runika Alzariva", "Suci Pratiwi", "Syafa Damia Sakhi", "Syech Faris maulana",
      "Tegar Tri Pambudi", "Tersa Usela", "Tiara Husna Humairah", "Zhafira Mayarista"
    ];

    const quotes = [
      "Jadilah seperti karang di lautan, kuat menghadapi ombak ujian.",
      "Ilmu itu seluas samudra, mari kita selami bersama di kelas 9 D.",
      "Pelaut yang tangguh tidak lahir dari laut yang tenang.",
      "Setiap tetes keringat belajarmu akan menjadi mutiara di masa depan."
    ];

    // Data Pengguna & Hak Akses dengan PIN Unik (Rahasia)
    const users = [
      { id: 'op1', name: 'Admin Operator', role: 'operator', pin: '676767' },
      { id: 'guru1', name: 'Bapak/Ibu Guru', role: 'guru', pin: '112233' },
      { id: 'ketua1', name: 'Ketua Kelas 9D', role: 'ketua', pin: '889900' },
      { id: 'sekretaris1', name: 'Sekretaris Kelas', role: 'sekretaris', pin: '445566' },
      { id: 'bendahara1', name: 'Bendahara Kelas', role: 'bendahara', pin: '223344' },
      { id: 'warga1', name: 'Siswa / Warga Kelas', role: 'warga', pin: '000000' },
    ];

    // --- CUSTOM INLINE SVG ICONS (Untuk Menjamin Keandalan Tanpa Library Eksternal) ---
    const IconHome = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" /><polyline points="9 22 9 12 15 12 15 22" /></svg>
    );
    const IconCheckSquare = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round" className={className}><rect width="18" height="18" x="3" y="3" rx="2" /><path d="m9 12 2 2 4-4" /></svg>
    );
    const IconBookOpen = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z" /><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z" /></svg>
    );
    const IconWallet = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M21 12V7H5a2 2 0 0 1 0-4h14v4" /><path d="M3 5v14a2 2 0 0 0 2 2h16v-5" /><path d="M16 12h6v4h-6z" /></svg>
    );
    const IconUser = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M19 21v-2a4 4 0 0 0-4-4H9a4 4 0 0 0-4 4v2" /><circle cx="12" cy="7" r="4" /></svg>
    );
    const IconCalendar = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><rect width="18" height="18" x="3" y="4" rx="2" ry="2" /><line x1="16" x2="16" y1="2" y2="6" /><line x1="8" x2="8" y1="2" y2="6" /><line x1="3" x2="21" y1="10" y2="10" /></svg>
    );
    const IconBook = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1-2.5-2.5Z" /><path d="M6 6h10" /><path d="M6 10h10" /></svg>
    );
    const IconDroplet = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M12 22a7 7 0 0 0 7-7c0-4.3-7-13-7-13s-7 8.7-7 13a7 7 0 0 0 7 7z" /></svg>
    );
    const IconHistory = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8" /><path d="M3 3v5h5" /><path d="M12 7v5l4 2" /></svg>
    );
    const IconLogOut = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4" /><polyline points="16 17 21 12 16 7" /><line x1="21" x2="9" y1="12" y2="12" /></svg>
    );
    const IconLock = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><rect width="18" height="11" x="3" y="11" rx="2" ry="2" /><path d="M7 11V7a5 5 0 0 1 10 0v4" /></svg>
    );
    const IconEdit3 = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M12 20h9" /><path d="M16.5 3.5a2.12 2.12 0 0 1 3 3L7 19l-4 1 1-4Z" /></svg>
    );
    const IconCheck = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round" className={className}><polyline points="20 6 9 17 4 12" /></svg>
    );
    const IconShieldAlert = ({ size = 20, className = "" }) => (
      <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z" /><line x1="12" x2="12" y1="8" y2="12" /><line x1="12" x2="12.01" y1="16" y2="16" /></svg>
    );

    function App() {
      // Auth State
      const [currentUser, setCurrentUser] = useState(null);
      const [loginForm, setLoginForm] = useState({ role: 'operator', pin: '' });
      const [loginError, setLoginError] = useState('');

      // App State
      const [activeTab, setActiveTab] = useState('dashboard');
      const [attendance, setAttendance] = useState({});
      const [kas, setKas] = useState({});
      const [journal, setJournal] = useState({ date: '', subject: '', material: '', notes: '' });
      const [quoteIndex, setQuoteIndex] = useState(0);
      
      // History State
      const [historyLogs, setHistoryLogs] = useState([]);
      const [editingLogId, setEditingLogId] = useState(null);
      const [editLogText, setEditLogText] = useState('');

      // Notifikasi Sementara (Toast)
      const [toastMessage, setToastMessage] = useState('');

      useEffect(() => {
        setQuoteIndex(Math.floor(Math.random() * quotes.length));
      }, [activeTab]);

      // Toast Notifier
      const showToast = (message) => {
        setToastMessage(message);
        setTimeout(() => setToastMessage(''), 3000);
      };

      const addHistory = (action, details) => {
        const newLog = {
          id: Date.now().toString(),
          timestamp: new Date().toLocaleString('id-ID'),
          user: currentUser.name,
          role: currentUser.role,
          action: action,
          details: details,
          editedBy: null,
          editedAt: null
        };
        setHistoryLogs(prev => [newLog, ...prev]);
      };

      const stats = useMemo(() => {
        let hadir = 0, sakit = 0, izin = 0, alpa = 0;
        Object.values(attendance).forEach(status => {
          if (status === 'Hadir') hadir++;
          else if (status === 'Sakit') sakit++;
          else if (status === 'Izin') izin++;
          else if (status === 'Alpa') alpa++;
        });

        let totalKas = 0;
        Object.values(kas).forEach(val => {
          totalKas += val.amount || 0;
        });

        return { hadir, sakit, izin, alpa, totalKas };
      }, [attendance, kas]);

      const bgPositions = {
        'dashboard': 'translate3d(0, 0, 0) scale(1.1)',
        'absen': 'translate3d(-4%, -2%, 0) scale(1.1)',
        'jurnal': 'translate3d(-8%, -4%, 0) scale(1.1)',
        'kas': 'translate3d(-12%, -1%, 0) scale(1.1)',
        'history': 'translate3d(-16%, -3%, 0) scale(1.1)',
      };

      // --- CEK PERMISSION / HAK AKSES ---
      const canEditAbsen = () => ['operator', 'guru', 'ketua', 'sekretaris'].includes(currentUser?.role);
      const canEditJurnal = () => ['operator', 'guru', 'ketua', 'sekretaris'].includes(currentUser?.role);
      const canEditKas = () => ['operator', 'guru', 'ketua', 'bendahara'].includes(currentUser?.role);

      const handleLogin = (e) => {
        e.preventDefault();
        const user = users.find(u => u.role === loginForm.role && u.pin === loginForm.pin);
        if (user) {
          setCurrentUser(user);
          setActiveTab('dashboard');
          setLoginError('');
          if(user.role !== 'warga') addHistory('Login', `${user.name} berhasil login.`);
        } else {
          setLoginError('PIN salah! Silakan periksa kembali.');
        }
      };

      const handleLogout = () => {
        if(currentUser.role !== 'warga') addHistory('Logout', `${currentUser.name} keluar dari sistem.`);
        setCurrentUser(null);
        setLoginForm({ role: 'operator', pin: '' });
      };

      const handleKasChange = (name, value) => {
        if (!canEditKas()) {
          showToast('Akses Ditolak: Hanya Bendahara, Ketua, Guru & Operator yang bisa mengubah Kas.');
          return;
        }
        const cleanValue = value.replace(/[^0-9]/g, '');
        const amount = parseInt(cleanValue) || 0;
        setKas(prev => ({ ...prev, [name]: { amount } }));
      };

      const handleKasBlur = (name, amount) => {
        if (canEditKas()) {
          addHistory('Update Kas', `Mengubah kas ${name} menjadi Rp ${amount.toLocaleString('id-ID')}`);
        }
      };

      const handleAttendanceChange = (student, status) => {
        if (!canEditAbsen()) {
          showToast('Akses Ditolak: Hanya Sekretaris, Ketua, Guru & Operator yang bisa mengubah Absen.');
          return;
        }
        setAttendance(prev => ({ ...prev, [student]: status }));
        addHistory('Update Absen', `Status ${student} diubah menjadi ${status}`);
      };

      const handleSaveJournal = () => {
        if (!canEditJurnal()) {
          showToast('Akses Ditolak: Anda tidak memiliki izin untuk menyimpan Jurnal.');
          return;
        }
        if (!journal.date || !journal.subject) {
          showToast('Tanggal dan Mata Pelajaran wajib diisi!');
          return;
        }
        addHistory('Simpan Jurnal', `Jurnal: ${journal.subject} (${journal.date}) - ${journal.material}`);
        showToast('Jurnal berhasil disimpan!');
      };

      const handleSaveEditLog = (logId) => {
        setHistoryLogs(prev => prev.map(log => {
          if (log.id === logId) {
            return {
              ...log,
              details: editLogText,
              editedBy: currentUser.name,
              editedAt: new Date().toLocaleString('id-ID')
            };
          }
          return log;
        }));
        setEditingLogId(null);
      };

      const allTabs = [
        { id: 'dashboard', icon: IconHome, label: 'Beranda' },
        { id: 'absen', icon: IconCheckSquare, label: 'Absen' },
        { id: 'jurnal', icon: IconBookOpen, label: 'Jurnal' },
        { id: 'kas', icon: IconWallet, label: 'Kas' },
      ];
      
      const activeTabsList = currentUser && ['operator', 'guru'].includes(currentUser.role) 
        ? [...allTabs, { id: 'history', icon: IconHistory, label: 'Riwayat' }] 
        : allTabs;

      if (!currentUser) {
        return (
          <div className="relative min-h-screen bg-slate-900 flex items-center justify-center p-4">
            <div className="absolute inset-0 z-0 transform-gpu" style={{ backgroundImage: 'url("https://images.unsplash.com/photo-1582967788606-a171c1080cb0?q=80&w=2500&auto=format&fit=crop")', backgroundSize: 'cover', backgroundPosition: 'center' }}>
              <div className="absolute inset-0 bg-gradient-to-br from-blue-950/90 via-slate-900/80 to-teal-950/90" />
            </div>
            
            <div className="relative z-10 w-full max-w-sm bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] p-8 shadow-2xl animate-ios-pop">
              <div className="text-center mb-8">
                <h1 className="text-3xl font-bold text-white tracking-tight mb-2">Nusantara Edu</h1>
                <p className="text-white/70">Login Portal Kelas 9D</p>
              </div>

              <form onSubmit={handleLogin} className="space-y-5">
                <div className="space-y-1.5">
                  <label className="text-white/90 text-sm font-medium px-1">Pilih Peran</label>
                  <select 
                    value={loginForm.role}
                    onChange={(e) => setLoginForm({...loginForm, role: e.target.value, pin: ''})}
                    className="w-full bg-black/40 border border-white/10 rounded-[20px] px-5 py-4 text-white appearance-none focus:outline-none focus:ring-2 focus:ring-blue-400/50"
                  >
                    <option value="operator" className="bg-slate-800">🛠️ Operator Utama</option>
                    <option value="guru" className="bg-slate-800">👨‍🏫 Guru / Wali Kelas</option>
                    <option value="ketua" className="bg-slate-800">👑 Ketua Kelas</option>
                    <option value="sekretaris" className="bg-slate-800">📝 Sekretaris</option>
                    <option value="bendahara" className="bg-slate-800">💰 Bendahara</option>
                    <option value="warga" className="bg-slate-800">👀 Warga (Hanya Lihat)</option>
                  </select>
                </div>

                <div className="space-y-1.5">
                  <label className="text-white/90 text-sm font-medium px-1">PIN Keamanan</label>
                  <div className="relative">
                    <IconLock className="absolute left-4 top-1/2 -translate-y-1/2 text-white/50" />
                    <input 
                      type="password"
                      placeholder="Contoh: 123456"
                      value={loginForm.pin}
                      onChange={(e) => setLoginForm({...loginForm, pin: e.target.value})}
                      className="w-full bg-black/40 border border-white/10 rounded-[20px] pl-12 pr-5 py-4 text-white placeholder-white/30 focus:outline-none focus:ring-2 focus:ring-blue-400/50 text-center tracking-widest text-lg"
                    />
                  </div>
                </div>

                {loginError && <p className="text-red-400 text-sm text-center font-medium bg-red-900/20 py-2 rounded-lg border border-red-500/30">{loginError}</p>}

                <button type="submit" className="w-full mt-4 bg-blue-500 hover:bg-blue-400 text-white font-bold py-4 rounded-[20px] shadow-lg transform-gpu transition-all active:scale-95">
                  Masuk Sistem
                </button>
              </form>
            </div>
          </div>
        );
      }

      const renderContent = () => {
        switch (activeTab) {
          case 'dashboard':
            return (
              <div className="space-y-6 animate-ios-pop pb-10">
                <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] p-8 shadow-2xl relative overflow-hidden">
                  <div className="absolute top-0 right-0 bg-blue-500/80 text-white text-[10px] font-bold px-3 py-1 rounded-bl-xl rounded-tr-[32px] uppercase tracking-wider backdrop-blur-md">
                    {currentUser.role}
                  </div>
                  <h2 className="text-3xl font-semibold text-white mb-1 tracking-tight">Halo, {currentUser.name.split(' ')[0]}</h2>
                  <p className="text-white/80 font-medium text-sm mb-6">Ringkasan Kelas 9 D Hari Ini</p>
                  
                  <div className="bg-black/20 rounded-2xl p-5 border border-white/10">
                    <p className="text-white/90 italic text-sm text-center leading-relaxed font-serif">
                      "{quotes[quoteIndex]}"
                    </p>
                  </div>
                </div>

                <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
                  <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] p-6 shadow-xl">
                    <div className="flex items-center space-x-3 mb-4">
                      <div className="p-3 bg-blue-500/20 rounded-full text-blue-200"><IconUser size={24} /></div>
                      <h3 className="text-white font-medium text-lg">Kehadiran</h3>
                    </div>
                    <div className="grid grid-cols-2 gap-2 text-white">
                      <div className="bg-green-500/30 rounded-xl p-3 text-center border border-green-400/20">
                        <div className="text-2xl font-bold">{stats.hadir}</div>
                        <div className="text-[10px] font-medium uppercase tracking-wider text-green-100">Hadir</div>
                      </div>
                      <div className="bg-red-500/30 rounded-xl p-3 text-center border border-red-400/20">
                        <div className="text-2xl font-bold">{stats.alpa}</div>
                        <div className="text-[10px] font-medium uppercase tracking-wider text-red-100">Alpa</div>
                      </div>
                    </div>
                  </div>

                  <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] p-6 shadow-xl flex flex-col justify-center items-center text-center">
                    <div className="p-4 bg-emerald-500/20 rounded-full text-emerald-200 mb-3"><IconWallet size={32} /></div>
                    <div className="text-sm text-emerald-100 font-medium mb-1">Total Kas Kelas</div>
                    <div className="text-3xl font-bold text-white tracking-tight">
                      Rp {stats.totalKas.toLocaleString('id-ID')}
                    </div>
                  </div>
                </div>
              </div>
            );

          case 'absen':
            return (
              <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] overflow-hidden shadow-2xl flex flex-col h-[75vh] animate-ios-slide relative">
                {!canEditAbsen() && (
                  <div className="absolute top-0 left-0 w-full bg-yellow-500/90 text-yellow-950 text-xs text-center py-1.5 font-semibold z-20 shadow-md">
                    Mode Hanya Lihat (Read-Only)
                  </div>
                )}
                <div className={`p-6 border-b border-white/10 bg-white/5 ${!canEditAbsen() ? 'mt-4' : ''}`}>
                  <h2 className="text-2xl font-bold text-white tracking-tight">Presensi Kehadiran</h2>
                </div>
                <div className="overflow-y-auto flex-1 p-4 space-y-3 custom-scrollbar transform-gpu">
                  {studentList.map((student, idx) => (
                    <div key={idx} className="bg-black/20 rounded-[20px] p-4 flex flex-col sm:flex-row sm:items-center justify-between gap-4 border border-white/10">
                      <div className="flex items-center gap-3">
                        <div className="w-8 h-8 rounded-full bg-blue-500/20 text-blue-100 flex items-center justify-center font-bold text-sm">
                          {idx + 1}
                        </div>
                        <span className="text-white font-medium text-sm">{student}</span>
                      </div>
                      <div className={`flex bg-black/40 rounded-xl p-1 w-full sm:w-auto ${!canEditAbsen() ? 'opacity-80' : ''}`}>
                        {['Hadir', 'Sakit', 'Izin', 'Alpa'].map((status) => {
                          const isActive = attendance[student] === status;
                          return (
                            <button
                              key={status}
                              onClick={() => handleAttendanceChange(student, status)}
                              className={`flex-1 sm:px-4 py-2 text-xs font-medium rounded-lg transition-all duration-200 ${
                                isActive
                                  ? status === 'Hadir' ? 'bg-green-500 text-white' 
                                    : status === 'Sakit' ? 'bg-yellow-500 text-white'
                                    : status === 'Izin' ? 'bg-blue-500 text-white'
                                    : 'bg-red-500 text-white'
                                  : 'text-white/60 hover:bg-white/10'
                              } ${!canEditAbsen() ? 'cursor-not-allowed' : 'active:scale-95'}`}
                            >
                              {status}
                            </button>
                          )
                        })}
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            );

          case 'jurnal':
            return (
              <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] overflow-hidden shadow-2xl h-[75vh] flex flex-col animate-ios-slide relative">
                 {!canEditJurnal() && (
                  <div className="absolute top-0 left-0 w-full bg-yellow-500/90 text-yellow-950 text-xs text-center py-1.5 font-semibold z-20 shadow-md">
                    Mode Hanya Lihat (Read-Only)
                  </div>
                )}
                <div className={`p-6 sm:p-8 flex-1 flex flex-col ${!canEditJurnal() ? 'mt-4' : ''}`}>
                  <h2 className="text-2xl font-bold text-white tracking-tight mb-4">Jurnal Kelas</h2>
                  <div className="flex-1 space-y-4 overflow-y-auto custom-scrollbar transform-gpu relative">
                    
                    {!canEditJurnal() && <div className="absolute inset-0 z-10 cursor-not-allowed" onClick={() => showToast('Akses ditolak: Hanya Lihat')}></div>}
                    
                    <input 
                      type="date" value={journal.date} onChange={(e) => setJournal({...journal, date: e.target.value})}
                      disabled={!canEditJurnal()}
                      className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 ${!canEditJurnal() ? 'opacity-70' : ''}`}
                    />
                    <input 
                      type="text" placeholder="Mata Pelajaran" value={journal.subject} onChange={(e) => setJournal({...journal, subject: e.target.value})}
                      disabled={!canEditJurnal()}
                      className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 ${!canEditJurnal() ? 'opacity-70' : ''}`}
                    />
                    <input 
                      type="text" placeholder="Materi / Topik" value={journal.material} onChange={(e) => setJournal({...journal, material: e.target.value})}
                      disabled={!canEditJurnal()}
                      className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 ${!canEditJurnal() ? 'opacity-70' : ''}`}
                    />
                    <textarea 
                      rows={3} placeholder="Catatan Tambahan" value={journal.notes} onChange={(e) => setJournal({...journal, notes: e.target.value})}
                      disabled={!canEditJurnal()}
                      className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 resize-none ${!canEditJurnal() ? 'opacity-70' : ''}`}
                    />
                    <button 
                      onClick={handleSaveJournal} 
                      disabled={!canEditJurnal()}
                      className={`w-full font-bold py-4 rounded-[20px] shadow-lg transition-transform ${canEditJurnal() ? 'bg-blue-500 hover:bg-blue-400 text-white active:scale-[0.97]' : 'bg-gray-600/50 text-gray-400 cursor-not-allowed'}`}
                    >
                      {canEditJurnal() ? 'Simpan Jurnal' : 'Tidak Punya Akses Simpan'}
                    </button>
                  </div>
                </div>
              </div>
            );

          case 'kas':
            return (
              <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] overflow-hidden shadow-2xl flex flex-col h-[75vh] animate-ios-slide relative">
                {!canEditKas() && (
                  <div className="absolute top-0 left-0 w-full bg-yellow-500/90 text-yellow-950 text-xs text-center py-1.5 font-semibold z-20 shadow-md">
                    Mode Hanya Lihat (Read-Only)
                  </div>
                )}
                <div className={`p-6 border-b border-white/10 bg-white/5 ${!canEditKas() ? 'mt-4' : ''}`}>
                  <h2 className="text-2xl font-bold text-white tracking-tight">Buku Kas</h2>
                  <p className="text-emerald-300/80 text-xs mt-1">Otomatis: Rp 1.000 = 1 Centang</p>
                </div>
                
                <div className="overflow-y-auto flex-1 p-4 space-y-4 custom-scrollbar transform-gpu relative">
                   {!canEditKas() && <div className="absolute inset-0 z-10 cursor-not-allowed" onClick={() => showToast('Akses ditolak: Hanya Bendahara yang bisa ubah Kas')}></div>}
                  
                  {studentList.map((student, idx) => {
                    const amount = kas[student]?.amount || 0;
                    const checkedDaysCount = Math.floor(amount / 1000);
                    
                    return (
                      <div key={idx} className="bg-black/20 rounded-[20px] p-4 border border-white/10 flex flex-col gap-3 relative">
                        <div className="flex justify-between items-center z-10">
                          <span className="text-white font-medium text-sm">{student}</span>
                          <div className="relative">
                            <span className="absolute left-3 top-1/2 -translate-y-1/2 text-white/50 text-sm">Rp</span>
                            <input
                              type="text"
                              value={amount === 0 ? '' : amount.toLocaleString('id-ID')}
                              onChange={(e) => handleKasChange(student, e.target.value)}
                              onBlur={() => handleKasBlur(student, amount)}
                              placeholder="0"
                              disabled={!canEditKas()}
                              className={`w-28 bg-black/40 border border-white/10 rounded-xl py-2 pl-8 pr-3 text-right text-white font-semibold text-sm focus:ring-2 focus:ring-emerald-400/50 ${!canEditKas() ? 'opacity-80' : ''}`}
                            />
                          </div>
                        </div>
                        <div className="flex gap-1 flex-wrap z-10 p-2 bg-black/20 rounded-xl">
                          {[...Array(30)].map((_, dayIdx) => (
                            <div key={dayIdx} className={`w-4 h-4 rounded-sm flex items-center justify-center transition-all ${dayIdx < checkedDaysCount ? 'bg-emerald-500' : 'bg-white/5'}`}>
                              {dayIdx < checkedDaysCount && <IconDroplet size={10} className="text-emerald-50" />}
                            </div>
                          ))}
                        </div>
                      </div>
                    );
                  })}
                </div>
              </div>
            );

          case 'history':
            return (
              <div className="bg-white/10 backdrop-blur-2xl border border-white/20 rounded-[32px] overflow-hidden shadow-2xl flex flex-col h-[75vh] animate-ios-slide">
                <div className="p-6 border-b border-white/10 bg-white/5">
                  <h2 className="text-2xl font-bold text-white tracking-tight">Riwayat Sistem</h2>
                  <p className="text-white/70 text-sm mt-1">Audit log aktivitas kelas</p>
                </div>
                
                <div className="overflow-y-auto flex-1 p-4 space-y-3 custom-scrollbar transform-gpu">
                  {historyLogs.length === 0 ? (
                    <p className="text-center text-white/50 py-10">Belum ada aktivitas tercatat.</p>
                  ) : (
                    historyLogs.map((log) => (
                      <div key={log.id} className="bg-black/30 rounded-[20px] p-4 border border-white/10 relative">
                        <div className="flex justify-between items-start mb-2">
                          <span className="text-xs font-bold text-blue-300 uppercase tracking-wider">{log.action}</span>
                          <span className="text-[10px] text-white/50">{log.timestamp}</span>
                        </div>
                        
                        {editingLogId === log.id ? (
                          <div className="flex gap-2 mt-2">
                            <input 
                              type="text" 
                              value={editLogText} 
                              onChange={(e) => setEditLogText(e.target.value)}
                              className="flex-1 bg-black/40 border border-blue-400/50 rounded-lg px-3 py-1.5 text-sm text-white focus:outline-none"
                              autoFocus
                            />
                            <button onClick={() => handleSaveEditLog(log.id)} className="bg-green-500 text-white p-1.5 rounded-lg flex items-center justify-center">
                              <IconCheck size={16} />
                            </button>
                          </div>
                        ) : (
                          <p className="text-white text-sm mb-3">{log.details}</p>
                        )}

                        <div className="flex justify-between items-center mt-2 pt-2 border-t border-white/5">
                          <div className="flex items-center gap-2">
                            <div className="w-5 h-5 rounded-full bg-white/20 flex items-center justify-center text-[10px] text-white">
                              <IconUser size={10} />
                            </div>
                            <span className="text-xs text-white/70">{log.user} ({log.role})</span>
                          </div>
                          
                          {!editingLogId && (
                            <button 
                              onClick={() => { setEditingLogId(log.id); setEditLogText(log.details); }}
                              className="text-white/40 hover:text-white transition-colors flex items-center gap-1 text-xs"
                            >
                              <IconEdit3 size={12} /> Edit
                            </button>
                          )}
                        </div>
                        
                        {log.editedBy && (
                          <div className="mt-2 bg-yellow-500/10 border border-yellow-500/20 rounded-md p-2 text-[10px] text-yellow-200/80 flex flex-col">
                            <span>⚠️ Riwayat ini telah diedit</span>
                            <span>Oleh: {log.editedBy} pada {log.editedAt}</span>
                          </div>
                        )}
                      </div>
                    ))
                  )}
                </div>
              </div>
            );

          default:
            return null;
        }
      };

      return (
        <div className="relative min-h-screen bg-slate-900 overflow-hidden font-sans selection:bg-blue-500/30">
          
          {/* Toast Notification Area */}
          {toastMessage && (
            <div className="fixed top-6 left-1/2 -translate-x-1/2 z-[100] animate-ios-slide">
              <div className="bg-black/80 backdrop-blur-md border border-white/20 text-white px-5 py-3 rounded-full flex items-center gap-2 shadow-2xl text-sm font-medium">
                <IconShieldAlert size={16} className="text-yellow-400" />
                {toastMessage}
              </div>
            </div>
          )}

          {/* Background Animasi Hardware Accelerated (Smooth 60fps/120fps) */}
          <div 
            className="absolute inset-0 z-0 transform-gpu will-change-transform transition-transform duration-[1200ms] ease-[cubic-bezier(0.32,0.72,0,1)]"
            style={{
              backgroundImage: 'url("https://images.unsplash.com/photo-1582967788606-a171c1080cb0?q=80&w=2500&auto=format&fit=crop")',
              backgroundSize: 'cover',
              backgroundPosition: 'center',
              backfaceVisibility: 'hidden',
              transform: bgPositions[activeTab] || bgPositions['dashboard']
            }}
          >
            <div className="absolute inset-0 bg-gradient-to-br from-blue-950/80 via-slate-900/70 to-teal-950/80 pointer-events-none" />
          </div>

          <div className="relative z-10 max-w-md mx-auto pt-10 px-4 pb-32 min-h-screen flex flex-col">
            {/* Header */}
            <header className="mb-6 px-2 flex justify-between items-center">
              <div>
                <h1 className="text-white/95 font-bold text-2xl tracking-tight">Nusantara Edu</h1>
                <p className="text-blue-200/80 text-sm font-medium">Kelas 9D • {currentUser.name}</p>
              </div>
              <button 
                onClick={handleLogout}
                className="w-11 h-11 rounded-full bg-red-500/20 hover:bg-red-500/40 backdrop-blur-2xl flex items-center justify-center border border-red-500/30 shadow-lg transition-all"
                title="Keluar"
              >
                <IconLogOut size={18} className="text-red-200" />
              </button>
            </header>

            {renderContent()}
          </div>

          {/* Dock Navigasi Adaptif Ala iOS 18 */}
          <div className="fixed bottom-6 left-1/2 -translate-x-1/2 z-50 w-[92%] max-w-sm">
            <div className="bg-black/40 backdrop-blur-3xl border border-white/10 rounded-[32px] p-2 flex justify-center gap-1 sm:gap-2 shadow-[0_20px_40px_-10px_rgba(0,0,0,0.6)] relative overflow-hidden">
              <div className="absolute top-0 left-0 w-full h-[1px] bg-gradient-to-r from-transparent via-white/30 to-transparent pointer-events-none" />
              
              {activeTabsList.map((tab) => {
                const IconComponent = tab.icon;
                const isActive = activeTab === tab.id;
                return (
                  <button
                    key={tab.id}
                    onClick={() => setActiveTab(tab.id)}
                    className={`relative flex flex-col items-center justify-center w-[58px] sm:w-[64px] h-[64px] rounded-[24px] transform-gpu transition-all duration-300 ease-[cubic-bezier(0.32,0.72,0,1)] z-10 ${
                      isActive ? 'bg-white/20 scale-100 shadow-inner' : 'hover:bg-white/10 active:scale-95'
                    }`}
                  >
                    <IconComponent size={isActive ? 22 : 20} className={`transition-all duration-300 ${isActive ? 'text-white' : 'text-white/50'}`} />
                    <span className={`text-[9px] font-semibold transition-all duration-300 absolute bottom-1.5 ${isActive ? 'text-white opacity-100 translate-y-0' : 'text-white/0 opacity-0 translate-y-2'}`}>
                      {tab.label}
                    </span>
                  </button>
                );
              })}
            </div>
          </div>
        </div>
      );
    }

    // Render App ke DOM
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>

```
