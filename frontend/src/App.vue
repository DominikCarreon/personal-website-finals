<script setup>
import { ref, onMounted } from 'vue'
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = 'https://lhshisjdrmyolwgzbqhk.supabase.co'
const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxoc2hpc2pkcm15b2x3Z3picWhrIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzE4NDk5MTcsImV4cCI6MjA4NzQyNTkxN30._Ke65Ac72WBEH_CPriA0reFOGR_KB6dBXxJJkiE644Q'
const supabase = createClient(supabaseUrl, supabaseKey)

const comments = ref([])
const newName = ref('')
const newMessage = ref('')
const typingElement = ref(null)
const isLoading = ref(false)

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

onMounted(() => {
  fetchComments()

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
          <span class="doms">DOM'S</span> PORTFOLIO
        </div>
        <nav>
          <a href="#" @click.prevent="scrollToSection('home')">HQ</a>
          <a href="#" @click.prevent="scrollToSection('about')">INTEL</a>
          <a href="#" @click.prevent="scrollToSection('skills')">LOADOUT</a>
          <a href="#" @click.prevent="scrollToSection('projects')">MISSIONS</a>
          <a href="#" @click.prevent="scrollToSection('guestbook')">COMMLINK</a>
          <a href="#" @click.prevent="scrollToSection('contact')">EXTRACTION</a>
        </nav>
      </div>
    </header>

    <section id="home" class="hero fade-up">
      <div class="hero-text">
        <h1>OPERATIVE <br><span>DOMINIK CARREON</span></h1>
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
              <a href="https://www.google.com/maps?q=Pasay+City,+Malibay" target="_blank">Pasay City, Malibay</a>
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
          <div class="icon-box tactical-glow"><span></></span></div>
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

      <div class="projects-grid">
        <div class="card project-card">
          <img src="./assets/RamsResNow.png" alt="RamsResNow">
          <div class="project-header">
            <h3>RamsResNow</h3>
            <span class="badge">DECLASS</span>
          </div>
          <p class="project-desc">A simplified reservation system designed for everyone's use.</p>
          <div class="tags">
            <span>Figma</span><span>MySQL</span><span>Reservation</span><span>APC Admins</span>
          </div>
        </div>

        <div class="card project-card">
          <img src="./assets/Sarisari.png" alt="Sari-Sari Trade">
          <div class="project-header">
            <h3>The Sari-Sari Trade</h3>
            <span class="badge">DECLASS</span>
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
              <span class="comment-date">{{ new Date(comment.created_at).toLocaleDateString() }}</span>
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
            <a href="https://www.google.com/maps?q=Pasay+City,+Malibay" target="_blank">Pasay City, Malibay</a>
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
          <a href="https://www.facebook.com/dominik.carreon" target="_blank" class="social-circle">F</a>
          <a href="https://x.com/" target="_blank" class="social-circle">X</a>
          <a href="https://www.linkedin.com/in/dominik-carreon-9990012aa/" target="_blank" class="social-circle">IN</a>
          <a href="https://github.com/DominikCarreon" target="_blank" class="social-circle">GIT</a>
        </div>
      </div>
    </section>

    <footer>
      © 2026 DOMINIK CARREON. END OF TRANSMISSION.
    </footer>
  </div>
</template>