<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Reportes de laboratorios</title>
<style>
:root {
  --bg: #f5f6f8; --surface: #ffffff; --text: #1f2933; --muted: #6b7280;
  --accent: #2563eb; --ok: #16a34a; --warn: #d97706; --border: #e5e7eb;
  color-scheme: light;
}
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    --bg: #0f1419; --surface: #1a2129; --text: #e5e7eb; --muted: #9ca3af;
    --accent: #60a5fa; --ok: #4ade80; --warn: #fbbf24; --border: #2d3742;
    color-scheme: dark;
  }
}
* { box-sizing: border-box; }
body {
  margin: 0; font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  background: var(--bg); color: var(--text);
  padding: env(safe-area-inset-top, 0px) 0 env(safe-area-inset-bottom, 0px);
}
header {
  background: var(--surface); border-bottom: 1px solid var(--border);
  padding: 14px 16px; display: flex; justify-content: space-between;
  align-items: center; gap: 8px; flex-wrap: wrap;
}
header h1 { font-size: 18px; margin: 0; }
.user-box { font-size: 14px; color: var(--muted); }
main { max-width: 760px; margin: 0 auto; padding: 16px; }
.card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 16px; margin-bottom: 16px; }
h2 { font-size: 16px; margin: 0 0 12px; }
label { display: block; font-size: 14px; margin-bottom: 4px; color: var(--muted); }
input, select, textarea {
  width: 100%; font: inherit; padding: 10px; border: 1px solid var(--border);
  border-radius: 8px; background: var(--bg); color: var(--text); margin-bottom: 12px;
}
textarea { min-height: 70px; resize: vertical; }
button {
  font: inherit; padding: 10px 16px; border: none; border-radius: 8px;
  background: var(--accent); color: #fff; cursor: pointer;
}
button.secondary { background: transparent; color: var(--accent); border: 1px solid var(--accent); }
button.ok { background: var(--ok); }
.row { display: flex; gap: 8px; flex-wrap: wrap; align-items: center; }
.sb { justify-content: space-between; }
.tabs { display: flex; gap: 8px; margin-bottom: 16px; flex-wrap: wrap; }
.tabs button { background: var(--surface); color: var(--text); border: 1px solid var(--border); }
.tabs button.active { background: var(--accent); color: #fff; border-color: var(--accent); }
.badge { display: inline-block; font-size: 12px; padding: 2px 8px; border-radius: 999px; border: 1px solid currentColor; white-space: nowrap; }
.badge.pendiente { color: var(--warn); }
.badge.en_proceso { color: var(--accent); }
.badge.solucionado { color: var(--ok); }
.report { border-top: 1px solid var(--border); padding: 12px 0; }
.report:first-child { border-top: none; }
.foto { max-width: 100%; border-radius: 8px; margin-top: 8px; max-height: 240px; object-fit: cover; }
.meta { font-size: 13px; color: var(--muted); margin-top: 4px; }
.notif { font-size: 14px; padding: 10px; border-radius: 8px; background: var(--bg); border-left: 4px solid var(--ok); margin: 8px 0; }
.mensaje { border-left: 3px solid var(--border); padding: 4px 0 4px 10px; margin: 8px 0; }
.composer { margin-top: 10px; padding-top: 10px; border-top: 1px dashed var(--border); }
.composer input[type="file"] { font-size: 13px; }
.hidden { display: none !important; }
.empty { color: var(--muted); font-size: 14px; }
.msg { font-size: 13px; color: var(--muted); margin: 0 0 8px; }
.msg.error { color: var(--warn); }
.demo-note { font-size: 13px; color: var(--muted); background: var(--surface); border: 1px dashed var(--border); border-radius: 8px; padding: 10px; margin-bottom: 16px; }
details summary { cursor: pointer; }
</style>
</head>
<body>

<header>
  <h1>Reportes de laboratorios</h1>
  <div class="row">
    <span class="user-box" id="userBox"></span>
    <button id="logoutBtn" class="secondary hidden" style="padding:6px 12px">Cerrar sesión</button>
  </div>
</header>

<main>
  <div class="demo-note">
    Prototipo de demostración. Las cuentas y los reportes se guardan en este navegador. No hay verificación de correo real y los datos no se comparten entre dispositivos. Los archivos adjuntos grandes pueden no descargarse desde la página publicada.
  </div>

  <!-- AUTENTICACIÓN -->
  <section class="card" id="authView">
    <h2 id="tituloAuth">Iniciar sesión</h2>
    <div id="camposRegistro" class="hidden">
      <label for="regNombre">Nombre completo</label>
      <input id="regNombre" placeholder="Ej. Ana López">
      <label for="regTipo">Tipo de cuenta</label>
      <select id="regTipo">
        <option value="reportero">Usuario (reporta daños en laboratorios)</option>
        <option value="administrativo">Administrativo (recibe y envía a técnicos)</option>
        <option value="tecnico">Técnico (repara y marca como solucionado)</option>
      </select>
      <div id="campoCodigo" class="hidden">
        <label for="regCodigo">Código de personal</label>
        <input id="regCodigo" placeholder="Código que te dará la institución">
      </div>
    </div>
    <div id="selectorRol">
      <label for="loginRol">Entrar como</label>
      <select id="loginRol">
        <option value="reportero">Usuario común</option>
        <option value="administrativo">Administrativo</option>
        <option value="tecnico">Técnico</option>
      </select>
    </div>
    <label for="loginCorreo">Correo electrónico</label>
    <input id="loginCorreo" type="email" autocomplete="email" placeholder="correo@escuela.edu">
    <label for="loginPass">Contraseña</label>
    <input id="loginPass" type="password" autocomplete="current-password" placeholder="Mínimo 6 caracteres">
    <div id="camposRegistro2" class="hidden">
      <label for="regPass2">Confirmar contraseña</label>
      <input id="regPass2" type="password" autocomplete="new-password">
    </div>
    <p id="authMsg" class="msg error"></p>
    <div class="demo-note" id="notaDemo">
      <strong>Cuentas de prueba</strong><br>
      Administrativo: admin01@demo.local / admin01<br>
      Técnico: tecnico01@demo.local / tecnico01<br>
      Usuario común: usuario01@demo.local / usuario01
    </div>
    <div class="row">
      <button id="loginBtn">Entrar</button>
      <button id="toggleRegBtn" class="secondary">Crear cuenta nueva</button>
    </div>
  </section>

  <div id="appView" class="hidden">
    <nav class="tabs" id="tabs"></nav>

    <!-- USUARIO: NUEVO REPORTE -->
    <section class="card vista hidden" id="vNuevo">
      <h2>Reportar daño o falla</h2>
      <label for="repLab">Laboratorio</label>
      <select id="repLab"></select>
      <label for="repTitulo">Título</label>
      <input id="repTitulo" placeholder="Ej. Microscopio sin enfoque">
      <label for="repDesc">Descripción</label>
      <textarea id="repDesc" placeholder="Describe el daño o la falla"></textarea>
      <label for="repFoto">Foto (opcional)</label>
      <input id="repFoto" type="file" accept="image/*">
      <span id="repFotoNombre" class="meta" style="display:block;margin-bottom:8px;">Ninguna foto seleccionada</span>
      <img id="repPreview" class="foto hidden" alt="Vista previa">
      <div style="margin-top:12px"><button id="enviarReporteBtn">Enviar reporte</button></div>
      <p id="repMsg" class="msg"></p>
    </section>

    <!-- USUARIO: MIS REPORTES -->
    <section class="card vista hidden" id="vMis">
      <h2>Mis reportes</h2>
      <div id="listaMis"></div>
    </section>

    <!-- ADMINISTRATIVO Y TÉCNICO: BUZÓN -->
    <section class="card vista hidden" id="vBuzon">
      <h2 id="tituloBuzon">Buzón</h2>
      <div id="contenidoBuzon"></div>
    </section>

    <!-- HISTORIAL POR LABORATORIO -->
    <section class="card vista hidden" id="vHistorial">
      <h2>Historial por laboratorio</h2>
      <label for="histLab">Laboratorio</label>
      <select id="histLab"></select>
      <div id="listaHistorial"></div>
    </section>

    <!-- NOTIFICACIONES -->
    <section class="card vista hidden" id="vNotif">
      <h2>Notificaciones</h2>
      <div id="listaNotif"></div>
    </section>
  </div>
</main>

<script>
const LABORATORIOS = [
  "Laboratorio de Sistemas", "Laboratorio de Electrónica", "Laboratorio de Mecatrónica",
  "Laboratorio de Química", "Laboratorio de Biología", "Laboratorio de Civil y Materiales",
  "Laboratorio de Cómputo", "Laboratorio de Idiomas"
];
const CODIGOS = { administrativo: "ADMIN-2026", tecnico: "TECNICO-2026" };
const ROLES = { reportero: "Usuario", administrativo: "Administrativo", tecnico: "Técnico" };
const ETIQUETAS = { pendiente: "Pendiente", en_proceso: "En proceso", solucionado: "Solucionado" };
const MAX_ARCHIVO = 2 * 1024 * 1024;
const CLAVE_DATOS = "laboratorios_datos";
const CLAVE_SESION = "laboratorios_sesion";

let estado = { cuentas: [], reportes: [], notificaciones: [], contador: 1 };
let usuario = null;
let modoRegistro = false;
let fotoActual = null;
let filtroBuzon = "pendiente";
let archivosPend = {};

const $ = id => document.getElementById(id);

function ahora() { return new Date().toISOString(); }
function fmt(s) { return new Date(s).toLocaleString("es-MX", { dateStyle: "medium", timeStyle: "short" }); }
function escapar(t) { const d = document.createElement("div"); d.textContent = t ?? ""; return d.innerHTML; }
function msg(id, texto, error = false) { $(id).textContent = texto; $(id).classList.toggle("error", error); }

function guardar() {
  try { localStorage.setItem(CLAVE_DATOS, JSON.stringify(estado)); }
  catch (err) { alert("No hay espacio suficiente en el navegador. Usa archivos o fotos más pequeños."); }
}
function guardarSesion() {
  try {
    if (usuario) localStorage.setItem(CLAVE_SESION, usuario.correo);
    else localStorage.removeItem(CLAVE_SESION);
  } catch (err) {}
}
function cargar() {
  try {
    const datos = JSON.parse(localStorage.getItem(CLAVE_DATOS) || "null");
    if (datos) estado = Object.assign(estado, datos);
    estado.reportes.forEach(r => {
      if (r.estado === "reenviado") r.estado = "en_proceso";
      r.mensajes = r.mensajes || [];
      if (!r.asignadoA && r.reenvios && r.reenvios.length) r.asignadoA = r.reenvios[0].para;
    });
    const correo = localStorage.getItem(CLAVE_SESION);
    const cuenta = estado.cuentas.find(c => c.correo === correo);
    if (cuenta) { usuario = { nombre: cuenta.nombre, correo: cuenta.correo, rol: cuenta.rol }; abrirApp(); }
  } catch (err) {}
}

function notificar(para, texto) { estado.notificaciones.push({ para, texto, fecha: ahora() }); }
function nombreDe(correo) { const c = estado.cuentas.find(x => x.correo === correo); return c ? c.nombre : correo; }
function tecnicos() { return estado.cuentas.filter(c => c.rol === "tecnico"); }
function administrativos() { return estado.cuentas.filter(c => c.rol === "administrativo"); }
function buscar(id) { return estado.reportes.find(x => x.id === id); }

// Quiénes siguen un reporte: el usuario que lo reportó, los administrativos y los técnicos asignados
function participantes(r) {
  const set = new Set([r.autorCorreo]);
  administrativos().forEach(a => set.add(a.correo));
  if (r.asignadoA === "todos") tecnicos().forEach(t => set.add(t.correo));
  else if (r.asignadoA) set.add(r.asignadoA);
  return [...set];
}
function notificarA(r, texto, excluir) {
  participantes(r).filter(c => c !== excluir).forEach(c => notificar(c, texto));
}

async function hashPassword(texto) {
  const buf = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(texto));
  return Array.from(new Uint8Array(buf)).map(b => b.toString(16).padStart(2, "0")).join("");
}

