```react
import React, { useState, useMemo, useEffect } from 'react';
import { Home, CheckSquare, BookOpen, Wallet, User, Calendar, Book, Droplets, History, LogOut, Lock, Edit3, Check, ShieldAlert } from 'lucide-react';

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

export default function App() {
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

  // Notifikasi Sementara
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
    'dashboard': 'translate-x-0 translate-y-0 scale-110',
    'absen': '-translate-x-[4%] -translate-y-[2%] scale-110',
    'jurnal': '-translate-x-[8%] -translate-y-[4%] scale-110',
    'kas': '-translate-x-[12%] -translate-y-[1%] scale-110',
    'history': '-translate-x-[16%] -translate-y-[3%] scale-110',
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
    if (!journal.date || !journal.subject) return alert('Tanggal dan Mata Pelajaran wajib diisi!');
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
    { id: 'dashboard', icon: Home, label: 'Beranda' },
    { id: 'absen', icon: CheckSquare, label: 'Absen' },
    { id: 'jurnal', icon: BookOpen, label: 'Jurnal' },
    { id: 'kas', icon: Wallet, label: 'Kas' },
  ];
  
  const activeTabsList = currentUser && ['operator', 'guru'].includes(currentUser.role) 
    ? [...allTabs, { id: 'history', icon: History, label: 'Riwayat' }] 
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
                className="w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white appearance-none focus:outline-none focus:ring-2 focus:ring-blue-400/50"
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
                <Lock className="absolute left-4 top-1/2 -translate-y-1/2 text-white/50" size={20} />
                <input 
                  type="password"
                  placeholder="Contoh: 123456" // Sekarang placeholder diganti teks statis yang aman
                  value={loginForm.pin}
                  onChange={(e) => setLoginForm({...loginForm, pin: e.target.value})}
                  className="w-full bg-black/20 border border-white/10 rounded-[20px] pl-12 pr-5 py-4 text-white placeholder-white/30 focus:outline-none focus:ring-2 focus:ring-blue-400/50 text-center tracking-widest text-lg"
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
                  <div className="p-3 bg-blue-500/20 rounded-full text-blue-200"><User size={24} /></div>
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
                <div className="p-4 bg-emerald-500/20 rounded-full text-emerald-200 mb-3"><Wallet size={32} /></div>
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
                
                {/* Invisible Overlay to block inputs visually if Read-Only without disabling scroll */}
                {!canEditJurnal() && <div className="absolute inset-0 z-10 cursor-not-allowed" onClick={() => showToast('Akses ditolak: Hanya Lihat')}></div>}
                
                <input 
                  type="date" value={journal.date} onChange={(e) => setJournal({...journal, date: e.target.value})}
                  readOnly={!canEditJurnal()}
                  className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 ${!canEditJurnal() ? 'opacity-70' : ''}`}
                />
                <input 
                  type="text" placeholder="Mata Pelajaran" value={journal.subject} onChange={(e) => setJournal({...journal, subject: e.target.value})}
                  readOnly={!canEditJurnal()}
                  className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 ${!canEditJurnal() ? 'opacity-70' : ''}`}
                />
                <input 
                  type="text" placeholder="Materi / Topik" value={journal.material} onChange={(e) => setJournal({...journal, material: e.target.value})}
                  readOnly={!canEditJurnal()}
                  className={`w-full bg-black/20 border border-white/10 rounded-[20px] px-5 py-4 text-white focus:ring-2 focus:ring-blue-400/50 ${!canEditJurnal() ? 'opacity-70' : ''}`}
                />
                <textarea 
                  rows={3} placeholder="Catatan Tambahan" value={journal.notes} onChange={(e) => setJournal({...journal, notes: e.target.value})}
                  readOnly={!canEditJurnal()}
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
            <div className={`p-6 border-b border-white/10 bg-white/5 ${!canEditKas
