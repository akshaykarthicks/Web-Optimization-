
# 🎨 Frontend Optimization for Django

This document explains **frontend optimization techniques** to improve the performance and user experience of Django applications.
These practices focus on reducing load time, optimizing assets, and ensuring efficient static file delivery.

---

## 🖼️ Image Optimization

-   Compress images before upload using tools like [TinyPNG](https://tinypng.com/), `pillow`, or `django-imagekit`.
-   Convert heavy formats (JPG/PNG) to **WebP** or **AVIF** for smaller file sizes.
-   Use **lazy loading** for below-the-fold images:

```html
<img src="/static/images/banner.webp" alt="Banner" loading="lazy">
```

```python
from django.db import models
from imagekit.models import ImageSpecField
from imagekit.processors import ResizeToFill

class Profile(models.Model):
    avatar = models.ImageField(upload_to='avatars/')
    avatar_optimized = ImageSpecField(
        source='avatar',
        processors=[ResizeToFill(200, 200)],
        format='WEBP',
        options={'quality': 80}
    )
```

# 📦 Static File Handling

### 1️⃣ Serve Static Files Efficiently

Use Whitenoise to serve static files in production without relying on external servers.

```python
# settings.py
MIDDLEWARE = [
    # Whitenoise Middleware should be placed right after SecurityMiddleware
    "django.middleware.security.SecurityMiddleware",
    "whitenoise.middleware.WhiteNoiseMiddleware",
    ...
]

STATICFILES_STORAGE = "whitenoise.storage.CompressedManifestStaticFilesStorage"
```

# ✂️ Minify CSS & JavaScript

Using `django-compressor`.

**Install:**

```bash
pip install django-compressor
```

**Configure `settings.py`:**

```python
INSTALLED_APPS = [
    # ... other apps
    "django.contrib.staticfiles",
    "compressor",
]

STATICFILES_FINDERS = [
    "django.contrib.staticfiles.finders.FileSystemFinder",
    "django.contrib.staticfiles.finders.AppDirectoriesFinder",
    "compressor.finders.CompressorFinder",
]
```

**Usage in a template:**

```html
{% load compress %}

{% compress css %}
<link rel="stylesheet" href="{{ STATIC_URL }}css/style.css">
{% endcompress %}
```
