<script lang="ts">
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';
	import type { Action } from 'svelte/action';
	import heroImage from '$lib/assets/First page.jpg';
	import imgFirstAlt from '$lib/assets/First page (1).jpg';
	import imgAssortedCookies from '$lib/assets/Assorted Dreamers cookies (matcha, golden biscoff, red velvet, Triple chocolate ,Og cookies, campfire Smores).jpg';
	import imgBanoffee from '$lib/assets/Banana spring roll Banoffee.png';
	import imgBbqHoneyButter from '$lib/assets/Barbeque and honey butter flavor.jpg';
	import imgChickenSequence from '$lib/assets/Chicken yard sequence.jpg';
	import imgChocCookieBites from '$lib/assets/Chocolate flavor cookie bites.jpg';
	import imgCookieBites from '$lib/assets/Cookie bites.jpg';
	import imgGoldenBiscoff from '$lib/assets/Golden biscoff with cream cheese interior.jpg';
	import imgKrustySliders from '$lib/assets/Krusty Sliders.jpg';
	import imgStrawberryCookies from '$lib/assets/Strawberry chocolate, triple chocolate, Ube creamcheese, OG chocolate Cookies.jpg';
	import imgChickenYardMeal from '$lib/assets/The chicken yard meal.jpg';
	import imgChickenYard from '$lib/assets/The chicken yard.jpg';
	import imgChickenCheeseBbq from '$lib/assets/The chicken yard’s cheese and barbeque flavor.jpg';

	/* ------------------------------------------------------------------ */
	/*  Content                                                            */
	/* ------------------------------------------------------------------ */

	const navLinks = [
		{ href: '#creations', label: 'Creations' },
		{ href: '#story', label: 'Story' },
		{ href: '#gallery', label: 'Gallery' },
		{ href: '#process', label: 'Process' },
		{ href: '#philosophy', label: 'Philosophy' }
	];

	const dishes = [
		{
			img: imgAssortedCookies,
			cat: 'Dreamers Cookies',
			name: 'The Dreamers Box',
			desc: 'Matcha, golden biscoff, red velvet, triple chocolate, OG and campfire s’mores — six moods in one box.'
		},
		{
			img: imgGoldenBiscoff,
			cat: 'Signature Cookie',
			name: 'Golden Biscoff',
			desc: 'A molten cream-cheese heart wrapped in caramelised biscoff and a soft-baked cookie shell.'
		},
		{
			img: imgStrawberryCookies,
			cat: 'The Cookie Lineup',
			name: 'The Stuffed Four',
			desc: 'Strawberry chocolate, triple chocolate, ube cream cheese and OG chocolate — thick and gooey.'
		},
		{
			img: imgKrustySliders,
			cat: 'The Chicken Yard',
			name: 'Krusty Sliders',
			desc: 'Crunchy fried chicken, soft buns and house sauce — small in size, loud in flavour.'
		},
		{
			img: imgChickenYardMeal,
			cat: 'The Chicken Yard',
			name: 'The Full Yard Meal',
			desc: 'Golden fried chicken piled high with all the sides, made for the whole table to share.'
		},
		{
			img: imgBanoffee,
			cat: 'Sweet Ending',
			name: 'Banoffee Spring Rolls',
			desc: 'Banana and toffee folded into crisp spring-roll pastry, fried until shattering-golden.'
		}
	];

	const gallery = [
		{ img: imgCookieBites, caption: 'Cookie Bites — pop-in-your-mouth minis' },
		{ img: imgChocCookieBites, caption: 'Chocolate Cookie Bites — double-dark batch' },
		{ img: imgChickenYard, caption: 'The Chicken Yard — straight from the fryer' },
		{ img: imgChickenCheeseBbq, caption: 'Cheese & Barbeque — the loaded flavour' },
		{ img: imgBbqHoneyButter, caption: 'Barbeque & Honey Butter — sticky and sweet' },
		{ img: imgChickenSequence, caption: 'The Chicken Yard — from yard to plate' },
		{ img: imgAssortedCookies, caption: 'Dreamers Box — the full assortment' },
		{ img: imgGoldenBiscoff, caption: 'Golden Biscoff — that cream-cheese core' },
		{ img: imgStrawberryCookies, caption: 'Stuffed Cookies — the flavour lineup' },
		{ img: imgKrustySliders, caption: 'Krusty Sliders — handheld and loaded' }
	];

	const processSteps = [
		{
			img: imgCookieBites,
			step: '01',
			title: 'The Ingredients',
			desc: 'It all starts with good butter, real chocolate and ripe fruit. If an ingredient isn’t proud, it doesn’t make the batch.'
		},
		{
			img: imgChickenSequence,
			step: '02',
			title: 'The Preparation',
			desc: 'Dough rested overnight, chicken brined and battered by hand. Prep is the slow part nobody sees but everybody tastes.'
		},
		{
			img: imgGoldenBiscoff,
			step: '03',
			title: 'The Finish',
			desc: 'Stuffed, glazed and baked to that exact gooey centre. Sauce first, crunch second, restraint always.'
		},
		{
			img: imgChickenYardMeal,
			step: '04',
			title: 'The Table',
			desc: 'Boxed up, piled high and passed around. A full table mid-feast is my favourite place on earth.'
		}
	];

	const stats = [
		{ to: 20, suffix: '+', label: 'Years at the Stove' },
		{ to: 350, suffix: '+', label: 'Recipes Developed' },
		{ to: 1200, suffix: '+', label: 'Plates Shared' }
	];

	/* ------------------------------------------------------------------ */
	/*  UI state                                                           */
	/* ------------------------------------------------------------------ */

	let scrolled = $state(false);
	let showTop = $state(false);
	let progress = $state(0);
	let menuOpen = $state(false);
	let lbOpen = $state(false);
	let lbIndex = $state(0);
	let heroShift = $state(0);
	let heroFade = $state(1);
	let closeBtn = $state<HTMLButtonElement | null>(null);

	const lbItem = $derived(gallery[lbIndex]);

	let reducedMotion = false;
	let rafId = 0;

	function onScroll() {
		if (rafId) return;
		rafId = requestAnimationFrame(() => {
			rafId = 0;
			const y = window.scrollY;
			scrolled = y > 40;
			showTop = y > 700;
			const max = document.documentElement.scrollHeight - window.innerHeight;
			progress = max > 0 ? y / max : 0;
			if (!reducedMotion && y < window.innerHeight) {
				heroShift = y * 0.22;
				heroFade = Math.max(0, 1 - y / (window.innerHeight * 0.9));
			}
		});
	}

	function onKey(e: KeyboardEvent) {
		if (e.key === 'Escape') {
			if (lbOpen) lbOpen = false;
			else if (menuOpen) menuOpen = false;
			return;
		}
		if (!lbOpen) return;
		if (e.key === 'ArrowRight') stepLightbox(1);
		if (e.key === 'ArrowLeft') stepLightbox(-1);
	}

	function openLightbox(i: number) {
		lbIndex = i;
		lbOpen = true;
	}

	function stepLightbox(dir: number) {
		lbIndex = (lbIndex + dir + gallery.length) % gallery.length;
	}

	function scrollTop() {
		window.scrollTo({ top: 0, behavior: reducedMotion ? 'auto' : 'smooth' });
	}

	// lock page scroll while an overlay is open
	$effect(() => {
		document.body.style.overflow = lbOpen || menuOpen ? 'hidden' : '';
		return () => {
			document.body.style.overflow = '';
		};
	});

	// move focus into the lightbox when it opens
	$effect(() => {
		if (lbOpen) closeBtn?.focus();
	});

	/* ------------------------------------------------------------------ */
	/*  Actions: scroll reveal & count-up                                  */
	/* ------------------------------------------------------------------ */

	let revealObserver: IntersectionObserver | undefined;

	const reveal: Action<HTMLElement, { delay?: number } | undefined> = (node, opts) => {
		node.setAttribute('data-reveal', '');
		if (opts?.delay) node.style.transitionDelay = `${opts.delay}ms`;
		revealObserver ??= new IntersectionObserver(
			(entries) => {
				for (const entry of entries) {
					if (entry.isIntersecting) {
						entry.target.classList.add('revealed');
						revealObserver?.unobserve(entry.target);
					}
				}
			},
			{ threshold: 0.12, rootMargin: '0px 0px -48px 0px' }
		);
		revealObserver.observe(node);
		return {
			destroy() {
				revealObserver?.unobserve(node);
			}
		};
	};

	const countup: Action<HTMLElement, { to: number; suffix?: string }> = (node, params) => {
		const format = (v: number) => v.toLocaleString('en-US') + (params.suffix ?? '');
		if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
			node.textContent = format(params.to);
			return;
		}
		node.textContent = format(0);
		const observer = new IntersectionObserver(
			(entries) => {
				if (!entries.some((e) => e.isIntersecting)) return;
				observer.disconnect();
				const start = performance.now();
				const duration = 1500;
				const tick = (now: number) => {
					const p = Math.min(1, (now - start) / duration);
					node.textContent = format(Math.round(params.to * (1 - Math.pow(1 - p, 3))));
					if (p < 1) requestAnimationFrame(tick);
				};
				requestAnimationFrame(tick);
			},
			{ threshold: 0.5 }
		);
		observer.observe(node);
		return {
			destroy() {
				observer.disconnect();
			}
		};
	};

	/* ------------------------------------------------------------------ */
	/*  Mount: reduced-motion flag + graceful image fallbacks              */
	/* ------------------------------------------------------------------ */

	function placeholder(label: string) {
		const safe = label.replace(/&/g, '&amp;').replace(/</g, '&lt;').slice(0, 44);
		const svg =
			`<svg xmlns="http://www.w3.org/2000/svg" width="800" height="1000" viewBox="0 0 800 1000">` +
			`<rect width="800" height="1000" fill="#242120"/>` +
			`<circle cx="400" cy="420" r="148" fill="none" stroke="#C8832A" stroke-width="3" opacity="0.75"/>` +
			`<circle cx="400" cy="420" r="100" fill="none" stroke="#C8832A" stroke-width="1.5" opacity="0.45"/>` +
			`<text x="400" y="434" font-family="Georgia, serif" font-size="40" font-style="italic" fill="#C8832A" text-anchor="middle">DM</text>` +
			`<text x="400" y="680" font-family="Georgia, serif" font-size="30" font-style="italic" fill="#F5F0E8" fill-opacity="0.7" text-anchor="middle">${safe}</text>` +
			`</svg>`;
		return `data:image/svg+xml,${encodeURIComponent(svg)}`;
	}

	onMount(() => {
		reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
		onScroll();

		// swap any broken Unsplash placeholder for a branded stand-in
		const onImgError = (e: Event) => {
			const img = e.target;
			if (!(img instanceof HTMLImageElement) || img.dataset.fallback) return;
			img.dataset.fallback = '1';
			img.src = placeholder(img.alt || 'Imagery placeholder');
		};
		document.addEventListener('error', onImgError, true);
		return () => document.removeEventListener('error', onImgError, true);
	});
