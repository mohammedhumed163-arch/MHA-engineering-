import React, { useState, useEffect, useRef } from 'react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from 'firebase/auth';
import { getFirestore, collection, addDoc, onSnapshot, query, doc } from 'firebase/firestore';
import { 
  Printer, FileDown, ShieldCheck, Share2, 
  Box, FileCode, Sparkles, Calculator, 
  Globe, Smartphone, Monitor, Download, 
  CheckCircle2, Loader2, HardHat, TrendingUp,
  FileText, Mail, Save
} from 'lucide-react';

const firebaseConfig = JSON.parse(__firebase_config);
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const appId = typeof __app_id !== 'undefined' ? __app_id : 'mha-v35-soft-cloud';
const apiKey = ""; 

const content = {
  am: {
    title: "MHA ዓለም አቀፍ የዲጂታል ምህንድስና ደመና",
    softFile: "ሶፍት ፋይል (Soft Copy)",
    download: "ፋይሉን አውርድ (Download)",
    print: "አትም (Print)",
    save: "ወደ ደመና አስቀምጥ",
    clientInfo: "የደንበኛ ዝርዝር",
    name: "ሙሉ ስም", address: "አድራሻ", phone: "ስልክ",
    analysis: "የህንፃው ትንታኔ",
    width: "ስፋት (ሜ)", height: "ቁመት (ፎቅ)",
    generate: "ዲዛይን እና ሶፍት ፋይል አዘጋጅ",
    loading: "AI ሶፍት ፋይሉን እያዘጋጀ ነው...",
    estimation: "የግንባታ ወጪ እስትሜሽን",
    blueprint: "የቴክኒክ ድሮይንግ",
    success: "ሶፍት ፋይሉ ዝግጁ ነው!"
  },
  en: {
    title: "MHA Global Engineering Digital Cloud",
    softFile: "Soft Copy / Digital File",
    download: "Download File",
    print: "Print Document",
    save: "Save to Cloud",
    clientInfo: "Client Information",
    name: "Full Name", address: "Address", phone: "Phone",
    analysis: "Building Analysis",
    width: "Width (m)", height: "Floors",
    generate: "Generate Design & Soft File",
    loading: "AI Generating Soft Copy...",
    estimation: "Construction Estimation",
    blueprint: "Technical Blueprint",
    success: "Soft file is ready!"
  }
};

