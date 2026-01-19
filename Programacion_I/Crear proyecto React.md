# Instalar Node con nvm (Windows)

```bat
nvm install 20.15.0
nvm use 20.15.0
node -v
npm -v
```

# Crear proyecto React

```bat
npx create-react-app miapp
cd miapp
npm start
```

# “Entorno virtual” práctico

Usa `nvm` por proyecto:

```bat
nvm use 20.15.0
```

Opcional por carpeta:

```bat
echo 20.15.0> .nvmrc
:: luego en cada sesión
nvm use (type .nvmrc)
```

---

A continuación, **reemplaza `src/App.js`** por cada ejemplo según necesites. Código mínimo, funcional con Create React App.

## 1) Comentarios

```jsx
export default function App(){ // Comentario en línea
  /* Comentario de bloque */
  return <div>Comentarios en código</div>
}
```

## 2) Matriz con id y nombres de carros

```jsx
export default function App(){
  const autos=[{id:1,n:"Civic"},{id:2,n:"Corolla"},{id:3,n:"Model 3"}];
  return <pre>{JSON.stringify(autos,null,2)}</pre>
}
```

## 3) Un elemento específico de la matriz

```jsx
export default function App(){
  const autos=[{id:1,n:"Civic"},{id:2,n:"Corolla"},{id:3,n:"Model 3"}];
  const uno=autos.find(a=>a.id===2);
  return <div>{uno.id} - {uno.n}</div>
}
```

## 4) Diccionario con los valores anteriores

```jsx
export default function App(){
  const dic={1:"Civic",2:"Corolla",3:"Model 3"};
  return <pre>{JSON.stringify(dic,null,2)}</pre>
}
```

## 5) Operadores aritméticos

```jsx
export default function App(){
  const a=7,b=3;
  return <div>{a+b},{a-b},{a*b},{a/b},{a%b}</div>
}
```

## 6) Operadores lógicos

```jsx
export default function App(){
  const a=true,b=false;
  return <div>{String(a&&b)} | {String(a||b)} | {!a&&"negado"}</div>
}
```

## 7) Operadores de comparación

```jsx
export default function App(){
  const x=5,y="5";
  return <div>{x==y?"==":"!="} | {x===y?"===":"!=="} | {x>3?"x>3":"no"}</div>
}
```

## 8) Operadores de asignación

```jsx
export default function App(){
  let x=10; x+=5; x*=2;
  return <div>{x}</div>
}
```

## 9) Condicionales if/else

```jsx
export default function App(){
  const edad=18; let r="";
  if(edad>=18) r="Adulto"; else r="Menor";
  return <div>{r}</div>
}
```

## 10) Bucle for

```jsx
export default function App(){
  let s=[]; for(let i=1;i<=5;i++) s.push(i);
  return <div>{s.join(",")}</div>
}
```

## 11) while

```jsx
export default function App(){
  let i=3,s=[];
  while(i>0){s.push(i);i--;}
  return <div>{s.join(" ")}</div>
}
```

## 12) do…while

```jsx
export default function App(){
  let i=0,s=[];
  do{s.push(i);i++;}while(i<3);
  return <div>{s.join(",")}</div>
}
```

## 13) Función sin parámetros

```jsx
function saluda(){return "Hola";}
export default function App(){return <div>{saluda()}</div>}
```

## 14) Función con un parámetro de una variable del código

```jsx
function dup(n){return n*2;}
export default function App(){
  const x=7;
  return <div>{dup(x)}</div>
}
```

## 15) Función con parámetro desde un input HTML

```jsx
import {useState} from "react";
function cuadrado(n){return n*n;}
export default function App(){
  const [v,setV]=useState("4");
  return (
    <div>
      <input value={v} onChange={e=>setV(e.target.value)} />
      <div>{cuadrado(Number(v)||0)}</div>
    </div>
  );
}
```

## 16) Enlaces con HTML

```jsx
export default function App(){
  return <a href="https://react.dev" target="_blank" rel="noreferrer">Ir a React</a>
}
```

## 17) Imágenes con HTML

```jsx
export default function App(){
  return <img alt="logo" src="https://via.placeholder.com/120" />
}
```

## 18) Listas con HTML

```jsx
export default function App(){
  const items=["uno","dos","tres"];
  return <ul>{items.map(x=><li key={x}>{x}</li>)}</ul>
}
```

## 19) Tablas con HTML

```jsx
export default function App(){
  const rows=[{id:1,n:"A"},{id:2,n:"B"}];
  return (
    <table><tbody>
      {rows.map(r=><tr key={r.id}><td>{r.id}</td><td>{r.n}</td></tr>)}
    </tbody></table>
  );
}
```

## 20) Formularios con HTML

```jsx
import {useState} from "react";
export default function App(){
  const [f,setF]=useState({n:"",e:""});
  const onC=e=>setF({...f,[e.target.name]:e.target.value});
  const onS=e=>{e.preventDefault(); alert(JSON.stringify(f));};
  return (
    <form onSubmit={onS}>
      <input name="n" placeholder="Nombre" value={f.n} onChange={onC}/>
      <input name="e" placeholder="Email" value={f.e} onChange={onC}/>
      <button>Enviar</button>
    </form>
  );
}
```

## 21) Archivo `.css` para fuentes y texto

Crea `src/styles.css`:

```css
/* styles.css */
body{font-family:Arial, Helvetica, sans-serif;}
h1{font-size:2rem;color:#1f2937;}
p{font-size:1rem;color:#374151;}
.small{font-size:.875rem}
.big{font-size:1.5rem}
.red{color:#dc2626}
.blue{color:#2563eb}
.bold{font-weight:700}
```

Importa en `src/index.js`:

```jsx
import './styles.css';
```

