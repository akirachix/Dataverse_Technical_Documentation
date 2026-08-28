<div class="mk-hero">
  <span class="mk-badge">✨ Maternal Health AI System</span>
  <h1 class="mk-hero-title">MyKira Technical Documentation</h1>
  <p class="mk-hero-lead">
    Zambia's flagship AI-powered maternal health platform combining WHO-aligned clinical risk modeling, low-bandwidth mobile access, and real-time triage dashboards.
  </p>
  <div class="mk-buttons">
    <a href="https://my-kira.vercel.app/" class="mk-btn mk-btn--gradient" target="_blank" rel="noopener">🌐 Explore Informational Website</a>
    <a href="how-it-works.md" class="mk-btn mk-btn--outline">📖 Architecture Overview</a>
  </div>
</div>

<div class="mk-carousel" id="carousel">
  <div class="mk-carousel-slide active">
    <img src="assets/brand/Dashoboard.png" alt="MyKira Dashboard Interface">
  </div>
  <div class="mk-carousel-slide">
    <img src="assets/brand/mykira_logoo-removebg-preview.png" alt="MyKira Platform Identity">
  </div>
  <div class="mk-carousel-slide">
    <img src="assets/brand/informational website.png" alt="MyKira Web Interface">
  </div>
</div>

<div class="mk-carousel-dots">
  <span class="mk-carousel-dot active" onclick="goToSlide(0)"></span>
  <span class="mk-carousel-dot" onclick="goToSlide(1)"></span>
  <span class="mk-carousel-dot" onclick="goToSlide(2)"></span>
</div>

<script>
(function() {
  let current = 0;
  const slides = document.querySelectorAll('.mk-carousel-slide');
  const dots = document.querySelectorAll('.mk-carousel-dot');
  const total = slides.length;
  let interval;
  
  function showSlide(index) {
    slides.forEach((s, i) => {
      s.classList.toggle('active', i === index);
      dots[i].classList.toggle('active', i === index);
    });
    current = index;
  }
  
  function nextSlide() {
    showSlide((current + 1) % total);
  }
  
  window.goToSlide = function(index) {
    clearInterval(interval);
    showSlide(index);
    interval = setInterval(nextSlide, 5000);
  };
  
  interval = setInterval(nextSlide, 5000);
})();
</script>

---

## About Us

**MyKira** is an Android mobile platform designed for maternal health monitoring and early risk detection in Zambia. Built to directly address community-level delays in seeking care—which account for 46.0% of local maternal deaths—MyKira provides mothers with an everyday home screening tool to recognize pregnancy danger signs early.

### Core Pillars & Framework

* **WHO & MoH Alignment**: Integrates a rule-based risk scoring engine structured according to the Zambian Ministry of Health Guidelines and the WHO PCPNC (Pregnancy, Childbirth, Postpartum, and Newborn Care) framework.
* **AI-Guided Support**: Features an interactive AI post-assessment chatbot providing instant, personalized guidance to help mothers understand when to seek emergency clinical care.
* **Community & Accountability**: Bridges peer isolation through trimester-matched community hubs, real-time peer chat, and weekly healthy habit challenges.
* **Care Continuity**: Includes appointment and medication reminders to reduce care drop-off and ensure continuous health tracking.

<div class="mk-stats-grid">
  <div class="mk-stat-card">
    <span class="mk-stat-number">46.0%</span>
    <span class="mk-stat-label">Community Delays Addressed</span>
  </div>
  <div class="mk-stat-card">
    <span class="mk-stat-number">WHO & MoH</span>
    <span class="mk-stat-label">PCPNC Protocol Aligned</span>
  </div>
  <div class="mk-stat-card">
    <span class="mk-stat-number">Risk Evaluation</span>
    <span class="mk-stat-label">Early At-Home Detection</span>
  </div>
</div>

---

## Developer Onboarding & Support

<div class="mk-help" markdown>

!!! tip "Quick Start Guide"
    If you are setting up the development environment for the first time, consult [How It Works](how-it-works.md) for pre-requisites and container configurations.

!!! question "Contribution Guidelines"
    Before submitting pull requests, review our [Code Standards](code-standards.md) and [Q/A Process](qa-process.md).
</div>