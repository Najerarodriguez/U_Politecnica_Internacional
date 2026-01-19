Se va a utilizar **React + Firebase (Firestore)**.

```bash
npm install firebase
```

# firebase.js (mismo para todos los escenarios)

```javascript
// src/firebase.js
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";

// ⬇️ Reemplaza con tu configuración de Firebase
const firebaseConfig = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_AUTH_DOMAIN",
  projectId: "TU_PROJECT_ID",
  storageBucket: "TU_STORAGE_BUCKET",
  messagingSenderId: "TU_SENDER_ID",
  appId: "TU_APP_ID",
};

const app = initializeApp(firebaseConfig);
export const db = getFirestore(app);

// Usaremos la colección "textos" con un único campo "texto"
```

---

# 1) Formulario con un solo input que guarda un texto en Firebase

```javascript
// src/App.js
import React, { useState } from "react";
import { db } from "./firebase";
import { collection, addDoc, serverTimestamp } from "firebase/firestore";

function App() {
  const [texto, setTexto] = useState("");
  const [estado, setEstado] = useState(null); // "ok" | "error"

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!texto.trim()) return;
    try {
      await addDoc(collection(db, "textos"), {
        texto: texto.trim(),
        createdAt: serverTimestamp(),
      });
      setTexto("");
      setEstado("ok");
      setTimeout(() => setEstado(null), 1500);
    } catch (err) {
      console.error(err);
      setEstado("error");
      setTimeout(() => setEstado(null), 2000);
    }
  };

  return (
    <div style={{ maxWidth: 480, margin: "40px auto", fontFamily: "sans-serif" }}>
      <h1>Guardar texto</h1>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          placeholder="Escribe algo..."
          value={texto}
          onChange={(e) => setTexto(e.target.value)}
          style={{ width: "100%", padding: 10 }}
        />
        <button type="submit" style={{ marginTop: 10 }}>Guardar</button>
      </form>
      {estado === "ok" && <p style={{ color: "green" }}>¡Guardado!</p>}
      {estado === "error" && <p style={{ color: "crimson" }}>Hubo un error</p>}
    </div>
  );
}

export default App;
```

---

# 2) Tabla con todos los datos de Firebase (un solo campo texto)

```javascript
// src/App.js
import React, { useEffect, useState } from "react";
import { db } from "./firebase";
import { collection, onSnapshot, query, orderBy } from "firebase/firestore";

function App() {
  const [filas, setFilas] = useState([]);

  useEffect(() => {
    const q = query(collection(db, "textos"), orderBy("createdAt", "desc"));
    const unsub = onSnapshot(q, (snap) => {
      const datos = snap.docs.map((d) => ({ id: d.id, ...d.data() }));
      setFilas(datos);
    });
    return () => unsub();
  }, []);

  return (
    <div style={{ maxWidth: 720, margin: "40px auto", fontFamily: "sans-serif" }}>
      <h1>Lista de textos</h1>
      <table width="100%" border="1" cellPadding="8" style={{ borderCollapse: "collapse" }}>
        <thead>
          <tr>
            <th>ID</th>
            <th>Texto</th>
          </tr>
        </thead>
        <tbody>
          {filas.map((f) => (
            <tr key={f.id}>
              <td style={{ fontFamily: "monospace" }}>{f.id}</td>
              <td>{f.texto ?? "(sin texto)"}</td>
            </tr>
          ))}
          {filas.length === 0 && (
            <tr>
              <td colSpan="2" style={{ textAlign: "center", color: "#666" }}>
                No hay datos
              </td>
            </tr>
          )}
        </tbody>
      </table>
    </div>
  );
}

export default App;
```

---

# 3) Input + botón “Añadir” y debajo mostrar todos los datos

