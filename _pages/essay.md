---
layout: default
title: Posts
permalink: /essay/
description:
---

<div class="posts-archive-layout">
  <div class="posts-archive">
    <section class="posts-archive-hero">
      <div class="posts-archive-hero-content">
        <figure class="posts-archive-artwork" id="postsArchiveArtwork" hidden>
          <img id="postsArchiveArtworkImage" alt="" decoding="async" data-zoomable>
          <figcaption id="postsArchiveArtworkCaption"></figcaption>
        </figure>
        <div class="posts-archive-quote">
          <h1 id="postsArchiveQuote">Loading quote...</h1>
          <p class="posts-archive-intro" id="postsArchiveAuthor"></p>
        </div>
      </div>
    </section>

    <nav class="posts-media-tabs" id="postsMediaTabs" aria-label="Post media" hidden></nav>

    <div class="posts-archive-filter-shell" id="postsArchiveFilterShell" hidden>
      <div class="posts-archive-filter" id="postsArchiveFilter"></div>
    </div>

    <section class="posts-archive-groups" id="postsArchiveGroups">
      <p class="posts-archive-summary">Loading posts...</p>
    </section>
  </div>

  <aside class="posts-archive-nav" id="postsArchiveNav" hidden>
    <div class="posts-archive-nav-box" id="postsArchiveNavBox"></div>
  </aside>
</div>

