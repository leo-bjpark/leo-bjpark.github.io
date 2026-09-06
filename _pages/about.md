---
layout: about
title: Home
permalink: /
subtitle: Bumjin Park
order: 1
profile: false
news: false
selected_papers: false
social: false
_styles: >
---

<div class="home-editorial">
  <section class="home-hero">
    <div class="home-hero-copy">
      <p class="home-eyebrow">PhD Student | AI Researcher</p>
      <p class="home-summary">
        I am a <strong>Ph.D. student at KAIST AI</strong>.
      </p>
      <p class="home-summary home-summary-subtle">
        My research studies <strong>knowledge structure</strong> in logic, AI, and human reasoning, with a focus on how constraints are represented and resolved in large language models. I investigate how such structures are realized within model computations, combining <strong>mechanistic interpretability</strong> (neuron- and circuit-level analysis) with <strong>constraint-aware modeling</strong>.
      </p>
    </div>

    <div class="home-hero-media">
      <div class="home-hero-avatar">
        <img src="{{ '/assets/img/bumjini2.png' | relative_url }}" alt="Portrait of Bumjin Park">
      </div>
    </div>
  </section>

  <section class="home-profile-meta" aria-label="Profile details">
    <p class="home-profile-line">
      My work aims to understand how models represent requirements, resolve conflicts, and produce behavior consistent with structured knowledge and user intent. Grounded in training across <strong>mathematics</strong>, <strong>computer science</strong>, and <strong>AI</strong> (<strong>B.S.</strong> in Mathematics and Computer Science, <strong>M.S.</strong> in AI), I build systems that are <strong>reliable, transparent, and robust</strong> in real-world settings.
    </p>
    <p class="home-profile-line"><strong>Research interests:</strong> AI Safety, non-monotonic reasoning, constraint conflict, factuality</p>
    <div class="home-contact-links">
      <a class="home-contact-link" href="https://www.linkedin.com/in/leobumjin/" target="_blank" rel="noreferrer">
        <img src="{{ '/assets/svg/linkedin.svg' | relative_url }}" alt="" aria-hidden="true">
        <span>LinkedIn</span>
      </a>
      <a class="home-contact-link" href="https://www.instagram.com/leobumjin" target="_blank" rel="noreferrer">
        <img src="{{ '/assets/svg/instagram.svg' | relative_url }}" alt="" aria-hidden="true">
        <span>Instagram</span>
      </a>
      <details class="home-contact-email">
        <summary class="home-contact-link">
          <img src="{{ '/assets/svg/mail.svg' | relative_url }}" alt="" aria-hidden="true">
          <span>Email</span>
        </summary>
        <div class="home-contact-email-panel">
          <div class="home-contact-email-row">
            <span class="home-contact-email-address">bumjin@kaist.ac.kr</span>
            <button type="button" class="home-contact-copy-btn" data-copy="bumjin@kaist.ac.kr" aria-label="Copy KAIST email">
              <img src="{{ '/assets/svg/copy.svg' | relative_url }}" alt="" aria-hidden="true">
            </button>
          </div>
          <div class="home-contact-email-row">
            <span class="home-contact-email-address">bumjini42@gmail.com</span>
            <button type="button" class="home-contact-copy-btn" data-copy="leo.bjpark@gmail.com" aria-label="Copy Gmail email">
              <img src="{{ '/assets/svg/copy.svg' | relative_url }}" alt="" aria-hidden="true">
            </button>
          </div>
        </div>
      </details>
    </div>
  </section>

  <section class="home-news" aria-labelledby="home-news-title">
    <div class="home-section-heading">
      <p class="home-section-label">News</p>
    </div>

    <div class="home-news-list" id="home-news-list">
      <p class="home-news-empty">Loading updates...</p>
    </div>
  </section>
</div>

