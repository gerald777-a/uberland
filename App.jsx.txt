import { useState, useRef, useEffect } from "react";
import { initializeApp } from "firebase/app";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut, onAuthStateChanged } from "firebase/auth";
import { getFirestore, collection, doc, setDoc, getDoc, addDoc, onSnapshot, query, orderBy, serverTimestamp, updateDoc } from "firebase/firestore";

const firebaseConfig = {
  apiKey: "AIzaSyCIh4fAO1aSWAStveWGijbBMcVrhZzDdYQ",
  authDomain: "uberland-e7513.firebaseapp.com",
  projectId: "uberland-e7513",
  storageBucket: "uberland-e7513.firebasestorage.app",
  messagingSenderId: "713322503082",
  appId: "1:713322503082:web:5f2689c11093a9eb4ea9d3"
};
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

const C = { ocean:"#0a7ea4", deep:"#064e6e", sand:"#f5d98b", sunset:"#f4845f", coral:"#e85d4a", palm:"#2d7a4f", dark:"#0d1b2a" };
const GCASH = "09300291729";
const OWNER_PHONE = "09300291729";
const OWNER_FB = "https://www.facebook.com/eriga.gerald";
const OWNER_EMAIL = "erigagerald72@gmail.com";
const ADMIN_PASS = "unsaman gelard9891**##$";
const SUB_PRICE = 299;
const REGIONS = ["Del Carmen", "Dapa", "General Luna", "San Benito", "San Isidro"];
const VEHICLE_TYPES = ["Habal-habal", "Tricycle", "Tok-tok"];
const VIOLATIONS = ["Harassment / Rude Behavior", "Asking Too Much Fare", "Dangerous Driving", "Threatening Passenger", "Sexual Harassment", "Fraud / Scam", "Did Not Follow Route", "Other Violation"];

const css = `
  @import url('https://fonts.googleapis.com/css2?family=Pacifico&family=Nunito:wght@400;600;700;800&display=swap');
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Nunito', sans-serif; background: #0d1b2a; color: #fff; }
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-thumb { background: #f4845f; border-radius: 4px; }
  @keyframes wave { 0%,100% { transform: translateY(0); } 50% { transform: translateY(-8px); } }
  @keyframes fadeIn { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: translateY(0); } }
  @keyframes pulse { 0%,100% { opacity: 1; } 50% { opacity: 0.4; } }
  @keyframes spin { to { transform: rotate(360deg); } }
  .wave { animation: wave 3s ease-in-out infinite; }
  .fi { animation: fadeIn 0.35s ease forwards; }
  .pu { animation: pulse 2s infinite; }
  .spin { animation: spin 0.8s linear infinite; }
`;

function Inp({ ph, val, set, type = "text", style }) {
  return <input type={type} placeholder={ph} value={val} onChange={e => set(e.target.value)} style={{ width:"100%", padding:"11px 15px", borderRadius:11, border:"1.5px solid rgba(255,255,255,0.15)", background:"rgba(255,255,255,0.07)", color:"#fff", fontFamily:"Nunito", fontSize:14, outline:"none", ...style }} />;
}

function Btn({ children, onClick, style, disabled, color }) {
  return <button onClick={onClick} disabled={disabled} style={{ background:color||`linear-gradient(135deg,${C.sunset},${C.coral})`, border:"none", borderRadius:11, padding:"11px 22px", color:"#fff", fontFamily:"Nunito", fontWeight:800, fontSize:14, cursor:disabled?"not-allowed":"pointer", opacity:disabled?0.5:1, ...style }}>{children}</button>;
}

function Card({ children, style }) {
  return <div style={{ background:"rgba(13,27,42,0.88)", backdropFilter:"blur(16px)", border:"1px solid rgba(255,255,255,0.1)", borderRadius:18, padding:20, ...style }}>{children}</div>;
}

function Logo({ sm }) {
  return <div style={{ display:"flex", alignItems:"center", gap:7 }}>
    <span style={{ fontSize:sm?20:28 }}>🏝️</span>
    <span style={{ fontFamily:"Pacifico, cursive", fontSize:sm?18:26, background:`linear-gradient(90deg,${C.sand},${C.sunset})`, WebkitBackgroundClip:"text", WebkitTextFillColor:"transparent" }}>UberLand</span>
  </div>;
}

function Spinner() {
  return <div className="spin" style={{ width:28, height:28, border:`3px solid rgba(255,255,255,0.1)`, borderTopColor:C.sunset, borderRadius:"50%", margin:"0 auto" }} />;
}

function BG() {
  return <div style={{ position:"fixed", inset:0, zIndex:0, overflow:"hidden", pointerEvents:"none" }}>
    <div style={{ position:"absolute", top:0, left:0, right:0, height:"55%", background:"linear-gradient(160deg,#1a0533,#0d2040,#0a4a6e)" }} />
    <div style={{ position:"absolute", bottom:0, left:0, right:0, height:"40%", background:`linear-gradient(to top,${C.deep}cc,transparent)` }} />
    <div style={{ position:"absolute", top:"10%", right:"14%", width:70, height:70, borderRadius:"50%", background:`radial-gradient(circle,#ffd700,${C.sunset})`, boxShadow:`0 0 50px 18px ${C.sunset}55` }} />
    {[0,1,2].map(i => <div key={i} className="wave" style={{ position:"absolute", bottom:`${i*11}%`, left:0, right:0, height:36, background:`rgba(10,126,164,${0.14+i*0.07})`, borderRadius:"50% 50% 0 0/18px 18px 0 0", animationDelay:`${i*0.7}s` }} />)}
    <div style={{ position:"absolute", bottom:"19%", left:"2%", fontSize:56, opacity:0.28 }}>🌴</div>
    <div style={{ position:"absolute", bottom:"17%", right:"3%", fontSize:70, opacity:0.22 }}>🌴</div>
  </div>;
}

