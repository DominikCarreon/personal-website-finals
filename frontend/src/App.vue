<script setup>
import { ref, onMounted } from 'vue'
import { createClient } from '@supabase/supabase-js'

// --- 1. Supabase Configuration ---
// REPLACE THESE WITH YOUR ACTUAL SUPABASE KEYS
const supabaseUrl = 'https://lhshisjdrmyolwgzbqhk.supabase.co'
const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxoc2hpc2pkcm15b2x3Z3picWhrIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzE4NDk5MTcsImV4cCI6MjA4NzQyNTkxN30._Ke65Ac72WBEH_CPriA0reFOGR_KB6dBXxJJkiE644Q'
const supabase = createClient(supabaseUrl, supabaseKey)

// --- 2. State & Logic ---
const comments = ref([])
const newName = ref('')
const newMessage = ref('')
const typingElement = ref(null)
const isLoading = ref(false)

// --- 3. Guestbook Functions (GET & POST) ---

// GET: Fetch comments from Supabase
const fetchComments = async () => {
  const { data, error } = await supabase
    .from('guestbook')
    .select('*')
    .order('created_at', { ascending: false })
  
  if (data) comments.value = data
  if (error) console.error('Error fetching comments:', error)
}

// POST: Add a new comment
const submitComment = async () => {
  if (!newName.value || !newMessage.value) return alert('Please fill in all fields')
  
  isLoading.value = true
  const { error } = await supabase
    .from('guestbook')
    .insert([{ name: newName.value, message: newMessage.value }])

  if (!error) {
    newName.value = ''
    newMessage.value = ''
    fetchComments() // Refresh list
  } else {
    alert('Error submitting comment')
    console.error(error)
  }
  isLoading.value = false
}

// --- 4. Animations & Effects (On Mount) ---
onMounted(() => {
  fetchComments()

  // Intersection Observer for Fade-up
  const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible')
      }
    })
  }, { threshold: 0.1 })

  document.querySelectorAll('.fade-up').forEach((el) => observer.observe(el))

  // Typing Effect
  const textPart1 = "Student at Asia Pacific College with passion for frontend development,"
  const textPart2 = " UI/UX design, and modern web technologies."
  const fullText = textPart1 + textPart2
  let charIndex = 0
  
  const typeWriter = () => {
    if (!typingElement.value) return
    
    if (charIndex < fullText.length) {
      if (charIndex === textPart1.length) {
        typingElement.value.innerHTML += "<br>"
      }
      typingElement.value.innerHTML += fullText.charAt(charIndex)
      charIndex++
      setTimeout(typeWriter, 25)
    }
  }
  setTimeout(typeWriter, 500)
})

// Smooth Scroll Helper
const scrollToSection = (id) => {
  const element = document.getElementById(id)
  if (element) element.scrollIntoView({ behavior: 'smooth' })
}
</script>

