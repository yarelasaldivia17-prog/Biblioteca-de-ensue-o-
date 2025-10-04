# Biblioteca-de-ensueño-
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Biblioteca de Ensueños</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <div class="logo">Biblioteca de Ensueños</div>
        <nav>
            <a href="#registro" id="navRegistro">Registro/Login</a>
            <a href="#premium">Premium</a>
            <a href="#historias">Historias</a>
            <a href="#recompensa">Recompensa diaria</a>
        </nav>
    </header>

    <section class="hero">
        <h1>Bienvenido a la Biblioteca de Ensueños</h1>
        <p>Explora historias, gana recompensas y disfruta contenido Premium.</p>
    </section>

    <!-- Registro/Login -->
    <section id="registro" class="card-section">
        <h2>Registro / Login</h2>
        <form id="userForm">
            <input type="text" placeholder="Nombre de usuario" id="username" required>
            <input type="password" placeholder="Contraseña" id="password" required>
            <button type="submit" id="registerBtn">Registrarse</button>
            <button type="button" id="loginBtn">Iniciar sesión</button>
        </form>
        <p id="userMsg"></p>
    </section>

    <!-- Premium -->
    <section id="premium" class="card-section">
        <h2>Contenido Premium</h2>
        <p>Suscríbete para acceder a historias exclusivas y beneficios especiales.</p>
        <button id="suscribirseBtn">Suscribirse</button>
        <p id="premiumMsg"></p>
    </section>

    <!-- Historias -->
    <section id="historias" class="card-section">
        <h2>Historias</h2>
        <div id="historiasLista"></div>
    </section>

    <!-- Recompensa diaria -->
    <section id="recompensa" class="card-section">
        <h2>Recompensa diaria</h2>
        <p>Haz clic en el botón para recibir tu recompensa del día:</p>
        <button id="recompensaBtn">Obtener recompensa</button>
        <p id="recompensaMsg"></p>
    </section>

    <footer>
        <p>© 2025 Biblioteca de Ensueños. Todos los derechos reservados.</p>
    </footer>

    <script src="script.js"></script>
</body>
</html>
/* Reset básico */
* { margin:0; padding:0; box-sizing:border-box; font-family: Arial, sans-serif; }

