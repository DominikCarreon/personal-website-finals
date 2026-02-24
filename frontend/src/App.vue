<script setup>
import { ref, onMounted, computed } from 'vue'
import { createClient } from '@supabase/supabase-js'

import ramsImg from './assets/RamsResNow.png'
import sariImg from './assets/Sarisari.png'
import pwdImg from './assets/PWD ID System.png'
import arduinoImg from './assets/Fire & Smoke Detector.png'

const supabaseUrl = 'https://lhshisjdrmyolwgzbqhk.supabase.co'
const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxoc2hpc2pkcm15b2x3Z3picWhrIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzE4NDk5MTcsImV4cCI6MjA4NzQyNTkxN30._Ke65Ac72WBEH_CPriA0reFOGR_KB6dBXxJJkiE644Q'
const supabase = createClient(supabaseUrl, supabaseKey)

const comments = ref([])
const newName = ref('')
const newMessage = ref('')
const typingElement = ref(null)
const isLoading = ref(false)
const isDarkMode = ref(false)
const isMenuOpen = ref(false)
const serverStatus = ref('PINGING...')

const activeFilter = ref('ALL')
const viewCount = ref(0)

const session = ref(null)
const adminEmail = ref('')
const adminPassword = ref('')
const showAdminModal = ref(false)
const showEasterEgg = ref(false)

const projects = [
{
id: 1,
title: 'RamsResNow',
type: 'SOFTWARE',
img: ramsImg,
desc: 'A simplified reservation system designed for everyone&#39;s use.',
tags: ['Figma', 'MySQL', 'Reservation', 'APC Admins']
},
{
id: 2,
title: 'The Sari-Sari Trade',
type: 'SOFTWARE',
img: sariImg,
desc: 'The Sari-Sari Trade System allows residents to trade food items.',
tags: ['Figma', 'Canva', 'Brgy&#39;s', 'Firebase']
},
{
id: 3,
title: 'PWD ID System',
type: 'SOFTWARE',
img: pwdImg,
desc: 'A digital PWD ID system with QR code verification to prevent the use of fake IDs in the Philippines.',
tags: ['Web', 'QR Code', 'Database']
},
{
id: 4,
title: 'Fire & Smoke Detector',
type: 'HARDWARE',
img: arduinoImg,
desc: 'A fire and smoke detector with SMS alerts using Arduino.',
tags: ['Arduino', 'C++', 'Electronics', 'SMS']
}
]

const filteredProjects = computed(() => {
if (activeFilter.value === 'ALL') return projects
return projects.filter(p => p.type === activeFilter.value)
})

const pingServer = async () => {
const { error } = await supabase.from('radar_tracking').select('id').limit(1)
if (error) {
serverStatus.value = 'SYSTEM OFFLINE'
} else {
serverStatus.value = 'SYSTEM ONLINE'
}
}

const updateRadar = async () => {
const { data, error } = await supabase.from('radar_tracking').select('views').eq('id', 1).single()
if (data) {
const newViews = data.views + 1
viewCount.value = newViews
await supabase.from('radar_tracking').update({ views: newViews }).eq('id', 1)
}
}

const toggleTheme = () => {
isDarkMode.value = !isDarkMode.value
if (isDarkMode.value) {
document.documentElement.setAttribute('data-theme', 'dark')
} else {
document.documentElement.removeAttribute('data-theme')
}
}

const toggleMenu = () => {
isMenuOpen.value = !isMenuOpen.value
}

const checkSession = async () => {
const { data } = await supabase.auth.getSession()
session.value = data.session
}

const handleLogin = async () => {
isLoading.value = true
const { error } = await supabase.auth.signInWithPassword({
email: adminEmail.value,
password: adminPassword.value
})
if (error) {
alert(error.message)
} else {
showAdminModal.value = false
checkSession()
}
isLoading.value = false
}

const handleLogout = async () => {
await supabase.auth.signOut()
session.value = null
}

const fetchComments = async () => {
const { data, error } = await supabase
.from('guestbook')
.select('*')
.order('created_at', { ascending: false })

if (data) comments.value = data
if (error) console.error(error)
}