<template>
  <div class="app-container">
    <header>
      <div class="nav">
        <div class="logo">
          <span class="doms">DOM'S</span> PORTFOLIO.
        </div>
        <nav>
          <a href="#" @click.prevent="scrollToSection('home')">Home</a>
          <a href="#" @click.prevent="scrollToSection('about')">About</a>
          <a href="#" @click.prevent="scrollToSection('skills')">Skills</a>
          <a href="#" @click.prevent="scrollToSection('projects')">Projects</a>
          <a href="#" @click.prevent="scrollToSection('guestbook')">Guestbook</a>
          <a href="#" @click.prevent="scrollToSection('contact')">Contact</a>
        </nav>
      </div>
    </header>

    <section id="home" class="hero fade-up">
      <div class="hero-text">
        <h1>Hello, I'm <br><span>Dominik Carreon</span></h1>
        <p ref="typingElement" class="typing-cursor"></p>
        <a href="#contact" @click.prevent="scrollToSection('contact')" class="btn">Hire Me</a>
      </div>
      <div class="img-container">
        <img src="./assets/1742300971993.jpeg" alt="Dominik Carreon" class="profile-pic">
      </div>
    </section>

    <section id="about" class="about-section fade-up">
      <div class="section-header">
        <h2>About <span>Me</span></h2>
        <div class="line"></div>
      </div>

      <div class="about-container">
        <div class="about-left">
          <p class="bio-text">
            Motivated and creative Bachelor of Science in Information Technology student at Asia Pacific College, passionate about designing intuitive and visually engaging digital experiences. With a strong foundation in web technologies, user interface design, and user experience principles, I aim to bridge the gap between functionality and aesthetics in every project I work on.
          </p>

          <div class="details-grid">
            <div class="detail-item full-width">
              <h4>Name:</h4>
              <p>Dominik V Carreon</p>
            </div>
            <div class="detail-item">
              <h4>Location:</h4>
              <a href="https://www.google.com/maps?q=Pasay+City,+Malibay" target="_blank" style="color:white; text-decoration:none;">Pasay City, Malibay</a>
            </div>
            <div class="detail-item">
              <h4>Availability:</h4>
              <p>Freelance</p>
            </div>
            <div class="detail-item full-width">
              <h4>Email:</h4>
              <p>Dominikcarreon123@gmail.com</p>
            </div>
          </div>
        </div>

        <div class="about-right">
          <div class="info-card">
            <h3>Education</h3>
            <ul>
              <li><span>📖</span> SHS Major in Information Technology, APC</li>
              <li><span>📖</span> B.S. Information in Technology, APC</li>
            </ul>
          </div>
          <div class="info-card">
            <h3>Certifications</h3>
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
      <div class="skills-header">
        <h2>My <span>Skills</span></h2>
        <div class="line"></div>
        <p class="skills-description">
          I have growing experience in UI/UX design, prototyping, and web projects, using tools like Figma and Canva to create simple, user-friendly designs and layouts.
        </p>
      </div>

      <div class="skills-grid">
        <div class="card skill-card">
          <div class="icon-box red-glow"><span>&lt;/&gt;</span></div>
          <h3>Frontend Development</h3>
          <p class="tech-stack">HTML, CSS, JavaScript, Vue.js</p>
          <div class="progress-wrapper">
            <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
            <span class="percentage">50%</span>
          </div>
        </div>

        <div class="card skill-card">
          <div class="icon-box yellow-glow"><span>✒️</span></div>
          <h3>UI/UX Design</h3>
          <p class="tech-stack">Figma, Canva</p>
          <div class="progress-wrapper">
            <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
            <span class="percentage">50%</span>
          </div>
        </div>

        <div class="card skill-card">
          <div class="icon-box red-glow"><span>🗄️</span></div>
          <h3>Database Management</h3>
          <p class="tech-stack">MySQL, Firebase, Supabase</p>
          <div class="progress-wrapper">
            <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
            <span class="percentage">50%</span>
          </div>
        </div>

        <div class="card skill-card">
          <div class="icon-box yellow-glow"><span>👥</span></div>
          <h3>Team Collaboration</h3>
          <p class="tech-stack">Git, Agile, Scrum, Project Management</p>
          <div class="progress-wrapper">
            <div class="progress-track"><div class="progress-fill" style="width: 50%"></div></div>
            <span class="percentage">50%</span>
          </div>
        </div>
      </div>
    </section>

    <section id="projects" class="projects-section fade-up">
      <div class="projects-header">
        <h2>My <span>Projects</span></h2>
        <div class="line"></div>
        <p class="projects-description">Explore my recent work across various domains and technologies.</p>
      </div>

      <div class="projects-grid">
        <div class="card project-card">
          <img src="https://via.placeholder.com/400x200?text=RamsResNow" alt="RamsResNow">
          <div class="project-header">
            <h3>RamsResNow</h3>
            <span class="badge">Web App</span>
          </div>
          <p class="project-desc">A simplified reservation system designed for everyone's use.</p>
          <div class="tags">
            <span>Figma</span><span>MySQL</span><span>Reservation</span><span>APC Admins</span>
          </div>
        </div>

        <div class="card project-card">
          <img src="https://via.placeholder.com/400x200?text=Sari-Sari+Trade" alt="Sari-Sari Trade">
          <div class="project-header">
            <h3>The Sari-Sari Trade</h3>
            <span class="badge">Web App</span>
          </div>
          <p class="project-desc">The Sari-Sari Trade System allows residents to trade food items.</p>
          <div class="tags">
            <span>Figma</span><span>Canva</span><span>Brgy's</span><span>Firebase</span>
          </div>
        </div>
      </div>
    </section>

    <section id="guestbook" class="guestbook-section fade-up">
      <div class="section-header">
        <h2>Guest<span>book</span></h2>
        <div class="line"></div>
        <p style="color:#ccc; text-align:center;">Leave a mark! (Powered by Supabase)</p>
      </div>

      <div class="guestbook-container">
        <div class="guestbook-form">
          <input v-model="newName" type="text" placeholder="Your Name" class="gb-input">
          <textarea v-model="newMessage" placeholder="Your Message" class="gb-textarea"></textarea>
          <button @click="submitComment" class="btn hire-btn" :disabled="isLoading">
            {{ isLoading ? 'Posting...' : 'Sign Guestbook' }}
          </button>
        </div>

        <div class="comments-list">
          <div v-if="comments.length === 0" style="text-align:center; color:#666;">No comments yet. Be the first!</div>
          
          <div v-for="comment in comments" :key="comment.id" class="comment-card">
            <div class="comment-header">
              <span class="comment-name">{{ comment.name }}</span>
              <span class="comment-date">{{ new Date(comment.created_at).toLocaleDateString() }}</span>
            </div>
            <p class="comment-body">{{ comment.message }}</p>
          </div>
        </div>
      </div>
    </section>

    <section id="contact" class="contact-section fade-up">
      <div class="contact-header">
        <h2>Get In <span>Touch</span></h2>
        <div class="line"></div>
        <p class="contact-desc">Have a project in mind or want to collaborate? Feel free to reach out!</p>
      </div>

      <div class="contact-info-row">
        <div class="contact-item">
          <div class="icon-box red-box"><span>📍</span></div>
          <div class="contact-text">
            <h4>Location</h4>
            <a href="https://www.google.com/maps?q=Pasay+City,+Malibay" target="_blank" style="color:#888;">Pasay City, Malibay</a>
          </div>
        </div>
        <div class="contact-item">
          <div class="icon-box yellow-box"><span>✉️</span></div>
          <div class="contact-text">
            <h4>Email</h4>
            <a href="mailto:dvcarreon@student.apc.edu.ph" style="color:#888;">dvcarreon@student.apc.edu.ph</a>
          </div>
        </div>
        <div class="contact-item">
          <div class="icon-box orange-box"><span>📞</span></div>
          <div class="contact-text">
            <h4>Phone</h4>
            <p>+69 0911223345</p>
          </div>
        </div>
      </div>

      <div class="social-section">
        <h4>Follow Me</h4>
        <div class="social-icons">
          <a href="https://www.facebook.com/dominik.carreon" target="_blank" class="social-circle">f</a>
          <a href="https://x.com/" target="_blank" class="social-circle">𝕏</a>
          <a href="https://www.linkedin.com/in/dominik-carreon-9990012aa/" target="_blank" class="social-circle">in</a>
          <a href="https://github.com/DominikCarreon" target="_blank" class="social-circle">Git</a>
        </div>
      </div>
    </section>

    <footer>
      © 2026 Dominik Carreon. All rights reserved.
    </footer>
  </div>
