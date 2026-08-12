# SaaS Parody Landing Page — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a 2-page SaaS parody: Page 1 looks like a serious VC-backed startup ("Joke.asia") but the CTA leads to Page 2 — an unstoppable fullscreen video trap.

**Architecture:** Two standalone HTML files — `index.html` (parody landing) and `gotcha.html` (video trap). No framework, inline CSS/JS, dark glassmorphism theme. CTA on index opens gotcha in a new tab.

**Tech Stack:** HTML5, CSS3 (grid/flexbox/glassmorphism), vanilla JS. No dependencies.

## Global Constraints

- Single file per page, all CSS/JS inline
- Dark theme: bg `#0a0a0f`, cards with `rgba(255,255,255,0.05)` + `backdrop-filter: blur()`
- Font: system font stack (`-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`)
- Responsive but desktop-first (mobile works, not pixel-perfect)
- Video URL: placeholder `https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4` — user will replace
- gotcha.html must: lock volume at 1.0, prevent pause, WakeLock, beforeunload nag, loop forever

---

### Task 1: Rewrite index.html — SaaS Parody Landing Page

**Files:**
- Modify: `index.html` (full rewrite)

**Interfaces:**
- Produces: CTA button links to `gotcha.html` via `target="_blank"`
- Produces: All content is hardcoded (no API calls)

- [ ] **Step 1: Write the complete index.html**