<script>
  document.addEventListener('DOMContentLoaded', function() {
    const newsRoot = document.getElementById('home-news-list');
    const copyButtons = document.querySelectorAll('.home-contact-copy-btn');

    const copyText = function(text) {
      if (navigator.clipboard && navigator.clipboard.writeText) {
        return navigator.clipboard.writeText(text);
      }
      return new Promise(function(resolve, reject) {
        try {
          const temp = document.createElement('textarea');
          temp.value = text;
          temp.setAttribute('readonly', '');
          temp.style.position = 'absolute';
          temp.style.left = '-9999px';
          document.body.appendChild(temp);
          temp.select();
          document.execCommand('copy');
          document.body.removeChild(temp);
          resolve();
        } catch (error) {
          reject(error);
        }
      });
    };

    copyButtons.forEach(function(btn) {
      btn.addEventListener('click', function(e) {
        e.preventDefault();
        e.stopPropagation();
        const target = btn.getAttribute('data-copy') || '';
        if (!target) return;
        const row = btn.closest('.home-contact-email-row');
        const addressNode = row ? row.querySelector('.home-contact-email-address') : null;
        const originalText = addressNode ? addressNode.textContent : '';

        copyText(target).then(function() {
          btn.classList.add('is-copied');
          if (addressNode) {
            addressNode.textContent = 'Copied to Clipboard';
            addressNode.classList.add('is-copied');
          }
          setTimeout(function() {
            btn.classList.remove('is-copied');
            if (addressNode) {
              addressNode.textContent = originalText;
              addressNode.classList.remove('is-copied');
            }
          }, 1400);
        }).catch(function() {
          if (addressNode) {
            addressNode.textContent = 'Copy failed';
            addressNode.classList.add('is-copied');
            setTimeout(function() {
              addressNode.textContent = originalText;
              addressNode.classList.remove('is-copied');
            }, 1400);
          }
        });
      });
    });

    if (!newsRoot) return;

    const escapeHtml = function(value) {
      return String(value || '')
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#39;');
    };

    const formatInlineBreaks = function(value) {
      return escapeHtml(value).replace(/&lt;br\s*\/?&gt;/gi, '<br>');
    };

    const renderEmpty = function(message) {
      newsRoot.innerHTML = '<p class="home-news-empty">' + message + '</p>';
    };

    const parseItems = function(rawText) {
      if (!rawText || !rawText.trim()) return [];

      try {
        const parsed = JSON.parse(rawText);
        if (Array.isArray(parsed)) return parsed;
        if (parsed && Array.isArray(parsed.items)) return parsed.items;
        return [];
      } catch (error) {
        return [];
      }
    };

    const normalizeItem = function(item) {
      if (typeof item === 'string') {
        return {
          year: '',
          date: '',
          title: item,
          color: '',
          darkColor: ''
        };
      }
      return {
        year: item.year || '',
        date: item.date || '',
        title: item.title || item.headline || 'Untitled',
        color: item.color || '',
        darkColor: item.dark_color || item.color_dark || '',
        url: item.url || item.link || '',
        urlDescription: item.url_description || item.url_desscription || '',
        highlight: Boolean(item.highlight),
        visible: item.visible !== false
      };
    };

    const sanitizeUrl = function(url) {
      const raw = String(url || '').trim();
      if (!raw) return '';
      try {
        const resolved = new URL(raw, window.location.origin);
        if (resolved.protocol === 'http:' || resolved.protocol === 'https:' || resolved.protocol === 'mailto:') {
          return resolved.href;
        }
        return '';
      } catch (e) {
        return '';
      }
    };

    const renderNewsCard = function(item, dateText) {
      const date = dateText ? '<p class="home-news-date">' + escapeHtml(dateText) + '</p>' : '';
      const markerColor = item.color || '';
      const markerDarkColor = item.darkColor || '';
      const markerStyle = [
        markerColor ? '--news-marker-color-light: ' + escapeHtml(markerColor) + ';' : '',
        markerDarkColor ? '--news-marker-color-dark: ' + escapeHtml(markerDarkColor) + ';' : ''
      ].filter(Boolean).join(' ');
      const highlightClass = item.highlight ? ' is-highlighted' : '';
      const body = '<span class="home-news-text">' + formatInlineBreaks(item.title || '') + '</span>';
      const safeUrl = sanitizeUrl(item.url || '');
      const urlDescription = String(item.urlDescription || '').trim();
      const markerDot = safeUrl
        ? '<span class="home-news-link-wrap">' +
            '<a class="home-news-dot home-news-dot-link" href="' + safeUrl + '" target="_blank" rel="noreferrer" aria-label="' + escapeHtml(urlDescription || 'Open link') + '">' +
              '<img src="{{ "/assets/svg/link.svg" | relative_url }}" alt="" aria-hidden="true">' +
            '</a>' +
            (urlDescription ? '<span class="home-news-link-tooltip" role="tooltip">' + escapeHtml(urlDescription) + '</span>' : '') +
          '</span>'
        : '<span class="home-news-dot"></span>';

      return '' +
        '<article class="home-news-item">' +
          '<div class="home-news-rail">' +
            '<div class="home-news-date-wrap' + highlightClass + '">' + date + '</div>' +
            '<div class="home-news-marker"' + (markerStyle ? ' style="' + markerStyle + '"' : '') + '>' +
              markerDot +
              '<span class="home-news-stem"></span>' +
            '</div>' +
          '</div>' +
          '<div class="home-news-main">' + body + '</div>' +
        '</article>';
    };

    const renderNews = function(rawItems) {
      if (!rawItems.length) {
        renderEmpty('No updates yet.');
        return;
      }

      const items = rawItems.map(normalizeItem).filter(function(item) {
        return item.visible;
      });

      if (!items.length) {
        renderEmpty('No updates yet.');
        return;
      }

      const isMobile = window.matchMedia && window.matchMedia('(max-width: 900px)').matches;

      if (isMobile) {
        const groups = {};
        const order = [];

        items.forEach(function(item) {
          const key = String(item.year || '').trim() || 'Updates';
          if (!groups[key]) {
            groups[key] = [];
            order.push(key);
          }
          groups[key].push(item);
        });

        newsRoot.innerHTML = order.map(function(yearKey) {
          const cards = groups[yearKey].map(function(item) {
            return renderNewsCard(item, item.date || item.year || '');
          }).join('');
          return '' +
            '<section class="home-news-year-group">' +
              '<p class="home-news-year-title">' + escapeHtml(yearKey) + '</p>' +
              cards +
            '</section>';
        }).join('');
        return;
      }

      newsRoot.innerHTML = items.map(function(item) {
        const fullDate = [item.year, item.date].filter(function(v) { return String(v || '').trim(); }).join(' ');
        return renderNewsCard(item, fullDate || item.date || item.year || '');
      }).join('');
    };

    let cachedNewsItems = [];
    const media = window.matchMedia ? window.matchMedia('(max-width: 900px)') : null;
    const rerender = function() {
      if (cachedNewsItems.length) renderNews(cachedNewsItems);
    };

    if (media) {
      if (typeof media.addEventListener === 'function') {
        media.addEventListener('change', rerender);
      } else if (typeof media.addListener === 'function') {
        media.addListener(rerender);
      }
    }

    fetch('{{ "/data/news.json" | relative_url }}', { cache: 'no-store' })
      .then(function(response) {
        if (!response.ok) throw new Error('Failed to load news.');
        return response.text();
      })
      .then(function(text) {
        cachedNewsItems = parseItems(text);
        renderNews(cachedNewsItems);
      })
      .catch(function() {
        renderEmpty('No updates yet.');
      });
  });
</script>