</template>

<style>
/* ===== GLOBAL FONTS & RESETS ===== */
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif;
}

body {
  background: #0b0b0b;
  color: #ffffff;
  line-height: 1.6;
  padding-top: 70px;
}

html {
  scroll-behavior: smooth;
}

/* FADE UP ANIMATION CLASSES */
.fade-up {
  opacity: 0;
  transform: translateY(30px);
  transition: opacity 0.8s ease-out, transform 0.8s ease-out;
}
.fade-up.visible {
  opacity: 1;
  transform: translateY(0);
}
</style>

<style scoped>
/* ===== FIXED TOP BAR ===== */
header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 70px;
  background: linear-gradient(to right, #000, #111);
  border-bottom: 1px solid #1e1e1e;
  z-index: 1000;
}

.nav {
  max-width: 1200px;
  margin: auto;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 40px;
}

.logo {
  font-size: 22px;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: 1px;
}

.doms {
  color: #ffd700;
}

nav a {
  margin-left: 32px;
  text-decoration: none;
  color: #bdbdbd;
  font-size: 15px;
  position: relative;
  padding-bottom: 6px;
  transition: color 0.3s;
}

nav a:hover {
  color: #ffffff;
}

/* ===== HERO ===== */
.hero {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 40px;
  min-height: 90vh;
  padding: 0 10%;
  background-color: #0d0d0d;
}