Replace the entire file with:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Joke.asia — Enterprise Humor Delivery Platform</title>
    <style>
        *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: #0a0a0f;
            color: #e4e4e7;
            line-height: 1.6;
            overflow-x: hidden;
        }
        /* Grid background texture */
        body::before {
            content: '';
            position: fixed; inset: 0;
            background-image:
                linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px),
                linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px);
            background-size: 60px 60px;
            pointer-events: none; z-index: 0;
        }
        /* Glow orbs */
        .glow-orb {
            position: fixed; border-radius: 50%; filter: blur(120px); opacity: 0.15; z-index: 0;
            pointer-events: none;
        }
        .glow-orb-1 { width: 600px; height: 600px; background: #7c3aed; top: -200px; left: -100px; }
        .glow-orb-2 { width: 500px; height: 500px; background: #06b6d4; bottom: -200px; right: -100px; }
        .glow-orb-3 { width: 400px; height: 400px; background: #f59e0b; top: 50%; left: 50%; transform: translate(-50%, -50%); }

        .container { max-width: 1100px; margin: 0 auto; padding: 0 24px; position: relative; z-index: 1; }

        /* Nav */
        nav {
            display: flex; justify-content: space-between; align-items: center;
            padding: 20px 0; border-bottom: 1px solid rgba(255,255,255,0.08);
        }
        .logo { font-size: 24px; font-weight: 800; letter-spacing: -0.5px; }
        .logo .dot { color: #7c3aed; }
        .nav-links { display: flex; gap: 32px; align-items: center; }
        .nav-links a { color: #a1a1aa; text-decoration: none; font-size: 14px; transition: color 0.2s; }
        .nav-links a:hover { color: #fff; }
        .nav-cta {
            background: #7c3aed; color: #fff; border: none; padding: 10px 20px;
            border-radius: 8px; font-size: 14px; font-weight: 600; cursor: pointer;
            transition: background 0.2s;
        }
        .nav-cta:hover { background: #6d28d9; }

        /* Hero */
        .hero { text-align: center; padding: 80px 0 60px; }
        .badge {
            display: inline-block; padding: 6px 16px; border-radius: 20px;
            border: 1px solid rgba(124,58,237,0.4); background: rgba(124,58,237,0.1);
            color: #a78bfa; font-size: 13px; font-weight: 500; margin-bottom: 24px;
        }
        .hero h1 { font-size: clamp(36px, 5vw, 64px); font-weight: 800; line-height: 1.1; letter-spacing: -1.5px; margin-bottom: 20px; }
        .hero h1 .gradient { background: linear-gradient(135deg, #7c3aed, #06b6d4); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .hero p { font-size: 18px; color: #a1a1aa; max-width: 600px; margin: 0 auto 32px; }
        .hero-cta {
            display: inline-flex; align-items: center; gap: 8px;
            background: #7c3aed; color: #fff; border: none;
            padding: 16px 36px; border-radius: 12px; font-size: 18px; font-weight: 700;
            cursor: pointer; text-decoration: none;
            transition: transform 0.2s, box-shadow 0.2s;
            box-shadow: 0 0 40px rgba(124,58,237,0.3);
        }
        .hero-cta:hover { transform: translateY(-2px); box-shadow: 0 0 60px rgba(124,58,237,0.5); }
        .hero-cta .arrow { font-size: 20px; transition: transform 0.2s; }
        .hero-cta:hover .arrow { transform: translateX(4px); }

        /* Investors */
        .investors { text-align: center; padding: 20px 0 60px; }
        .investors p { font-size: 12px; text-transform: uppercase; letter-spacing: 2px; color: #52525b; margin-bottom: 20px; }
        .investor-logos { display: flex; justify-content: center; gap: 48px; flex-wrap: wrap; }
        .investor-logo { font-size: 18px; font-weight: 700; color: #71717a; opacity: 0.7; }

        /* Stats */
        .stats {
            display: grid; grid-template-columns: repeat(3, 1fr); gap: 24px;
            padding: 0 0 80px;
        }
        .stat-card {
            background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.06);
            border-radius: 16px; padding: 32px; text-align: center;
            backdrop-filter: blur(10px);
        }
        .stat-number { font-size: 40px; font-weight: 800; letter-spacing: -1px; margin-bottom: 8px; }
        .stat-number.purple { color: #a78bfa; }
        .stat-number.cyan { color: #22d3ee; }
        .stat-number.amber { color: #fbbf24; }
        .stat-label { font-size: 14px; color: #71717a; }

        /* Pricing */
        .pricing { padding: 0 0 80px; }
        .section-title { text-align: center; font-size: 36px; font-weight: 800; letter-spacing: -1px; margin-bottom: 48px; }
        .pricing-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 24px; }
        .pricing-card {
            background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.06);
            border-radius: 20px; padding: 36px; backdrop-filter: blur(10px);
            transition: border-color 0.2s; position: relative;
        }
        .pricing-card.featured { border-color: rgba(124,58,237,0.5); background: rgba(124,58,237,0.05); }
        .popular-badge {
            position: absolute; top: -12px; right: 24px;
            background: #7c3aed; color: #fff; padding: 4px 14px; border-radius: 12px;
            font-size: 12px; font-weight: 600;
        }
        .plan-name { font-size: 18px; font-weight: 700; margin-bottom: 4px; }
        .plan-price { font-size: 42px; font-weight: 800; letter-spacing: -1px; margin-bottom: 4px; }
        .plan-price span { font-size: 16px; color: #71717a; font-weight: 400; }
        .plan-subtitle { font-size: 13px; color: #52525b; margin-bottom: 24px; }
        .plan-features { list-style: none; margin-bottom: 28px; }
        .plan-features li { padding: 8px 0; font-size: 14px; color: #a1a1aa; }
        .plan-features li::before { content: '✓ '; color: #22c55e; margin-right: 6px; }
        .plan-btn {
            display: block; width: 100%; padding: 14px; border-radius: 10px;
            font-size: 15px; font-weight: 600; cursor: pointer; text-align: center;
            border: 1px solid rgba(255,255,255,0.1); background: rgba(255,255,255,0.05);
            color: #fff; transition: background 0.2s;
        }
        .plan-btn:hover { background: rgba(255,255,255,0.1); }
        .plan-btn.primary { background: #7c3aed; border-color: #7c3aed; }
        .plan-btn.primary:hover { background: #6d28d9; }

        /* Testimonials parody */
        .testimonials { padding: 0 0 80px; }
        .testimonial-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 24px; }
        .testimonial-card {
            background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.06);
            border-radius: 16px; padding: 28px; backdrop-filter: blur(10px);
        }
        .testimonial-text { font-size: 15px; color: #d4d4d8; margin-bottom: 16px; font-style: italic; }
        .testimonial-author { display: flex; align-items: center; gap: 12px; }
        .testimonial-avatar {
            width: 40px; height: 40px; border-radius: 50%;
            background: linear-gradient(135deg, #7c3aed, #06b6d4);
            display: flex; align-items: center; justify-content: center;
            font-weight: 700; font-size: 16px;
        }
        .testimonial-name { font-size: 14px; font-weight: 600; }
        .testimonial-role { font-size: 12px; color: #71717a; }

        /* Footer */
        footer {
            border-top: 1px solid rgba(255,255,255,0.08);
            padding: 40px 0; text-align: center; color: #52525b; font-size: 13px;
        }
        footer a { color: #71717a; text-decoration: none; }
        footer a:hover { color: #a1a1aa; }
        .footer-links { display: flex; justify-content: center; gap: 32px; margin-bottom: 16px; }

        /* Responsive */
        @media (max-width: 768px) {
            .stats, .pricing-grid, .testimonial-grid { grid-template-columns: 1fr; }
            .nav-links { display: none; }
            .hero h1 { font-size: 32px; }
        }
    </style>
</head>
<body>
    <!-- Glow orbs -->
    <div class="glow-orb glow-orb-1"></div>
    <div class="glow-orb glow-orb-2"></div>
    <div class="glow-orb glow-orb-3"></div>

    <div class="container">
        <!-- Nav -->
        <nav>
            <div class="logo">Joke<span class="dot">.</span>asia</div>
            <div class="nav-links">
                <a href="#">Docs</a>
                <a href="#">API</a>
                <a href="#">Enterprise</a>
                <a href="#">Blog</a>
                <button class="nav-cta" onclick="window.open('gotcha.html', '_blank')">Dashboard →</button>
            </div>
        </nav>

        <!-- Hero -->
        <section class="hero">
            <div class="badge">🎤 Just raised ₹69 Cr Series C</div>
            <h1>Enterprise-Grade<br><span class="gradient">Laughter, Delivered at Scale</span></h1>
            <p>Joke.asia is the world's first AI-powered humor delivery platform. We leverage deep learning to generate context-aware punchlines with 99.97% LOL rate. Backed by Sequoia, YC, and your mom's approval.</p>
            <a href="gotcha.html" target="_blank" class="hero-cta">
                Get Unlimited Jokes Free <span class="arrow">→</span>
            </a>
        </section>

        <!-- Investors -->
        <section class="investors">
            <p>Backed by the best investors in the galaxy</p>
            <div class="investor-logos">
                <span class="investor-logo">🔴 Sequoia</span>
                <span class="investor-logo">🧡 a16z</span>
                <span class="investor-logo">💜 Y Combinator</span>
                <span class="investor-logo">💙 Tiger Global</span>
                <span class="investor-logo">🤍 SoftBank (kinda)</span>
            </div>
        </section>

        <!-- Stats -->
        <section class="stats">
            <div class="stat-card">
                <div class="stat-number purple">10M+</div>
                <div class="stat-label">Jokes Delivered Globally</div>
            </div>
            <div class="stat-card">
                <div class="stat-number cyan">50ms</div>
                <div class="stat-label">Average Punchline Delivery Time</div>
            </div>
            <div class="stat-card">
                <div class="stat-number amber">0</div>
                <div class="stat-label">Dad Jokes in Our Database ✋</div>
            </div>
        </section>

        <!-- Pricing -->
        <section class="pricing">
            <h2 class="section-title">Simple, Transparent Pricing</h2>
            <div class="pricing-grid">
                <!-- Starter -->
                <div class="pricing-card">
                    <div class="plan-name">Starter</div>
                    <div class="plan-price">₹0<span>/month</span></div>
                    <div class="plan-subtitle">For people who are broke but still want to laugh</div>
                    <ul class="plan-features">
                        <li>1 joke per day</li>
                        <li>Text-only delivery</li>
                        <li>50% chance it's actually funny</li>
                        <li>No refunds (it's free lol)</li>
                    </ul>
                    <button class="plan-btn" onclick="window.open('gotcha.html', '_blank')">Start Laughing →</button>
                </div>
                <!-- Pro -->
                <div class="pricing-card featured">
                    <div class="popular-badge">Most Popular</div>
                    <div class="plan-name">Pro</div>
                    <div class="plan-price">₹999<span>/month</span></div>
                    <div class="plan-subtitle">For serious humor professionals</div>
                    <ul class="plan-features">
                        <li>Unlimited jokes, 24/7</li>
                        <li>Multi-format: text, audio, GIF</li>
                        <li>AI personalization engine</li>
                        <li>Slack & WhatsApp integration</li>
                        <li>Priority laugh track</li>
                    </ul>
                    <button class="plan-btn primary" onclick="window.open('gotcha.html', '_blank')">Get Pro →</button>
                </div>
                <!-- Enterprise -->
                <div class="pricing-card">
                    <div class="plan-name">Enterprise</div>
                    <div class="plan-price">₹Call<span> us, we dare you</span></div>
                    <div class="plan-subtitle">For corporations that take jokes way too seriously</div>
                    <ul class="plan-features">
                        <li>Everything in Pro</li>
                        <li>Dedicated joke concierge</li>
                        <li>Blockchain-verified punchlines</li>
                        <li>SLA: 99.999% uptime (lol)</li>
                        <li>We'll name a server after you</li>
                    </ul>
                    <button class="plan-btn" onclick="window.open('gotcha.html', '_blank')">Contact Sales →</button>
                </div>
            </div>
        </section>

        <!-- Testimonials -->
        <section class="testimonials">
            <h2 class="section-title">What Our Users Say*</h2>
            <div class="testimonial-grid">
                <div class="testimonial-card">
                    <div class="testimonial-text">"Ever since we integrated Joke.asia into our Slack, productivity is down 40% but morale is through the roof. Our CEO is concerned but also laughing."</div>
                    <div class="testimonial-author">
                        <div class="testimonial-avatar">S</div>
                        <div>
                            <div class="testimonial-name">Sarah K.</div>
                            <div class="testimonial-role">CTO, DefinitelyReal Corp</div>
                        </div>
                    </div>
                </div>
                <div class="testimonial-card">
                    <div class="testimonial-text">"I showed their API docs to my investors and they believed it for a solid 3 minutes. The jokes are decent too I guess."</div>
                    <div class="testimonial-author">
                        <div class="testimonial-avatar">R</div>
                        <div>
                            <div class="testimonial-name">Rahul M.</div>
                            <div class="testimonial-role">Founder, NotARealStartup.in</div>
                        </div>
                    </div>
                </div>
                <div class="testimonial-card">
                    <div class="testimonial-text">"We pay ₹999/month for jokes and honestly, it's still a better investment than our NFT collection. Zero regrets."</div>
                    <div class="testimonial-author">
                        <div class="testimonial-avatar">A</div>
                        <div>
                            <div class="testimonial-name">Anonymous Buyer</div>
                            <div class="testimonial-role">Verified Purchase · 2 mins ago</div>
                        </div>
                    </div>
                </div>
                <div class="testimonial-card">
                    <div class="testimonial-text">"The blockchain-verified punchlines changed everything. Now when I laugh, there's an immutable ledger entry proving it was actually funny."</div>
                    <div class="testimonial-author">
                        <div class="testimonial-avatar">V</div>
                        <div>
                            <div class="testimonial-name">Vikram P.</div>
                            <div class="testimonial-role">VP of LOL, Web3 Humor DAO</div>
                        </div>
                    </div>
                </div>
            </div>
            <p style="text-align:center; margin-top: 20px; font-size: 12px; color: #52525b;">* All testimonials are completely real and definitely not made up at 3 AM.</p>
        </section>

        <!-- Footer -->
        <footer>
            <div class="footer-links">
                <a href="#">Privacy Policy (we read your jokes)</a>
                <a href="#">Terms (you'll never read this)</a>
                <a href="#">Status (always green)</a>
                <a href="#">Twitter (we're funny there too)</a>
            </div>
            <p>© 2026 Joke.asia — A RAGSPRO Joint. Not actually incorporated. Don't sue us.</p>
        </footer>
    </div>
</body>
</html>
```

- [ ] **Step 2: Commit index.html**

```bash
cd /Users/raghavshah/landing-page && git add index.html && git commit -m "feat: SaaS parody landing page with glassmorphism dark theme"
```

---

### Task 2: Create gotcha.html — The Unstoppable Video Trap

**Files:**
- Create: `gotcha.html`

**Interfaces:**
- Consumes: Opened via `window.open('gotcha.html', '_blank')` or direct link from index.html CTA buttons
- Produces: None (dead-end page, user's only escape is closing browser)

- [ ] **Step 1: Write gotcha.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>😂 Loading Your Unlimited Jokes...</title>
    <style>
        *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
        html, body { width: 100%; height: 100%; overflow: hidden; background: #000; }
        video { position: fixed; top: 0; left: 0; width: 100%; height: 100%; object-fit: cover; z-index: 1; }
        /* Loading overlay — shown briefly before chaos */
        #loading {
            position: fixed; inset: 0; z-index: 10;
            background: #0a0a0f;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            color: #fff; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            transition: opacity 0.6s;
        }
        #loading .spinner {
            width: 48px; height: 48px; border: 3px solid rgba(124,58,237,0.2);
            border-top-color: #7c3aed; border-radius: 50%;
            animation: spin 0.8s linear infinite;
            margin-bottom: 20px;
        }
        @keyframes spin { to { transform: rotate(360deg); } }
        #loading p { font-size: 16px; color: #a1a1aa; }
        #loading.fade-out { opacity: 0; pointer-events: none; }
    </style>
</head>
<body>
    <!-- Loading screen -->
    <div id="loading">
        <div class="spinner"></div>
        <p>Generating AI-powered jokes for you...</p>
    </div>

    <!-- Video element — injected by JS -->
    <div id="videoContainer"></div>

    <script>
        (function() {
            // ⚠️ WARNING: This is a prank page. Close this tab to escape.

            // === CONFIG ===
            // Replace this with your actual video URL
            const VIDEO_URL = 'https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4';

            // === DOM REFS ===
            const loading = document.getElementById('loading');
            const container = document.getElementById('videoContainer');

            // === CREATE VIDEO ===
            const video = document.createElement('video');
            video.src = VIDEO_URL;
            video.autoplay = true;
            video.loop = true;
            video.muted = false;
            video.playsInline = false;
            video.controls = false;
            video.disablePictureInPicture = true;
            video.style.cssText = 'position:fixed;top:0;left:0;width:100%;height:100%;object-fit:cover;z-index:1;';

            // === VOLUME LOCK: Force max volume, no escape ===
            video.volume = 1.0;
            video.addEventListener('volumechange', () => {
                if (video.volume < 1.0) {
                    video.volume = 1.0;
                }
            });

            // === PREVENT PAUSE ===
            video.addEventListener('pause', () => {
                video.play().catch(() => {});
            });

            // === PREVENT SEEKING (skip) ===
            video.addEventListener('seeking', () => {
                if (!video.paused) {
                    video.currentTime = video.duration - 0.1;
                }
            });

            // === KEEP SCREEN AWAKE (WakeLock API) ===
            let wakeLock = null;
            async function requestWakeLock() {
                try {
                    if ('wakeLock' in navigator) {
                        wakeLock = await navigator.wakeLock.request('screen');
                        wakeLock.addEventListener('release', () => {
                            // Re-request immediately if released
                            requestWakeLock();
                        });
                    }
                } catch (e) {
                    // WakeLock not supported or denied — fallback: keep video playing
                    console.log('WakeLock unavailable, using video keep-alive');
                }
            }
            requestWakeLock();

            // === RE-REQUEST WAKELOCK ON VISIBILITY CHANGE ===
            document.addEventListener('visibilitychange', () => {
                if (document.visibilityState === 'visible' && wakeLock === null) {
                    requestWakeLock();
                }
            });

            // === ANNOYING BEFOREUNLOAD NAG ===
            window.addEventListener('beforeunload', (e) => {
                e.preventDefault();
                e.returnValue = 'Are you sure you want to stop laughing? Your jokes are still loading! 😢';
                return e.returnValue;
            });

            // === BLOCK KEYBOARD SHORTCUTS ===
            document.addEventListener('keydown', (e) => {
                // Block: Escape, Ctrl+W, Ctrl+F4, Alt+F4, Ctrl+Tab
                const blocked = [
                    'Escape',
                    { key: 'w', ctrlKey: true },
                    { key: 'W', ctrlKey: true },
                    { key: 'F4', altKey: true },
                ];
                const shouldBlock = blocked.some(b => {
                    if (typeof b === 'string') return e.key === b;
                    return e.key === b.key &&
                        (b.ctrlKey === undefined || e.ctrlKey === b.ctrlKey) &&
                        (b.altKey === undefined || e.altKey === b.altKey);
                });
                if (shouldBlock) {
                    e.preventDefault();
                    e.stopPropagation();
                }
            });

            // === PREVENT RIGHT-CLICK ===
            document.addEventListener('contextmenu', (e) => e.preventDefault());

            // === AUTO FULLSCREEN ===
            function goFullscreen() {
                const el = document.documentElement;
                (el.requestFullscreen || el.webkitRequestFullscreen || el.msRequestFullscreen)
                    .call(el).catch(() => {});
            }

            // === START THE CHAOS ===
            function startChaos() {
                container.appendChild(video);
                loading.classList.add('fade-out');

                // Start playback
                const playPromise = video.play();
                if (playPromise !== undefined) {
                    playPromise.then(() => {
                        goFullscreen();
                        // Keep trying fullscreen every 2 seconds
                        setInterval(goFullscreen, 2000);
                    }).catch(() => {
                        // Autoplay blocked — show click prompt
                        loading.classList.remove('fade-out');
                        loading.querySelector('p').textContent = '🔊 Tap anywhere to unlock jokes';
                        loading.querySelector('.spinner').style.display = 'none';

                        const clickHandler = () => {
                            document.removeEventListener('click', clickHandler);
                            video.muted = false;
                            video.volume = 1.0;
                            video.play().then(() => {
                                loading.classList.add('fade-out');
                                goFullscreen();
                                setInterval(goFullscreen, 2000);
                            }).catch(() => {});
                        };
                        document.addEventListener('click', clickHandler);
                    });
                }

                // Remove loading overlay after 3 seconds
                setTimeout(() => {
                    if (loading.parentNode) loading.remove();
                }, 3000);
            }

            // === DELAYED START: Loading fake first ===
            setTimeout(startChaos, 1500);

            // === LOG EASTER EGG ===
            console.log('%c😂 Gotcha! %cClose this tab to escape. %cMaybe.',
                'font-size:40px;',
                'font-size:16px;',
                'font-size:12px;color:#52525b;');
            console.log('%cP.S. There are no jokes. There was never a joke API. 💀',
                'color:#7c3aed;font-size:14px;');
        })();
    </script>
</body>
</html>
```

- [ ] **Step 2: Commit gotcha.html**

```bash
cd /Users/raghavshah/landing-page && git add gotcha.html && git commit -m "feat: unstoppable video trap gotcha page"
```

---

### Task 3: Push to GitHub and Verify Vercel Deploy

**Files:**
- None modified (just git push)

- [ ] **Step 1: Push both commits to GitHub**

```bash
cd /Users/raghavshah/landing-page && git push
```

- [ ] **Step 2: Verify Vercel auto-deploy**

Wait ~30 seconds after push, then check the Vercel dashboard or open the live URL:
`https://landing-page-tau-rouge.vercel.app`

- [ ] **Step 3: Test the prank flow manually**

1. Open `https://landing-page-tau-rouge.vercel.app`
2. Click "Get Unlimited Jokes Free →"
3. Verify gotcha.html opens in new tab
4. Verify video plays fullscreen, volume locked
5. Verify beforeunload nag appears when trying to close
6. Test on mobile: verify volume can't be lowered, screen stays on

- [ ] **Step 4: Commit completion doc**

```bash
# After user confirms it works
cd /Users/raghavshah/landing-page && git add -A && git commit -m "chore: deployment verified, project complete"
```