// ── Contact Banner ─────────────────────────────────────────
function ContactBanner() {
  return <div style={{ background:`linear-gradient(135deg,${C.deep},#0a3352)`, borderBottom:`2px solid ${C.ocean}`, padding:"10px 16px", textAlign:"center", fontSize:12, color:"#ccc", lineHeight:1.9, position:"relative", zIndex:10 }}>
    <div style={{ fontWeight:800, color:C.sand, fontSize:13, marginBottom:3 }}>📞 Need help? Contact UberLand Support</div>
    <div style={{ display:"flex", justifyContent:"center", gap:16, flexWrap:"wrap" }}>
      <a href={`tel:${OWNER_PHONE}`} style={{ color:"#4ade80", textDecoration:"none", fontWeight:700 }}>📱 {OWNER_PHONE}</a>
      <a href={OWNER_FB} target="_blank" rel="noreferrer" style={{ color:"#60a5fa", textDecoration:"none", fontWeight:700 }}>📘 Facebook</a>
      <a href={`mailto:${OWNER_EMAIL}`} style={{ color:C.sand, textDecoration:"none", fontWeight:700 }}>✉️ Email</a>
    </div>
    <div style={{ fontSize:11, color:"#888", marginTop:3 }}>For driver registration assistance, GCash setup &amp; account help</div>
  </div>;
}

// ── Violation Warning ──────────────────────────────────────
function ViolationWarning() {
  return <div style={{ background:"linear-gradient(135deg,rgba(232,93,74,0.18),rgba(180,30,30,0.22))", border:"1.5px solid rgba(232,93,74,0.5)", borderRadius:12, padding:13, fontSize:12, lineHeight:1.85, color:"#ffd0c8" }}>
    <div style={{ fontWeight:800, fontSize:13, color:"#ff7b6b", marginBottom:5 }}>⚠️ COMMUNITY RULES — READ CAREFULLY</div>
    Violations that result in a <strong style={{ color:"#ff4444" }}>PERMANENT BAN</strong>:<br />
    🚫 Harassment or rude behavior<br />
    🚫 Asking excessive or unfair fares<br />
    🚫 Dangerous or reckless driving<br />
    🚫 Threats, intimidation, or sexual harassment<br />
    🚫 Fraud, scamming, or dishonesty<br />
    <span style={{ fontSize:11, color:"#ff9980", marginTop:5, display:"block" }}>
      Passengers can report &amp; block you anytime. All reports are reviewed by admin. Violators are permanently removed — no refund.
    </span>
  </div>;
}

// ── Report Modal ───────────────────────────────────────────
function ReportModal({ peer, user, msgs, onReport, onClose }) {
  const [selected, setSelected] = useState([]);
  const [details, setDetails] = useState("");
  const [loading, setLoading] = useState(false);

  function toggle(v) { setSelected(s => s.includes(v) ? s.filter(x => x !== v) : [...s, v]); }

  async function submit() {
    if (selected.length === 0) { alert("Please select at least one violation."); return; }
    setLoading(true);
    try {
      await addDoc(collection(db, "reports"), {
        reportedUserId: peer.id, reportedUserName: peer.name,
        reportedUserVehicle: peer.vehicle || "", reportedUserLicense: peer.license || "",
        reportedByName: user.name, reportedById: user.id,
        violations: selected, details, chatLog: msgs,
        date: serverTimestamp(), status: "pending"
      });
      onReport(); onClose();
      alert(`✅ Report submitted! Admin will review. ${peer.name} is now blocked.`);
    } catch (e) { alert("Error: " + e.message); }
    setLoading(false);
  }

  return <div style={{ position:"fixed", inset:0, zIndex:100, background:"rgba(0,0,0,0.85)", display:"flex", alignItems:"center", justifyContent:"center", padding:16 }}>
    <Card style={{ width:"100%", maxWidth:440, maxHeight:"90vh", overflowY:"auto" }}>
      <div style={{ display:"flex", alignItems:"center", justifyContent:"space-between", marginBottom:16 }}>
        <p style={{ fontWeight:800, color:C.coral, fontSize:16 }}>🚨 Report &amp; Block Driver</p>
        <button onClick={onClose} style={{ background:"none", border:"none", color:"#aaa", fontSize:20, cursor:"pointer" }}>✕</button>
      </div>
      <p style={{ fontSize:13, color:"#ccc", marginBottom:12 }}>Reporting: <strong style={{ color:C.sand }}>{peer.name}</strong></p>
      <p style={{ fontSize:12, color:"#aaa", marginBottom:10, fontWeight:700 }}>Select violation(s):</p>
      <div style={{ display:"flex", flexDirection:"column", gap:7, marginBottom:14 }}>
        {VIOLATIONS.map(v => (
          <button key={v} onClick={() => toggle(v)} style={{ textAlign:"left", padding:"9px 13px", borderRadius:10, border:`1.5px solid ${selected.includes(v)?C.coral:"rgba(255,255,255,0.12)"}`, background:selected.includes(v)?"rgba(232,93,74,0.2)":"rgba(255,255,255,0.04)", color:selected.includes(v)?"#ff9980":"#ccc", cursor:"pointer", fontFamily:"Nunito", fontSize:13, fontWeight:selected.includes(v)?700:400 }}>
            {selected.includes(v) ? "✓ " : ""}{v}
          </button>
        ))}
      </div>
      <p style={{ fontSize:12, color:"#aaa", marginBottom:6, fontWeight:700 }}>Your statement (optional):</p>
      <textarea value={details} onChange={e => setDetails(e.target.value)} placeholder="Describe what happened..." style={{ width:"100%", padding:"10px 13px", borderRadius:10, border:"1.5px solid rgba(255,255,255,0.12)", background:"rgba(255,255,255,0.06)", color:"#fff", fontFamily:"Nunito", fontSize:13, outline:"none", resize:"vertical", minHeight:70, marginBottom:14 }} />
      <div style={{ display:"flex", gap:8 }}>
        <Btn onClick={onClose} color="rgba(255,255,255,0.1)" style={{ flex:1 }}>Cancel</Btn>
        <Btn onClick={submit} disabled={loading} color={`linear-gradient(135deg,${C.coral},#b91c1c)`} style={{ flex:2 }}>{loading?"Submitting...":"🚨 Submit & Block"}</Btn>
      </div>
    </Card>
  </div>;
}

