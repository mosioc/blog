---
title: "How to Build Offline-First Apps (PWA in 2025)"
description: "Offline? No problem."
date: "12/2/2025"
draft: false
---

In an era where users expect instant access and seamless experiences regardless of network conditions, Progressive Web Apps (PWAs) have become essential. Building an offline-first application isn't just about handling network failures, it's about creating resilient, fast, and user-centric experiences that work anywhere, anytime.

This comprehensive guide will walk you through everything you need to know about building offline-first PWAs in 2025, from core concepts to implementation strategies.

## Why Offline-First Matters

Traditional web applications break when connectivity is lost. An offline-first approach flips this paradigm by designing applications that function primarily from local resources, syncing with servers when connections are available. This architecture delivers several critical benefits:

- **Reliability**: Your application continues to function during network interruptions or slow connections
- **Performance**: Assets load instantly from cache rather than over the network
- **User Experience**: No frustrating error pages or loading spinners
- **Resilience**: Actions are queued and synchronized automatically when connectivity returns

## Core Technologies

Building offline-first PWAs relies on three foundational web technologies:

### 1. Service Workers

Service workers are JavaScript files that run independently from your main application thread. They act as programmable network proxies, intercepting requests and determining how to respond to them.

Service workers operate through a specific lifecycle:

- **Installation**: When first registered, the service worker downloads and installs
- **Activation**: After installation, the service worker activates and can take control
- **Fetch Interception**: Once active, it can intercept all network requests from your application

Here's a basic service worker registration:

```javascript
if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker
      .register('/service-worker.js')
      .then(registration => {
        console.log('Service Worker registered successfully');
      })
      .catch(error => {
        console.error('Service Worker registration failed:', error);
      });
  });
}
```

### 2. Cache API

The Cache API provides storage specifically designed for network requests and responses. Unlike traditional browser caching, you have complete programmatic control over what gets cached and when.

### 3. IndexedDB

For structured data storage, IndexedDB offers a powerful NoSQL database running entirely in the browser. It's essential for storing dynamic content like user data, form submissions, or application state.

## Caching Strategies

Choosing the right caching strategy is crucial for optimal performance. Each strategy offers different tradeoffs between speed and freshness.

### Cache First (Cache Falling Back to Network)

This strategy prioritizes speed by serving cached content immediately, only hitting the network if the resource isn't cached.

**Best for**: Static assets that rarely change (fonts, images, CSS, JavaScript libraries)

```javascript
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      if (response) {
        return response; // Return cached version
      }
      return fetch(event.request).then((networkResponse) => {
        // Cache the new response for future use
        return caches.open('dynamic-cache').then((cache) => {
          cache.put(event.request, networkResponse.clone());
          return networkResponse;
        });
      });
    })
  );
});
```

### Network First (Network Falling Back to Cache)

This strategy attempts to fetch fresh content from the network first, falling back to cached versions if the network fails.

**Best for**: Frequently updated content where freshness is important (news articles, social feeds, API data)

```javascript
self.addEventListener('fetch', (event) => {
  event.respondWith(
    fetch(event.request)
      .then((networkResponse) => {
        // Update cache with fresh content
        caches.open('dynamic-cache').then((cache) => {
          cache.put(event.request, networkResponse.clone());
        });
        return networkResponse;
      })
      .catch(() => {
        // Network failed, return cached version
        return caches.match(event.request);
      })
  );
});
```

### Stale While Revalidate

This hybrid strategy returns cached content immediately while simultaneously fetching updated content in the background for next time.

**Best for**: Content that should be fast but also reasonably current (user profiles, product details, settings)

```javascript
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.open('dynamic-cache').then((cache) => {
      return cache.match(event.request).then((cachedResponse) => {
        const fetchPromise = fetch(event.request).then((networkResponse) => {
          cache.put(event.request, networkResponse.clone());
          return networkResponse;
        });
        return cachedResponse || fetchPromise;
      });
    })
  );
});
```

### Cache Only

Resources are served exclusively from cache, with no network requests.

**Best for**: Precached application shell components that should always be available

### Network Only

All requests bypass the cache entirely and go straight to the network.

**Best for**: Content that must always be current, like real-time data or authenticated endpoints

## Simplifying with Workbox

While implementing caching strategies manually gives you complete control, Google's Workbox library dramatically simplifies the process. Workbox is the industry standard for PWA development, used by over 54% of mobile sites.

### Installing Workbox

```bash
npm install workbox-webpack-plugin --save-dev
```

### Basic Workbox Configuration