</script>

<svelte:head>
	<title>Dave Malinao — Crafting Memories Through Food</title>
	<meta
		name="description"
		content="The culinary portfolio of Dave Malinao — home chef, food creator and storyteller. Signature dishes, kitchen stories and a philosophy of slow, honest cooking."
	/>
	<meta property="og:title" content="Dave Malinao — Crafting Memories Through Food" />
	<meta
		property="og:description"
		content="A premium culinary portfolio celebrating craftsmanship, creativity and a deep love of food."
	/>
	<meta property="og:type" content="website" />
</svelte:head>

<svelte:window onscroll={onScroll} onkeydown={onKey} />

<!-- reading progress -->
<div class="progress" style:transform={`scaleX(${progress})`} aria-hidden="true"></div>

<!-- ============================== NAV ============================== -->
<header class="nav" class:scrolled>
	<div class="wrap nav-row">
		<a class="logo" href="#top">Dave <em>Malinao</em></a>
		<nav class="nav-links" aria-label="Primary">
			{#each navLinks as link (link.href)}
				<a href={link.href}>{link.label}</a>
			{/each}
		</nav>
		<a class="nav-cta" href="#contact">Contact</a>
		<button
			class="menu-btn"
			class:open={menuOpen}
			aria-expanded={menuOpen}
			aria-controls="mobile-menu"
			aria-label="Toggle menu"
			onclick={() => (menuOpen = !menuOpen)}
		>
			<span></span>
			<span></span>
		</button>
	</div>
</header>

<!-- mobile menu overlay -->
<nav id="mobile-menu" class="mobile-menu" class:open={menuOpen} aria-label="Mobile">
	{#each [...navLinks, { href: '#contact', label: 'Contact' }] as link, i (link.href)}
		<a
			href={link.href}
			style:transition-delay={menuOpen ? `${90 + i * 55}ms` : '0ms'}
			onclick={() => (menuOpen = false)}
		>
			{link.label}
		</a>
	{/each}
</nav>

<main>
	<!-- ============================ HERO ============================ -->
	<section class="hero" id="top">
		<div class="hero-bg" aria-hidden="true">
			<img
				src={heroImage}
				alt=""
				fetchpriority="high"
			/>
		</div>
		<div class="hero-frame" aria-hidden="true"></div>

		<div class="wrap hero-inner" style:transform={`translateY(${heroShift}px)`} style:opacity={heroFade}>
			<h1 class="hero-up" style="animation-delay: 0.3s">Dave <em>Malinao</em></h1>
			<p class="hero-tagline hero-up" style="animation-delay: 0.45s">
				Crafting Memories Through Food
			</p>
			<div class="hero-up" style="animation-delay: 0.6s">
				<a class="btn" href="#creations">
					Explore My Creations
					<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
						<line x1="5" y1="12" x2="19" y2="12" />
						<polyline points="12 5 19 12 12 19" />
					</svg>
				</a>
			</div>
		</div>

		<a class="hero-scroll" href="#creations" aria-label="Scroll to featured creations">
			<span>Scroll</span>
			<span class="hs-line"><span class="hs-dot"></span></span>
		</a>
	</section>

	<!-- ====================== FEATURED CREATIONS ===================== -->
	<section class="section creations" id="creations">
		<div class="wrap">
			<div class="sec-head" use:reveal>
				<p class="overline">01 · Signature Work</p>
				<h2 class="sec-title">Featured <em>Creations</em></h2>
				<p class="sec-sub muted">
					Six dishes that keep earning their place at my table — scribbled on napkins, tested on
					friends, perfected long after midnight.
				</p>
			</div>

			<div class="cards">
				{#each dishes as dish, i (dish.name)}
					<figure class="card" use:reveal={{ delay: (i % 3) * 110 }}>
						<div class="card-media">
							<img src={dish.img} alt={dish.name} loading="lazy" decoding="async" />
						</div>
						<span class="card-num" aria-hidden="true">0{i + 1}</span>
						<figcaption class="card-body">
							<span class="card-cat">{dish.cat}</span>
							<h3 class="card-title">{dish.name}</h3>
							<p class="card-desc">{dish.desc}</p>
						</figcaption>
					</figure>
				{/each}
			</div>
		</div>
	</section>

	<!-- ========================= THE STORY ========================== -->
	<section class="section story" id="story">
		<div class="wrap story-grid">
			<div class="portrait-wrap" use:reveal>
				<div class="portrait-frame" aria-hidden="true"></div>
				<img
					class="portrait"
					src={imgFirstAlt}
					alt="A signature spread from Dave Malinao's kitchen"
					loading="lazy"
					decoding="async"
				/>
				<div class="portrait-card">
					<span class="pc-quote">“Taste. Adjust. Repeat.”</span>
					<span class="pc-label">House Rule № 1</span>
				</div>
			</div>

			<div class="story-copy" use:reveal={{ delay: 120 }}>
				<p class="overline">02 · My Journey</p>
				<h2 class="sec-title">The Story Behind <em>the Kitchen</em></h2>

				<p class="dropcap">
					It began in my grandmother’s kitchen in Lyon, where Sunday afternoons smelled of browned
					butter and patience. I was seven, standing on a wooden chair, when I learned that feeding
					someone is the shortest path to their memory.
				</p>
				<p>
					There was no culinary school. There were markets at dawn, roadside grills, borrowed
					cookbooks and a thousand burnt experiments. Twenty years on, my kitchen is still at home —
					and that is precisely the point. I cook the way people remember being cooked for.
				</p>
				<p>
					Every plate that leaves my counter carries the same intention: honest ingredients, treated
					slowly, shared generously. Food, for me, has never been a profession. It’s a love letter I
					keep rewriting.
				</p>

				<div class="stats">
					{#each stats as stat (stat.label)}
						<div class="stat">
							<span class="stat-num" use:countup={{ to: stat.to, suffix: stat.suffix }}>
								{stat.to.toLocaleString('en-US')}{stat.suffix}
							</span>
							<span class="stat-label">{stat.label}</span>
						</div>
					{/each}
				</div>

				<span class="signature" aria-hidden="true">Dave Malinao</span>
			</div>
		</div>
	</section>

	<!-- ====================== AMBER MARQUEE ========================= -->
	<div class="marquee" aria-hidden="true">
		<div class="marquee-track">
			<span>Seasonal&nbsp; ✦ &nbsp;Slow-Cooked&nbsp; ✦ &nbsp;Market-Driven&nbsp; ✦ &nbsp;Hand-Made&nbsp; ✦ &nbsp;Honest&nbsp; ✦ &nbsp;Always Shared&nbsp; ✦ &nbsp;</span>
			<span>Seasonal&nbsp; ✦ &nbsp;Slow-Cooked&nbsp; ✦ &nbsp;Market-Driven&nbsp; ✦ &nbsp;Hand-Made&nbsp; ✦ &nbsp;Honest&nbsp; ✦ &nbsp;Always Shared&nbsp; ✦ &nbsp;</span>
		</div>
	</div>

	<!-- ===================== SIGNATURE COLLECTION ==================== -->
	<section class="section gallery" id="gallery">
		<div class="wrap">
			<div class="sec-head center" use:reveal>
				<p class="overline">03 · The Collection</p>
				<h2 class="sec-title">The Signature <em>Collection</em></h2>
				<p class="sec-sub muted">
					A living archive of plates, moods and golden-hour moments I couldn’t let go of. Click any
					frame to step inside.
				</p>
			</div>

			<div class="masonry">
				{#each gallery as item, i (item.caption)}
					<button
						class="m-item"
						use:reveal={{ delay: (i % 3) * 90 }}
						onclick={() => openLightbox(i)}
						aria-label={`View ${item.caption}`}
					>
						<img src={item.img} alt={item.caption} loading="lazy" decoding="async" />
						<span class="m-cap">
							{item.caption}
							<svg width="17" height="17" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
								<polyline points="15 3 21 3 21 9" />
								<polyline points="9 21 3 21 3 15" />
								<line x1="21" y1="3" x2="14" y2="10" />
								<line x1="3" y1="21" x2="10" y2="14" />
							</svg>
						</span>
					</button>
				{/each}
			</div>
		</div>
	</section>

	<!-- ======================= BEHIND THE SCENES ===================== -->
	<section class="section process" id="process">
		<div class="wrap">
			<div class="sec-head" use:reveal>
				<p class="overline">04 · The Craft</p>
				<h2 class="sec-title">Behind <em>the Scenes</em></h2>
				<p class="sec-sub muted">
					The unglamorous, beautiful work between the market and the table.
				</p>
			</div>

			<div class="process-grid">
				{#each processSteps as item, i (item.title)}
					<article class="pcard" use:reveal={{ delay: i * 100 }}>
						<div class="pcard-media">
							<img src={item.img} alt={item.title} loading="lazy" decoding="async" />
						</div>
						<div class="pcard-body">
							<span class="pcard-step">{item.step}</span>
							<h3>{item.title}</h3>
							<span class="pcard-tick" aria-hidden="true"></span>
							<p>{item.desc}</p>
						</div>
					</article>
				{/each}
			</div>
		</div>
	</section>

	<!-- ====================== CULINARY PHILOSOPHY ==================== -->
	<section class="section philosophy" id="philosophy">
		<div class="wrap philo-inner" use:reveal>
			<p class="overline philo-overline">05 · Culinary Philosophy</p>
			<span class="philo-mark" aria-hidden="true">“</span>
			<blockquote>
				Great cooking has never been about perfection — it’s about the moment someone takes a bite,
				closes their eyes, and <em>feels at home</em>.
			</blockquote>
			<div class="philo-attr">
				<span class="rule" aria-hidden="true"></span>
				<cite>Dave Malinao</cite>
				<span class="rule" aria-hidden="true"></span>
			</div>
		</div>
	</section>

	<!-- ========================== CONTACT ============================ -->
	<section class="section contact" id="contact">
		<div class="wrap center" use:reveal>
			<p class="overline">06 · Get in Touch</p>
			<h2 class="sec-title">Let’s Create Something <em>Memorable</em></h2>
			<p class="sec-sub muted contact-sub">
				Private tables, collaborations, recipe features — or simply to talk food. My inbox is always
				warm.
			</p>

			<a class="contact-mail" href="mailto:hello@davemalinao.kitchen">hello@davemalinao.kitchen</a>

			<div class="socials">
				<a class="social" href="https://instagram.com" target="_blank" rel="noreferrer" aria-label="Instagram">
					<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
						<rect x="2" y="2" width="20" height="20" rx="5" ry="5" />
						<path d="M16 11.37A4 4 0 1 1 12.63 8 4 4 0 0 1 16 11.37z" />
						<line x1="17.5" y1="6.5" x2="17.51" y2="6.5" />
					</svg>
				</a>
				<a class="social" href="https://youtube.com" target="_blank" rel="noreferrer" aria-label="YouTube">
					<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
						<path d="M22.54 6.42a2.78 2.78 0 0 0-1.94-2C18.88 4 12 4 12 4s-6.88 0-8.6.46a2.78 2.78 0 0 0-1.94 2A29 29 0 0 0 1 11.75a29 29 0 0 0 .46 5.33A2.78 2.78 0 0 0 3.4 19c1.72.46 8.6.46 8.6.46s6.88 0 8.6-.46a2.78 2.78 0 0 0 1.94-2 29 29 0 0 0 .46-5.25 29 29 0 0 0-.46-5.33z" />
						<polygon points="9.75 15.02 15.5 11.75 9.75 8.48 9.75 15.02" />
					</svg>
				</a>
				<a class="social" href="https://twitter.com" target="_blank" rel="noreferrer" aria-label="Twitter">
					<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
						<path d="M23 3a10.9 10.9 0 0 1-3.14 1.53 4.48 4.48 0 0 0-7.86 3v1A10.66 10.66 0 0 1 3 4s-4 9 5 13a11.64 11.64 0 0 1-7 2c9 5 20 0 20-11.5a4.5 4.5 0 0 0-.08-.83A7.72 7.72 0 0 0 23 3z" />
					</svg>
				</a>
				<a class="social" href="https://facebook.com" target="_blank" rel="noreferrer" aria-label="Facebook">
					<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
						<path d="M18 2h-3a5 5 0 0 0-5 5v3H7v4h3v8h4v-8h3l1-4h-4V7a1 1 0 0 1 1-1h3z" />
					</svg>
				</a>
			</div>

			<p class="contact-loc">Based in Philippines · Cooking Everywhere</p>
		</div>
	</section>
</main>

<!-- =========================== FOOTER ============================== -->
<footer class="footer">
	<div class="wrap footer-row">
		<a class="footer-logo" href="#top">Dave <em>Malinao</em></a>
		<p>© 2026 Dave Malinao. All rights reserved.</p>
		<p class="footer-tag">Crafted with patience, brown butter &amp; too many taste tests.</p>
	</div>
</footer>

<!-- ========================= BACK TO TOP =========================== -->
<button class="to-top" class:show={showTop} onclick={scrollTop} aria-label="Back to top">
	<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
		<line x1="12" y1="19" x2="12" y2="5" />
		<polyline points="5 12 12 5 19 12" />
	</svg>
</button>

<!-- ========================== LIGHTBOX ============================= -->
{#if lbOpen}
	<div class="lightbox" role="dialog" aria-modal="true" aria-label="Gallery viewer" transition:fade={{ duration: 260 }}>
		<button class="lb-backdrop" onclick={() => (lbOpen = false)} aria-label="Close gallery"></button>

		<button class="lb-btn lb-close" bind:this={closeBtn} onclick={() => (lbOpen = false)} aria-label="Close">
			<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
				<line x1="18" y1="6" x2="6" y2="18" />
				<line x1="6" y1="6" x2="18" y2="18" />
			</svg>
		</button>
		<button class="lb-btn lb-prev" onclick={() => stepLightbox(-1)} aria-label="Previous image">
			<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
				<polyline points="15 18 9 12 15 6" />
			</svg>
		</button>
		<button class="lb-btn lb-next" onclick={() => stepLightbox(1)} aria-label="Next image">
			<svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
				<polyline points="9 18 15 12 9 6" />
			</svg>
		</button>

		<figure>
			{#key lbIndex}
				<img src={lbItem.img} alt={lbItem.caption} />
			{/key}
			<figcaption>
				<span class="lb-cap">{lbItem.caption}</span>
				<span class="lb-count">{lbIndex + 1} / {gallery.length}</span>
			</figcaption>
		</figure>
	</div>
{/if}

<style>
	/* ------------------------------------------------------------------ */
	/*  Primitives                                                         */
	/* ------------------------------------------------------------------ */

	.wrap {
		width: min(1180px, 100% - clamp(40px, 10vw, 96px));
		margin-inline: auto;
	}

	.section {
		padding-block: clamp(88px, 11vw, 152px);
	}

	.muted {
		color: var(--cream-60);
		font-weight: 300;
		line-height: 1.75;
	}

	.overline {
		display: flex;
		align-items: center;
		gap: 14px;
		margin: 0 0 20px;
		color: var(--amber);
		font-size: 0.72rem;
		font-weight: 600;
		letter-spacing: 0.3em;
		text-transform: uppercase;
	}
	.overline::before {
		content: '';
		width: 44px;
		height: 1px;
		background: var(--amber);
		flex: none;
	}

	.center {
		text-align: center;
	}
	.center .overline {
		justify-content: center;
	}
	.center .overline::after {
		content: '';
		width: 44px;
		height: 1px;
		background: var(--amber);
		flex: none;
	}
	.center .sec-sub {
		margin-inline: auto;
	}

	.sec-head {
		margin-bottom: clamp(44px, 6vw, 76px);
	}
	.sec-title {
		margin: 0 0 18px;
		font-family: var(--font-display);
		font-weight: 500;
		font-size: clamp(2.2rem, 5vw, 3.6rem);
		line-height: 1.1;
		letter-spacing: -0.01em;
	}
	.sec-title em,
	.logo em,
	.footer-logo em,
	h1 em {
		font-style: italic;
		color: var(--amber);
	}
	.sec-sub {
		max-width: 560px;
		margin: 0;
		font-size: 0.98rem;
	}

	.btn {
		display: inline-flex;
		align-items: center;
		gap: 12px;
		padding: 19px 34px;
		border-radius: 999px;
		background: var(--amber);
		color: var(--charcoal);
		font-size: 0.8rem;
		font-weight: 600;
		letter-spacing: 0.16em;
		text-transform: uppercase;
		text-decoration: none;
		box-shadow: 0 10px 30px rgba(200, 131, 42, 0.25);
		transition:
			transform 0.45s var(--ease),
			box-shadow 0.45s var(--ease),
			background 0.45s;
	}
	.btn:hover {
		transform: translateY(-3px);
		background: var(--amber-bright);
		box-shadow: 0 18px 40px rgba(200, 131, 42, 0.35);
	}
	.btn svg {
		transition: transform 0.45s var(--ease);
	}
	.btn:hover svg {
		transform: translateX(5px);
	}

	.progress {
		position: fixed;
		inset: 0 0 auto;
		height: 2px;
		z-index: 210;
		background: linear-gradient(90deg, var(--amber), #e3a455);
		transform-origin: 0 50%;
		transform: scaleX(0);
	}

	/* ------------------------------------------------------------------ */
	/*  Navigation                                                         */
	/* ------------------------------------------------------------------ */

	.nav {
		position: fixed;
		inset: 0 0 auto;
		z-index: 100;
		border-bottom: 1px solid transparent;
		transition:
			background 0.45s,
			border-color 0.45s;
	}
	.nav.scrolled {
		background: rgba(26, 26, 26, 0.72);
		-webkit-backdrop-filter: blur(16px);
		backdrop-filter: blur(16px);
		border-bottom-color: rgba(245, 240, 232, 0.08);
	}
	.nav-row {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 28px;
		height: 78px;
	}
	.logo {
		font-family: var(--font-display);
		font-size: 1.35rem;
		color: var(--cream);
		text-decoration: none;
		z-index: 2;
	}
	.nav-links {
		display: none;
		align-items: center;
		gap: 34px;
		margin-left: auto;
	}
	.nav-links a {
		position: relative;
		padding: 6px 0;
		color: var(--cream-60);
		font-size: 0.78rem;
		font-weight: 500;
		letter-spacing: 0.18em;
		text-transform: uppercase;
		text-decoration: none;
		transition: color 0.3s;
	}
	.nav-links a::after {
		content: '';
		position: absolute;
		left: 0;
		bottom: 0;
		width: 0;
		height: 1px;
		background: var(--amber);
		transition: width 0.4s var(--ease);
	}
	.nav-links a:hover {
		color: var(--cream);
	}
	.nav-links a:hover::after {
		width: 100%;
	}
	.nav-cta {
		display: none;
		padding: 11px 24px;
		border: 1px solid var(--amber-soft);
		border-radius: 999px;
		color: var(--amber);
		font-size: 0.72rem;
		font-weight: 600;
		letter-spacing: 0.18em;
		text-transform: uppercase;
		text-decoration: none;
		transition:
			background 0.35s,
			color 0.35s,
			border-color 0.35s;
	}
	.nav-cta:hover {
		background: var(--amber);
		border-color: var(--amber);
		color: var(--charcoal);
	}
	.menu-btn {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 44px;
		height: 44px;
		padding: 0;
		border: 0;
		background: none;
		cursor: pointer;
		z-index: 2;
	}
	.menu-btn span {
		display: block;
		width: 24px;
		height: 2px;
		margin: 3px 0;
		background: var(--cream);
		transition: transform 0.4s var(--ease);
	}
	.menu-btn.open span:nth-child(1) {
		transform: translateY(4px) rotate(45deg);
	}
	.menu-btn.open span:nth-child(2) {
		transform: translateY(-4px) rotate(-45deg);
	}

	@media (min-width: 900px) {
		.nav-links {
			display: flex;
		}
		.nav-cta {
			display: inline-block;
		}
		.menu-btn {
			display: none;
		}
	}

	.mobile-menu {
		position: fixed;
		inset: 0;
		z-index: 95;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 8px;
		background: rgba(20, 19, 18, 0.86);
		-webkit-backdrop-filter: blur(22px);
		backdrop-filter: blur(22px);
		opacity: 0;
		visibility: hidden;
		transition:
			opacity 0.45s,
			visibility 0.45s;
	}
	.mobile-menu.open {
		opacity: 1;
		visibility: visible;
	}
	.mobile-menu a {
		padding: 8px 0;
		font-family: var(--font-display);
		font-size: clamp(1.9rem, 7vw, 2.6rem);
		color: var(--cream);
		text-decoration: none;
		opacity: 0;
		transform: translateY(16px);
		transition:
			opacity 0.5s var(--ease),
			transform 0.5s var(--ease),
			color 0.3s;
	}
	.mobile-menu.open a {
		opacity: 1;
		transform: none;
	}
	.mobile-menu a:hover {
		color: var(--amber);
	}

	/* ------------------------------------------------------------------ */
	/*  Hero                                                               */
	/* ------------------------------------------------------------------ */

	.hero {
		position: relative;
		display: flex;
		align-items: center;
		min-height: 100vh;
		min-height: 100svh;
		overflow: hidden;
	}
	.hero-bg {
		position: absolute;
		inset: 0;
	}
	.hero-bg img {
		width: 100%;
		height: 100%;
		object-fit: cover;
		animation: kenburns 16s ease-in-out infinite alternate;
	}
	@keyframes kenburns {
		from {
			transform: scale(1);
		}
		to {
			transform: scale(1.09);
		}
	}
	.hero-bg::after {
		content: '';
		position: absolute;
		inset: 0;
		background:
			radial-gradient(
				120% 90% at 50% 10%,
				rgba(26, 26, 26, 0) 0%,
				rgba(26, 26, 26, 0.4) 70%,
				rgba(26, 26, 26, 0.75) 100%
			),
			linear-gradient(
				180deg,
				rgba(26, 26, 26, 0.6) 0%,
				rgba(26, 26, 26, 0.34) 40%,
				rgba(26, 26, 26, 0.97) 100%
			);
	}
	.hero-frame {
		position: absolute;
		inset: 98px 20px 20px;
		display: none;
		border: 1px solid rgba(245, 240, 232, 0.14);
		border-radius: 18px;
		pointer-events: none;
	}
	@media (min-width: 760px) {
		.hero-frame {
			display: block;
		}
	}
	.hero-inner {
		position: relative;
		z-index: 1;
		padding-top: 40px;
		text-align: center;
	}
	.hero-up {
		opacity: 0;
		animation: rise 1.1s var(--ease) forwards;
	}
	@keyframes rise {
		from {
			opacity: 0;
			transform: translateY(34px);
		}
		to {
			opacity: 1;
			transform: none;
		}
	}
	.hero-overline {
		display: inline-flex;
		align-items: center;
		gap: 18px;
		margin: 0;
		color: var(--amber);
		font-size: 0.7rem;
		font-weight: 600;
		letter-spacing: 0.32em;
		text-transform: uppercase;
	}
	.hero-overline::before,
	.hero-overline::after {
		content: '';
		width: 52px;
		height: 1px;
		background: var(--amber-soft);
	}
	.hero h1 {
		margin: 26px 0 22px;
		font-family: var(--font-display);
		font-weight: 500;
		font-size: clamp(3.4rem, 11vw, 7.2rem);
		line-height: 1.02;
		letter-spacing: -0.015em;
	}
	.hero-tagline {
		margin: 0 0 46px;
		color: var(--cream-60);
		font-weight: 300;
		font-size: clamp(0.95rem, 2.2vw, 1.25rem);
		letter-spacing: 0.34em;
		text-transform: uppercase;
	}
	.hero-scroll {
		position: absolute;
		left: 50%;
		bottom: 28px;
		transform: translateX(-50%);
		z-index: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 12px;
		color: var(--cream-40);
		font-size: 0.64rem;
		letter-spacing: 0.34em;
		text-transform: uppercase;
		text-decoration: none;
		opacity: 0;
		animation: fadein 1s ease 1.15s forwards;
		transition: color 0.3s;
	}
	.hero-scroll:hover {
		color: var(--amber);
	}
	@keyframes fadein {
		to {
			opacity: 1;
		}
	}
	.hs-line {
		position: relative;
		width: 1px;
		height: 58px;
		background: rgba(245, 240, 232, 0.22);
		overflow: hidden;
	}
	.hs-dot {
		position: absolute;
		top: -24%;
		left: -1px;
		width: 3px;
		height: 24%;
		background: var(--amber);
		animation: drip 2.2s var(--ease) infinite;
	}
	@keyframes drip {
		0% {
			top: -24%;
		}
		70%,
		100% {
			top: 105%;
		}
	}

	/* ------------------------------------------------------------------ */
	/*  Featured creations                                                 */
	/* ------------------------------------------------------------------ */

	.cards {
		display: grid;
		gap: 22px;
	}
	@media (min-width: 640px) {
		.cards {
			grid-template-columns: repeat(2, 1fr);
		}
	}
	@media (min-width: 1024px) {
		.cards {
			grid-template-columns: repeat(3, 1fr);
			gap: 26px;
		}
	}

	.card {
		position: relative;
		margin: 0;
		aspect-ratio: 4 / 5;
		border-radius: var(--radius);
		overflow: hidden;
		background: var(--surface);
		box-shadow: 0 18px 44px rgba(0, 0, 0, 0.35);
		isolation: isolate;
	}
	.card-media {
		position: absolute;
		inset: 0;
	}
	.card-media img {
		width: 100%;
		height: 100%;
		object-fit: cover;
		transition: transform 1.3s var(--ease);
	}
	.card:hover .card-media img {
		transform: scale(1.07);
	}
	.card-media::after {
		content: '';
		position: absolute;
		inset: 0;
		background: linear-gradient(
			180deg,
			rgba(26, 26, 26, 0) 30%,
			rgba(26, 26, 26, 0.38) 62%,
			rgba(26, 26, 26, 0.93) 100%
		);
	}
	.card-num {
		position: absolute;
		top: 14px;
		right: 22px;
		z-index: 1;
		font-family: var(--font-display);
		font-style: italic;
		font-size: 3rem;
		line-height: 1.2;
		color: rgba(245, 240, 232, 0.16);
		transition: color 0.5s;
	}
	.card:hover .card-num {
		color: rgba(200, 131, 42, 0.6);
	}
	.card-body {
		position: absolute;
		inset: auto 0 0 0;
		z-index: 1;
		padding: 26px;
	}
	.card-cat {
		display: block;
		margin-bottom: 10px;
		color: var(--amber);
		font-size: 0.64rem;
		font-weight: 600;
		letter-spacing: 0.26em;
		text-transform: uppercase;
	}
	.card-title {
		margin: 0;
		font-family: var(--font-display);
		font-weight: 500;
		font-size: 1.5rem;
		line-height: 1.2;
	}
	.card-desc {
		margin: 0;
		max-height: 0;
		overflow: hidden;
		color: rgba(245, 240, 232, 0.78);
		font-size: 0.9rem;
		font-weight: 300;
		line-height: 1.6;
		opacity: 0;
		transform: translateY(8px);
		transition:
			max-height 0.6s var(--ease),
			opacity 0.6s var(--ease),
			transform 0.6s var(--ease),
			margin-top 0.6s var(--ease);
	}
	.card:hover .card-desc {
		max-height: 5.5em;
		margin-top: 10px;
		opacity: 1;
		transform: none;
	}
	@media (hover: none) {
		.card-desc {
			max-height: none;
			margin-top: 10px;
			opacity: 1;
			transform: none;
		}
	}

	/* ------------------------------------------------------------------ */
	/*  Story (cream editorial)                                            */
	/* ------------------------------------------------------------------ */

	.story {
		background: var(--cream);
		color: var(--ink);
	}
	.story-grid {
		display: grid;
		gap: 72px;
		align-items: center;
	}
	@media (min-width: 980px) {
		.story-grid {
			grid-template-columns: 10fr 11fr;
			gap: 84px;
		}
	}
	.portrait-wrap {
		position: relative;
		max-width: 460px;
		margin-inline: auto;
	}
	.portrait-frame {
		position: absolute;
		inset: 22px -20px -20px 22px;
		border: 2px solid var(--amber);
		border-radius: var(--radius);
		opacity: 0.8;
	}
	.portrait {
		position: relative;
		width: 100%;
		aspect-ratio: 4 / 5;
		object-fit: cover;
		border-radius: var(--radius);
		box-shadow: 0 34px 70px rgba(26, 26, 26, 0.28);
	}
	.portrait-card {
		position: absolute;
		left: -14px;
		bottom: 30px;
		z-index: 2;
		max-width: 230px;
		padding: 18px 22px;
		border: 1px solid rgba(245, 240, 232, 0.14);
		border-radius: 14px;
		background: rgba(26, 26, 26, 0.78);
		-webkit-backdrop-filter: blur(12px);
		backdrop-filter: blur(12px);
		color: var(--cream);
		box-shadow: 0 24px 50px rgba(26, 26, 26, 0.35);
	}
	@media (max-width: 480px) {
		.portrait-card {
			left: 12px;
			right: 12px;
			bottom: 16px;
			max-width: none;
		}
		.portrait-frame {
			inset: 14px -10px -10px 14px;
		}
	}
	.pc-quote {
		display: block;
		margin-bottom: 8px;
		font-family: var(--font-display);
		font-style: italic;
		font-size: 1.06rem;
	}
	.pc-label {
		color: var(--amber);
		font-size: 0.62rem;
		font-weight: 600;
		letter-spacing: 0.24em;
		text-transform: uppercase;
	}
	.story-copy .sec-title {
		margin-bottom: 34px;
		color: var(--ink);
	}
	.story-copy p:not(.overline) {
		max-width: 56ch;
		margin: 0 0 24px;
		color: var(--ink-75);
		font-size: 1.03rem;
		line-height: 1.85;
	}
	.dropcap::first-letter {
		float: left;
		margin: 10px 14px 0 0;
		color: var(--amber);
		font-family: var(--font-display);
		font-weight: 600;
		font-size: 4.4em;
		line-height: 0.76;
	}
	.stats {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 18px;
		margin: 42px 0 34px;
		padding-top: 34px;
		border-top: 1px solid rgba(26, 26, 26, 0.14);
	}
	.stat-num {
		display: block;
		font-family: var(--font-display);
		font-weight: 600;
		font-size: clamp(1.8rem, 4vw, 2.6rem);
		line-height: 1;
		color: var(--ink);
	}
	.stat-label {
		display: block;
		margin-top: 10px;
		color: rgba(26, 26, 26, 0.55);
		font-size: 0.64rem;
		font-weight: 600;
		letter-spacing: 0.2em;
		text-transform: uppercase;
	}
	.signature {
		display: inline-block;
		margin-top: 6px;
		transform: rotate(-3deg);
		font-family: var(--font-display);
		font-style: italic;
		font-size: 2.1rem;
		color: var(--ink);
	}

	/* ------------------------------------------------------------------ */
	/*  Amber marquee divider                                              */
	/* ------------------------------------------------------------------ */

	.marquee {
		padding: 17px 0;
		background: var(--amber);
		overflow: hidden;
	}
	.marquee-track {
		display: flex;
		width: max-content;
		animation: marquee 32s linear infinite;
	}
	.marquee:hover .marquee-track {
		animation-play-state: paused;
	}
	.marquee-track span {
		font-family: var(--font-display);
		font-style: italic;
		font-size: clamp(1.05rem, 2vw, 1.4rem);
		color: var(--charcoal);
		white-space: nowrap;
	}
	@keyframes marquee {
		to {
			transform: translateX(-50%);
		}
	}

	/* ------------------------------------------------------------------ */
	/*  Signature collection (masonry + lightbox)                          */
	/* ------------------------------------------------------------------ */

	.masonry {
		column-width: 280px;
		column-count: 3;
		column-gap: 22px;
	}
	.m-item {
		position: relative;
		display: block;
		width: 100%;
		margin: 0 0 22px;
		padding: 0;
		border: 0;
		border-radius: 14px;
		overflow: hidden;
		background: var(--surface);
		box-shadow: 0 14px 34px rgba(0, 0, 0, 0.3);
		break-inside: avoid;
		cursor: zoom-in;
	}
	.m-item img {
		display: block;
		width: 100%;
		transition: transform 1.2s var(--ease);
	}
	.m-item:hover img {
		transform: scale(1.05);
	}
	.m-cap {
		position: absolute;
		inset: auto 0 0 0;
		display: flex;
		align-items: flex-end;
		justify-content: space-between;
		gap: 12px;
		padding: 56px 18px 16px;
		background: linear-gradient(180deg, rgba(20, 19, 18, 0) 0%, rgba(20, 19, 18, 0.88) 100%);
		color: var(--cream);
		font-size: 0.86rem;
		text-align: left;
		opacity: 0;
		transform: translateY(12px);
		transition:
			opacity 0.5s var(--ease),
			transform 0.5s var(--ease);
	}
	.m-item:hover .m-cap,
	.m-item:focus-visible .m-cap {
		opacity: 1;
		transform: none;
	}
	.m-cap svg {
		flex: none;
		margin-bottom: 2px;
		opacity: 0.85;
	}
	@media (hover: none) {
		.m-cap {
			opacity: 1;
			transform: none;
		}
	}

	.lightbox {
		position: fixed;
		inset: 0;
		z-index: 300;
		display: flex;
		align-items: center;
		justify-content: center;
		padding: clamp(16px, 4vw, 48px);
	}
	.lb-backdrop {
		position: absolute;
		inset: 0;
		border: 0;
		background: rgba(17, 16, 14, 0.93);
		-webkit-backdrop-filter: blur(10px);
		backdrop-filter: blur(10px);
		cursor: zoom-out;
	}
	.lightbox figure {
		position: relative;
		z-index: 1;
		margin: 0;
		max-width: min(1040px, 100%);
		text-align: center;
		pointer-events: none;
	}
	.lightbox img {
		max-width: 100%;
		max-height: 76vh;
		border-radius: 12px;
		box-shadow: 0 40px 90px rgba(0, 0, 0, 0.6);
		animation: lb-in 0.5s var(--ease);
		pointer-events: auto;
	}
	@keyframes lb-in {
		from {
			opacity: 0;
			transform: scale(0.96);
		}
		to {
			opacity: 1;
			transform: none;
		}
	}
	.lightbox figcaption {
		display: flex;
		align-items: baseline;
		justify-content: center;
		gap: 16px;
		margin-top: 18px;
		pointer-events: auto;
	}
	.lb-cap {
		font-family: var(--font-display);
		font-style: italic;
		font-size: 1.05rem;
		color: var(--cream);
	}
	.lb-count {
		color: var(--cream-40);
		font-size: 0.7rem;
		letter-spacing: 0.22em;
	}
	.lb-btn {
		position: absolute;
		z-index: 2;
		display: grid;
		place-items: center;
		width: 46px;
		height: 46px;
		padding: 0;
		border: 1px solid rgba(245, 240, 232, 0.16);
		border-radius: 999px;
		background: rgba(26, 26, 26, 0.55);
		-webkit-backdrop-filter: blur(10px);
		backdrop-filter: blur(10px);
		color: var(--cream);
		cursor: pointer;
		transition:
			background 0.3s,
			color 0.3s,
			border-color 0.3s;
	}
	.lb-btn:hover {
		background: var(--amber);
		border-color: var(--amber);
		color: var(--charcoal);
	}
	.lb-close {
		top: 18px;
		right: 18px;
	}
	.lb-prev {
		left: 14px;
		top: 50%;
		margin-top: -23px;
	}
	.lb-next {
		right: 14px;
		top: 50%;
		margin-top: -23px;
	}

	/* ------------------------------------------------------------------ */
	/*  Behind the scenes                                                  */
	/* ------------------------------------------------------------------ */

	.process {
		background: var(--charcoal-deep);
	}
	.process-grid {
		display: grid;
		gap: 22px;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
	}
	.pcard {
		border: 1px solid rgba(245, 240, 232, 0.07);
		border-radius: var(--radius);
		overflow: hidden;
		background: var(--surface);
		transition:
			transform 0.55s var(--ease),
			box-shadow 0.55s var(--ease),
			border-color 0.4s;
	}
	.pcard:hover {
		transform: translateY(-7px);
		border-color: rgba(200, 131, 42, 0.35);
		box-shadow: 0 26px 54px rgba(0, 0, 0, 0.45);
	}
	.pcard-media {
		height: 215px;
		overflow: hidden;
	}
	.pcard-media img {
		width: 100%;
		height: 100%;
		object-fit: cover;
		transition: transform 1.2s var(--ease);
	}
	.pcard:hover .pcard-media img {
		transform: scale(1.08);
	}
	.pcard-body {
		padding: 24px 24px 28px;
	}
	.pcard-step {
		font-family: var(--font-display);
		font-style: italic;
		font-size: 0.95rem;
		color: var(--amber);
	}
	.pcard h3 {
		margin: 8px 0 0;
		font-family: var(--font-display);
		font-weight: 500;
		font-size: 1.35rem;
	}
	.pcard-tick {
		display: block;
		width: 28px;
		height: 2px;
		margin: 14px 0;
		border-radius: 2px;
		background: var(--amber);
		transition: width 0.5s var(--ease);
	}
	.pcard:hover .pcard-tick {
		width: 58px;
	}
	.pcard p {
		margin: 0;
		color: var(--cream-60);
		font-size: 0.9rem;
		font-weight: 300;
		line-height: 1.7;
	}

	/* ------------------------------------------------------------------ */
	/*  Philosophy                                                         */
	/* ------------------------------------------------------------------ */

	.philosophy {
		position: relative;
		background-color: var(--charcoal);
		background-image: radial-gradient(
			58% 46% at 50% 0%,
			rgba(200, 131, 42, 0.09),
			transparent 70%
		);
		text-align: center;
	}
	.philo-inner {
		max-width: 880px;
	}
	.philo-overline {
		justify-content: center;
		margin-bottom: 34px;
	}
	.philo-overline::after {
		content: '';
		width: 44px;
		height: 1px;
		background: var(--amber);
		flex: none;
	}
	.philo-mark {
		display: block;
		height: 0.42em;
		font-family: var(--font-display);
		font-size: clamp(5.5rem, 12vw, 8.5rem);
		line-height: 0.6;
		color: var(--amber);
	}
	.philosophy blockquote {
		margin: 30px 0 42px;
		font-family: var(--font-display);
		font-style: italic;
		font-weight: 500;
		font-size: clamp(1.7rem, 4.4vw, 2.9rem);
		line-height: 1.35;
		color: var(--cream);
	}
	.philosophy blockquote em {
		color: var(--amber);
	}
	.philo-attr {
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 22px;
	}
	.philo-attr .rule {
		width: clamp(40px, 8vw, 76px);
		height: 1px;
		background: var(--amber);
		opacity: 0.7;
	}
	.philo-attr cite {
		font-style: normal;
		font-size: 0.7rem;
		font-weight: 600;
		letter-spacing: 0.28em;
		text-transform: uppercase;
		color: var(--cream-60);
	}

	/* ------------------------------------------------------------------ */
	/*  Contact & footer                                                   */
	/* ------------------------------------------------------------------ */

	.contact {
		background: var(--charcoal-deep);
		border-top: 1px solid rgba(245, 240, 232, 0.06);
	}
	.contact-sub {
		margin-bottom: 8px;
	}
	.contact-mail {
		display: inline-block;
		margin: 26px 0 44px;
		padding-bottom: 8px;
		font-family: var(--font-display);
		font-style: italic;
		font-size: clamp(1.45rem, 4.6vw, 2.5rem);
		color: var(--cream);
		text-decoration: none;
		background-image: linear-gradient(var(--amber), var(--amber));
		background-repeat: no-repeat;
		background-size: 0% 2px;
		background-position: 0 100%;
		transition:
			background-size 0.6s var(--ease),
			color 0.4s;
	}
	.contact-mail:hover {
		background-size: 100% 2px;
		color: var(--amber);
	}
	.socials {
		display: flex;
		justify-content: center;
		gap: 14px;
		margin-bottom: 38px;
	}
	.social {
		display: grid;
		place-items: center;
		width: 46px;
		height: 46px;
		border: 1px solid rgba(245, 240, 232, 0.18);
		border-radius: 999px;
		color: var(--cream-60);
		transition:
			background 0.35s var(--ease),
			border-color 0.35s var(--ease),
			color 0.35s var(--ease),
			transform 0.35s var(--ease),
			box-shadow 0.35s var(--ease);
	}
	.social:hover {
		transform: translateY(-4px);
		background: var(--amber);
		border-color: var(--amber);
		color: var(--charcoal);
		box-shadow: 0 12px 26px rgba(200, 131, 42, 0.3);
	}
	.contact-loc {
		margin: 0;
		color: var(--cream-40);
		font-size: 0.7rem;
		letter-spacing: 0.24em;
		text-transform: uppercase;
	}

	.footer {
		padding: 34px 0 38px;
		background: var(--charcoal-deep);
		border-top: 1px solid rgba(245, 240, 232, 0.08);
	}
	.footer-row {
		display: flex;
		flex-wrap: wrap;
		align-items: center;
		justify-content: space-between;
		gap: 14px;
	}
	.footer-logo {
		font-family: var(--font-display);
		font-size: 1.1rem;
		color: var(--cream);
		text-decoration: none;
	}
	.footer p {
		margin: 0;
		color: var(--cream-40);
		font-size: 0.78rem;
	}
	.footer-tag {
		font-family: var(--font-display);
		font-style: italic;
		color: rgba(245, 240, 232, 0.5);
	}

	.to-top {
		position: fixed;
		right: 22px;
		bottom: 22px;
		z-index: 80;
		display: grid;
		place-items: center;
		width: 46px;
		height: 46px;
		padding: 0;
		border: 1px solid rgba(245, 240, 232, 0.16);
		border-radius: 999px;
		background: rgba(26, 26, 26, 0.62);
		-webkit-backdrop-filter: blur(12px);
		backdrop-filter: blur(12px);
		color: var(--cream-60);
		cursor: pointer;
		opacity: 0;
		visibility: hidden;
		transform: translateY(10px);
		transition:
			opacity 0.4s var(--ease),
			visibility 0.4s var(--ease),
			transform 0.4s var(--ease),
			background 0.3s,
			color 0.3s,
			border-color 0.3s;
	}
	.to-top.show {
		opacity: 1;
		visibility: visible;
		transform: none;
	}
	.to-top:hover {
		background: var(--amber);
		border-color: var(--amber);
		color: var(--charcoal);
	}
</style>