// ── Chat Window ────────────────────────────────────────────
function ChatWindow({ user, peer, onClose, onBlock }) {
  const [msgs, setMsgs] = useState([]);
  const [inp, setInp] = useState("");
  const [showReport, setShowReport] = useState(false);
  const bot = useRef();
  const isPassenger = user.role === "passenger";
  const roomId = [user.id, peer.id].sort().join("_");

  useEffect(() => {
    const q = query(collection(db, "chats", roomId, "messages"), orderBy("ts"));
    const unsub = onSnapshot(q, snap => {
      setMsgs(snap.docs.map(d => ({ id:d.id, ...d.data() })));
      setTimeout(() => bot.current && bot.current.scrollIntoView({ behavior:"smooth" }), 80);
    });
    return unsub;
  }, [roomId]);

  async function send() {
    if (!inp.trim()) return;
    const text = inp; setInp("");
    await addDoc(collection(db, "chats", roomId, "messages"), { from:user.id, fromName:user.name, text, ts:serverTimestamp() });
    await setDoc(doc(db, "chats", roomId), { participants:[user.id, peer.id], lastMessage:text, updatedAt:serverTimestamp() }, { merge:true });
  }

  return <>
    {showReport && <ReportModal peer={peer} user={user} msgs={msgs} onReport={() => { onBlock(peer.id); onClose(); }} onClose={() => setShowReport(false)} />}
    <div style={{ display:"flex", flexDirection:"column", height:"100%" }}>
      <div style={{ display:"flex", alignItems:"center", gap:10, padding:"11px 14px", borderBottom:"1px solid rgba(255,255,255,0.1)", background:"rgba(0,0,0,0.35)" }}>
        <button onClick={onClose} style={{ background:"none", border:"none", color:C.sand, fontSize:20, cursor:"pointer" }}>←</button>
        <span style={{ fontSize:26 }}>{peer.avatar || "🏍️"}</span>
        <div style={{ flex:1 }}>
          <div style={{ fontWeight:800 }}>{peer.name}</div>
          <div style={{ fontSize:12, color:C.sand }}>{peer.vehicle || "Passenger"} · {peer.region}</div>
        </div>
        <div className="pu" style={{ width:9, height:9, borderRadius:"50%", background:"#4ade80" }} />
        {isPassenger && <button onClick={() => setShowReport(true)} style={{ background:"rgba(232,93,74,0.2)", border:`1px solid ${C.coral}`, borderRadius:8, padding:"5px 10px", color:C.coral, cursor:"pointer", fontFamily:"Nunito", fontSize:12, fontWeight:700, marginLeft:6 }}>🚨 Report</button>}
      </div>
      <div style={{ flex:1, overflowY:"auto", padding:14, display:"flex", flexDirection:"column", gap:8 }}>
        {msgs.length === 0 && <div style={{ textAlign:"center", padding:30, color:"#666", fontSize:13 }}>Start the conversation! 👋<br /><span style={{ fontSize:11 }}>Messages saved in real-time ☁️</span></div>}
        {msgs.map(m => {
          const mine = m.from === user.id;
          return <div key={m.id} style={{ display:"flex", justifyContent:mine?"flex-end":"flex-start" }}>
            <div style={{ maxWidth:"72%", padding:"9px 13px", borderRadius:mine?"16px 16px 3px 16px":"16px 16px 16px 3px", background:mine?`linear-gradient(135deg,${C.sunset},${C.coral})`:"rgba(255,255,255,0.1)", fontSize:14, lineHeight:1.5 }}>{m.text}</div>
          </div>;
        })}
        <div ref={bot} />
      </div>
      <div style={{ padding:10, display:"flex", gap:8, borderTop:"1px solid rgba(255,255,255,0.1)" }}>
        <input value={inp} onChange={e => setInp(e.target.value)} onKeyDown={e => e.key==="Enter" && send()} placeholder="Type a message..." style={{ flex:1, padding:"10px 15px", borderRadius:22, border:"none", background:"rgba(255,255,255,0.1)", color:"#fff", fontFamily:"Nunito", fontSize:14, outline:"none" }} />
        <button onClick={send} style={{ background:`linear-gradient(135deg,${C.sunset},${C.coral})`, border:"none", borderRadius:"50%", width:42, height:42, fontSize:17, cursor:"pointer" }}>➤</button>
      </div>
    </div>
  </>;
}

