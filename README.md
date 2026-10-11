# Inview IntersectionObserver Utility

A lightweight, high-performance, and modular JavaScript helper built on top of the native `IntersectionObserver` API. Designed to handle scroll-triggered animations effortlessly with zero dependencies.

## Features

- **Lightweight & Fast:** Uses native browser APIs (`IntersectionObserver`) for optimal scroll performance.
- **Modular & Reusable:** Easily importable ES6 function that can be plugged into any project setup (Vite, Webpack, Vanilla JS).
- **Customizable Thresholds:** Support for element-specific thresholds via HTML `data-threshold` attributes or default programmatic options.
- **Flexible Playback Modes:** Supports both one-time animations (unobserves after the first trigger) and repeating loop animations (toggles classes on enter/leave).

---

## Installation & Setup

1. Place `inview-observer.js` inside your vendor or helper directory.
2. Import the function into your script file.

### 1. The Utility Function (`inview-observer.js`)

```javascript
/**
 * Observes a target element's visibility within the viewport.
 * 
 * @param {HTMLElement} targetElement - The DOM element to observe.
 * @param {Function} callback - Callback function executed on intersection change, receives (entry, observerInstance).
 * @param {Object} options - Configuration options (threshold, repeat).
 */
export function observeElementInView(targetElement, callback, options = {}) {
	const threshold = parseFloat(targetElement.dataset.threshold) || options.threshold || 0;
	const repeat = options.repeat || false; // Set to true if you want the animation to repeat on scroll

	const observer = new IntersectionObserver((entries, observerInstance) => {
		entries.forEach(entry => {
			callback(entry, observerInstance);

			// Unobserve once triggered unless repeat mode is enabled
			if (entry.isIntersecting && !repeat) {
				observerInstance.unobserve(targetElement);
			}
		});
	}, {
		threshold: threshold
	});

	observer.observe(targetElement);
}
```

---

### 2. Implementation in Main Script (`general.js`) or in another file where you need this functionality

Integrate the observer into your animation handling function:

```javascript
import { observeElementInView } from './vendor/inview-observer.js';

const inviewAnimate = () => {
	// Select all elements designated for scroll animations
	const ivElements = document.querySelectorAll(`
		.inview-animate--fade,
		.inview-animate--move-up,
		.inview-animate--move-down,
		.inview-animate--move-left,
		.inview-animate--move-right,
		.inview-animate,
		.inview-animate--scale
	`);

	ivElements.forEach(ivElement => {
		// Fallback to default 0.05 threshold (5%) if data-threshold is missing
		const threshold = parseFloat(ivElement.getAttribute('data-threshold')) || 0.05;

		observeElementInView(ivElement, (entry) => {
			const visibleRatio = entry.intersectionRatio;

			if (entry.isIntersecting) {
				if (visibleRatio >= threshold) {
					// Add active class when element enters the viewport
					entry.target.classList.add('inview-active');
				}
			} else {
				// Optional: Remove class when element leaves viewport for repeating animations; use with "repeat: true"
				//entry.target.classList.remove('inview-active');
			}
		}, {
			threshold: threshold,
			//repeat: true // Set to true to enable repeating animations on every scroll interaction
		});
	});
};

// Initialize on DOM ready
$(() => {
	inviewAnimate();
});
```

---

## HTML Usage

Add the appropriate animation classes and an optional `data-threshold` value to your HTML elements:

```html
<div class="inview-animate--move-up" data-threshold="0.2">
	This element will animate when 20% of it becomes visible.
</div>
```

## License

MIT License. Feel free to use and adapt this code for your projects.