<script>
  document.addEventListener('DOMContentLoaded', function () {
    var groupsRoot = document.getElementById('postsArchiveGroups');
    var filterShell = document.getElementById('postsArchiveFilterShell');
    var filterBar = document.getElementById('postsArchiveFilter');
    var navRoot = document.getElementById('postsArchiveNav');
    var navBox = document.getElementById('postsArchiveNavBox');
    var quoteTitle = document.getElementById('postsArchiveQuote');
    var quoteAuthor = document.getElementById('postsArchiveAuthor');
    var artwork = document.getElementById('postsArchiveArtwork');
    var artworkImage = document.getElementById('postsArchiveArtworkImage');
    var artworkCaption = document.getElementById('postsArchiveArtworkCaption');
    var mediaTabs = document.getElementById('postsMediaTabs');
    if (!groupsRoot) return;
    var allItems = [];
    var activeMedia = 'Research';
    var activeCategory = '';
    var colorMapping = {};
    var mediaOrder = [];

    function escapeHtml(value) {
      return String(value || '')
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#39;');
    }

    function resolveThemeColors(value) {
      if (!value) return { light: '', dark: '' };
      if (typeof value === 'string') return { light: value, dark: value };
      if (typeof value !== 'object') return { light: '', dark: '' };
      var light = value.white || value.light || value.default || '';
      var dark = value.dark || light || '';
      return { light: light, dark: dark };
    }

    function toThemeStyleVars(colors, lightVarName, darkVarName) {
      if (!colors || (!colors.light && !colors.dark)) return '';
      return ' style="' +
        lightVarName + ':' + escapeHtml(colors.light || colors.dark || '') + ';' +
        darkVarName + ':' + escapeHtml(colors.dark || colors.light || '') + ';"';
    }

    function buildPostUrlFromLink(linkValue) {
      var raw = String(linkValue || '').trim();
      if (!raw) return '';

      var normalized = raw.replace(/^\.?\/*/, '');
      normalized = normalized.replace(/^_posts\//, '');

      var fileName = normalized.split('/').pop() || '';
      if (!fileName) return '';

      var stem = fileName.replace(/\.md$/i, '');
      var match = stem.match(/^(\d{4})-(\d{2})(?:-(\d{2}))?-(.+)$/);
      if (!match) return '';

      var year = match[1];
      var slug = match[4];
      var lowerPath = normalized.toLowerCase();
      var lang = '';

      if (/(^|\/)[^/]*_kr\//.test(lowerPath) || /-kr\.md$/i.test(raw)) {
        lang = 'kr';
      } else if (/(^|\/)[^/]*_en\//.test(lowerPath) || /-en\.md$/i.test(raw)) {
        lang = 'en';
      }

      if (lang && !new RegExp('-' + lang + '$', 'i').test(slug)) {
        slug = slug + '-' + lang;
      }
      if (!year || !slug) return '';

      return '{{ "/blog/" | relative_url }}' + year + '/' + encodeURIComponent(slug).replace(/%2F/gi, '/') + '/';
    }

    function isMobileListView() {
      return window.matchMedia && window.matchMedia('(max-width: 900px)').matches;
    }

    function formatDate(dateValue) {
      if (!dateValue) return '';
      var parsed = new Date(dateValue + 'T00:00:00');
      if (Number.isNaN(parsed.getTime())) return dateValue;
      if (isMobileListView()) {
        var yy = String(parsed.getFullYear()).slice(-2);
        var mm = String(parsed.getMonth() + 1).padStart(2, '0');
        var dd = String(parsed.getDate()).padStart(2, '0');
        return yy + '-' + mm + '-' + dd;
      }
      return parsed.toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' });
    }

    function pickRandomQuote(quotes) {
      if (!Array.isArray(quotes) || !quotes.length) return null;
      var activeQuotes = quotes.filter(function (quote) {
        return quote && quote.active === true;
      });
      if (!activeQuotes.length) return null;
      var randomIndex = Math.floor(Math.random() * activeQuotes.length);
      return activeQuotes[randomIndex];
    }

    function pickRandomImage(images) {
      if (!Array.isArray(images) || !images.length) return null;
      var activeImages = images.filter(function (image) {
        return image && image.active === true && String(image.src || '').trim();
      });
      if (!activeImages.length) return null;
      return activeImages[Math.floor(Math.random() * activeImages.length)];
    }

    function renderArtwork(image) {
      if (!artwork || !artworkImage || !image) return;
      var src = String(image.src || '').trim();
      if (!src) return;

      var caption = String(image.author || image.title || '').trim();
      artworkImage.src = src;
      artworkImage.alt = String(image.alt || caption || 'Featured artwork');
      artworkImage.setAttribute('data-zoom-caption', caption);
      if (artworkCaption) {
        artworkCaption.textContent = caption;
        artworkCaption.hidden = !caption;
      }
      artworkImage.addEventListener('error', function () {
        artwork.hidden = true;
        if (artwork.parentElement) artwork.parentElement.classList.remove('has-artwork');
      }, { once: true });
      artwork.hidden = false;
      if (artwork.parentElement) artwork.parentElement.classList.add('has-artwork');
      if (typeof medium_zoom !== 'undefined' && medium_zoom && typeof medium_zoom.attach === 'function') {
        medium_zoom.attach(artworkImage);
      }
    }

    function renderFilterBar() {
      if (!filterBar) return;
      if (!activeCategory) {
        filterBar.innerHTML = '';
        if (filterShell) filterShell.hidden = true;
        return;
      }

      if (filterShell) filterShell.hidden = false;
      filterBar.innerHTML =
        '<button class="posts-archive-filter-pill is-active" type="button" data-category-filter="' + escapeHtml(activeCategory) + '">' +
          escapeHtml(activeCategory) +
        '</button>';
    }

    function renderMediaTabs() {
      if (!mediaTabs) return;
      var mediaNames = Array.from(new Set(allItems.map(function (item) {
        return String(item.media || 'Other').trim() || 'Other';
      }))).sort(function (a, b) {
        return a.localeCompare(b, 'en', { sensitivity: 'base' });
      });

      if (!mediaNames.length) {
        mediaTabs.innerHTML = '';
        mediaTabs.hidden = true;
        return;
      }

      if (mediaNames.indexOf(activeMedia) === -1) {
        activeMedia = mediaNames.indexOf('Research') !== -1 ? 'Research' : mediaNames[0];
      }

      mediaTabs.innerHTML = mediaNames.map(function (mediaName) {
        var isActive = activeMedia === mediaName;
        var label = mediaName;
        var count = allItems.filter(function (item) { return (item.media || 'Other') === mediaName; }).length;
        return '<button class="posts-media-tab' + (isActive ? ' is-active' : '') + '" type="button"' +
          ' data-media-filter="' + escapeHtml(mediaName) + '" aria-pressed="' + (isActive ? 'true' : 'false') + '">' +
          '<span>' + escapeHtml(label) + '</span><small>' + count + '</small></button>';
      }).join('');
      mediaTabs.hidden = false;
    }

    function sectionIdFromName(name) {
      var normalized = String(name || '').toLowerCase().trim()
        .replace(/\s+/g, '-')
        .replace(/[^a-z0-9\-]/g, '');
      return normalized ? 'posts-section-' + normalized : 'posts-section';
    }

    function renderItems(items) {
      return items.map(function (item) {
        var postUrl = buildPostUrlFromLink(item && item.en_link);
        var media = item.media || '';
        var title = escapeHtml(item.title || 'Untitled');
        var category = escapeHtml(item.category || '');
        var date = escapeHtml(formatDate(item.date));
        var postPath = postUrl ? postUrl.replace(/^https?:\/\/[^/]+/i, '') : '';
        var viewsHtml = postPath
          ? '<span class="posts-archive-view-count" data-page-views-path="' + escapeHtml(postPath) + '" data-page-views-locale="en" hidden></span>'
          : '';
        var isHighlight = !!item.highlight;
        var itemClass = isHighlight ? 'posts-archive-item is-highlight' : 'posts-archive-item';
        var titleClass = isHighlight ? 'posts-archive-title is-highlight' : 'posts-archive-title';
        var highlightColors = resolveThemeColors(colorMapping[media] || item.color || '');
        var titleStyle = (isHighlight && (highlightColors.light || highlightColors.dark))
          ? toThemeStyleVars(highlightColors, '--post-highlight-light', '--post-highlight-dark')
          : '';
        return '' +
          '<article class="' + itemClass + '">' +
            '<div class="posts-archive-rail">' +
              '<div class="posts-archive-date-wrap">' +
                '<p class="posts-archive-date">' + date + '</p>' +
              '</div>' +
            '</div>' +
            '<div class="posts-archive-main">' +
              '<h2 class="' + titleClass + '"' + titleStyle + '>' +
                (postUrl
                  ? '<a href="' + postUrl + '">' + title + '</a>'
                  : '<span aria-disabled="true">' + title + '</span>') +
              '</h2>' +
              (viewsHtml ? '<div class="posts-archive-inline-meta">' + viewsHtml + '</div>' : '') +
            '</div>' +
            '<div class="posts-archive-side">' +
              (category ? '<button class="posts-archive-category" type="button" data-category-filter="' + category + '">' + category + '</button>' : '') +
            '</div>' +
          '</article>';
      }).join('');
    }

    function renderPosts() {
      var visibleItems = allItems.filter(function (item) {
        var matchesMedia = !activeMedia || (item.media || 'Other') === activeMedia;
        var matchesCategory = !activeCategory || (item.category || '') === activeCategory;
        return matchesMedia && matchesCategory;
      });

      if (!visibleItems.length) {
        groupsRoot.innerHTML = '<p class="posts-archive-summary">No posts yet.</p>';
        renderFilterBar();
        return;
      }

      var groups = [];
      var seen = {};

      mediaOrder.forEach(function (mediaName) {
        var groupItems = visibleItems.filter(function (item) { return (item.media || '') === mediaName; });
        if (!groupItems.length) return;
        seen[mediaName] = true;
        groups.push({ name: mediaName, items: groupItems });
      });

      visibleItems.forEach(function (item) {
        var mediaName = item.media || 'Other';
        if (seen[mediaName]) return;
        seen[mediaName] = true;
        groups.push({
          name: mediaName,
          items: visibleItems.filter(function (entry) { return (entry.media || 'Other') === mediaName; })
        });
      });

      groupsRoot.innerHTML = groups.map(function (group) {
        var sectionId = sectionIdFromName(group.name);
        var sectionColors = resolveThemeColors(colorMapping[group.name] || '');
        var sectionStyle = toThemeStyleVars(sectionColors, '--post-section-color-light', '--post-section-color-dark');
        return '' +
          '<section class="posts-archive-group" id="' + sectionId + '">' +
            '<div class="posts-archive-section-heading">' +
              '<p class="home-section-label posts-archive-section-label"' + sectionStyle + '>' + escapeHtml(group.name) + '</p>' +
            '</div>' +
            '<div class="posts-archive-list">' +
              '<div class="posts-archive-items">' + renderItems(group.items) + '</div>' +
            '</div>' +
          '</section>';
      }).join('');

      if (navRoot && navBox) {
        navBox.innerHTML = groups.map(function (group) {
          var sectionId = sectionIdFromName(group.name);
          return '<a class="posts-archive-nav-link" href="#' + sectionId + '" data-posts-nav-target="' + sectionId + '">' +
            escapeHtml(group.name) +
          '</a>';
        }).join('');
        navRoot.hidden = groups.length === 0;
      }

      renderFilterBar();
      renderMediaTabs();
    }

    Promise.all([
      fetch('{{ "/data/posts.json" | relative_url }}').then(function (response) {
        if (!response.ok) throw new Error('Failed to load posts.');
        return response.json();
      }),
      fetch('{{ "/data/quote.json" | relative_url }}').then(function (response) {
        if (!response.ok) throw new Error('Failed to load quotes.');
        return response.json();
      }).catch(function () {
        return [];
      }),
      fetch('{{ "/data/images.json" | relative_url }}').then(function (response) {
        if (!response.ok) throw new Error('Failed to load images.');
        return response.json();
      }).catch(function () {
        return [];
      })
    ])
      .then(function (results) {
        var postData = results[0];
        var quotes = results[1];
        var images = results[2];
        var items = Array.isArray(postData) ? postData : (Array.isArray(postData.posts) ? postData.posts : []);
        var meta = postData && typeof postData === 'object' && !Array.isArray(postData) ? postData['meta-data'] || {} : {};

        colorMapping = meta['color-mapping'] || {};
        mediaOrder = Object.keys(colorMapping);

        var selectedQuote = pickRandomQuote(quotes);
        renderArtwork(pickRandomImage(images));
        if (quoteTitle) {
          quoteTitle.textContent = selectedQuote && selectedQuote.quote ? '"' + selectedQuote.quote + '"' : 'Posts';
        }
        if (quoteAuthor) {
          quoteAuthor.textContent = selectedQuote && selectedQuote.author ? selectedQuote.author : '';
        }

        if (!Array.isArray(items) || !items.length) {
          groupsRoot.innerHTML = '<p class="posts-archive-summary">No posts yet.</p>';
          return;
        }

        allItems = items.filter(function (item) {
          return item && item.visible !== false;
        }).sort(function (a, b) {
          return new Date(b.date || 0) - new Date(a.date || 0);
        });
        renderMediaTabs();
        renderPosts();
      })
      .catch(function () {
        if (quoteTitle) quoteTitle.textContent = 'Posts';
        if (quoteAuthor) quoteAuthor.textContent = '';
        groupsRoot.innerHTML = '<p class="posts-archive-summary">Unable to load posts.</p>';
      });

    document.addEventListener('click', function (event) {
      var mediaTrigger = event.target.closest('[data-media-filter]');
      if (mediaTrigger) {
        activeMedia = mediaTrigger.getAttribute('data-media-filter') || '';
        activeCategory = '';
        renderPosts();
        return;
      }

      var navTrigger = event.target.closest('[data-posts-nav-target]');
      if (navTrigger) {
        event.preventDefault();
        var targetId = navTrigger.getAttribute('data-posts-nav-target') || '';
        var target = targetId ? document.getElementById(targetId) : null;
        if (!target) return;
        var top = window.scrollY + target.getBoundingClientRect().top - 86;
        window.scrollTo({ top: top, behavior: 'smooth' });
        return;
      }

      var trigger = event.target.closest('[data-category-filter]');
      if (!trigger) return;

      var selected = trigger.getAttribute('data-category-filter') || '';
      activeCategory = activeCategory === selected ? '' : selected;
      renderPosts();
    });
  });
</script>