```javascript
import { registerRoute } from 'workbox-routing';
import { CacheFirst, NetworkFirst, StaleWhileRevalidate } from 'workbox-strategies';
import { precacheAndRoute } from 'workbox-precaching';

// Precache critical assets
precacheAndRoute(self.__WB_MANIFEST);

// Cache images with Cache First strategy
registerRoute(
  ({request}) => request.destination === 'image',
  new CacheFirst({
    cacheName: 'images',
    plugins: [
      new ExpirationPlugin({
        maxEntries: 60,
        maxAgeSeconds: 30 * 24 * 60 * 60, // 30 Days
      }),
    ],
  })
);

// API requests with Network First
registerRoute(
  ({url}) => url.pathname.startsWith('/api/'),
  new NetworkFirst({
    cacheName: 'api-cache',
    plugins: [
      new ExpirationPlugin({
        maxEntries: 50,
        maxAgeSeconds: 5 * 60, // 5 minutes
      }),
    ],
  })
);

// CSS and JavaScript with Stale While Revalidate
registerRoute(
  ({request}) => request.destination === 'style' || 
                 request.destination === 'script',
  new StaleWhileRevalidate({
    cacheName: 'static-resources',
  })
);
```

## Storing Dynamic Data with IndexedDB

For offline functionality beyond static assets, you'll need to store dynamic data locally. IndexedDB is the solution for this.

### Setting Up IndexedDB

```javascript
// Open a database
const openDB = () => {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open('MyAppDB', 1);
    
    request.onerror = () => reject(request.error);
    request.onsuccess = () => resolve(request.result);
    
    request.onupgradeneeded = (event) => {
      const db = event.target.result;
      
      // Create object stores
      if (!db.objectStoreNames.contains('posts')) {
        const postsStore = db.createObjectStore('posts', { 
          keyPath: 'id', 
          autoIncrement: true 
        });
        postsStore.createIndex('timestamp', 'timestamp', { unique: false });
      }
      
      if (!db.objectStoreNames.contains('syncQueue')) {
        db.createObjectStore('syncQueue', { 
          keyPath: 'id', 
          autoIncrement: true 
        });
      }
    };
  });
};

// Add data to IndexedDB
const addPost = async (post) => {
  const db = await openDB();
  const transaction = db.transaction(['posts'], 'readwrite');
  const store = transaction.objectStore('posts');
  
  return new Promise((resolve, reject) => {
    const request = store.add({
      ...post,
      timestamp: Date.now()
    });
    
    request.onsuccess = () => resolve(request.result);
    request.onerror = () => reject(request.error);
  });
};

// Retrieve data from IndexedDB
const getAllPosts = async () => {
  const db = await openDB();
  const transaction = db.transaction(['posts'], 'readonly');
  const store = transaction.objectStore('posts');
  
  return new Promise((resolve, reject) => {
    const request = store.getAll();
    request.onsuccess = () => resolve(request.result);
    request.onerror = () => reject(request.error);
  });
};
```

### Using Helper Libraries

For easier IndexedDB management, consider using wrapper libraries:

- **idb**: A tiny Promise-based wrapper for IndexedDB
- **Dexie.js**: A minimalistic wrapper with simpler syntax
- **RxDB**: A reactive database with built-in replication and conflict resolution

## Background Synchronization

One of the most powerful features of offline-first apps is background sync. When users perform actions while offline (like submitting a form), those actions can be queued and automatically executed when connectivity returns.

```javascript
// In your service worker
self.addEventListener('sync', (event) => {
  if (event.tag === 'sync-posts') {
    event.waitUntil(syncPosts());
  }
});

async function syncPosts() {
  const db = await openDB();
  const transaction = db.transaction(['syncQueue'], 'readonly');
  const store = transaction.objectStore('syncQueue');
  const queuedPosts = await store.getAll();
  
  for (const post of queuedPosts) {
    try {
      await fetch('/api/posts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(post.data)
      });
      
      // Remove from queue after successful sync
      const deleteTransaction = db.transaction(['syncQueue'], 'readwrite');
      const deleteStore = deleteTransaction.objectStore('syncQueue');
      await deleteStore.delete(post.id);
    } catch (error) {
      console.error('Sync failed, will retry later:', error);
    }
  }
}

// In your application code
async function submitPost(postData) {
  try {
    // Try to submit immediately
    await fetch('/api/posts', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(postData)
    });
  } catch (error) {
    // If offline, queue for later
    const db = await openDB();
    const transaction = db.transaction(['syncQueue'], 'readwrite');
    const store = transaction.objectStore('syncQueue');
    
    await store.add({
      data: postData,
      timestamp: Date.now()
    });
    
    // Register sync
    if ('serviceWorker' in navigator && 'sync' in navigator.serviceWorker) {
      const registration = await navigator.serviceWorker.ready;
      await registration.sync.register('sync-posts');
    }
  }
}
```

## Creating an Installable PWA

