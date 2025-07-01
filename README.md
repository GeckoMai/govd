# govd
a telegram bot for downloading media from various platforms.

this project draws significant inspiration from [yt-dlp](https://github.com/yt-dlp/yt-dlp).

- official instance: [@govd_bot](https://t.me/govd_bot)
- support group: [govdsupport](https://t.me/govdsupport)

---

* [dependencies](#dependencies)
* [installation](#installation)
    * [build](#build)
    * [docker](#docker-recommended)
* [configuration](#configuration)
* [authentication](#authentication)
* [proxying](#proxying)
* [todo](#todo)

# dependencies
* ffmpeg >= 7.x
    * with shared libraries
* libheif >= 1.19.7
* pkg-config
* sql database
    * mysql or mariadb 

# installation
## build
> [!NOTE]
> there's no official support for windows yet. if you want to run the bot on it, please follow [docker installation](#docker-recommended).

1. clone the repository:

    ```bash
    git clone https://github.com/govdbot/govd.git && cd govd
    ```

2. create or update the `.env` file to set the database properties.  
   for enhanced security, it is recommended to change the `DB_PASSWORD` property.

3. make sure your database is up and running.

4. build and run the bot:

    ```bash
    sh build.sh && ./govd
    ```

## docker (recommended)
1. clone the repository:

    ```bash
    git clone https://github.com/govdbot/govd.git && cd govd
    ```

2. create or update the `.env` file to ensure the database properties match the environment variables defined for the mariadb service in the `docker-compose.yml` file.  
   for enhanced security, it is recommended to change the `MARIADB_PASSWORD` property in `docker-compose.yaml` and ensure `DB_PASSWORD` in `.env` matches it.

    the following line in the `.env` file **must** be set as:

    ```
    DB_HOST=db
    ``` 

3. run the compose to start all services:

    ```bash
    docker compose up -d
    ```

> [!TIP]
> after updating your `.env` file, rebuilding the image is not necessary. simply restart the containers with the `docker compose restart` command to apply the changes.


# configuration
you can configure the bot using the `.env` file. here are the available options:

## database

| variable      | description                         | default                              |
|---------------|-------------------------------------|--------------------------------------|
| DB_HOST       | database host                       | localhost                            |
| DB_PORT       | database port                       | 3306                                 |
| DB_NAME       | database name                       | govd                                 |
| DB_USER       | database user                       | govd                                 |
| DB_PASSWORD   | database password                   | none                                 |

## telegram

| variable           | description                      | default                              |
|--------------------|----------------------------------|--------------------------------------|
| BOT_API_URL        | telegram bot api url             | https://api.telegram.org             |
| BOT_TOKEN          | telegram bot token               | none                                 |
| CONCURRENT_UPDATES | max concurrent updates handled   | 50                                   |

## media & files

| variable       | description                     | default    |
|----------------|---------------------------------|------------|
| DOWNLOADS_DIR  | directory for downloaded files  | downloads  |
| MAX_DURATION   | max duration (parsed string)    | 1h         |
| MAX_FILE_SIZE  | max file size in mb             | 1000       |
| CACHING        | whether to enable media caching | true       |

## proxying

| variable     | description       | default            |
|--------------|-------------------|--------------------|
| HTTP_PROXY   | http proxy        | none _(disabled)_  |
| HTTPS_PROXY  | https proxy       | none _(disabled)_  |
| NO_PROXY     | no proxy domains  | none _(disabled)_  |

## security

| variable    | description                             | default            |
|-------------|-----------------------------------------|--------------------|
| WHITELIST   | list of allowed ids separated by commas | none _(disabled)_  |

## default groups settings

| variable                | description                                 | default   |
|-------------------------|---------------------------------------------|-----------|
| DEFAULT_ENABLE_CAPTIONS | show original captions on messages          | false     |
| DEFAULT_ENABLE_SILENT   | omit error messages in groups (silent fail) | false     |
| DEFAULT_ENABLE_NSFW     | enable nsfw content in groups               | false     |
| DEFAULT_MEDIA_LIMIT     | max media files in a single message         | 10        |

## messages

| variable            | description                        | default |
|---------------------|------------------------------------|---------|
| CAPTION_HEADER      | customizable caption's header      | -       |
| CAPTION_DESCRIPTION | customizable caption's description | -       |

## development

| variable      | description                           | default                         |
|---------------|---------------------------------------|---------------------------------|
| REPO_URL      | project repository url                | https://github.com/govdbot/govd |
| PROFILER_PORT | port for profiler http server (pprof) | 0 _(disabled)_                  |
| LOG_LEVEL     | log level (debug, info, warn, error)  | info                            |
| LOG_FILE      | whether to enable file logging        | false                           |

## extractors
you can configure specific extractors options with `config.yaml` file ([learn more](CONFIGURATION.md)).

> [!IMPORTANT]  
> to avoid limits on files, you should host your own telegram botapi and set `BOT_API_URL` variable according. public bot instance is currently running under a botapi fork, [tdlight-telegram-bot-api](https://github.com/tdlight-team/tdlight-telegram-bot-api), but you can use the official botapi client too.

# proxying
there are two types of proxying available:
* **http proxy**: this is a standard http proxy that can be used to route requests through a proxy server. you can set the `HTTP_PROXY` and `HTTPS_PROXY` environment variables to use this feature. (SOCKS5 is supported too)
* **edge proxy**: this is a custom proxy that is used to route requests through a specific url. currenrly, you can only set this proxy with `config.yaml` file ([learn more](EDGEPROXY.md)).

> [!TIP]
> by settings `NO_PROXY` environment variable, you can specify domains that should not be proxied.

# authentication
some extractors require cookies to access the content. please refer to [this page](AUTHENTICATION.md) for more information on how to set up authentication for each extractor.

# todo
* [ ] add tests
* [ ] add support for telegram webhooks
* [ ] switch to pgsql (maybe)
* [ ] better api
* [ ] better docs

# Instagram Media Extractor - Python Version

A comprehensive Python rewrite of the Go Instagram media extractor. This library provides the exact same functionality as the original Go implementation, allowing you to extract media from Instagram posts, reels, and stories.

## 🚀 Features

- **Complete parity** with the original Go implementation
- **Multiple extraction methods** with automatic fallback:
  1. Instagram GraphQL API (primary)
  2. Instagram embed page parsing (fallback)
  3. Third-party service (IGram) (last resort)
- **Support for all Instagram media types**:
  - Regular posts (images/videos)
  - Instagram Reels
  - Instagram Stories
  - Share URLs (with redirect handling)
- **Multiple media formats** with codec information
- **Carousel/sidecar posts** (multiple images/videos in one post)
- **Caption extraction**
- **Thumbnail support**
- **Type-safe** with comprehensive data models

## 📦 Installation

```bash
pip install -r requirements.txt
```

## 🔧 Requirements

- Python 3.7+
- requests >= 2.28.0

## 📖 Usage

### Basic Usage

```python
from instagram_extractor import Extractor
from instagram_extractor.models import DownloadContext

# Example Instagram post URL
url = "https://www.instagram.com/p/ABC123DEF456/"
content_id = "ABC123DEF456"

# Create download context
ctx = DownloadContext(
    matched_content_id=content_id,
    matched_content_url=url,
    extractor=Extractor
)

# Extract media
response = Extractor.run(ctx)

# Process results
for media in response.media_list:
    print(f"Media ID: {media.content_id}")
    print(f"Caption: {media.caption}")
    
    for format in media.formats:
        print(f"Type: {format.type.value}")
        print(f"URL: {format.url[0] if format.url else 'No URL'}")
        if format.video_codec:
            print(f"Video Codec: {format.video_codec.value}")
        if format.audio_codec:
            print(f"Audio Codec: {format.audio_codec.value}")
```

### Different Instagram Content Types

```python
from instagram_extractor import Extractor, StoriesExtractor, ShareURLExtractor

# Regular posts and reels
regular_extractor = Extractor

# Stories
stories_extractor = StoriesExtractor

# Share URLs (redirects)
share_extractor = ShareURLExtractor
```

### URL Pattern Matching

```python
import re

# Check if URL matches any extractor
url = "https://www.instagram.com/p/ABC123/"

if Extractor.url_pattern.search(url):
    print("Matches regular post/reel extractor")
elif StoriesExtractor.url_pattern.search(url):
    print("Matches stories extractor")
elif ShareURLExtractor.url_pattern.search(url):
    print("Matches share URL extractor")
```

## 🏗️ Architecture

### Core Components

- **`extractor.py`** - Main extraction logic with three extractor types
- **`models.py`** - Comprehensive data models matching the Go implementation
- **`utils.py`** - Utility functions for API calls, parsing, and data conversion
- **`enums.py`** - Type definitions for media types, codecs, and extractor categories
- **`errors.py`** - Custom exception classes for error handling

### Extraction Methods

The extractor implements a three-tier fallback system:

1. **GraphQL API** - Primary method using Instagram's internal API
2. **Embed Page Parsing** - Fallback method parsing embedded post pages
3. **Third-party Service** - Last resort using IGram API

### Data Models

The implementation uses Python dataclasses that mirror the Go structs:

```python
@dataclass
class Media:
    content_id: str
    content_url: str
    extractor_code_name: str
    caption: Optional[str] = None
    formats: List[MediaFormat] = field(default_factory=list)

@dataclass
class MediaFormat:
    format_id: str
    type: MediaType
    url: List[str] = field(default_factory=list)
    video_codec: Optional[MediaCodec] = None
    audio_codec: Optional[MediaCodec] = None
    # ... additional fields
```

## 🔍 Supported URL Patterns

### Regular Posts & Reels
- `https://www.instagram.com/p/{id}/`
- `https://www.instagram.com/reels/{id}/`
- `https://www.instagram.com/reel/{id}/`
- `https://www.instagram.com/tv/{id}/`
- `https://ddinstagram.com/p/{id}/` (alternative domain)

### Stories
- `https://www.instagram.com/stories/{username}/{id}`

### Share URLs
- `https://www.instagram.com/share/p/{id}`
- `https://www.instagram.com/share/reels/{id}`
- `https://www.instagram.com/share/video/{id}`

## 🛠️ Error Handling

The library includes comprehensive error handling with custom exceptions:

```python
from instagram_extractor.errors import (
    AllMethodsFailedError,
    GQLJSONNotFoundError,
    GQLNilResponseError
)

try:
    response = extractor.run(ctx)
except AllMethodsFailedError:
    print("All extraction methods failed")
except GQLNilResponseError:
    print("Instagram API returned empty response")
```

## 📊 Media Types & Codecs

### Supported Media Types
- `VIDEO` - Video content
- `AUDIO` - Audio-only content  
- `PHOTO` - Image content

### Supported Codecs
- **Video**: AVC (H.264), HEVC (H.265), VP9, VP8, AV1
- **Audio**: AAC, MP3, Opus, Vorbis, FLAC

## 🔒 Authentication & Rate Limiting

The extractor works without authentication but respects Instagram's rate limiting:

- Uses realistic browser headers
- Implements proper request timing
- Falls back gracefully when rate limited

## 🧪 Testing

Run the example script to test the implementation:

```bash
python example_usage.py
```

## 📋 Comparison with Go Implementation

| Feature | Go Version | Python Version | Status |
|---------|------------|----------------|--------|
| GraphQL API extraction | ✅ | ✅ | ✅ Complete |
| Embed page parsing | ✅ | ✅ | ✅ Complete |
| IGram fallback | ✅ | ✅ | ✅ Complete |
| Regular posts | ✅ | ✅ | ✅ Complete |
| Reels | ✅ | ✅ | ✅ Complete |
| Stories | ✅ | ✅ | ✅ Complete |
| Share URLs | ✅ | ✅ | ✅ Complete |
| Carousel posts | ✅ | ✅ | ✅ Complete |
| Caption extraction | ✅ | ✅ | ✅ Complete |
| Multiple formats | ✅ | ✅ | ✅ Complete |
| Error handling | ✅ | ✅ | ✅ Complete |
| Type safety | ✅ | ✅ | ✅ Complete |

## 🤝 Contributing

This is a faithful rewrite of the Go implementation. When contributing:

1. Maintain parity with the Go version
2. Follow the existing code structure
3. Add comprehensive type hints
4. Include proper error handling

## 📄 License

This project maintains the same license as the original Go implementation.

## 🔗 Related Projects

- [Original Go Implementation](../ext/instagram/) - The source implementation this project is based on

---

**Note**: This Python implementation provides 100% functional parity with the original Go Instagram extractor while maintaining Python idioms and best practices.