const submitComment = async () => {
if (!newName.value || !newMessage.value) return alert('Please fill in all fields')

isLoading.value = true
const { error } = await supabase
.from('guestbook')
.insert([{ name: newName.value, message: newMessage.value }])

if (!error) {
newName.value = ''
newMessage.value = ''
fetchComments()
} else {
alert('Error submitting comment')
console.error(error)
}
isLoading.value = false
}

const deleteComment = async (id) => {
const { error } = await supabase.from('guestbook').delete().eq('id', id)
if (!error) {
fetchComments()
} else {
alert('Error deleting transmission')
}
}

onMounted(() => {
pingServer()
fetchComments()
updateRadar()
checkSession()

let keySequence = ''
window.addEventListener('keydown', (e) => {
keySequence += e.key.toLowerCase()
if (keySequence.length > 4) {
keySequence = keySequence.slice(-4)
}
if (keySequence === 'king') {
showEasterEgg.value = true
}
})

const observer = new IntersectionObserver((entries) => {
entries.forEach((entry) => {
if (entry.isIntersecting) {
entry.target.classList.add('visible')
}
})
}, { threshold: 0.1 })

document.querySelectorAll('.fade-up').forEach((el) => observer.observe(el))

const textPart1 = "Student at Asia Pacific College with passion for frontend development,"
const textPart2 = " UI/UX design, and modern web technologies."
const fullText = textPart1 + textPart2
let charIndex = 0

const typeWriter = () => {
if (!typingElement.value) return

if (charIndex &lt; fullText.length) {
  if (charIndex === textPart1.length) {
    typingElement.value.innerHTML += &quot;<br>&quot;
  }
  typingElement.value.innerHTML += fullText.charAt(charIndex)
  charIndex++
  setTimeout(typeWriter, 25)
}
}
setTimeout(typeWriter, 500)
})

const scrollToSection = (id) => {
const element = document.getElementById(id)
if (element) element.scrollIntoView({ behavior: 'smooth' })
isMenuOpen.value = false
}
</script>

<template>
<div class="app-container">
<header>
<div class="nav">
<div class="logo-group">
<div class="logo">
<span class="doms">DOM'S</span> PORTFOLIO
</div>
<div class="server-status">
<span class="status-dot" :class="{ 'online': serverStatus === 'SYSTEM ONLINE', 'offline': serverStatus === 'SYSTEM OFFLINE' }"></span>
{{ serverStatus }}
</div>
</div>
<button class="hamburger" @click="toggleMenu">☰</button>
<nav :class="{ 'nav-active': isMenuOpen }">
<a href="#" @click.prevent="scrollToSection('home')">HQ</a>
<a href="#" @click.prevent="scrollToSection('about')">INTEL</a>
<a href="#" @click.prevent="scrollToSection('skills')">LOADOUT</a>
<a href="#" @click.prevent="scrollToSection('projects')">MISSIONS</a>
<a href="#" @click.prevent="scrollToSection('guestbook')">COMMLINK</a>
<a href="#" @click.prevent="scrollToSection('contact')">EXTRACTION</a>
<button @click="toggleTheme" class="theme-btn">
{{ isDarkMode ? 'LIGHT MODE' : 'DARK MODE' }}
</button>
</nav>
</div>
</header>

<section id="home" class="hero fade-up">
  <div class="hero-text">
    <h1>OPERATIVE <br><span>Dominik Carreon</span></h1>
    <p ref="typingElement" class="typing-cursor"></p>
    <a href="#contact" @click.prevent="scrollToSection('contact')" class="btn">RECRUIT ME</a>
  </div>
  <div class="img-container">
    <img src="./assets/1742300971993.jpeg" alt="Dominik Carreon" class="profile-pic">
  </div>
</section>

