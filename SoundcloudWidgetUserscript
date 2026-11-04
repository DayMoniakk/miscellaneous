// ---------------------------------------------------------------------------------------------------------------------------------------------------
// INFO
// Nan7s_peluche's (https://www.twitch.tv/nan7s_peluche) widget for her streams, it requires the "Violentmonkey" (or Tampermonkey) extension to use.
// Also very scuffed since you need to capture the widget with a window capture in OBS, but it eliminates the need for sketchy browser extensions.
// ---------------------------------------------------------------------------------------------------------------------------------------------------
// LICENSE NOTICE
// Uses the "query", "timestamp_to_ms" and "collectedSoundCloud from this extension: https://github.com/tizhproger/now_playing (GPL v3.0)
// Everything else is custom
// ---------------------------------------------------------------------------------------------------------------------------------------------------

// ==UserScript==
// @name         Soundcloud Widget
// @namespace    https://github.com/DayMoniakk
// @version      2026-11-03
// @description  Now playing
// @author       DayMoniakk
// @match        https://soundcloud.com/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=soundcloud.com
// @grant        none
// ==/UserScript==

let lastWidget;
let widgetImage, widgetTitle, widgetArtist, widgetProgressContainer, widgetProgress, widgetCurrentTime, widgetSongTime;

(function() {
    'use strict';

    spawnWidget();

    widgetImage = document.querySelector('.now-playing-widget img');
    widgetTitle = document.querySelector('.now-playing-widget h1');
    widgetArtist = document.querySelector('.now-playing-widget h2');
    widgetProgressContainer = document.querySelector('.now-playing-progress-container');
    widgetProgress = document.querySelector('#now-playing-progress');
    widgetCurrentTime = document.querySelector('.now-playing-widget #current-time');
    widgetSongTime = document.querySelector('.now-playing-widget #song-time');

    widgetProgressContainer.style.display = 'none';

    setInterval(() => {
      let { cover, title, artists, status, progress, duration, song_link } = collectSoundCloud();

      if (status !== "playing") {
        widgetImage.src = 'https://cdn.7tv.app/emote/01HRHQ91MR000E6CRVZHEAPK97/4x.avif';
        widgetTitle.textContent = 'No song playing';
        widgetArtist.textContent = '';
        widgetProgressContainer.style.display = 'none';
        return;
      }

      widgetImage.src = cover;
      widgetTitle.textContent = title;
      widgetArtist.textContent = artists.join(', ');
      widgetProgress.value = (progress / duration) * 100;
      widgetProgress.max = (duration / duration) * 100;
      widgetCurrentTime.textContent = new Date(progress).toISOString().slice(14, 19);
      widgetSongTime.textContent = new Date(duration).toISOString().slice(14, 19);
      widgetProgressContainer.style.display = 'flex';
    }, 1000);
})();

function query(selector, fn, alt = null) {
  const el = document.querySelector(selector);
  if (!el) return alt;
  try {
    return fn(el);
  } catch (_) {
    return alt;
  }
}

function timestamp_to_ms(ts) {
  if (!ts) return 0;

  ts = String(ts).trim();
  const parts = ts.split(':').map(Number);

  if (parts.some(isNaN)) return 0;

  if (parts.length === 2) {
    const [m, s] = parts;
    return (m * 60 + s) * 1000;
  }

  if (parts.length === 3) {
    const [h, m, s] = parts;
    return (h * 3600 + m * 60 + s) * 1000;
  }

  return 0;
}

function collectSoundCloud() {
  const status = query('.playControl', e =>
    e.classList.contains('playing') ? 'playing' : 'stopped', 'unknown');

  const cover = query('.playbackSoundBadge span.sc-artwork', e =>
    e.style.backgroundImage
      ? e.style.backgroundImage.slice(5, -2).replace('t50x50', 't500x500')
      : '', '');

  const title = query('.playbackSoundBadge__titleLink', e => e.title || '');
  const artists = [query('.playbackSoundBadge__lightLink', e => e.title || '', '')];

  const progress = query('.playbackTimeline__timePassed span:nth-child(2)',
    e => timestamp_to_ms(e.textContent), 0);

  const duration = query('.playbackTimeline__duration span:nth-child(2)',
    e => timestamp_to_ms(e.textContent), 0);

  let song_link = '';
  const avatars = document.getElementsByClassName('playbackSoundBadge__avatar');
  if (avatars.length > 0) {
    song_link = avatars[0].href.split('?')[0];
  }

  return { cover, title, artists, status, progress, duration, song_link };
}

function spawnWidget() {
  if (lastWidget !== undefined) {
    lastWidget.remove();
  }

  const widget = document.createElement("div");
  widget.className = 'now-playing-widget';

  widget.innerHTML = `
        <img src="https://cdn.7tv.app/emote/01HRHQ91MR000E6CRVZHEAPK97/4x.avif" alt="">
        <div class="now-playing-details">
            <h1>No song playing</h1>
            <h2></h2>
            <div class="now-playing-progress-container">
                <span id="current-time">00:00</span>
                <progress id="now-playing-progress" max="100" value="0"></progress>
                <span id="song-time">00:00</span>
            </div>
        </div>
  `;

  const style = document.createElement('style');
  style.textContent = `
   @import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100..900;1,100..900&display=swap');

    .now-playing-widget {
        --widget-width: 600px;
        display: flex;
        background-color: rgb(31, 31, 31);
        color: white;
        max-width: var(--widget-width);
        width: var(--widget-width);
        bottom: 48px;
        right: 0;
        position: fixed;
        gap: 10px;
        justify-content: center;
        align-items: center;
        padding: 10px;
        max-height: 120px;
        overflow: hidden;
        z-index: 10000;
        user-select: none;
        font-family: "Roboto", sans-serif;
    }

    .now-playing-widget img {
        width: 100px;
        height: 100px;
        -webkit-user-drag: none;
    }

    .now-playing-details {
        display: flex;
        flex-direction: column;
        justify-content: start;
        width: 100%;
        min-height: 100px;
    }

    .now-playing-details h1 {
        font-size: 24px;
        max-width: 490px;
        margin: 0;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .now-playing-details h2 {
        font-size: 18px;
        margin: 0;
        max-width: 490px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .now-playing-progress-container {
        display: flex;
        justify-content: space-between;
        align-items: center;
        gap: 8px;
        margin-top: auto;
    }

    .now-playing-progress-container progress {
        width: 100%;
        height: 30px;
        accent-color: rgb(255, 255, 255);
    }

    .now-playing-progress-container span {
        font-size: 18px;
        font-weight: bold;
        max-width: 50px;
        min-width: 50px;
        text-align: center;
    }
  `;

  document.body.appendChild(style);
  document.body.appendChild(widget);
  lastWidget = widget;
}