To make your application truly app-like, you need a web app manifest that enables installation.

### The Web App Manifest

Create a `manifest.json` file in your root directory:

```json
{
  "name": "My Offline-First App",
  "short_name": "OfflineApp",
  "description": "A fully functional offline-first progressive web application",
  "start_url": "/",
  "display": "standalone",
  "orientation": "portrait-primary",
  "theme_color": "#2196F3",
  "background_color": "#FFFFFF",
  "icons": [
    {
      "src": "/icons/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "/icons/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ],
  "screenshots": [
    {
      "src": "/screenshots/home.png",
      "sizes": "540x720",
      "type": "image/png"
    }
  ]
}
```

Link it in your HTML:

```html
<link rel="manifest" href="/manifest.json">
<meta name="theme-color" content="#2196F3">
```

### Custom Install Prompt

Provide a better installation experience by implementing a custom prompt:

```javascript
let deferredPrompt;

window.addEventListener('beforeinstallprompt', (e) => {
  // Prevent the mini-infobar from appearing on mobile
  e.preventDefault();
  
  // Store the event for later use
  deferredPrompt = e;
  
  // Show your custom install button
  showInstallButton();
});

async function handleInstallClick() {
  if (!deferredPrompt) {
    return;
  }
  
  // Show the install prompt
  deferredPrompt.prompt();
  
  // Wait for the user's response
  const { outcome } = await deferredPrompt.userChoice;
  
  console.log(`User response: ${outcome}`);
  
  // Clear the saved prompt
  deferredPrompt = null;
  
  // Hide the install button
  hideInstallButton();
}
```

## Handling Offline States

Provide clear feedback to users about their connection status and offline capabilities.

```javascript
// Detect online/offline status
window.addEventListener('online', handleOnline);
window.addEventListener('offline', handleOffline);

function handleOnline() {
  showNotification('Connection restored', 'success');
  
  // Trigger sync if available
  if ('serviceWorker' in navigator && 'sync' in navigator.serviceWorker) {
    navigator.serviceWorker.ready.then(registration => {
      registration.sync.register('sync-data');
    });
  }
}

function handleOffline() {
  showNotification('Working offline', 'info');
}

function showNotification(message, type) {
  const notification = document.createElement('div');
  notification.className = `notification notification-${type}`;
  notification.textContent = message;
  document.body.appendChild(notification);
  
  setTimeout(() => notification.remove(), 3000);
}
```

### Custom Offline Page

Always provide a friendly offline fallback:

```javascript
// In your service worker
const OFFLINE_PAGE = '/offline.html';

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open('offline').then((cache) => {
      return cache.add(OFFLINE_PAGE);
    })
  );
});

self.addEventListener('fetch', (event) => {
  if (event.request.mode === 'navigate') {
    event.respondWith(
      fetch(event.request).catch(() => {
        return caches.match(OFFLINE_PAGE);
      })
    );
  }
});
```

## Testing Your Offline-First App

Thorough testing is essential for offline functionality:

### Chrome DevTools

1. Open Chrome DevTools (F12)
2. Go to the **Application** tab
3. Check the **Offline** checkbox under Service Workers
4. Test your application's offline behavior
5. Use the **Network** tab to throttle connection speed (Slow 3G, Fast 3G)

### Lighthouse Audit

Run Lighthouse audits to verify PWA compliance:

1. Open Chrome DevTools
2. Navigate to the **Lighthouse** tab
3. Select **Progressive Web App** category
4. Click **Analyze page load**

Aim for a score above 90 for production apps.

## Best Practices for 2025

As PWA technology continues to evolve, keep these best practices in mind:

### Security First

Always serve your PWA over HTTPS. Service workers only function on secure origins, and users' sensitive data deserves protection.

### Optimize Cache Storage

Implement expiration policies to prevent excessive storage usage:

```javascript
import { ExpirationPlugin } from 'workbox-expiration';

new CacheFirst({
  cacheName: 'images',
  plugins: [
    new ExpirationPlugin({
      maxEntries: 100,        // Maximum number of cached items
      maxAgeSeconds: 30 * 24 * 60 * 60, // 30 days
      purgeOnQuotaError: true // Automatically cleanup on quota errors
    }),
  ],
});
```

### Monitor Storage Quota

Check available storage to prevent errors:

```javascript
async function checkStorageQuota() {
  if ('storage' in navigator && 'estimate' in navigator.storage) {
    const estimate = await navigator.storage.estimate();
    const percentUsed = (estimate.usage / estimate.quota) * 100;
    
    console.log(`Storage used: ${percentUsed.toFixed(2)}%`);
    console.log(`Available: ${(estimate.quota - estimate.usage) / 1024 / 1024} MB`);
    
    if (percentUsed > 80) {
      // Implement cleanup strategy
      cleanupOldCache();
    }
  }
}
```