// ── Main App ───────────────────────────────────────────────
function MainApp({ user, onLogout }) {
  const [chat, setChat] = useState(null);
  const [peers, setPeers] = useState([]);
  const [blockedIds, setBlockedIds] = useState(user.blockedIds || []);
  const isDriver = user.role === "driver";

  useEffect(() => {
    const targetRole = isDriver ? "passenger" : "driver";
    const unsub = onSnapshot(collection(db, "users"), snap => {
      const list = snap.docs.map(d => ({ id:d.id, ...d.data() }))
        .filter(u => u.role === targetRole && u.id !== user.id && !u.banned && !blockedIds.includes(u.id) && u.region === user.region);
      setPeers(list);
    });
    return unsub;
  }, [blockedIds, user.region]);

  useEffect(() => {
    updateDoc(doc(db, "users", user.id), { online:true });
    return () => { updateDoc(doc(db, "users", user.id), { online:false }); };
  }, []);

  if (user.banned) return <div style={{ height:"100vh", display:"flex", alignItems:"center", justifyContent:"center", position:"relative", zIndex:1, padding:20 }}>
    <Card><div style={{ textAlign:"center" }}>
      <div style={{ fontSize:56, marginBottom:12 }}>🚫</div>
      <p style={{ fontWeight:800, fontSize:18, color:C.coral, marginBottom:8 }}>Account Permanently Banned</p>
      <p style={{ fontSize:14, color:"#ccc", lineHeight:1.8 }}>Your account was banned due to a violation of UberLand community rules.<br /><span style={{ fontSize:12, color:"#888" }}>Contact admin if you think this is a mistake.</span></p>
      <a href={OWNER_FB} target="_blank" rel="noreferrer" style={{ color:"#60a5fa", fontSize:13, display:"block", marginTop:10 }}>📘 Contact Admin on Facebook</a>
    </div></Card>
  </div>;

  function blockUser(id) {
    const updated = [...blockedIds, id];
    setBlockedIds(updated);
    updateDoc(doc(db, "users", user.id), { blockedIds:updated });
  }

  return <div style={{ height:"100vh", display:"flex", flexDirection:"column", position:"relative", zIndex:1 }}>
    <ContactBanner />
    <div style={{ display:"flex", alignItems:"center", justifyContent:"space-between", padding:"11px 15px", background:"rgba(13,27,42,0.93)", backdropFilter:"blur(12px)", borderBottom:"1px solid rgba(255,255,255,0.1)" }}>
      <Logo sm />
      <div style={{ display:"flex", alignItems:"center", gap:9 }}>
        <div style={{ textAlign:"right" }}>
          <div style={{ fontSize:13, color:"#bbb" }}>Hi, {user.name.split(" ")[0]}! 👋</div>
          <div style={{ fontSize:11, color:C.sand }}>📍 {user.region}</div>
        </div>
        {isDriver && <div style={{ fontSize:11, background:`${C.palm}33`, border:`1px solid ${C.palm}`, padding:"3px 9px", borderRadius:16, color:"#4ade80" }}>✓ Trial</div>}
        <Btn onClick={onLogout} style={{ padding:"6px 13px", fontSize:12 }}>Logout</Btn>
      </div>
    </div>
    <div style={{ flex:1, overflow:"hidden" }}>
      {chat ? <ChatWindow user={user} peer={chat} onClose={() => setChat(null)} onBlock={blockUser} /> :
      <div style={{ height:"100%", overflowY:"auto", padding:15 }}>
        <div style={{ maxWidth:500, margin:"0 auto" }}>
          {isDriver && <div style={{ marginBottom:14 }}><ViolationWarning /></div>}
          <p style={{ fontWeight:800, fontSize:17, marginBottom:3 }}>{isDriver ? "🧑 Passengers Looking for Rides" : "🏍️ Available Drivers Near You"}</p>
          <p style={{ fontSize:12, color:"#999", marginBottom:4 }}>{isDriver ? "Tap a passenger to offer your service" : "Tap a driver to start chatting"}</p>
          <div style={{ background:`${C.ocean}22`, border:`1px solid ${C.ocean}44`, borderRadius:10, padding:"6px 12px", marginBottom:12, fontSize:12, color:C.sand }}>📍 Showing {isDriver?"passengers":"drivers"} in <strong>{user.region}</strong> only</div>
          {peers.length === 0 && <Card><div style={{ textAlign:"center", padding:20 }}>
            <div style={{ fontSize:52, marginBottom:10 }}>🌊</div>
            <p style={{ fontWeight:800, color:C.sand, fontSize:16 }}>{isDriver ? "No passengers in your area yet" : "No drivers in your area yet"}</p>
            <p style={{ fontSize:13, color:"#888", marginTop:6 }}>Share UberLand with people in {user.region}! 🌴</p>
          </div></Card>}
          <div style={{ display:"flex", flexDirection:"column", gap:9 }}>
            {peers.map(p => (
              <div key={p.id} onClick={() => setChat(p)} className="fi" style={{ background:"rgba(255,255,255,0.06)", borderRadius:14, padding:13, border:"1px solid rgba(255,255,255,0.1)", cursor:"pointer", display:"flex", alignItems:"center", gap:12, transition:"background 0.2s" }}
                onMouseEnter={e => { e.currentTarget.style.background = "rgba(244,132,95,0.15)"; }}
                onMouseLeave={e => { e.currentTarget.style.background = "rgba(255,255,255,0.06)"; }}>
                <div style={{ fontSize:30, position:"relative" }}>
                  {p.avatar || "🧑"}
                  <span style={{ position:"absolute", bottom:0, right:-3, width:9, height:9, borderRadius:"50%", background:p.online?"#4ade80":"#555", border:"2px solid #0d1b2a" }} />
                </div>
                <div style={{ flex:1 }}>
                  <div style={{ fontWeight:800, fontSize:15 }}>{p.name}</div>
                  <div style={{ fontSize:12, color:C.sand, marginTop:2 }}>{p.vehicle || "📍 Looking for a ride"}</div>
                </div>
                <span style={{ color:C.sunset, fontSize:18 }}>💬</span>
              </div>
            ))}
          </div>
          <Card style={{ marginTop:14, borderColor:`${C.ocean}44` }}>
            <p style={{ fontSize:13, color:"#ccc", lineHeight:1.9 }}>
              <strong style={{ color:C.sand }}>How it works 🌊</strong><br />
              {isDriver ? "1. Browse passengers • 2. Tap to offer a ride • 3. Agree on pickup & fare • 4. Earn! 💰" : "1. Browse drivers • 2. Tap to message • 3. Share pickup & destination • 4. Choose your driver! 🏝️"}
            </p>
          </Card>
          {!isDriver && <p style={{ fontSize:11, color:"#666", textAlign:"center", marginTop:10 }}>🛡️ You can report &amp; block any driver after chatting</p>}
        </div>
      </div>}
    </div>
  </div>;
}