function mostrarError(texto) { msg("authMsg", texto, true); }

function toggleRegistro() {
  modoRegistro = !modoRegistro;
  $("camposRegistro").classList.toggle("hidden", !modoRegistro);
  $("camposRegistro2").classList.toggle("hidden", !modoRegistro);
  $("selectorRol").classList.toggle("hidden", modoRegistro);
  $("tituloAuth").textContent = modoRegistro ? "Crear cuenta" : "Iniciar sesión";
  $("loginBtn").textContent = modoRegistro ? "Registrarme" : "Entrar";
  $("toggleRegBtn").textContent = modoRegistro ? "Ya tengo cuenta" : "Crear cuenta nueva";
  mostrarError("");
}

async function entrar() {
  const correo = $("loginCorreo").value.trim().toLowerCase();
  const pass = $("loginPass").value;

  if (modoRegistro) {
    const nombre = $("regNombre").value.trim();
    const tipo = $("regTipo").value;
    if (!nombre) return mostrarError("Escribe tu nombre.");
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(correo)) return mostrarError("Escribe un correo válido.");
    if (pass.length < 6) return mostrarError("La contraseña debe tener al menos 6 caracteres.");
    if (pass !== $("regPass2").value) return mostrarError("Las contraseñas no coinciden.");
    if (tipo !== "reportero" && $("regCodigo").value.trim() !== CODIGOS[tipo]) return mostrarError("Código de personal incorrecto.");
    if (estado.cuentas.some(c => c.correo === correo)) return mostrarError("Ya existe una cuenta con ese correo.");
    estado.cuentas.push({ nombre, correo, rol: tipo, hash: await hashPassword(pass) });
    guardar();
    usuario = { nombre, correo, rol: tipo };
    abrirApp();
    return;
  }

  const cuenta = estado.cuentas.find(c => c.correo === correo);
  const hash = cuenta ? await hashPassword(pass) : null;
  if (!cuenta || cuenta.hash !== hash) return mostrarError("Correo o contraseña incorrectos.");
  const rolElegido = $("loginRol").value;
  if (cuenta.rol !== rolElegido) return mostrarError(`Esta cuenta no es de tipo ${ROLES[rolElegido]}. Elige el tipo correcto.`);
  usuario = { nombre: cuenta.nombre, correo: cuenta.correo, rol: cuenta.rol };
  abrirApp();
}