```javascript
// src/App.js
import React, { useEffect, useState } from "react";
import { db } from "./firebase";
import {
  collection,
  addDoc,
  onSnapshot,
  query,
  orderBy,
  serverTimestamp,
} from "firebase/firestore";

function App() {
  const [texto, setTexto] = useState("");
  const [items, setItems] = useState([]);

  useEffect(() => {
    const q = query(collection(db, "textos"), orderBy("createdAt", "desc"));
    const unsub = onSnapshot(q, (snap) => {
      setItems(snap.docs.map((d) => ({ id: d.id, ...d.data() })));
    });
    return () => unsub();
  }, []);

  const agregar = async () => {
    if (!texto.trim()) return;
    await addDoc(collection(db, "textos"), {
      texto: texto.trim(),
      createdAt: serverTimestamp(),
    });
    setTexto("");
  };

  return (
    <div style={{ maxWidth: 520, margin: "40px auto", fontFamily: "sans-serif" }}>
      <h1>Textos</h1>
      <div style={{ display: "flex", gap: 8 }}>
        <input
          value={texto}
          onChange={(e) => setTexto(e.target.value)}
          placeholder="Escribe y añade"
          style={{ flex: 1, padding: 10 }}
        />
        <button onClick={agregar}>Añadir</button>
      </div>

      <ul style={{ marginTop: 20, paddingLeft: 20 }}>
        {items.map((it) => (
          <li key={it.id}>{it.texto ?? "(sin texto)"}</li>
        ))}
        {items.length === 0 && <li style={{ color: "#666" }}>No hay datos</li>}
      </ul>
    </div>
  );
}

export default App;
```

---

# 4) Input + añadir + lista con botones para eliminar y modificar cada dato

```javascript
// src/App.js
import React, { useEffect, useState } from "react";
import { db } from "./firebase";
import {
  collection,
  addDoc,
  onSnapshot,
  query,
  orderBy,
  serverTimestamp,
  deleteDoc,
  doc,
  updateDoc,
} from "firebase/firestore";

function App() {
  const [texto, setTexto] = useState("");
  const [items, setItems] = useState([]);
  const [editandoId, setEditandoId] = useState(null);
  const [editValor, setEditValor] = useState("");

  useEffect(() => {
    const q = query(collection(db, "textos"), orderBy("createdAt", "desc"));
    const unsub = onSnapshot(q, (snap) => {
      setItems(snap.docs.map((d) => ({ id: d.id, ...d.data() })));
    });
    return () => unsub();
  }, []);

  const agregar = async () => {
    if (!texto.trim()) return;
    await addDoc(collection(db, "textos"), {
      texto: texto.trim(),
      createdAt: serverTimestamp(),
    });
    setTexto("");
  };

  const eliminar = async (id) => {
    await deleteDoc(doc(db, "textos", id));
  };

  const empezarEditar = (item) => {
    setEditandoId(item.id);
    setEditValor(item.texto ?? "");
  };

  const cancelarEditar = () => {
    setEditandoId(null);
    setEditValor("");
  };

  const guardarEditar = async () => {
    if (!editandoId) return;
    await updateDoc(doc(db, "textos", editandoId), { texto: editValor.trim() });
    cancelarEditar();
  };

  return (
    <div style={{ maxWidth: 600, margin: "40px auto", fontFamily: "sans-serif" }}>
      <h1>CRUD de textos</h1>

      <div style={{ display: "flex", gap: 8 }}>
        <input
          value={texto}
          onChange={(e) => setTexto(e.target.value)}
          placeholder="Escribe y añade"
          style={{ flex: 1, padding: 10 }}
        />
        <button onClick={agregar}>Añadir</button>
      </div>

      <div style={{ marginTop: 24 }}>
        {items.length === 0 ? (
          <p style={{ color: "#666" }}>No hay datos</p>
        ) : (
          <ul style={{ listStyle: "none", paddingLeft: 0 }}>
            {items.map((it) => (
              <li
                key={it.id}
                style={{
                  display: "flex",
                  gap: 8,
                  alignItems: "center",
                  marginBottom: 8,
                }}
              >
                {editandoId === it.id ? (
                  <>
                    <input
                      value={editValor}
                      onChange={(e) => setEditValor(e.target.value)}
                      style={{ flex: 1, padding: 8 }}
                    />
                    <button onClick={guardarEditar}>Guardar</button>
                    <button onClick={cancelarEditar}>Cancelar</button>
                  </>
                ) : (
                  <>
                    <span style={{ flex: 1 }}>{it.texto ?? "(sin texto)"}</span>
                    <button onClick={() => empezarEditar(it)}>Modificar</button>
                    <button onClick={() => eliminar(it.id)}>Eliminar</button>
                  </>
                )}
              </li>
            ))}
          </ul>
        )}
      </div>
    </div>
  );
}

export default App;
```

---