.hero-text {
  max-width: 520px;
  flex-basis: 50%;
}

.hero h1 {
  font-size: 3.5rem;
  color: white;
  line-height: 1.2;
}

.hero span {
  color: #ff3b3b;
  text-shadow: 0 0 10px rgba(255, 71, 87, 0.6), 
               0 0 20px rgba(255, 71, 87, 0.4);
}

.btn {
  background: #ff3b3b;
  color: #ffffff;
  padding: 14px 30px;
  border-radius: 6px;
  text-decoration: none;
  font-weight: 600;
  display: inline-block;
  border: none;
  cursor: pointer;
  box-shadow: 0 0 15px rgba(255, 71, 87, 0.7);
  transition: all 0.3s ease;
}

.btn:hover {
  box-shadow: 0 0 25px rgba(255, 71, 87, 1);
  transform: scale(1.05);
}

.img-container {
  flex-basis: 45%;
  display: flex;
  justify-content: center;
  align-items: center;
  background: radial-gradient(circle, rgba(255, 235, 59, 0.5) 0%, rgba(255, 193, 7, 0.2) 50%, transparent 70%);
  border-radius: 50%;
  padding: 40px;
}

.profile-pic {
  width: 100%;
  max-width: 400px;
  height: auto;
  border-radius: 50%;
  object-fit: cover;
  box-shadow: 0 0 30px rgba(0,0,0,0.5);
}

