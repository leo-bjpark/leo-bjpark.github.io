---
layout: default
title: Work
permalink: /work/
---

<div class="work-page">
  <div class="work-layout">
    <aside class="work-nav" aria-label="Work sections">
      <div class="work-nav-box">
        <a class="work-nav-link work-nav-top-link" href="#">Work</a>
        <a class="work-nav-link" href="#work-publications">Publications</a>
        <a class="work-nav-link" href="#work-projects">Projects</a>
        <a class="work-nav-link" href="#work-experiences">Experience</a>
        <a class="work-nav-link" href="#work-education">Education</a>
      </div>
    </aside>

    <div class="work-main">
      <div id="work-top"></div> 

      <section class="work-section" id="work-publications">
        <div class="work-section-heading">
          <h2 class="home-section-label">Publications</h2>
        </div>
        <div class="work-list" id="work-publications-list">
          <p class="posts-archive-summary">Loading publications...</p>
        </div>
      </section>

      <section class="work-section" id="work-projects">
        <div class="work-section-heading">
          <h2 class="home-section-label">Projects</h2>
        </div>
        <div class="work-list" id="work-projects-list">
          <p class="posts-archive-summary">Loading projects...</p>
        </div>
      </section>

      <section class="work-section" id="work-experiences">
        <div class="work-section-heading">
          <h2 class="home-section-label">Experience</h2>
        </div>
        <div class="work-list" id="work-experiences-list">
          <p class="posts-archive-summary">Loading experience...</p>
        </div>
      </section>

      <section class="work-section" id="work-education">
        <div class="work-section-heading">
          <h2 class="home-section-label">Education</h2>
        </div>
        <div class="work-list" id="work-education-list">
          <p class="posts-archive-summary">Loading education...</p>
        </div>
        <details class="work-domestic-publications">
          <summary>
            <span class="work-domestic-summary-main">
              <span class="work-domestic-badge" aria-hidden="true">KR</span>
              <span class="work-domestic-label is-closed">Show Korean Domestic Papers</span>
              <span class="work-domestic-label is-open">Hide Korean Domestic Papers</span>
            </span>
            <span class="work-domestic-chevron" aria-hidden="true">↓</span>
          </summary>
          <div class="work-list work-domestic-publications-list" id="work-domestic-publications-list">
            <p class="posts-archive-summary">Loading Korean domestic publications...</p>
          </div>
        </details>
      </section>
    </div>
  </div>
</div>