body { background-color: #fdf5e6; color: #333; }

header {
    display:flex; justify-content:space-between; align-items:center;
    padding:1rem 2rem; background-color:#fff;
    box-shadow:0 2px 4px rgba(0,0,0,0.1);
}

header .logo { font-size:1.5rem; font-weight:bold; }
header nav a {
    margin-left:1rem; text-decoration:none; color:#333; font-weight:bold;
    transition: color 0.3s;
}
header nav a:hover { color:#b5895b; }

.hero { text-align:center; padding:4rem 2rem; background-color:#fff; margin-bottom:2rem; }
.hero h1 { font-size:2.5rem; margin-bottom:1rem; }

.card-section {
    max-width:600px; margin:2rem auto; padding:2rem;
    background-color:#fff; border-radius:12px;
    box-shadow:0 4px 8px rgba(0,0,0,0.1);
}

.card-section h2 { margin-bottom:1rem; color:#b5895b; }

.card-section form input {
    display:block; width:100%; padding:0.8rem; margin-bottom:1rem;
    border:1px solid #ccc; border-radius:6px;
}

.card-section button {
    padding:0.8rem 1.5rem; border:none; border-radius:6px;
    background-color:#b5895b; color:#fff; font-weight:bold; cursor:pointer;
    transition: background-color 0.3s;
}
.card-section button:hover { background-color:#a6784a; }

footer { text-align:center; padding:1.5rem; background-color:#fff; margin-top:2rem;
    box-shadow:0 -2px 4px rgba(0,0,0,0.1);
}

/* Historias */
.historiaCard {
    background-color: #fdf5e6; border:1px solid #b5895b;
    border-radius:10px; padding:1rem; margin-bottom:1rem;
}
.historiaCard h3 { margin-bottom:0.5rem; color:#b5895b; }

/* Responsive */
@media (max-width:768px){
    header { flex-direction:column; align-items:flex-start; }
    header nav { margin-top:0.5rem; }
}
// --- Variables y estado ---
const userForm = document.getElementById('userForm');
const usernameInput = document.getElementById('username');
const passwordInput = document.getElementById('password');
const userMsg = document.getElementById('userMsg');
let currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;

// --- Función para actualizar usuario ---
function updateUser(user){
    let users = JSON.parse(localStorage.getItem('users')) || [];
    const index = users.findIndex(u=>u.username===user.username);
    if(index!==-1){ users[index] = user; localStorage.setItem('users',JSON.stringify(users)); }
    localStorage.setItem('currentUser', JSON.stringify(user));
    currentUser = user;
    cargarHistorias();
}

// --- Registro ---
document.getElementById('registerBtn').addEventListener('click', e=>{
    e.preventDefault();
    const username = usernameInput.value.trim();
    const password = passwordInput.value.trim();
    if(!username || !password) return alert('Rellena todos los campos');

    let users = JSON.parse(localStorage.getItem('users')) || [];
    if(users.find(u=>u.username===username)){ userMsg.textContent='El usuario ya existe.'; return; }

    let newUser = {username, password, premium:false, lastReward:null};
    users.push(newUser);
    localStorage.setItem('users', JSON.stringify(users));
    localStorage.setItem('currentUser', JSON.stringify(newUser));
    currentUser = newUser;
    userMsg.textContent=`Registro exitoso. Bienvenido, ${username}!`;
    userForm.reset();
    cargarHistorias();
});

// --- Login ---
document.getElementById('loginBtn').addEventListener('click', e=>{
    e.preventDefault();
    const username = usernameInput.value.trim();
    const password = passwordInput.value.trim();
    let users = JSON.parse(localStorage.getItem('users')) || [];
    let user = users.find(u=>u.username===username && u.password===password);
    if(user){ updateUser(user); userMsg.textContent=`¡Bienvenido de nuevo, ${username}!`; userForm.reset(); }
    else { userMsg.textContent='Usuario o contraseña incorrectos.'; }
});

// --- Premium ---
const suscribirseBtn = document.getElementById('suscribirseBtn');
const premiumMsg = document.getElementById('premiumMsg');
suscribirseBtn.addEventListener('click', ()=>{
    if(!currentUser){ premiumMsg.textContent='Debes iniciar sesión para suscribirte.'; return; }
    currentUser.premium = true;
    updateUser(currentUser);
    premiumMsg.textContent='¡Ahora eres usuario Premium!';
});

// --- Recompensa diaria ---
const recompensaBtn = document.getElementById('recompensaBtn');
const recompensaMsg = document.getElementById('recompensaMsg');
recompensaBtn.addEventListener('click', ()=>{
    if(!currentUser){ recompensaMsg.textContent='Debes iniciar sesión para reclamar tu recompensa.'; return; }
    const today = new Date().toDateString();
    if(currentUser.lastReward === today){ recompensaMsg.textContent='Ya has reclamado tu recompensa diaria.'; return; }

    const rewards = ['50 puntos','1 historia premium','10 monedas','1 capítulo extra'];
    const reward = rewards[Math.floor(Math.random()*rewards.length)];
    recompensaMsg.textContent=`¡Felicidades! Has recibido: ${reward}`;
    currentUser.lastReward = today;
    updateUser(currentUser);
});

// --- Historias ---
const historias = [
    {titulo:'El bosque encantado', premium:false, contenido:'Una historia mágica para todos...'},
    {titulo:'El castillo secreto', premium:true, contenido:'Solo los suscriptores Premium pueden leer esto.'},
    {titulo:'Viaje a las estrellas', premium:false, contenido:'Una aventura espacial para todos.'},
    {titulo:'El diario de los sueños', premium:true, contenido:'Historia Premium exclusiva.'}
];
const historiasLista = document.getElementById('historiasLista');

function cargarHistorias(){
    historiasLista.innerHTML='';
    historias.forEach(h=>{
        const card=document.createElement('div'); card.className='historiaCard';
        const titulo=document.createElement('h3'); titulo.textContent=h.titulo;
        const contenido=document.createElement('p');
        if(h.premium){
            contenido.textContent = (currentUser && currentUser.premium) ? h.contenido : 'Contenido Premium. Suscríbete para leerlo.';
        } else { contenido.textContent=h.contenido; }
        card.appendChild(titulo); card.appendChild(contenido);
        historiasLista.appendChild(card);
    });
}

// Cargar historias al inicio
cargarHistorias();