### Progressive Enhancement

Design your application to work without service workers as a baseline, then enhance with offline capabilities:

```javascript
if ('serviceWorker' in navigator) {
  // Enhanced experience with offline support
  registerServiceWorker();
} else {
  // Basic functionality still works
  console.log('Service Workers not supported');
}
```

### Responsive Design

Ensure your PWA works seamlessly across all device sizes. Use CSS Grid and Flexbox for flexible layouts, and test on various screen sizes.

### Performance Optimization

Focus on Core Web Vitals:

- **Largest Contentful Paint (LCP)**: Under 2.5 seconds
- **First Input Delay (FID)**: Under 100 milliseconds
- **Cumulative Layout Shift (CLS)**: Under 0.1

## Common Pitfalls to Avoid

### 1. Over-Caching

Don't cache everything. Be selective about what you cache to avoid storage bloat and stale content issues.

### 2. Ignoring Cache Versioning

Implement proper cache versioning to ensure users get updated content:

```javascript
const CACHE_VERSION = 'v2';
const CACHE_NAME = `app-cache-${CACHE_VERSION}`;

self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames
          .filter((name) => name !== CACHE_NAME)
          .map((name) => caches.delete(name))
      );
    })
  );
});
```

### 3. Forgetting Error Handling

Always implement comprehensive error handling for network requests, cache operations, and IndexedDB transactions.

### 4. Not Testing Offline Scenarios

Test your application thoroughly in various network conditions, including complete offline mode, slow connections, and intermittent connectivity.

## Real-World Example: Building an Offline Note-Taking App

Let's put it all together with a practical example. Here's the architecture for a simple offline-first note-taking application:

```javascript
// service-worker.js
import { precacheAndRoute } from 'workbox-precaching';
import { registerRoute } from 'workbox-routing';
import { StaleWhileRevalidate, NetworkFirst } from 'workbox-strategies';

// Precache app shell
precacheAndRoute(self.__WB_MANIFEST);

// Cache app pages with stale-while-revalidate
registerRoute(
  ({ request }) => request.mode === 'navigate',
  new NetworkFirst({
    cacheName: 'pages',
    plugins: [
      new ExpirationPlugin({ maxEntries: 50 }),
    ],
  })
);

// Handle background sync for note submissions
self.addEventListener('sync', (event) => {
  if (event.tag === 'sync-notes') {
    event.waitUntil(syncNotes());
  }
});

async function syncNotes() {
  const db = await openNotesDB();
  const pendingNotes = await getPendingNotes(db);
  
  for (const note of pendingNotes) {
    try {
      await fetch('/api/notes', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(note)
      });
      await markNoteSynced(db, note.id);
    } catch (error) {
      console.error('Sync failed for note:', note.id);
    }
  }
}
```

## Framework Integration

Most modern frameworks provide excellent PWA support:

### React

Use Create React App with PWA template:

```bash
npx create-react-app my-app --template cra-template-pwa
```

### Angular

Angular CLI includes built-in PWA support:

```bash
ng add @angular/pwa
```

### Vue

Vue CLI offers a PWA plugin:

```bash
vue add pwa
```

### Next.js

Use next-pwa plugin:

```bash
npm install next-pwa
```

## Conclusion

Building offline-first PWAs in 2025 is no longer optional, it's essential for delivering robust, user-centric web experiences. By implementing service workers, strategic caching, IndexedDB storage, and background synchronization, you can create applications that work seamlessly regardless of network conditions.

The key principles to remember:

- Design for offline from the start, not as an afterthought
- Choose appropriate caching strategies for different content types
- Use IndexedDB for dynamic data storage
- Implement background sync for user actions
- Provide clear feedback about connection status
- Test thoroughly across various network conditions
- Monitor storage usage and implement cleanup strategies
- Prioritize security with HTTPS

The web platform has evolved dramatically, and with tools like Workbox and modern APIs, building offline-first applications has never been more accessible. Your users expect applications that work everywhere, always, and now you have the knowledge to deliver exactly that.

Start small, iterate based on user needs, and gradually enhance your offline capabilities. The future of web applications is offline-first, and the time to build is now.

## Additional Resources

- [MDN Web Docs - Progressive Web Apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)
- [Workbox Documentation](https://developer.chrome.com/docs/workbox/)
- [web.dev PWA Learning Path](https://web.dev/learn/pwa/)
- [IndexedDB API Reference](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
- [Service Worker Cookbook](https://serviceworke.rs/)

---

*Ready to build your first offline-first PWA? Start with a simple project, implement basic caching, and progressively enhance from there. The journey to creating truly resilient web applications begins with a single service worker registration.*