<section id="about" class="about-section fade-up">
  <div class="section-header">
    <h2>OPERATIVE <span>INTEL</span></h2>
    <div class="line"></div>
  </div>

  <div class="about-container">
    <div class="about-left">
      <p class="bio-text">
        I am an IT student aiming to specialize in Full-Stack Frontend development and Database programming.
      </p>

      <div class="details-grid">
        <div class="detail-item full-width">
          <h4>CALLSIGN:</h4>
          <p>Dominik V Carreon</p>
        </div>
        <div class="detail-item">
          <h4>BASE OF OPERATIONS:</h4>
          <a href="[https://www.google.com/maps?q=Pasay+City,+Malibay](https://www.google.com/maps?q=Pasay+City,+Malibay)" target="_blank">Pasay City, Malibay</a>
        </div>
        <div class="detail-item">
          <h4>STATUS:</h4>
          <p>Freelance</p>
        </div>
        <div class="detail-item full-width">
          <h4>COMMS:</h4>
          <p>Dominikcarreon123@gmail.com</p>
        </div>
      </div>
    </div>

    <div class="about-right">
      <div class="info-card">
        <h3>TRAINING</h3>
        <ul>
          <li><span>🛡️</span> SHS Major in Information Technology, APC</li>
          <li><span>🛡️</span> B.S. Information Technology, APC</li>
        </ul>
      </div>
      <div class="info-card">
        <h3>CLEARANCES</h3>
        <ul>
          <li><span>★</span> Programming Foundations: Data Structures (2019)</li>
          <li><span>★</span> Python: Recursion</li>
          <li><span>★</span> Python Data Structures</li>
        </ul>
      </div>
    </div>
  </div>
</section>

<section id="skills" class="skills-section fade-up">
  <div class="section-header">
    <h2>TACTICAL <span>LOADOUT</span></h2>
    <div class="line"></div>
    <p class="section-description">
      Equipped with UI/UX design, prototyping, and web project capabilities.
    </p>
  </div>

  <div class="skills-grid">
    <div class="card skill-card">
      <div class="icon-box tactical-glow"><span>&lt;/&gt;</span></div>
      <h3>FRONTEND</h3>
      <p class="tech-stack">HTML, CSS, JavaScript, Vue.js</p>
      <div class="progress-wrapper">
        <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
        <span class="percentage">50%</span>
      </div>
    </div>

    <div class="card skill-card">
      <div class="icon-box tactical-glow"><span>✒️</span></div>
      <h3>DESIGN</h3>
      <p class="tech-stack">Figma, Canva</p>
      <div class="progress-wrapper">
        <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
        <span class="percentage">50%</span>
      </div>
    </div>

    <div class="card skill-card">
      <div class="icon-box tactical-glow"><span>🗄️</span></div>
      <h3>DATABASE</h3>
      <p class="tech-stack">MySQL, Firebase, Supabase</p>
      <div class="progress-wrapper">
        <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
        <span class="percentage">50%</span>
      </div>
    </div>

    <div class="card skill-card">
      <div class="icon-box tactical-glow"><span>👥</span></div>
      <h3>SQUAD COMMAND</h3>
      <p class="tech-stack">Git, Agile, Scrum, Project Management</p>
      <div class="progress-wrapper">
        <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
        <span class="percentage">50%</span>
      </div>
    </div>
  </div>
</section>

<section id="projects" class="projects-section fade-up">
  <div class="section-header">
    <h2>MISSION <span>LOG</span></h2>
    <div class="line"></div>
    <p class="section-description">Declassified records of recent operations.</p>
  </div>

  <div class="filter-controls">
    <button @click="activeFilter = 'ALL'" :class="{ active: activeFilter === 'ALL' }" class="filter-btn">ALL</button>
    <button @click="activeFilter = 'SOFTWARE'" :class="{ active: activeFilter === 'SOFTWARE' }" class="filter-btn">SOFTWARE</button>
    <button @click="activeFilter = 'HARDWARE'" :class="{ active: activeFilter === 'HARDWARE' }" class="filter-btn">HARDWARE</button>
  </div>

  <div class="projects-grid">
    <div v-for="project in filteredProjects" :key="project.id" class="card project-card">
      <img :src="project.img" :alt="project.title">
      <div class="project-header">
        <h3>{{ project.title }}</h3>
        <span class="badge">DECLASS</span>
      </div>
      <p class="project-desc">{{ project.desc }}</p>
      <div class="tags">
        <span v-for="tag in project.tags" :key="tag">{{ tag }}</span>
      </div>
    </div>
  </div>
</section>