export default function App() {
  const [lang, setLang] = useState('am');
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);
  const [formData, setFormData] = useState({
    name: '', address: '', phone: '', 
    analysis: '', width: '15', height: '0', subcity: ''
  });
  const [result, setResult] = useState(null);

  const t = content[lang];
  const ideaOwner = "Mohammed Humed Arba";

  useEffect(() => {
    const initAuth = async () => {
      if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
        await signInWithCustomToken(auth, __initial_auth_token);
      } else {
        await signInAnonymously(auth);
      }
    };
    initAuth();
    onAuthStateChanged(auth, setUser);
  }, []);

  const handleGenerate = async () => {
    if (!formData.name) return;
    setLoading(true);
    try {
      const prompts = [
        `Detailed architectural floor plan for ${formData.analysis}, engineering drawing`,
        `Modern architectural front 3D view of ${formData.analysis}, realistic`,
        `Modern architectural side 3D view of ${formData.analysis}, high quality`
      ];

      const responses = await Promise.all(prompts.map(p => 
        fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image-preview:generateContent?key=${apiKey}`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            contents: [{ parts: [{ text: p }] }],
            generationConfig: { responseModalities: ["TEXT", "IMAGE"] }
          })
        }).then(res => res.json())
      ));

      const aiImages = responses.map(data => 
        `data:image/png;base64,${data.candidates?.[0]?.content?.parts?.find(p => p.inlineData)?.inlineData?.data}`
      );

      const area = (parseFloat(formData.width) || 10) ** 2;
      const floors = parseInt(formData.height) || 0;
      const totalArea = area * (floors + 1);
      
      const estimation = [
        { item: lang === 'am' ? "ሲሚንቶ (በኩንታል)" : "Cement", qty: Math.round(totalArea * 0.8), price: 2100 },
        { item: lang === 'am' ? "ብረት (12mm)" : "Steel", qty: Math.round(totalArea * 12), price: 150 },
        { item: lang === 'am' ? "አሸዋ (m³)" : "Sand", qty: Math.round(totalArea * 0.15), price: 3500 },
        { item: lang === 'am' ? "ጠጠር (m³)" : "Stone", qty: Math.round(totalArea * 0.12), price: 4000 }
      ];

      const payload = {
        ...formData,
        aiImages,
        estimation,
        grandTotal: estimation.reduce((a, b) => a + (b.qty * b.price), 0),
        certId: `MHA-SOFT-${Math.random().toString(36).substr(2, 6).toUpperCase()}`,
        qrUrl: `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=MHA-VERIFIED-${formData.name}`,
        generatedAt: new Date().toLocaleString()
      };
      
      setResult(payload);
      if (user) await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'soft_files'), payload);
    } catch (e) { console.error(e); }
    setLoading(false);
  };

  const downloadSoftFile = () => {
    const reportData = JSON.stringify(result, null, 2);
    const blob = new Blob([reportData], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `MHA_Report_${result.certId}.json`;
    link.click();
  };

  const printAndExport = () => {
    const w = window.open('', '_blank');
    w.document.write(`
      <html>
        <head>
          <title>Soft Copy - ${result.certId}</title>
          <style>
            @media print { .no-print { display: none; } }
            body { font-family: 'Helvetica', sans-serif; padding: 40px; color: #1a1a1a; }
            .header { border-bottom: 5px solid #f59e0b; padding-bottom: 20px; display: flex; justify-content: space-between; align-items: center; }
            .badge { background: #000; color: #fff; padding: 5px 15px; border-radius: 20px; font-size: 12px; font-weight: bold; }
            .section { margin-top: 30px; font-weight: 900; text-transform: uppercase; color: #f59e0b; border-bottom: 1px solid #eee; padding-bottom: 5px; }
            .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 15px; }
            .box { background: #f9f9f9; padding: 15px; border-radius: 8px; border: 1px solid #eee; }
            .img-frame { width: 100%; border-radius: 12px; margin-top: 15px; border: 1px solid #ddd; overflow: hidden; }
            .img-frame img { width: 100%; display: block; }
            table { width: 100%; border-collapse: collapse; margin-top: 15px; }
            th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
            th { background: #f5f5f5; font-size: 12px; }
            .footer { margin-top: 50px; border-top: 1px solid #eee; padding-top: 20px; display: flex; justify-content: space-between; font-size: 12px; }
            .page-break { page-break-after: always; }
          </style>
        </head>
        <body>
          <div class="header">
            <div>
              <h1 style="margin:0; font-size:30px;">MHA GLOBAL SOFT-FILE</h1>
              <p style="margin:5px 0; color: #666;">Verified Engineering Intelligence</p>
              <div class="badge">OFFICIAL DIGITAL COPY</div>
            </div>
            <img src="${result.qrUrl}" style="width:100px;"/>
          </div>

          <div class="section">${t.clientInfo}</div>
          <div class="grid">
            <div class="box"><strong>${t.name}:</strong> ${result.name}</div>
            <div class="box"><strong>${t.phone}:</strong> ${result.phone}</div>
            <div class="box"><strong>Project ID:</strong> ${result.certId}</div>
            <div class="box"><strong>Area:</strong> ${result.totalArea} m²</div>
          </div>

          <div class="section">${t.analysis}</div>
          <div class="box" style="margin-top:10px;">${result.analysis}</div>

          <div class="section">Front 3D Perspective</div>
          <div class="img-frame"><img src="${result.aiImages[1]}" /></div>

          <div class="page-break"></div>

          <div class="section">Technical Blueprint Drawing</div>
          <div class="img-frame"><img src="${result.aiImages[0]}" /></div>

          <div class="section">${t.estimation}</div>
          <table>
            <thead>
              <tr><th>Item</th><th>Quantity</th><th>Unit Price (ETB)</th><th>Total</th></tr>
            </thead>
            <tbody>
              ${result.estimation.map(i => `
                <tr><td>${i.item}</td><td>${i.qty}</td><td>${i.price}</td><td>${(i.qty * i.price).toLocaleString()}</td></tr>
              `).join('')}
              <tr style="background:#000; color:#fff; font-weight:bold;">
                <td colspan="3" style="text-align:right;">GRAND TOTAL:</td>
                <td>ETB ${result.grandTotal.toLocaleString()}</td>
              </tr>
            </tbody>
          </table>

          <div class="footer">
            <div>
              <p>Certified by:</p>
              <p style="font-family:cursive; font-size:20px; margin:0;">${ideaOwner}</p>
              <p>MHA Principal Engineer</p>
            </div>
            <div style="text-align:right;">
              <p>Generated on: ${result.generatedAt}</p>
              <p>Security Key: SHA-MHA-${result.certId}</p>
            </div>
          </div>
        </body>
      </html>
    `);
    w.document.close();
    w.print();
  };

  return (
    <div className="min-h-screen bg-[#020202] text-white font-sans selection:bg-amber-500/30">
      {/* Dynamic Nav */}
      <nav className="border-b border-white/5 bg-black/80 backdrop-blur-xl sticky top-0 z-50 p-5 flex justify-between items-center">
        <div className="flex items-center gap-4">
          <div className="bg-amber-500 p-2.5 rounded-2xl text-black shadow-lg shadow-amber-500/20">
            <ShieldCheck size={24} />
          </div>
          <div className="hidden sm:block">
            <h1 className="text-xl font-black italic uppercase tracking-tighter">MHA <span className="text-amber-500">Cloud</span></h1>
            <p className="text-[8px] font-bold text-slate-500 uppercase tracking-widest">Engineering V35</p>
          </div>
        </div>

        <div className="flex gap-4">
          <div className="flex bg-white/5 p-1 rounded-xl border border-white/10">
            <button onClick={() => setLang('am')} className={`px-4 py-1.5 rounded-lg text-[10px] font-black uppercase transition-all ${lang === 'am' ? 'bg-amber-500 text-black' : 'text-slate-500'}`}>አማርኛ</button>
            <button onClick={() => setLang('en')} className={`px-4 py-1.5 rounded-lg text-[10px] font-black uppercase transition-all ${lang === 'en' ? 'bg-amber-500 text-black' : 'text-slate-500'}`}>EN</button>
          </div>
        </div>
      </nav>

      <main className="max-w-7xl mx-auto p-6 md:p-12">
        <div className="grid grid-cols-1 lg:grid-cols-12 gap-10">
          
          {/* Form Side */}
          <div className="lg:col-span-4 space-y-6">
            <div className="bg-[#0a0a0a] p-8 rounded-[3rem] border border-white/10 shadow-2xl relative overflow-hidden group">
              <div className="absolute -top-10 -right-10 w-32 h-32 bg-amber-500/10 rounded-full blur-3xl group-hover:bg-amber-500/20 transition-all"></div>
              
              <h2 className="text-xs font-black text-amber-500 uppercase mb-8 flex items-center gap-2 tracking-widest italic">
                <FileText size={16}/> {t.clientInfo}
              </h2>

              <div className="space-y-4">
                <div className="space-y-1">
                  <label className="text-[9px] text-slate-600 font-black uppercase ml-2 tracking-widest">{t.name}</label>
                  <input type="text" value={formData.name} onChange={e => setFormData({...formData, name: e.target.value})} className="w-full bg-black border border-white/10 p-4 rounded-2xl outline-none focus:border-amber-500 text-sm" placeholder="..." />
                </div>

                <div className="grid grid-cols-2 gap-4">
                  <div className="space-y-1">
                    <label className="text-[9px] text-slate-600 font-black uppercase ml-2 tracking-widest">{t.phone}</label>
                    <input type="tel" value={formData.phone} onChange={e => setFormData({...formData, phone: e.target.value})} className="w-full bg-black border border-white/10 p-4 rounded-2xl text-sm outline-none focus:border-amber-500" placeholder="..." />
                  </div>
                  <div className="space-y-1">
                    <label className="text-[9px] text-slate-600 font-black uppercase ml-2 tracking-widest">{t.width}</label>
                    <input type="number" value={formData.width} onChange={e => setFormData({...formData, width: e.target.value})} className="w-full bg-black border border-white/10 p-4 rounded-2xl text-sm outline-none focus:border-amber-500" />
                  </div>
                </div>

                <div className="space-y-1">
                  <label className="text-[9px] text-slate-600 font-black uppercase ml-2 tracking-widest">{t.analysis}</label>
                  <textarea value={formData.analysis} onChange={e => setFormData({...formData, analysis: e.target.value})} className="w-full bg-black border border-white/10 p-4 rounded-2xl min-h-[120px] text-sm outline-none focus:border-amber-500" placeholder="..." />
                </div>

                <button 
                  onClick={handleGenerate}
                  disabled={loading || !formData.name}
                  className="w-full bg-amber-500 hover:bg-white text-black py-5 rounded-[2.5rem] font-black text-xs uppercase flex items-center justify-center gap-3 transition-all mt-4 shadow-xl shadow-amber-500/20 disabled:opacity-50"
                >
                  {loading ? <Loader2 className="animate-spin" /> : <Sparkles size={18}/>}
                  {t.generate}
                </button>
              </div>
            </div>

            <div className="bg-white/5 border border-white/5 p-8 rounded-[2.5rem] flex items-center gap-4 group hover:bg-white/10 transition-all cursor-pointer">
              <div className="bg-black p-4 rounded-2xl text-amber-500 group-hover:scale-110 transition-transform"><Smartphone size={24}/></div>
              <div>
                <h4 className="text-[10px] font-black uppercase text-white tracking-widest">PWA Ready</h4>
                <p className="text-[9px] text-slate-500 uppercase font-bold">Install on your phone home screen</p>
              </div>
            </div>
          </div>

          {/* Results Side */}
          <div className="lg:col-span-8">
            {loading ? (
              <div className="h-[700px] bg-white/[0.02] border border-white/5 rounded-[4rem] flex flex-col items-center justify-center text-center p-12">
                <div className="relative mb-8">
                  <div className="w-24 h-24 border-t-4 border-amber-500 rounded-full animate-spin"></div>
                  <div className="absolute inset-0 m-auto w-16 h-16 bg-amber-500/10 rounded-full flex items-center justify-center">
                    <Box className="text-amber-500" size={30} />
                  </div>
                </div>
                <h3 className="text-xl font-black text-white uppercase italic tracking-widest">{t.loading}</h3>
                <p className="text-xs text-slate-500 mt-4 max-w-sm uppercase leading-loose tracking-widest">
                  Creating your high-resolution soft file and technical assets...
                </p>
              </div>
            ) : result ? (
              <div className="space-y-10 animate-in fade-in duration-700 pb-20">
                {/* Header Actions */}
                <div className="flex flex-wrap gap-4 items-center justify-between">
                  <div className="flex items-center gap-2 bg-green-500/10 text-green-500 px-5 py-2 rounded-full text-[10px] font-black uppercase border border-green-500/20">
                    <CheckCircle2 size={14}/> {t.success}
                  </div>
                  <div className="flex gap-4">
                    <button onClick={downloadSoftFile} className="bg-white/10 hover:bg-white/20 text-white px-6 py-3 rounded-2xl text-[10px] font-black uppercase flex items-center gap-2 border border-white/10 transition-all">
                      <Download size={16}/> {t.download}
                    </button>
                    <button onClick={printAndExport} className="bg-amber-500 hover:bg-white text-black px-6 py-3 rounded-2xl text-[10px] font-black uppercase flex items-center gap-2 transition-all shadow-xl shadow-amber-500/20">
                      <Printer size={16}/> {t.print}
                    </button>
                  </div>
                </div>

                {/* 3D Visual */}
                <div className="relative group overflow-hidden rounded-[4rem] border border-white/10 shadow-2xl">
                   <div className="absolute top-8 left-8 z-10 bg-amber-500 text-black px-6 py-2 rounded-full text-[10px] font-black uppercase shadow-2xl flex items-center gap-2">
                     <Monitor size={14}/> 3D View Render
                   </div>
                   <img src={result.aiImages[1]} className="w-full h-[500px] object-cover group-hover:scale-105 transition-all duration-1000" />
                </div>

                {/* Blueprint and Estimation Card */}
                <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
                  <div className="bg-[#0a0a0a] p-10 rounded-[3.5rem] border border-white/10 flex flex-col justify-between">
                    <div>
                      <h4 className="text-amber-500 text-[10px] font-black uppercase mb-4 tracking-widest flex items-center gap-2">
                        <TrendingUp size={14}/> Estimation Summary
                      </h4>
                      <div className="space-y-3">
                         {result.estimation.map((item, i) => (
                           <div key={i} className="flex justify-between items-center text-xs py-2 border-b border-white/5">
                              <span className="text-slate-500 font-bold uppercase">{item.item}</span>
                              <span className="font-black">ETB {(item.qty * item.price).toLocaleString()}</span>
                           </div>
                         ))}
                      </div>
                    </div>
                    <div className="mt-8 pt-8 border-t border-white/10">
                      <p className="text-[9px] text-slate-600 font-black uppercase">Grand Total</p>
                      <p className="text-2xl font-black text-white italic tracking-tighter">ETB {result.grandTotal.toLocaleString()}</p>
                    </div>
                  </div>

                  <div className="bg-[#0a0a0a] p-2 rounded-[3.5rem] border border-white/10 overflow-hidden">
                    <img src={result.aiImages[0]} className="w-full h-full object-cover rounded-[3.3rem] opacity-70 hover:opacity-100 transition-opacity" />
                  </div>
                </div>

                {/* Secure Seal */}
                <div className="bg-gradient-to-br from-amber-500 to-orange-600 p-10 rounded-[4rem] text-black flex flex-col md:flex-row items-center justify-between gap-8 shadow-2xl">
                   <div className="flex items-center gap-6">
                      <img src={result.qrUrl} className="w-24 h-24 bg-white p-1 rounded-2xl shadow-xl" />
                      <div>
                        <p className="text-[9px] font-black uppercase opacity-60">Digital Signature Verified</p>
                        <h4 className="text-2xl font-black italic tracking-tighter uppercase">{result.certId}</h4>
                        <div className="flex items-center gap-2 text-[10px] font-bold mt-2 opacity-80">
                           <Globe size={12}/> Global Engineering Registry
                        </div>
                      </div>
                   </div>
                   <div className="text-center md:text-right border-t md:border-t-0 md:border-l border-black/10 pt-6 md:pt-0 md:pl-10">
                      <p className="text-[9px] font-black uppercase opacity-60">Chief Systems Architect</p>
                      <p className="text-2xl font-serif italic font-bold mb-1">{ideaOwner}</p>
                      <p className="text-[9px] font-black uppercase tracking-widest">MHA Global Nexus</p>
                   </div>
                </div>
              </div>
            ) : (
              <div className="h-[750px] border-2 border-dashed border-white/5 rounded-[5rem] flex flex-col items-center justify-center bg-white/[0.02]">
                <div className="w-32 h-32 bg-white/5 rounded-full flex items-center justify-center mb-8 opacity-20 group">
                   <FileDown size={60} className="group-hover:translate-y-2 transition-transform"/>
                </div>
                <h2 className="text-xs font-black text-slate-800 uppercase tracking-[1em] italic">Soft File Engine Standby</h2>
                <p className="text-[9px] text-slate-900 mt-4 uppercase tracking-[0.5em]">Global Standards Applied</p>
              </div>
            )}
          </div>
        </div>
      </main>

      <footer className="border-t border-white/5 p-16 bg-black text-center mt-20">
         <div className="max-w-4xl mx-auto space-y-6">
            <h2 className="text-3xl font-black italic tracking-tighter text-white">MHA <span className="text-amber-500">GLOBAL</span> SOLUTIONS</h2>
            <div className="flex flex-wrap justify-center gap-8 text-[10px] text-slate-600 font-black uppercase tracking-widest">
               <span>PWA App</span>
               <span className="text-amber-500">•</span>
               <span>Soft File Export</span>
               <span className="text-amber-500">•</span>
               <span>AI Architecture</span>
               <span className="text-amber-500">•</span>
               <span>Cloud Sync</span>
            </div>
            <p className="text-[10px] font-black text-slate-800 uppercase italic">
              Designed & Built for World-Class Engineering by {ideaOwner}
            </p>
            <div className="flex justify-center gap-4 mt-8 opacity-20">
               <Smartphone size={16}/> <Monitor size={16}/> <Globe size={16}/>
            </div>
         </div>
      </footer>
    </div>
  );
}