function abrirApp() {
  guardarSesion();
  filtroBuzon = usuario.rol === "tecnico" ? "en_proceso" : "pendiente";
  $("authView").classList.add("hidden");
  $("appView").classList.remove("hidden");
  $("userBox").textContent = `${usuario.nombre} · ${ROLES[usuario.rol]}`;
  $("logoutBtn").classList.remove("hidden");
  construirTabs();
  llenarSelects();
  const primera = tabsPara(usuario.rol)[0];
  mostrarVista(primera[0], primera[2]);
}

function salir() {
  usuario = null;
  fotoActual = null;
  archivosPend = {};
  guardarSesion();
  $("appView").classList.add("hidden");
  $("authView").classList.remove("hidden");
  $("logoutBtn").classList.add("hidden");
  $("userBox").textContent = "";
  $("loginCorreo").value = "";
  $("loginPass").value = "";
  $("regPass2").value = "";
  mostrarError("");
}

function tabsPara(rol) {
  if (rol === "administrativo" || rol === "tecnico") return [
    ["vBuzon", "Buzón", renderBuzon],
    ["vHistorial", "Historial por laboratorio", renderHistorial],
    ["vNotif", "Notificaciones", renderNotif]
  ];
  return [
    ["vNuevo", "Reportar daño", null],
    ["vMis", "Mis reportes", renderMis],
    ["vNotif", "Notificaciones", renderNotif]
  ];
}

