# HEALTHUPP
This is an app free for the older adult for their daily health management 
git remote add origin https://github.com/melodiyus/HEALTHUPP.git
git branch -M main
git push -u origin main
// React + Tailwind + Firebase yapılandırmalı Vercel uyumlu Sağlık Asistanı Uygulaması

// Adım 1: Gerekli bağımlılıkları kur (Vercel ile uyumlu)
// Terminalde:
// npx create-react-app saglik-asistanim
// cd saglik-asistanim
// npm install firebase
// npm install -D tailwindcss postcss autoprefixer
// npx tailwindcss init -p

// tailwind.config.js
// module.exports = {
//   content: ["./src/**/*.{js,jsx,ts,tsx}"],
//   theme: { extend: {} },
//   plugins: [],
// };

// index.css
// @tailwind base;
// @tailwind components;
// @tailwind utilities;

// src/firebase.js
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";

const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_FIREBASE_APP_ID
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

export default db;

// src/App.js
import React, { useState, useEffect } from "react";
import db from "./firebase";
import { collection, addDoc, serverTimestamp } from "firebase/firestore";

function App() {
  const [view, setView] = useState("home");
  const [bloodPressure, setBloodPressure] = useState("");
  const [bloodSugar, setBloodSugar] = useState("");
  const [stepCount, setStepCount] = useState(0);
  const [aiMessage, setAiMessage] = useState("");

  useEffect(() => {
    const hour = new Date().getHours();
    if (hour === 8) {
      const message = `Günaydın! Bugün nasılsınız? Tansiyon: ${bloodPressure || "-"}, Şeker: ${bloodSugar || "-"}.`;
      setAiMessage(message);
      const utterance = new SpeechSynthesisUtterance(message);
      utterance.lang = "tr-TR";
      speechSynthesis.speak(utterance);
    }
  }, [bloodPressure, bloodSugar]);

  const saveToFirebase = async () => {
    try {
      await addDoc(collection(db, "dailyHealth"), {
        bloodPressure,
        bloodSugar,
        stepCount,
        timestamp: serverTimestamp(),
      });
      alert("Kayıt başarılı!");
    } catch (e) {
      alert("Kayıt hatası");
    }
  };

  if (view === "home") {
    return (
      <div className="min-h-screen bg-gradient-to-br from-blue-800 to-indigo-900 flex flex-col items-center justify-center text-white p-4">
        <h1 className="text-4xl font-bold">Sağlık Asistanım</h1>
        <div className="mt-8 grid gap-4 w-full max-w-md">
          <button className="bg-pink-500 py-4 rounded-xl text-lg" onClick={() => setView("veriGiris")}>📋 Veri Girişi</button>
          <button className="bg-green-500 py-4 rounded-xl text-lg" onClick={() => setView("veriGoster")}>📊 Verilerim</button>
          <button className="bg-yellow-500 py-4 rounded-xl text-lg" onClick={() => speechSynthesis.speak(new SpeechSynthesisUtterance("Merhaba! Sağlık durumunuzu kontrol edelim."))}>🗣️ Sesli Yardım</button>
        </div>
      </div>
    );
  }

  if (view === "veriGiris") {
    return (
      <div className="min-h-screen p-6 bg-white">
        <button className="mb-4" onClick={() => setView("home")}>← Geri</button>
        <h2 className="text-2xl font-bold mb-4">Veri Girişi</h2>
        <div className="space-y-4">
          <input className="w-full p-2 border rounded" placeholder="Tansiyon" value={bloodPressure} onChange={(e) => setBloodPressure(e.target.value)} />
          <input className="w-full p-2 border rounded" placeholder="Kan Şekeri" value={bloodSugar} onChange={(e) => setBloodSugar(e.target.value)} />
          <input className="w-full p-2 border rounded" placeholder="Adım Sayısı" value={stepCount} onChange={(e) => setStepCount(e.target.value)} />
          <button className="bg-blue-600 text-white py-2 px-4 rounded" onClick={saveToFirebase}>💾 Kaydet</button>
        </div>
      </div>
    );
  }

  if (view === "veriGoster") {
    return (
      <div className="min-h-screen p-6 bg-gray-50">
        <button className="mb-4" onClick={() => setView("home")}>← Geri</button>
        <h2 className="text-2xl font-bold">Kayıtlı Veriler</h2>
        <p>📊 (Buraya ileride Firestore'dan veri çekme kodu eklenebilir)</p>
      </div>
    );
  }

  return null;
}

export default App;

// .env dosyasına Firebase bilgilerini eklemeyi unutma (Vercel'de de tanımlamalısın):
// REACT_APP_FIREBASE_API_KEY=...
// REACT_APP_FIREBASE_AUTH_DOMAIN=...
// REACT_APP_FIREBASE_PROJECT_ID=...
// REACT_APP_FIREBASE_STORAGE_BUCKET=...
// REACT_APP_FIREBASE_MESSAGING_SENDER_ID=...
// REACT_APP_FIREBASE_APP_ID=...

// Vercel'e deploy için: GitHub'a gönder, Vercel ile bağla, otomatik yayınla!