// ── Auth ───────────────────────────────────────────────────
function Auth({ onLogin }) {
  const [role, setRole] = useState(null);
  const [name, setName] = useState(""); const [email, setEmail] = useState(""); const [password, setPassword] = useState("");
  const [license, setLicense] = useState(""); const [vehicle, setVehicle] = useState(""); const [vtype, setVtype] = useState("Habal-habal");
  const [region, setRegion] = useState("");
  const [isLogin, setIsLogin] = useState(false); const [loading, setLoading] = useState(false);
  const [adminPw, setAdminPw] = useState(""); const [adminMode, setAdminMode] = useState(false);

  const vehicleAvatar = { "Habal-habal":"🏍️", "Tricycle":"🛺", "Tok-tok":"🛻" };

  async function handleAuth() {
    if (!email.trim() || !password.trim()) { alert("Please enter email and password"); return; }
    if (!isLogin && !name.trim()) { alert("Please enter your name"); return; }
    if (!isLogin && !region) { alert("Please select your region"); return; }
    if (!isLogin && role === "driver" && !license.trim()) { alert("Enter your license number"); return; }
    if (!isLogin && role === "driver" && !vehicle.trim()) { alert("Enter vehicle color & brand"); return; }
    setLoading(true);
    try {
      let uid;
      if (isLogin) {
        const cred = await signInWithEmailAndPassword(auth, email, password);
        uid = cred.user.uid;
      } else {
        const cred = await createUserWithEmailAndPassword(auth, email, password);
        uid = cred.user.uid;
        const userData = {
          id:uid, name, email, role, region,
          avatar: role === "driver" ? (vehicleAvatar[vtype] || "🏍️") : "🧑",
          vehicle: role === "driver" ? `${vtype} • ${vehicle}` : "",
          license: role === "driver" ? license : "",
          subscribed:true, banned:false, online:true,
          joinedDate: new Date().toLocaleDateString(),
          blockedIds:[], createdAt:serverTimestamp()
        };
        await setDoc(doc(db, "users", uid), userData);
      }
      const snap = await getDoc(doc(db, "users", uid));
      if (snap.exists()) {
        const data = snap.data();
        if (data.banned) { alert("This account has been permanently banned."); await signOut(auth); setLoading(false); return; }
        onLogin(data);
      } else { alert("User data not found. Please sign up first."); await signOut(auth); }
    } catch (e) {
      const msgs = { "auth/email-already-in-use":"Email already registered. Please log in instead.", "auth/wrong-password":"Wrong password.", "auth/user-not-found":"No account found. Please sign up.", "auth/invalid-email":"Please enter a valid email.", "auth/weak-password":"Password must be at least 6 characters." };
      alert(msgs[e.code] || e.message);
    }
    setLoading(false);
  }

  if (adminMode) return <div style={{ minHeight:"100vh", display:"flex", alignItems:"center", justifyContent:"center", padding:20, position:"relative", zIndex:1 }}>
    <Card style={{ width:"100%", maxWidth:360 }}>
      <div style={{ textAlign:"center", marginBottom:18 }}><div style={{ fontSize:40, marginBottom:6 }}>🔐</div><Logo /><p style={{ color:C.sand, fontWeight:800, marginTop:8 }}>Admin Access</p></div>
      <div style={{ display:"flex", flexDirection:"column", gap:10 }}>
        <Inp ph="Admin password" val={adminPw} set={setAdminPw} type="password" />
        <Btn onClick={() => { if (adminPw === ADMIN_PASS) onLogin({ role:"admin", id:"admin", name:"Admin" }); else alert("Wrong password!"); }} style={{ width:"100%" }}>Enter Dashboard →</Btn>
        <button onClick={() => setAdminMode(false)} style={{ background:"none", border:"none", color:"#888", cursor:"pointer", fontFamily:"Nunito", fontSize:13 }}>← Back</button>
      </div>
    </Card>
  </div>;

  return <div style={{ minHeight:"100vh", display:"flex", alignItems:"center", justifyContent:"center", padding:20, position:"relative", zIndex:1 }}>
    <div style={{ width:"100%", maxWidth:420 }}>
      <ContactBanner />
      <Card style={{ borderTopLeftRadius:0, borderTopRightRadius:0 }}>
        <div style={{ textAlign:"center", marginBottom:22 }}><Logo /><p style={{ color:`${C.sand}88`, marginTop:5, fontSize:13 }}>Siargao's Island Ride Network 🌊</p></div>
        {!role ? <>
          <p style={{ textAlign:"center", marginBottom:12, color:"#ccc", fontWeight:700 }}>I am a...</p>
          <div style={{ display:"grid", gridTemplateColumns:"1fr 1fr", gap:10, marginBottom:12 }}>
            {[{r:"passenger",icon:"🧑",label:"Passenger",sub:"Free forever"},{r:"driver",icon:"🏍️",label:"Driver",sub:"1 month free trial"}].map(({r,icon,label,sub}) => (
              <button key={r} onClick={() => setRole(r)} style={{ padding:"18px 10px", borderRadius:14, border:`2px solid ${C.ocean}`, background:"rgba(10,126,164,0.15)", cursor:"pointer", color:"#fff", fontFamily:"Nunito" }}>
                <div style={{ fontSize:32 }}>{icon}</div>
                <div style={{ fontWeight:800, marginTop:5 }}>{label}</div>
                <div style={{ fontSize:11, color:C.sand, marginTop:2 }}>{sub}</div>
              </button>
            ))}
          </div>
          <button onClick={() => setAdminMode(true)} style={{ width:"100%", background:"rgba(255,255,255,0.05)", border:"1px solid rgba(255,255,255,0.1)", borderRadius:10, padding:9, color:"#777", cursor:"pointer", fontFamily:"Nunito", fontSize:12 }}>🔐 Admin Login</button>
        </> : <div className="fi">
          <p style={{ fontWeight:800, color:C.sand, marginBottom:14 }}>{role==="driver"?"🏍️ Driver":"🧑 Passenger"} — {isLogin?"Log In":"Sign Up"}</p>
          <div style={{ display:"flex", flexDirection:"column", gap:10 }}>
            {!isLogin && <Inp ph="Full name" val={name} set={setName} />}
            <Inp ph="Email address" val={email} set={setEmail} type="email" />
            <Inp ph="Password (min 6 characters)" val={password} set={setPassword} type="password" />
            {!isLogin && <>
              <div>
                <p style={{ fontSize:12, color:"#aaa", marginBottom:6, fontWeight:700 }}>📍 Select your region:</p>
                <div style={{ display:"grid", gridTemplateColumns:"1fr 1fr", gap:6 }}>
                  {REGIONS.map(r => (
                    <button key={r} onClick={() => setRegion(r)} style={{ padding:"9px 8px", borderRadius:10, border:`1.5px solid ${region===r?C.sand:"rgba(255,255,255,0.12)"}`, background:region===r?"rgba(245,217,139,0.15)":"rgba(255,255,255,0.04)", color:region===r?C.sand:"#ccc", cursor:"pointer", fontFamily:"Nunito", fontSize:12, fontWeight:region===r?800:400 }}>
                      {region===r?"✓ ":""}{r}
                    </button>
                  ))}
                </div>
              </div>
              {role==="driver" && <>
                <Inp ph="Driver's License No." val={license} set={setLicense} />
                <div style={{ display:"flex", gap:8 }}>
                  <select value={vtype} onChange={e => setVtype(e.target.value)} style={{ padding:"11px 10px", borderRadius:11, border:"1.5px solid rgba(255,255,255,0.15)", background:"rgba(255,255,255,0.07)", color:"#fff", fontFamily:"Nunito", fontSize:13, flex:"0 0 120px" }}>
                    {VEHICLE_TYPES.map(v => <option key={v}>{v}</option>)}
                  </select>
                  <Inp ph="Color & Brand (e.g. Blue Honda)" val={vehicle} set={setVehicle} />
                </div>
                <ViolationWarning />
                <div style={{ background:"rgba(244,132,95,0.13)", borderRadius:10, padding:11, border:`1px solid ${C.sunset}44`, fontSize:13, color:C.sand, lineHeight:1.7 }}>
                  🎉 <strong>1st month FREE!</strong> Then ₱{SUB_PRICE}/month.<br />
                  💚 Pay via GCash: <strong style={{ color:"#4ade80", fontSize:15 }}>{GCASH}</strong><br />
                  <span style={{ fontSize:11, color:"#aaa" }}>No personal GCash? You can pay through any GCash outlet (sari-sari store, etc.) or contact us for help!</span><br />
                  <a href={OWNER_FB} target="_blank" rel="noreferrer" style={{ color:"#60a5fa", fontSize:12 }}>📘 Message us on Facebook for assistance</a>
                </div>
              </>}
            </>}
            <Btn onClick={handleAuth} disabled={loading} style={{ width:"100%" }}>{loading ? <Spinner /> : isLogin ? "Log In 🌴" : "Create Account 🌴"}</Btn>
            <button onClick={() => setIsLogin(l => !l)} style={{ background:"none", border:"none", color:C.sand, cursor:"pointer", fontFamily:"Nunito", fontSize:13 }}>{isLogin?"No account yet? Sign Up →":"Already have an account? Log In →"}</button>
            <button onClick={() => setRole(null)} style={{ background:"none", border:"none", color:"#888", cursor:"pointer", fontFamily:"Nunito", fontSize:13 }}>← Go back</button>
          </div>
        </div>}
      </Card>
    </div>
  </div>;
}

