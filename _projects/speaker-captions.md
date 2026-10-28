---
layout: page
title: Speaker Captions
description: A local captioning app that combines speech recognition and face tracking to label who is speaking in a video.
img: assets/img/projects/speaker-captions/poster.jpg
importance: 2
category: Learning
permalink: /projects/speaker-captions/
related_publications: false
---

<link rel="stylesheet" href="{{ '/assets/css/speaker-captions.css' | relative_url }}">

<div class="speaker-project">
  <section class="speaker-hero" aria-labelledby="speaker-introduction">
    <p class="speaker-eyebrow">Speech recognition and computer vision</p>
    <h2 id="speaker-introduction">Captions with<br><span>speaker labels.</span></h2>
    <p class="speaker-intro">In this project, I worked on combining speech recognition with face tracking to generate captions and associate them with the person speaking. The app runs locally and supports both live camera input and recorded videos. In my idea, this app was intended to assist imparied people to understand who is talking and keep track of the speaker visually.</p>
    <div class="speaker-actions">
      <a class="speaker-button" href="#speaker-demo">Watch the demo <span aria-hidden="true">↗</span></a>
      <a class="speaker-source" href="https://github.com/KingPowa/lip_reading">Code on GitHub <span aria-hidden="true">↗</span></a>
    </div>
    <ul class="speaker-tags" aria-label="Project technologies"><li>Python</li><li>Computer vision</li><li>Speech recognition</li><li>Electron</li></ul>
  </section>

  <figure class="speaker-demo" id="speaker-demo">
    <div class="speaker-window"><span class="speaker-window-dots" aria-hidden="true">● ● ●</span><span>Speaker Captions / offline output</span><span class="speaker-window-label">24 SEC</span></div>
    <video controls playsinline preload="metadata" poster="{{ '/assets/img/projects/speaker-captions/poster.jpg' | relative_url }}" aria-label="Speaker Captions demo: an Italian parliamentary speech with generated speaker labels, highlights, and burned-in captions" aria-describedby="speaker-demo-caption">
      <source src="{{ '/assets/video/speaker-captions/speaker-tracking.mp4' | relative_url }}" type="video/mp4">
      <track kind="captions" src="{{ '/assets/video/speaker-captions/captions.vtt' | relative_url }}" srclang="it" label="Italian (generated)">
      Your browser does not support embedded video. <a href="{{ '/assets/video/speaker-captions/speaker-tracking.mp4' | relative_url }}">Download the demo</a>.
    </video>
    <figcaption id="speaker-demo-caption"><strong>Example output</strong> This clip shows 24 seconds of a Giuseppe Conte speech processed by the app. The highlighted person and their captions share a colour. The known voice stays linked to Person 1’s tracked face, keeping the rectangle and caption consistent through brief visual-score fluctuations. The audio and captions are in Italian.</figcaption>
  </figure>

  <section class="speaker-section" aria-labelledby="speaker-modes">
    <p class="speaker-eyebrow">Features</p>
    <h2 id="speaker-modes">Live captions and video processing</h2>
    <div class="speaker-grid">
      <div class="speaker-card"><span class="speaker-card-number">LIVE</span><h3>Camera and microphone</h3><p>The Mac app highlights the person speaking and displays their captions in a transcript. It uses face and voice features to keep speaker labels consistent during a session, including when someone leaves and returns to the frame.</p><p class="speaker-detail">Vosk · MediaPipe · SFace · ECAPA</p></div>
      <div class="speaker-card"><span class="speaker-card-number">OFFLINE</span><h3>Recorded videos</h3><p>For recorded videos, the app exports an MP4 with highlights and captions, a subtitle file, and JSON files with the intermediate results. A review page helps check the transcript alongside the video.</p><p class="speaker-detail">Whisper · ECAPA · MediaPipe · Light-ASD</p></div>
    </div>
  </section>

  <section class="speaker-section" aria-labelledby="speaker-pipeline">
    <p class="speaker-eyebrow">Implementation</p>
    <h2 id="speaker-pipeline">How it works</h2>
    <ol class="speaker-pipeline">
      <li><span>01</span><div><h3>Speech recognition</h3><p>Speech recognition produces the transcript and word timestamps. Voice embeddings are used to group speech from the same person, without identifying who they are.</p></div></li>
      <li><span>02</span><div><h3>Face tracking</h3><p>Face tracking locates people in the video. For recorded videos, Light-ASD estimates which face is speaking. Live mode uses mouth movement together with evidence collected over recent frames.</p></div></li>
      <li><span>03</span><div><h3>Speaker assignment</h3><p>The audio and video results are aligned by timestamp to assign each caption to a person. The app also handles cases where the speaker is off camera or the assignment is uncertain.</p></div></li>
    </ol>
  </section>

  <section class="speaker-learning" aria-labelledby="speaker-lessons">
    <p class="speaker-eyebrow">Development notes</p>
    <h2 id="speaker-lessons">Challenges and limitations</h2>
    <p>Much of the work went into speaker assignment. A listener moving their mouth can be mistaken for the speaker, small faces are difficult to track, and a person may keep talking after leaving the frame. I used replays of the example video to check the results and tune the live pipeline.</p>
    <p>The transcript comes from audio, so the app does not read lips from silent video. Speaker assignment is still experimental and has mainly been evaluated on one recording. Overlapping speech, background noise, and small faces can lead to mistakes.</p>
    <p>The app is still unreliable for multiple speakers, which is next step of development. It works, but it has difficulties in distinguishing speakers when they talk together or move out of the camera.</p>
    <div class="speaker-notes"><p><strong>Local processing.</strong> Models are downloaded on first use and run on the machine. Speaker labels are kept within each recording or live session.</p><p><strong>AI assistance.</strong> The code was created with Claude, with my supervision of the design.</p></div>
    <a class="speaker-source" href="https://github.com/KingPowa/lip_reading#readme">Documentation and evaluation notes <span aria-hidden="true">↗</span></a>
  </section>
</div>