function construirTabs() {
  const tabs = tabsPara(usuario.rol);
  $("tabs").innerHTML = tabs.map(([id, label]) => `<button data-vista="${id}">${label}</button>`).join("");
  $("tabs").querySelectorAll("button").forEach((b, i) => {
    b.onclick = () => mostrarVista(tabs[i][0], tabs[i][2]);
  });
}

function mostrarVista(id, render) {
  document.querySelectorAll(".vista").forEach(v => v.classList.add("hidden"));
  $(id).classList.remove("hidden");
  document.querySelectorAll("#tabs button").forEach(b => b.classList.toggle("active", b.dataset.vista === id));
  if (render) render();
}

function llenarSelects() {
  const opts = LABORATORIOS.map(l => `<option>${l}</option>`).join("");
  $("repLab").innerHTML = opts;
  $("histLab").innerHTML = opts;
}

/* ---------- FOTOS Y ARCHIVOS ---------- */

function comprimirImagen(file) {
  return new Promise((resolve, reject) => {
    const url = URL.createObjectURL(file);
    const img = new Image();
    img.onload = () => {
      const escala = Math.min(1, 900 / Math.max(img.width, img.height));
      const canvas = document.createElement("canvas");
      canvas.width = Math.round(img.width * escala);
      canvas.height = Math.round(img.height * escala);
      canvas.getContext("2d").drawImage(img, 0, 0, canvas.width, canvas.height);
      URL.revokeObjectURL(url);
      resolve(canvas.toDataURL("image/jpeg", 0.7));
    };
    img.onerror = () => { URL.revokeObjectURL(url); reject(new Error("imagen")); };
    img.src = url;
  });
}