// ── Admin Dashboard ────────────────────────────────────────
function AdminDash({ onBack }) {
  const [tab, setTab] = useState("overview");
  const [users, setUsers] = useState([]);
  const [reports, setReports] = useState([]);
  const [reviewReport, setReviewReport] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const u1 = onSnapshot(collection(db, "users"), snap => { setUsers(snap.docs.map(d => ({ id:d.id, ...d.data() }))); setLoading(false); });
    const u2 = onSnapshot(collection(db, "reports"), snap => { setReports(snap.docs.map(d => ({ id:d.id, ...d.data() }))); });
    return () => { u1(); u2(); };
  }, []);

  const drivers = users.filter(u => u.role === "driver");
  const passengers = users.filter(u => u.role === "passenger");
  const banned = users.filter(u => u.banned);
  const pending = reports.filter(r => r.status === "pending");

  async function banUser(userId) { await updateDoc(doc(db, "users", userId), { banned:true }); }
  async function updateReportStatus(reportId, status) { await updateDoc(doc(db, "reports", reportId), { status }); }

  function TabBtn({ id, label }) {
    return <button onClick={() => setTab(id)} style={{ flex:1, padding:"9px 4px", background:tab===id?`linear-gradient(135deg,${C.sunset},${C.coral})`:"rgba(255,255,255,0.07)", border:"none", borderRadius:10, color:"#fff", fontFamily:"Nunito", fontWeight:800, fontSize:11, cursor:"pointer" }}>
      {label}{id==="reports"&&pending.length>0&&<span style={{ marginLeft:4, background:"#e85d4a", borderRadius:"50%", padding:"1px 5px", fontSize:10 }}>{pending.length}</span>}
    </button>;
  }

  if (loading) return <div style={{ height:"100vh", display:"flex", alignItems:"center", justifyContent:"center", position:"relative", zIndex:1 }}><div style={{ textAlign:"center" }}><Spinner /><p style={{ marginTop:12, color:"#aaa" }}>Loading...</p></div></div>;

  if (reviewReport) return <div style={{ minHeight:"100vh", position:"relative", zIndex:1, padding:16, overflowY:"auto" }}>
    <div style={{ maxWidth:600, margin:"0 auto" }}>
      <div style={{ display:"flex", alignItems:"center", gap:10, marginBottom:16, paddingTop:8 }}>
        <button onClick={() => setReviewReport(null)} style={{ background:"none", border:"none", color:C.sand, fontSize:20, cursor:"pointer" }}>←</button>
        <p style={{ fontWeight:800, color:C.coral, fontSize:16 }}>🚨 Report Review</p>
      </div>
      <Card style={{ marginBottom:16 }}>
        <p style={{ fontWeight:800, marginBottom:4 }}>Reported Driver: <span style={{ color:C.coral }}>{reviewReport.reportedUserName}</span></p>
        <p style={{ fontSize:13, color:C.sand, marginBottom:2 }}>{reviewReport.reportedUserVehicle} · License: {reviewReport.reportedUserLicense}</p>
        <p style={{ fontSize:12, color:"#888", marginBottom:12 }}>Reported by: {reviewReport.reportedByName}</p>
        <p style={{ fontWeight:700, marginBottom:8, fontSize:13 }}>Violations:</p>
        {(reviewReport.violations||[]).map(v => <div key={v} style={{ background:"rgba(232,93,74,0.15)", border:"1px solid #e85d4a55", borderRadius:8, padding:"6px 12px", marginBottom:6, fontSize:13, color:"#ff9980" }}>🚫 {v}</div>)}
        {reviewReport.details && <><p style={{ fontWeight:700, marginBottom:6, fontSize:13, marginTop:10 }}>Passenger's Statement:</p><div style={{ background:"rgba(255,255,255,0.05)", borderRadius:8, padding:12, fontSize:13, color:"#ccc", lineHeight:1.7 }}>{reviewReport.details}</div></>}
        <p style={{ fontWeight:700, marginTop:14, marginBottom:8, fontSize:13 }}>💬 Chat Log:</p>
        <div style={{ background:"rgba(0,0,0,0.3)", borderRadius:10, padding:12, maxHeight:200, overflowY:"auto", display:"flex", flexDirection:"column", gap:6 }}>
          {(reviewReport.chatLog||[]).map((m,i) => {
            const isReported = m.from === reviewReport.reportedUserId;
            return <div key={i} style={{ display:"flex", justifyContent:isReported?"flex-start":"flex-end" }}>
              <div style={{ maxWidth:"75%", padding:"7px 12px", borderRadius:10, background:isReported?"rgba(232,93,74,0.2)":"rgba(10,126,164,0.25)", fontSize:12 }}>
                <span style={{ fontSize:10, color:"#aaa", display:"block", marginBottom:2 }}>{isReported?reviewReport.reportedUserName:reviewReport.reportedByName}</span>
                {m.text}
              </div>
            </div>;
          })}
          {(!reviewReport.chatLog||reviewReport.chatLog.length===0)&&<p style={{ color:"#666", fontSize:12, textAlign:"center" }}>No chat log</p>}
        </div>
        <div style={{ display:"flex", gap:8, marginTop:16 }}>
          <Btn onClick={async () => { await updateReportStatus(reviewReport.id,"dismissed"); setReviewReport(null); }} color="rgba(255,255,255,0.1)" style={{ flex:1 }}>✓ Dismiss</Btn>
          <Btn onClick={async () => { await banUser(reviewReport.reportedUserId); await updateReportStatus(reviewReport.id,"banned"); alert(`${reviewReport.reportedUserName} has been permanently banned.`); setReviewReport(null); }} color={`linear-gradient(135deg,${C.coral},#7f1d1d)`} style={{ flex:1.5 }}>🚫 Permanently Ban</Btn>
        </div>
      </Card>
    </div>
  </div>;

  return <div style={{ minHeight:"100vh", position:"relative", zIndex:1, padding:16, overflowY:"auto" }}>
    <div style={{ maxWidth:600, margin:"0 auto" }}>
      <div style={{ display:"flex", alignItems:"center", gap:12, marginBottom:16, paddingTop:8 }}>
        <Logo sm /><span style={{ color:C.sand, fontWeight:800 }}>Admin Dashboard</span>
        <Btn onClick={onBack} style={{ marginLeft:"auto", padding:"6px 13px", fontSize:12 }}>← Logout</Btn>
      </div>
      <div style={{ display:"flex", gap:6, marginBottom:14 }}>
        <TabBtn id="overview" label="📊 Overview" /><TabBtn id="drivers" label="🏍️ Drivers" /><TabBtn id="passengers" label="🧑 Passengers" /><TabBtn id="reports" label="🚨 Reports" />
      </div>

      {tab==="overview" && <>
        <div style={{ display:"grid", gridTemplateColumns:"1fr 1fr", gap:10, marginBottom:14 }}>
          {[{icon:"🏍️",label:"Drivers",val:drivers.length,col:C.sunset},{icon:"🧑",label:"Passengers",val:passengers.length,col:C.ocean},{icon:"💰",label:"Subscriptions",val:`₱${drivers.filter(d=>d.subscribed).length*SUB_PRICE}`,col:"#4ade80"},{icon:"🚨",label:"Reports",val:reports.length,col:C.coral}].map(s => (
            <Card key={s.label} style={{ padding:16 }}><div style={{ textAlign:"center" }}><div style={{ fontSize:28 }}>{s.icon}</div><div style={{ fontWeight:800, fontSize:22, color:s.col, marginTop:4 }}>{s.val}</div><div style={{ fontSize:12, color:"#aaa", marginTop:2 }}>{s.label}</div></div></Card>
          ))}
        </div>
        <Card style={{ borderColor:`${C.sunset}55`, marginBottom:12 }}>
          <p style={{ fontWeight:800, color:C.sand, marginBottom:10 }}>💳 GCash Payment Collection</p>
          <p style={{ fontSize:14, color:"#ccc", lineHeight:1.9 }}>
            Drivers pay <strong style={{ color:C.sunset }}>₱{SUB_PRICE}/month</strong> after free trial.<br />
            GCash: <strong style={{ color:"#4ade80", fontSize:16 }}>{GCASH}</strong><br />
            FB: <a href={OWNER_FB} target="_blank" rel="noreferrer" style={{ color:"#60a5fa" }}>facebook.com/eriga.gerald</a><br />
            <span style={{ fontSize:12, color:"#888" }}>Drivers without GCash can pay through sari-sari store GCash outlets.</span>
          </p>
        </Card>
        <div style={{ display:"grid", gridTemplateColumns:"1fr 1fr 1fr 1fr 1fr", gap:6, marginBottom:12 }}>
          {REGIONS.map(r => {
            const dCount = drivers.filter(d => d.region===r).length;
            const pCount = passengers.filter(p => p.region===r).length;
            return <Card key={r} style={{ padding:10, textAlign:"center" }}>
              <div style={{ fontSize:11, fontWeight:800, color:C.sand, marginBottom:4 }}>{r}</div>
              <div style={{ fontSize:11, color:"#4ade80" }}>🏍️ {dCount}</div>
              <div style={{ fontSize:11, color:"#60a5fa" }}>🧑 {pCount}</div>
            </Card>;
          })}
        </div>
        {banned.length>0&&<Card style={{ borderColor:`${C.coral}55` }}><p style={{ fontSize:13, color:"#ff9980" }}><strong style={{ color:C.coral }}>🚫 Banned: {banned.length}</strong> accounts</p></Card>}
      </>}

      {tab==="drivers" && <Card>
        <p style={{ fontWeight:800, color:C.sand, marginBottom:12 }}>🏍️ Drivers ({drivers.length})</p>
        {drivers.length===0&&<p style={{ color:"#777", fontSize:13 }}>No drivers yet.</p>}
        {drivers.map(d => <div key={d.id} style={{ display:"flex", alignItems:"center", gap:10, padding:"10px 0", borderBottom:"1px solid rgba(255,255,255,0.07)" }}>
          <span style={{ fontSize:22 }}>{d.avatar}</span>
          <div style={{ flex:1 }}>
            <div style={{ fontWeight:700 }}>{d.name}{d.banned&&<span style={{ marginLeft:6, fontSize:10, color:"#f44", background:"rgba(255,0,0,0.15)", padding:"2px 6px", borderRadius:6 }}>BANNED</span>}</div>
            <div style={{ fontSize:11, color:C.sand }}>{d.vehicle} · 📋 {d.license}</div>
            <div style={{ fontSize:11, color:"#777" }}>{d.email} · 📍 {d.region} · {d.joinedDate}</div>
          </div>
          <div style={{ fontSize:11, background:d.banned?"rgba(255,0,0,0.2)":`${C.palm}33`, border:`1px solid ${d.banned?"#f00":C.palm}`, padding:"3px 9px", borderRadius:14, color:d.banned?"#f44":"#4ade80" }}>{d.banned?"🚫 Banned":"✓ Active"}</div>
        </div>)}
      </Card>}

      {tab==="passengers" && <Card>
        <p style={{ fontWeight:800, color:C.sand, marginBottom:12 }}>🧑 Passengers ({passengers.length})</p>
        {passengers.length===0&&<p style={{ color:"#777", fontSize:13 }}>No passengers yet.</p>}
        {passengers.map(p => <div key={p.id} style={{ display:"flex", alignItems:"center", gap:10, padding:"10px 0", borderBottom:"1px solid rgba(255,255,255,0.07)" }}>
          <span style={{ fontSize:22 }}>🧑</span>
          <div style={{ flex:1 }}><div style={{ fontWeight:700 }}>{p.name}</div><div style={{ fontSize:11, color:"#777" }}>{p.email} · 📍 {p.region} · {p.joinedDate}</div></div>
          <div style={{ fontSize:11, background:`${C.ocean}33`, border:`1px solid ${C.ocean}`, padding:"3px 9px", borderRadius:14, color:"#7dd3fc" }}>Free</div>
        </div>)}
      </Card>}

      {tab==="reports" && <>
        <p style={{ fontSize:13, color:"#aaa", marginBottom:12 }}>Review reports. View chat evidence and decide to ban or dismiss.</p>
        {reports.length===0&&<Card><div style={{ textAlign:"center", padding:20 }}><div style={{ fontSize:40, marginBottom:8 }}>✅</div><p style={{ color:"#4ade80", fontWeight:800 }}>No reports yet!</p><p style={{ fontSize:13, color:"#888", marginTop:4 }}>Community is behaving well 🌴</p></div></Card>}
        <div style={{ display:"flex", flexDirection:"column", gap:10 }}>
          {reports.map(r => <Card key={r.id} style={{ borderColor:r.status==="pending"?`${C.coral}55`:"rgba(255,255,255,0.07)" }}>
            <div style={{ display:"flex", alignItems:"flex-start", justifyContent:"space-between", marginBottom:8 }}>
              <div>
                <p style={{ fontWeight:800, color:C.coral }}>🚨 {r.reportedUserName}</p>
                <p style={{ fontSize:11, color:"#888" }}>By: {r.reportedByName}</p>
              </div>
              <div style={{ fontSize:11, padding:"3px 9px", borderRadius:12, background:r.status==="banned"?"rgba(255,0,0,0.2)":r.status==="dismissed"?"rgba(255,255,255,0.07)":"rgba(244,132,95,0.2)", border:`1px solid ${r.status==="banned"?"#f44":r.status==="dismissed"?"#555":C.sunset}`, color:r.status==="banned"?"#f44":r.status==="dismissed"?"#888":"#ffa07a" }}>
                {r.status==="banned"?"🚫 Banned":r.status==="dismissed"?"✓ Dismissed":"⏳ Pending"}
              </div>
            </div>
            <div style={{ display:"flex", flexWrap:"wrap", gap:5, marginBottom:10 }}>
              {(r.violations||[]).map(v => <span key={v} style={{ fontSize:11, background:"rgba(232,93,74,0.15)", border:"1px solid #e85d4a55", borderRadius:6, padding:"3px 8px", color:"#ff9980" }}>🚫 {v}</span>)}
            </div>
            {r.status==="pending"&&<Btn onClick={() => setReviewReport(r)} color={`linear-gradient(135deg,${C.ocean},${C.deep})`} style={{ width:"100%", padding:"8px" }}>🔍 Review Chat &amp; Decide</Btn>}
          </Card>)}
        </div>
      </>}
    </div>
  </div>;
}

// ── Root ───────────────────────────────────────────────────
export default function UberLand() {
  const [user, setUser] = useState(null);
  const [checking, setChecking] = useState(true);

  useEffect(() => {
    const unsub = onAuthStateChanged(auth, async firebaseUser => {
      if (firebaseUser) {
        const snap = await getDoc(doc(db, "users", firebaseUser.uid));
        if (snap.exists()) setUser(snap.data());
      }
      setChecking(false);
    });
    return unsub;
  }, []);

  function logout() { signOut(auth); setUser(null); }

  if (checking) return <>
    <style>{css}</style><BG />
    <div style={{ height:"100vh", display:"flex", alignItems:"center", justifyContent:"center", position:"relative", zIndex:1 }}>
      <div style={{ textAlign:"center" }}><Logo /><div style={{ marginTop:20 }}><Spinner /></div><p style={{ marginTop:12, color:"#aaa", fontSize:13 }}>Loading UberLand... 🌴</p></div>
    </div>
  </>;

  return <>
    <style>{css}</style><BG />
    {user?.role==="admin" ? <AdminDash onBack={logout} /> : user ? <MainApp user={user} onLogout={logout} /> : <Auth onLogin={setUser} />}
  </>;
}