<script>
  document.addEventListener('DOMContentLoaded', function () {
    var svgBasePath = '{{ "/assets/svg/" | relative_url }}';
    var topButton = document.querySelector('.work-nav-top-link');
    var defaultPublicationLinkMapping = {
      Paper: 'paper.svg',
      Program: 'paper.svg',
      Proceedings: 'paper.svg',
      Workshop: 'rocket.svg',
      Project: 'rocket.svg',
      Github: 'github.svg',
      Drive: 'google-drive.svg'
    };

    var sections = [
      { id: 'work-publications-list', path: '{{ "/data/publication.json" | relative_url }}', type: 'publication' },
      { id: 'work-projects-list', path: '{{ "/data/projects.json" | relative_url }}', type: 'project' },
      { id: 'work-experiences-list', path: '{{ "/data/experiences.json" | relative_url }}', type: 'experience' },
      { id: 'work-education-list', path: '{{ "/data/education.json" | relative_url }}', type: 'education' },
      { id: 'work-domestic-publications-list', path: '{{ "/data/korean_publications.json" | relative_url }}', type: 'publication', reverse: true }
    ];

    function escapeHtml(value) {
      return String(value || '')
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#39;');
    }

    if (topButton) {
      topButton.addEventListener('click', function (e) {
        e.preventDefault();
        window.scrollTo({ top: 0, behavior: 'smooth' });
      });
    }

    function findMappedIcon(linkMapping, label) {
      if (!linkMapping || !label) return '';
      if (Object.prototype.hasOwnProperty.call(linkMapping, label)) {
        return linkMapping[label];
      }
      var wanted = String(label).toLowerCase();
      var keys = Object.keys(linkMapping);
      for (var i = 0; i < keys.length; i += 1) {
        if (keys[i].toLowerCase() === wanted) return linkMapping[keys[i]];
      }
      return '';
    }

    function inferIconFromUrl(url) {
      var value = String(url || '').toLowerCase();
      if (!value) return 'link.svg';
      if (value.indexOf('github.com') !== -1) return 'github.svg';
      if (value.indexOf('youtube.com') !== -1 || value.indexOf('youtu.be') !== -1) return 'youtube.svg';
      if (value.indexOf('drive.google.com') !== -1 || value.indexOf('1drv.ms') !== -1 || value.indexOf('googleusercontent.com') !== -1) return 'google-drive.svg';
      return 'link.svg';
    }

    function iconThemeClass(iconFileName) {
      var name = String(iconFileName || '').toLowerCase();
      if (name === 'youtube.svg') return ' work-link-youtube';
      return '';
    }

    function renderPublicationIcon(iconFileName, label) {
      var name = String(iconFileName || '').toLowerCase();
      var attrs = 'class="work-link-icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true" focusable="false"';

      if (name === 'paper.svg') {
        return '<svg ' + attrs + '><path d="M15 12h-5"/><path d="M15 8h-5"/><path d="M19 17V5a2 2 0 0 0-2-2H4"/><path d="M8 21h12a2 2 0 0 0 2-2v-1a1 1 0 0 0-1-1H11a1 1 0 0 0-1 1v1a2 2 0 1 1-4 0V5a2 2 0 1 0-4 0v2a1 1 0 0 0 1 1h3"/></svg>';
      }

      if (name === 'rocket.svg') {
        return '<svg ' + attrs + '><path d="M4 13a8 8 0 0 1 7 7a6 6 0 0 0 3-5a9 9 0 0 0 6-8a3 3 0 0 0-3-3a9 9 0 0 0-8 6a6 6 0 0 0-5 3"/><path d="M7 14a6 6 0 0 0-3 6a6 6 0 0 0 6-3"/><path d="M14 9a1 1 0 1 0 2 0a1 1 0 1 0-2 0"/></svg>';
      }

      if (name === 'github.svg') {
        return '<svg ' + attrs + '><path d="M9 19c-4.3 1.4-4.3-2.5-6-3m12 5v-3.5c0-1 .1-1.4-.5-2c2.8-.3 5.5-1.4 5.5-6a4.6 4.6 0 0 0-1.3-3.2a4.2 4.2 0 0 0-.1-3.2s-1.1-.3-3.5 1.3a12.3 12.3 0 0 0-6.2 0c-2.4-1.6-3.5-1.3-3.5-1.3a4.2 4.2 0 0 0-.1 3.2A4.6 4.6 0 0 0 4 9.5c0 4.6 2.7 5.7 5.5 6c-.6.6-.6 1.2-.5 2V21"/></svg>';
      }

      if (name === 'google-drive.svg') {
        return '<svg ' + attrs + '><path d="M12 10l-6 10l-3-5l6-10l3 5"/><path d="M9 15h12l-3 5H6"/><path d="M15 15L9 5h6l6 10h-6"/></svg>';
      }

      return '<img src="' + escapeHtml(svgBasePath + iconFileName) + '" alt="' + escapeHtml(label) + '" loading="lazy">';
    }

    function renderLinks(links, metadata, type) {
      if (!Array.isArray(links) || !links.length) return '';
      var linkMapping = metadata && metadata['link-mapping'] ? metadata['link-mapping'] : {};
      return links.map(function (link) {
        var label = link.label || 'Link';
        var mappedIcon = findMappedIcon(linkMapping, label);
        if (!mappedIcon && type === 'publication') {
          mappedIcon = findMappedIcon(defaultPublicationLinkMapping, label);
        }
        var url = String(link.url || '');

        if (!mappedIcon && /github\.com/i.test(url)) {
          mappedIcon = findMappedIcon(linkMapping, 'Github');
        }

        if (type === 'publication' && mappedIcon) {
          var publicationThemeClass = iconThemeClass(mappedIcon);
          return '' +
            '<a class="work-link-icon' + publicationThemeClass + '" href="' + escapeHtml(url) + '" target="_blank" rel="noreferrer" aria-label="' + escapeHtml(label) + '">' +
              renderPublicationIcon(mappedIcon, label) +
            '</a>';
        }

        var iconName = mappedIcon || inferIconFromUrl(url);
        var linkThemeClass = iconThemeClass(iconName);
        var targetAttrs = link.sameTab ? '' : ' target="_blank" rel="noreferrer"';
        return '' +
          '<a class="work-link work-link-with-icon' + linkThemeClass + '" href="' + escapeHtml(url) + '"' + targetAttrs + '>' +
            '<img class="work-link-inline-icon" src="' + escapeHtml(svgBasePath + iconName) + '" alt="" loading="lazy" aria-hidden="true">' +
            '<span>' + escapeHtml(label) + '</span>' +
          '</a>';
      }).join('');
    }

    function renderNotes(notes) {
      if (!Array.isArray(notes) || !notes.length) return '';
      return '<ul class="work-item-notes">' + notes.map(function (note) {
        return '<li>' + escapeHtml(note) + '</li>';
      }).join('') + '</ul>';
    }

    function parseHexColor(hex) {
      if (typeof hex !== 'string') return null;
      var value = hex.trim();
      if (!value) return null;
      if (value.charAt(0) === '#') value = value.slice(1);
      if (value.length === 3) {
        value = value.split('').map(function (c) { return c + c; }).join('');
      }
      if (!/^[0-9a-fA-F]{6}$/.test(value)) return null;
      return {
        r: parseInt(value.slice(0, 2), 16),
        g: parseInt(value.slice(2, 4), 16),
        b: parseInt(value.slice(4, 6), 16)
      };
    }

    function getReadableTextColor(bgColor) {
      var rgb = parseHexColor(bgColor);
      if (!rgb) return '#ffffff';
      var luminance = (0.2126 * rgb.r + 0.7152 * rgb.g + 0.0722 * rgb.b) / 255;
      return luminance > 0.6 ? '#111111' : '#ffffff';
    }

    function findChipConfig(colorMapping, chip) {
      if (!colorMapping || !chip) return null;
      if (Object.prototype.hasOwnProperty.call(colorMapping, chip)) {
        return colorMapping[chip];
      }
      var chipLower = String(chip).toLowerCase();
      var keys = Object.keys(colorMapping);
      for (var i = 0; i < keys.length; i += 1) {
        if (keys[i].toLowerCase() === chipLower) {
          return colorMapping[keys[i]];
        }
      }
      return null;
    }

    function renderChipLabel(chip) {
      return allowBasicInlineHtml(
        escapeHtml(chip || '')
        .replace(/\\n/g, '<br>')
        .replace(/\n/g, '<br>')
      );
    }

    function allowBasicInlineHtml(value) {
      return String(value || '')
        .replace(/&lt;br\s*\/?&gt;/gi, '<br>')
        .replace(/&lt;strong&gt;/gi, '<strong>')
        .replace(/&lt;\/strong&gt;/gi, '</strong>');
    }

    function renderAuthors(authors, authorsNote) {
      if (!authors) return '';
      var safe = allowBasicInlineHtml(escapeHtml(String(authors)));
      var note = String(authorsNote || '').trim();

      if (!/<strong>\s*bumjin park\s*<\/strong>/i.test(safe)) {
        safe = safe.replace(/\bbumjin park\b/gi, '<strong>Bumjin Park</strong>');
      }

      if (note) {
        safe += ' <span class="work-item-author-note">(' + escapeHtml(note) + ')</span>';
      }

      return safe;
    }

    function formatPublicationVenue(rawVenue, dateText) {
      var venue = String(rawVenue || '').trim();
      if (!venue) return '';
      var parts = venue.split(',');
      var result = '';

      if (parts.length > 1) {
        result = parts.slice(1).join(',').trim();
      } else {
        result = venue;
      }

      var date = String(dateText || '').trim();
      if (date) {
        var suffix = ', ' + date;
        if (result.slice(-suffix.length) === suffix) {
          result = result.slice(0, -suffix.length).trim();
        } else if (result === date) {
          result = parts[0] ? String(parts[0]).trim() : '';
        }
      }

      return result;
    }

    function renderItem(item, type, metadata) {
      var title = '';
      var kind = '';
      var meta = '';
      var metaHtml = '';
      var venueHtml = '';
      var compactMeta = '';
      var headerDate = '';
      var summary = '';
      var notes = '';
      var actions = '';
      var chipStyle = '';
      var colorMapping = metadata && metadata['color-mapping'] ? metadata['color-mapping'] : {};

      if (type === 'publication' && item.chip) {
        var chipConfig = findChipConfig(colorMapping, item.chip);
        var lightColor = '';
        var darkColor = '';

        if (typeof chipConfig === 'string') {
          lightColor = chipConfig;
          darkColor = chipConfig;
        } else if (chipConfig && typeof chipConfig === 'object') {
          lightColor = chipConfig.white || chipConfig.light || '';
          darkColor = chipConfig.dark || lightColor;
        }

        if (lightColor || darkColor) {
          if (!lightColor) lightColor = darkColor;
          if (!darkColor) darkColor = lightColor;

          chipStyle = ' style="' +
            '--chip-accent-light: ' + escapeHtml(lightColor) + '; ' +
            '--chip-accent-dark: ' + escapeHtml(darkColor) + ';"';
        }
      }

      if (type === 'publication') {
        title = item.title || '';
        meta = item.authors || '';
        metaHtml = renderAuthors(item.authors || '', item['authors-note'] || '');
        summary = '';
        kind = formatPublicationVenue(item.venue || '', item.date || '');
        if (!kind) {
          kind = String(item.venue || '').trim();
        }
        venueHtml = kind ? escapeHtml(kind) : '';
        notes = item.chip ? '<div class="work-item-chips"><span class="work-item-chip"' + chipStyle + '>' + renderChipLabel(item.chip) + '</span></div>' : '';
        actions = renderLinks(item.links, metadata, type);
        var chipActions = item.chip && actions
          ? '<span class="work-item-chip-actions">' + actions + '</span>'
          : '';
        var chipHtml = item.chip
          ? '<span class="work-item-chip"' + chipStyle + '><span class="work-item-chip-label">' + renderChipLabel(item.chip) + '</span>' + chipActions + '</span>'
          : '';
        if (item.chip && actions) {
          actions = '';
        }

        var pubMetaParts = [];
        if (chipHtml) {
          pubMetaParts.push('<span class="work-item-pub-part">' + chipHtml + '</span>');
        }
        if (venueHtml) {
          pubMetaParts.push('<span class="work-item-pub-part"><span class="work-item-venue-label">' + venueHtml + '</span></span>');
        }
        if (item.date) {
          pubMetaParts.push('<span class="work-item-pub-part"><span class="work-item-year-label">' + escapeHtml(item.date) + '</span></span>');
        }
        if (actions) {
          pubMetaParts.push('<span class="work-item-inline-actions">' + actions + '</span>');
        }
        compactMeta = pubMetaParts.join('');
      } else if (type === 'project') {
        title = item.title || '';
        headerDate = item.date || '';
        meta = '';
        var projectMetaParts = [];
        if (item.organization) {
          projectMetaParts.push('<span class="work-item-inline-org">' + escapeHtml(item.organization) + '</span>');
        }
        if (item.focus) {
          projectMetaParts.push('<span class="work-item-venue-label">' + escapeHtml(item.focus) + '</span>');
        }
        if (item.tools) {
          projectMetaParts.push('<span class="work-item-venue-label">' + escapeHtml(item.tools) + '</span>');
        }
        var projectActions = renderLinks(item.links, metadata, type);
        if (projectActions) {
          projectMetaParts.push('<span class="work-item-inline-actions">' + projectActions + '</span>');
        }
        compactMeta = projectMetaParts.join('<span class="work-item-pub-sep"> | </span>');
        actions = '';
      } else if (type === 'experience') {
        title = item.title || '';
        headerDate = item.date || '';
        meta = '';
        var expMetaParts = [];
        if (item.organization) {
          expMetaParts.push('<span class="work-item-inline-org">' + escapeHtml(item.organization) + '</span>');
        }
        if (item.focus) {
          expMetaParts.push('<span class="work-item-venue-label">' + escapeHtml(item.focus) + '</span>');
        }
        var expActions = renderLinks(item.links, metadata, type);
        if (expActions) {
          expMetaParts.push('<span class="work-item-inline-actions">' + expActions + '</span>');
        }
        compactMeta = expMetaParts.join('<span class="work-item-pub-sep"> | </span>');
        actions = '';
      } else if (type === 'education') {
        title = item.institution || '';
        headerDate = item.date || '';
        meta = '';
        var eduMetaParts = [];
        if (item.degree) {
          eduMetaParts.push('<span class="work-item-inline-org">' + escapeHtml(item.degree) + '</span>');
        }
        if (item.major || item.focus) {
          eduMetaParts.push('<span class="work-item-venue-label">' + escapeHtml(item.major || item.focus) + '</span>');
        }
        compactMeta = eduMetaParts.join('<span class="work-item-pub-sep"> | </span>');
      }

      return '' +
        '<article class="work-item is-compact' +
          (type === 'publication' ? ' is-publication' : '') +
          (type === 'project' ? ' is-project' : '') +
          (type === 'experience' ? ' is-experience' : '') +
          (type === 'education' ? ' is-education' : '') +
        '">' +
          '<div class="work-item-date-column">' +
            '<div class="work-item-date">' + escapeHtml(item.date || '') + '</div>' +
            (type === 'publication' && item.chip ? '<div class="work-item-chips">' + chipHtml + '</div>' : '') +
          '</div>' +
          '<div class="work-item-main">' +
            '<div class="work-item-header">' +
              '<h2 class="work-item-title">' + escapeHtml(title) + '</h2>' +
              (type !== 'publication' && kind ? '<span class="work-item-kind">' + escapeHtml(kind) + '</span>' : '') +
              (type !== 'publication' && headerDate ? '<span class="work-item-header-date">' + escapeHtml(headerDate) + '</span>' : '') +
            '</div>' +
            (meta ? '<p class="work-item-meta">' + (type === 'publication' ? metaHtml : escapeHtml(meta)) + '</p>' : '') +
            (compactMeta ? '<p class="work-item-pub-meta">' + compactMeta + '</p>' : '') +
            (summary ? '<p class="work-item-summary">' + escapeHtml(summary) + '</p>' : '') +
            (type === 'publication' ? '' : notes) +
          '</div>' +
          (actions ? '<div class="work-item-actions">' + actions + '</div>' : '') +
        '</article>';
    }

    sections.forEach(function (section) {
      var root = document.getElementById(section.id);
      if (!root) return;

      fetch(section.path, { cache: 'no-store' })
        .then(function (response) {
          if (!response.ok) throw new Error('Failed to load data.');
          return response.json();
        })
        .then(function (data) {
          var items = Array.isArray(data) ? data : (data && Array.isArray(data.items) ? data.items : []);
          var metadata = data && data.metadata ? data.metadata : {};
          if (section.reverse) {
            items = items.slice().reverse();
          }
          if (!items.length) {
            root.innerHTML = '<p class="posts-archive-summary">No entries yet.</p>';
            return;
          }
          root.innerHTML = items.map(function (item) {
            return renderItem(item, section.type, metadata);
          }).join('');
        })
        .catch(function () {
          root.innerHTML = '<p class="posts-archive-summary">Unable to load entries.</p>';
        });
    });
  });
</script>