$("repFoto").addEventListener("change", async e => {
  const file = e.target.files && e.target.files[0];
  fotoActual = null;
  $("repPreview").classList.add("hidden");
  $("repFotoNombre").textContent = file ? `Foto: ${file.name}` : "Ninguna foto seleccionada";
  if (!file) return;
  try {
    fotoActual = await comprimirImagen(file);
    $("repPreview").src = fotoActual;
    $("repPreview").classList.remove("hidden");
  } catch (err) {
    msg("repMsg", "No se pudo cargar esa foto. Prueba con otra imagen (JPG o PNG).", true);
    e.target.value = "";
    $("repFotoNombre").textContent = "Ninguna foto seleccionada";
  }
});

// Archivos adjuntos a mensajes de administrativos y técnicos (delegación, porque las tarjetas se redibujan)
document.addEventListener("change", e => {
  const t = e.target;
  if (!(t.matches && t.matches("input[type='file'][data-rid]"))) return;
  const id = Number(t.dataset.rid);
  const file = t.files[0];
  delete archivosPend[id];
  if (!file) return;
  if (file.size > MAX_ARCHIVO) {
    alert("El archivo es muy grande (máximo 2 MB).");
    t.value = "";
    return;
  }
  const reader = new FileReader();
  reader.onload = ev => { archivosPend[id] = { nombre: file.name, tipo: file.type || "", data: ev.target.result }; };
  reader.readAsDataURL(file);
});

/* ---------- USUARIO ---------- */

$("enviarReporteBtn").onclick = () => {
  const titulo = $("repTitulo").value.trim();
  const desc = $("repDesc").value.trim();
  const lab = $("repLab").value;
  if (!titulo || !desc) return msg("repMsg", "Escribe un título y una descripción.", true);

  const id = estado.contador++;
  estado.reportes.push({
    id, lab, titulo, descripcion: desc, foto: fotoActual,
    autorCorreo: usuario.correo, autorNombre: usuario.nombre,
    estado: "pendiente", asignadoA: null, fecha: ahora(),
    historial: [{ fecha: ahora(), texto: `Reporte creado por ${usuario.nombre}` }],
    mensajes: []
  });
  notificar(usuario.correo, `Recibimos tu reporte "${titulo}" de ${lab}.`);
  administrativos().forEach(a => notificar(a.correo, `Nuevo reporte de ${lab}: "${titulo}".`));
  guardar();

  $("repTitulo").value = "";
  $("repDesc").value = "";
  $("repFoto").value = "";
  $("repFotoNombre").textContent = "Ninguna foto seleccionada";
  $("repPreview").classList.add("hidden");
  fotoActual = null;
  msg("repMsg", "Reporte enviado correctamente.");
};

function renderMis() {
  const mios = estado.reportes.filter(r => r.autorCorreo === usuario.correo).sort((a, b) => b.fecha.localeCompare(a.fecha));
  $("listaMis").innerHTML = mios.length
    ? mios.map(r => tarjeta(r)).join("")
    : `<p class="empty">Aún no has reportado daños.</p>`;
}

/* ---------- TARJETAS Y MENSAJES ---------- */

function hilo(r) {
  if (!r.mensajes.length) return `<p class="meta">Sin mensajes todavía.</p>`;
  return r.mensajes.map(m => {
    const adjunto = m.archivo
      ? (m.archivo.tipo.startsWith("image/")
          ? `<img class="foto" src="${m.archivo.data}" alt="${escapar(m.archivo.nombre)}">`
          : `<div class="meta">📎 ${escapar(m.archivo.nombre)}</div>`)
      : "";
    return `<div class="mensaje">
      <div class="meta"><strong>${escapar(m.autorNombre)}</strong> (${ROLES[m.rol]}) · ${fmt(m.fecha)}</div>
      ${m.texto ? `<p style="margin:4px 0">${escapar(m.texto)}</p>` : ""}
      ${adjunto}
    </div>`;
  }).join("");
}

function composer(r, extra = "") {
  return `<div class="composer">
    <textarea id="txt_${r.id}" placeholder="Escribe un mensaje o nota"></textarea>
    <label class="meta">Adjuntar archivo (máx. 2 MB)</label>
    <input type="file" data-rid="${r.id}">
    <div class="row">${extra}<button class