.typing-cursor {
    min-height: 1.6em;
    margin: 20px 0 30px;
    color: #bdbdbd;
    font-size: 1.1rem;
}
.typing-cursor::after {
  content: '|';
  display: inline-block;
  margin-left: 4px;
  color: #ff3b3b;
  animation: blink 0.7s infinite;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

/* ===== SECTIONS SHARED ===== */
section {
  padding: 80px 10%;
}

.section-header {
  text-align: center;
  margin-bottom: 60px;
}

.section-header h2 {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 10px;
}

.section-header span { color: #ff4d4d; }

.line {
  width: 100px;
  height: 4px;
  background-color: #ff4d4d;
  margin: 0 auto;
  border-radius: 2px;
}

/* ===== ABOUT ===== */
.about-section { background-color: #0d0d0d; }

.about-container {
  display: grid;
  grid-template-columns: 1.5fr 1fr;
  gap: 50px;
}

.bio-text {
  color: #cccccc;
  line-height: 1.8;
  font-size: 1.1rem;
  margin-bottom: 40px;
}

.details-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
}

.detail-item h4 {
  color: #ffcc00;
  font-size: 0.9rem;
  margin-bottom: 5px;
  font-weight: bold;
}

.full-width { grid-column: span 2; }

.about-right {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.info-card {
  background-color: #1a1a1a;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.3);
}

.info-card h3 { font-size: 1.4rem; margin-bottom: 20px; }
.info-card ul { list-style: none; }
.info-card li {
  color: #cccccc;
  margin-bottom: 15px;
  display: flex;
  gap: 10px;
}
.info-card li span { color: #ffcc00; }

/* ===== SKILLS ===== */
.skills-section { background-color: #000000; }
.skills-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
}
.skill-card {
  background: #111111;
  border: 1px solid #222;
  border-radius: 12px;
  padding: 30px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  box-shadow: 0 4px 20px rgba(0,0,0,0.5);
}

.icon-box {
  width: 50px; height: 50px;
  border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
  font-size: 1.5rem; margin-bottom: 20px;
  background-color: rgba(255, 255, 255, 0.05);
}

.red-glow { color: #ff4d4d; border: 1px solid rgba(255, 77, 77, 0.3); box-shadow: 0 0 10px rgba(255, 77, 77, 0.1); }
.yellow-glow { color: #ffca28; border: 1px solid rgba(255, 202, 40, 0.3); box-shadow: 0 0 10px rgba(255, 202, 40, 0.1); }

.progress-track {
  width: 100%; height: 6px;
  background-color: #333;
  border-radius: 3px; margin-bottom: 8px;
}
.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #ff4d4d, #ffca28);
  border-radius: 3px;
}
.percentage { display: block; text-align: right; color: #888; font-size: 0.85rem; }

/* ===== PROJECTS ===== */
.projects-section { background-color: #0d0d0d; }
.projects-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; }

.project-card {
  background: #1a1a1a;
  border-radius: 12px; padding: 25px;
  display: flex; flex-direction: column;
  transition: transform 0.3s ease;
}
.project-card:hover { transform: translateY(-5px); }
.project-card img { width: 100%; border-radius: 8px; margin-bottom: 20px; object-fit: cover; }
.project-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
.badge {
  background-color: rgba(255, 77, 77, 0.15);
  color: #ff4d4d;
  padding: 6px 12px; border-radius: 20px; font-size: 0.8rem;
}
.tags { display: flex; flex-wrap: wrap; gap: 10px; margin-top: auto; }
.tags span {
  background-color: #2a2a2a; color: #aaa;
  padding: 6px 14px; border-radius: 6px; font-size: 0.85rem;
}

/* ===== GUESTBOOK (NEW) ===== */
.guestbook-section { background-color: #111; }
.guestbook-container { max-width: 800px; margin: 0 auto; }
.guestbook-form {
  display: flex; flex-direction: column; gap: 15px; margin-bottom: 40px;
}
.gb-input, .gb-textarea {
  background: #1a1a1a; border: 1px solid #333;
  color: white; padding: 15px; border-radius: 8px; font-size: 1rem;
}
.gb-textarea { min-height: 100px; resize: vertical; }
.gb-input:focus, .gb-textarea:focus { outline: none; border-color: #ff3b3b; }

.comment-card {
  background: #1a1a1a; border-left: 4px solid #ff3b3b;
  padding: 20px; margin-bottom: 15px; border-radius: 4px;
}
.comment-header { display: flex; justify-content: space-between; margin-bottom: 10px; }
.comment-name { font-weight: bold; color: #ffd700; }
.comment-date { font-size: 0.8rem; color: #888; }
.comment-body { color: #ccc; }

/* ===== CONTACT ===== */
.contact-section { background-color: #000; text-align: center; }
.contact-info-row { display: flex; justify-content: center; flex-wrap: wrap; gap: 60px; margin-bottom: 80px; }
.contact-item { display: flex; align-items: center; gap: 20px; }
.red-box { background-color: rgba(255, 59, 59, 0.1); color: #ff3b3b; border: 1px solid rgba(255, 59, 59, 0.2); }
.yellow-box { background-color: rgba(255, 204, 0, 0.1); color: #ffcc00; border: 1px solid rgba(255, 204, 0, 0.2); }
.orange-box { background-color: rgba(255, 115, 0, 0.1); color: #ff7300; border: 1px solid rgba(255, 115, 0, 0.2); }

.social-icons { display: flex; justify-content: center; gap: 20px; }
.social-circle {
  width: 50px; height: 50px; border-radius: 50%;
  background-color: #1a1a1a; color: #888;
  display: flex; align-items: center; justify-content: center;
  text-decoration: none; border: 1px solid #333; transition: all 0.3s;
}
.social-circle:hover {
  background-color: #ff4d4d; color: white; border-color: #ff4d4d;
  transform: translateY(-5px);
}

/* ===== FOOTER ===== */
footer {
  text-align: center; padding: 20px;
  border-top: 1px solid #222; color: #888; font-size: 13px;
  background: #000;
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  .hero { flex-direction: column-reverse; text-align: center; padding: 60px 20px; }
  .hero-text, .img-container { flex-basis: 100%; }
  .img-container { margin-bottom: 30px; }
  .profile-pic { max-width: 280px; }
  .about-container, .details-grid, .skills-grid, .projects-grid, .contact-info-row {
    grid-template-columns: 1fr;
  }
  .full-width { grid-column: span 1; }
  .nav { padding: 0 20px; }
  nav a { margin-left: 15px; font-size: 13px; }
}
</style>