<section id="guestbook" class="guestbook-section fade-up">
  <div class="section-header">
    <h2>SECURE <span>COMMLINK</span></h2>
    <div class="line"></div>
    <p class="section-description">Transmit your message to the server.</p>
  </div>

  <div class="guestbook-container">
    <div class="guestbook-form">
      <input v-model="newName" type="text" placeholder="CALLSIGN" class="gb-input">
      <textarea v-model="newMessage" placeholder="TRANSMISSION" class="gb-textarea"></textarea>
      <button @click="submitComment" class="btn hire-btn" :disabled="isLoading">
        {{ isLoading ? 'TRANSMITTING...' : 'SEND TRANSMISSION' }}
      </button>
    </div>

    <div class="comments-list">
      <div v-if="comments.length === 0" class="empty-comms">NO TRANSMISSIONS INTERCEPTED.</div>
      
      <div v-for="comment in comments" :key="comment.id" class="comment-card">
        <div class="comment-header">
          <span class="comment-name">{{ comment.name }}</span>
          <div class="comment-actions">
            <span class="comment-date">{{ new Date(comment.created_at).toLocaleDateString() }}</span>
            <button v-if="session" @click="deleteComment(comment.id)" class="delete-btn">PURGE</button>
          </div>
        </div>
        <p class="comment-body">{{ comment.message }}</p>
      </div>
    </div>
  </div>
</section>

<section id="contact" class="contact-section fade-up">
  <div class="section-header">
    <h2>EXTRACTION <span>POINT</span></h2>
    <div class="line"></div>
    <p class="section-description">Initiate contact sequence.</p>
  </div>

  <div class="contact-info-row">
    <div class="contact-item">
      <div class="icon-box tactical-box"><span>📍</span></div>
      <div class="contact-text">
        <h4>COORDINATES</h4>
        <a href="[https://www.google.com/maps?q=Pasay+City,+Malibay](https://www.google.com/maps?q=Pasay+City,+Malibay)" target="_blank">Pasay City, Malibay</a>
      </div>
    </div>
    <div class="contact-item">
      <div class="icon-box tactical-box"><span>✉️</span></div>
      <div class="contact-text">
        <h4>SECURE EMAIL</h4>
        <a href="mailto:dvcarreon@student.apc.edu.ph">dvcarreon@student.apc.edu.ph</a>
      </div>
    </div>
    <div class="contact-item">
      <div class="icon-box tactical-box"><span>📞</span></div>
      <div class="contact-text">
        <h4>FREQ</h4>
        <p>+69 0911223345</p>
      </div>
    </div>
  </div>

  <div class="social-section">
    <h4>ALLIED NETWORKS</h4>
    <div class="social-icons">
      <a href="[https://www.facebook.com/dominik.carreon](https://www.facebook.com/dominik.carreon)" target="_blank" class="social-circle">F</a>
      <a href="[https://x.com/](https://x.com/)" target="_blank" class="social-circle">X</a>
      <a href="[https://www.linkedin.com/in/dominik-carreon-9990012aa/](https://www.linkedin.com/in/dominik-carreon-9990012aa/)" target="_blank" class="social-circle">IN</a>
      <a href="[https://github.com/DominikCarreon](https://github.com/DominikCarreon)" target="_blank" class="social-circle">GIT</a>
    </div>
  </div>
</section>

<footer>
  <div class="radar-stats">
    <span>ACTIVE RADAR: {{ viewCount }} INTERCEPTIONS</span>
  </div>
  <div class="admin-trigger-container">
    <button v-if="!session" @click="showAdminModal = true" class="admin-trigger">COMMAND CENTER LOGIN</button>
    <button v-if="session" @click="handleLogout" class="admin-trigger">LOGOUT COMMAND CENTER</button>
  </div>
  © 2026 DOMINIK CARREON. END OF TRANSMISSION.
</footer>

<div v-if="showAdminModal" class="modal-overlay">
  <div class="modal-content">
    <h3>ADMIN OVERRIDE</h3>
    <input v-model="adminEmail" type="email" placeholder="ADMIN EMAIL" class="gb-input">
    <input v-model="adminPassword" type="password" placeholder="PASSWORD" class="gb-input">
    <button @click="handleLogin" class="btn hire-btn" :disabled="isLoading">
      {{ isLoading ? 'VERIFYING...' : 'LOGIN' }}
    </button>
    <button @click="showAdminModal = false" class="btn filter-btn">CANCEL</button>
  </div>
</div>

<div v-if="showEasterEgg" class="modal-overlay">
  <div class="modal-content">
    <h3>CLASSIFIED INTEL ACCESSED</h3>
    <p>ALL HAIL KING JAMES.</p>
    <button @click="showEasterEgg = false" class="btn hire-btn">CLOSE INTEL</button>
  </div>
</div>
</div>
</template>