/* ============================================================
   LAYO BIRTHDAY — SCRIPT
   Music intro + screen navigation + confetti
   ============================================================ */

const screens = document.querySelectorAll('.screen');
const story = document.getElementById('story');
const musicIntro = document.getElementById('musicIntro');
const bgMusic = document.getElementById('bgMusic');
let current = 0;

// ============================================================
// MUSIC INTRO
// ============================================================

function startExperience() {
  bgMusic.volume = 0.4;
  bgMusic.play().catch(() => {});
  launchStory();
}

function startExperienceNoMusic() {
  launchStory();
}

function launchStory() {
  musicIntro.style.transition = 'opacity 0.6s ease';
  musicIntro.style.opacity = '0';
  setTimeout(() => {
    musicIntro.style.display = 'none';
    story.classList.remove('hidden');
    showScreen(0);
  }, 650);
}

// ============================================================
// NAVIGATION
// ============================================================

function next() {
  if (current >= screens.length - 1) return;

  const outgoing = screens[current];
  outgoing.classList.add('exiting');
  outgoing.classList.remove('active');

  setTimeout(() => {
    outgoing.classList.remove('exiting');
    outgoing.querySelectorAll('.fade-child').forEach(el => {
      el.style.transition = 'none';
      el.style.opacity = '0';
      el.style.transform = 'translateY(14px)';
      // force reflow then remove inline so CSS takes over next time
      outgoing._resetPending = true;
    });
  }, 350);

  current++;
  showScreen(current);
  screens[current].scrollTop = 0;
}

function showScreen(index) {
  const screen = screens[index];

  // Clear any pending inline resets from previous visit
  screen.querySelectorAll('.fade-child').forEach(el => {
    el.style.transition = '';
    el.style.opacity = '';
    el.style.transform = '';
  });

  screen.classList.add('active');
  screen.scrollTop = 0;

  const screenNum = parseInt(screen.dataset.screen);
  if (screenNum === 47) {
    setTimeout(launchConfetti, 400);
  }
}

// ============================================================
// CONFETTI
// ============================================================

function launchConfetti() {
  const container = document.getElementById('confetti');
  if (!container) return;
  container.innerHTML = '';

  const colors = ['#f472b6', '#db2777', '#fbcfe8', '#ffffff', '#f9a8d4'];
  const count = 70;

  for (let i = 0; i < count; i++) {
    const piece = document.createElement('div');
    piece.className = 'confetti-piece';

    const size = 5 + Math.random() * 7;
    const left = Math.random() * 100;
    const delay = Math.random() * 2.2;
    const duration = 2.4 + Math.random() * 2;
    const color = colors[Math.floor(Math.random() * colors.length)];

    piece.style.cssText = `
      background: ${color};
      left: ${left}%;
      width: ${size}px;
      height: ${size}px;
      animation-duration: ${duration}s;
      animation-delay: ${delay}s;
    `;
    container.appendChild(piece);
  }
}

// ============================================================
// KEYBOARD (desktop preview)
// ============================================================

document.addEventListener('keydown', e => {
  if (['ArrowRight', 'Enter', ' '].includes(e.key)) {
    e.preventDefault();
    next();
  }
});

// ============================================================
// SWIPE (mobile)
// ============================================================

let touchStartX = 0;
let touchStartY = 0;

document.addEventListener('touchstart', e => {
  touchStartX = e.touches[0].clientX;
  touchStartY = e.touches[0].clientY;
}, { passive: true });

document.addEventListener('touchend', e => {
  const dx = touchStartX - e.changedTouches[0].clientX;
  const dy = Math.abs(touchStartY - e.changedTouches[0].clientY);
  if (dx > 55 && dy < 70) next();
}, { passive: true });
