// =========================================================
// 1. MODO CLARO / ESCURO (com persistência em localStorage)
// =========================================================
const themeToggle = document.getElementById('theme-toggle');
const root = document.documentElement;
const THEME_KEY = 'portfolio-theme';

function applyTheme(theme) {
    if (theme === 'dark') {
        root.setAttribute('data-theme', 'dark');
        themeToggle.textContent = '☀️';
    } else {
        root.removeAttribute('data-theme');
        themeToggle.textContent = '🌙';
    }
}

function getPreferredTheme() {
    const saved = localStorage.getItem(THEME_KEY);
    if (saved) return saved;
    return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
}

applyTheme(getPreferredTheme());

themeToggle.addEventListener('click', () => {
    const isDark = root.getAttribute('data-theme') === 'dark';
    const nextTheme = isDark ? 'light' : 'dark';
    applyTheme(nextTheme);
    localStorage.setItem(THEME_KEY, nextTheme);
});

// =========================================================
// 2. MENU RESPONSIVO (mobile)
// =========================================================
const menuToggle = document.getElementById('menu-toggle');
const navMenu = document.getElementById('nav-menu');

menuToggle.addEventListener('click', () => {
    const isOpen = navMenu.classList.toggle('open');
    menuToggle.setAttribute('aria-expanded', String(isOpen));
    menuToggle.textContent = isOpen ? '✕' : '☰';
});

// Fecha o menu ao clicar em um link (útil no celular)
navMenu.querySelectorAll('a').forEach((link) => {
    link.addEventListener('click', () => {
        navMenu.classList.remove('open');
        menuToggle.setAttribute('aria-expanded', 'false');
        menuToggle.textContent = '☰';
    });
});

// =========================================================
// 3. LINK ATIVO NO MENU CONFORME A ROLAGEM
// =========================================================
const sections = document.querySelectorAll('main section[id]');
const navLinks = document.querySelectorAll('.nav-menu a');

function setActiveLink() {
    let currentId = sections[0]?.id;
    const scrollPos = window.scrollY + 120;

    sections.forEach((section) => {
        if (scrollPos >= section.offsetTop) {
            currentId = section.id;
        }
    });

    navLinks.forEach((link) => {
        link.classList.toggle('active-link', link.getAttribute('href') === `#${currentId}`);
    });
}

window.addEventListener('scroll', setActiveLink, { passive: true });
setActiveLink();

// =========================================================
// 4. FILTRO DE PROJETOS
// =========================================================
const filterButtons = document.querySelectorAll('.filter');
const projectCards = document.querySelectorAll('.project-card');

filterButtons.forEach((button) => {
    button.addEventListener('click', () => {
        filterButtons.forEach((btn) => btn.classList.remove('active'));
        button.classList.add('active');

        const filter = button.dataset.filter;

        projectCards.forEach((card) => {
            const matches = filter === 'todos' || card.dataset.category === filter;
            card.classList.toggle('hidden', !matches);
        });
    });
});

// =========================================================
// 5. VALIDAÇÃO DO FORMULÁRIO DE CONTATO
// =========================================================
const contactForm = document.getElementById('contact-form');
const formMessage = document.getElementById('form-message');

function showFormMessage(text, type) {
    formMessage.textContent = text;
    formMessage.className = `form-message ${type}`;
}

function markField(field, isValid) {
    field.classList.toggle('invalid', !isValid);
}

contactForm.addEventListener('submit', (event) => {
    event.preventDefault();

    const nameField = document.getElementById('name');
    const emailField = document.getElementById('email');
    const messageField = document.getElementById('message');

    const name = nameField.value.trim();
    const email = emailField.value.trim();
    const message = messageField.value.trim();

    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

    const isNameValid = name.length >= 2;
    const isEmailValid = emailPattern.test(email);
    const isMessageValid = message.length >= 10;

    markField(nameField, isNameValid);
    markField(emailField, isEmailValid);
    markField(messageField, isMessageValid);

    if (!isNameValid || !isEmailValid || !isMessageValid) {
        showFormMessage('Por favor, verifique os campos destacados antes de enviar.', 'error');
        return;
    }

    // Este site é estático (GitHub Pages), então não há backend para
    // processar o envio. Aqui simulamos a confirmação para o usuário.
    showFormMessage(`Obrigado, ${name}! Sua mensagem foi registrada localmente (este site é estático e não envia e-mails de verdade).`, 'success');
    contactForm.reset();
});

// Remove o destaque de erro assim que o usuário corrige o campo
['name', 'email', 'message'].forEach((id) => {
    document.getElementById(id).addEventListener('input', (event) => {
        event.target.classList.remove('invalid');
    });
});

// =========================================================
// 6. BOTÃO "VOLTAR AO TOPO"
// =========================================================
const backToTopButton = document.getElementById('back-to-top');

window.addEventListener('scroll', () => {
    backToTopButton.classList.toggle('visible', window.scrollY > 480);
}, { passive: true });

backToTopButton.addEventListener('click', () => {
    window.scrollTo({ top: 0, behavior: 'smooth' });
});
