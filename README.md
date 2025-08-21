# Auphonic API Documentation - Complete Reference

## Table of Contents

1. [API Overview](#api-overview)
2. [Simple API](#simple-api)
3. [JSON API: Complex Operations](#json-api-complex-operations)  
4. [API Details and Advanced Features](#api-details-and-advanced-features)
5. [Query Data](#query-data)
6. [Update and Delete Resources](#update-and-delete-resources)
7. [Multitrack Productions](#multitrack-productions)
8. [Authentication](#authentication)
9. [Webhooks](#webhooks)
10. [External Service Integration](#external-service-integration)
11. [API Examples](#api-examples)

---

## API Overview

The Auphonic [REST](http://en.wikipedia.org/wiki/Representational_state_transfer) API allows you to integrate our complete services and algorithms into your scripts, external workflows and third-party applications. If you run/use a podcast hosting (or similar) service, it is also possible to [integrate your service as publishing target](http://auphonic.com/help/api/external_service.html) within Auphonic.

**Our API comes in two flavours:**
- A [Simple API](http://auphonic.com/help/api/simple_api.html) for quick shell scripts and batch processing of multiple files.
- A complete [JSON API](http://auphonic.com/help/api/complex.html) for full control.

The [Simple API](http://auphonic.com/help/api/simple_api.html) can upload files, set metadata, save and start productions in a **single request** by referencing an existing [Preset](http://auphonic.com/help/web/preset.html#web-preset), without storing an internal state. This makes it ideal for quick shell scripts and batch processing of multiple files.

If you want to have **full control**, like detailed file formats or outgoing services without the use of predefined presets, you should use our [JSON API](http://auphonic.com/help/api/complex.html). The [JSON](http://en.wikipedia.org/wiki/JSON)-based API might require **multiple requests** and parsing of the JSON response (when uploading files and sending JSON data).

The Auphonic APIs can be **used by any tool or programming language** which is able to perform HTTP requests. All examples in this documentation utilize the command line tool [curl](http://curl.haxx.se/) for demonstration purpose.

Resources (presets, productions, services, etc.) are always referenced using an [UUID](http://en.wikipedia.org/wiki/Universally_unique_identifier), which is a unique string of 22 characters out of `[a-zA-Z0-9]`.

**Authentication** is possible with [API Key Authentication](http://auphonic.com/help/api/authentication.html#api-key-auth), [HTTP Basic Authentication](http://auphonic.com/help/api/authentication.html#http-basic) and [OAuth 2.0 Authentication](http://auphonic.com/help/api/authentication.html#oauth). All documentation examples use [API Key Authentication](http://auphonic.com/help/api/authentication.html#api-key-auth), which is our recommended method for accessing your own resources.

---

## Simple API

The Auphonic Simple API should be used for small scripts like batch processing of multiple files. It is possible to upload files, set metadata, reference an existing [Preset](https://auphonic.com/help/web/preset.html#web-preset), save and start productions in a single `multipart/form-data` HTTP request. This makes it ideal for quick shell scripts and easy integrations.

### Start a Production and Upload a File

The following [POST request](http://en.wikipedia.org/wiki/POST_(HTTP)_) creates a new production and references an existing [Preset](https://auphonic.com/help/web/preset.html#web-preset):

```bash
curl -X POST https://auphonic.com/api/simple/productions.json \
    -H "Authorization: bearer {api_key}" \
    -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
    -F "title=My First Production" \
    -F "input_file=@/home/user/your_audio_or_video_file.mp3" \
    -F "action=start"
```

Parameters:
- `api_key`: your Auphonic API Key
- `preset`: UUID of the referenced preset
- `title`: the title metadata tag of the production
- `input_file`: the local audio or video file you want to upload
- `action`: can be _start_ to start your production, or _save_ to just save the data without starting the production

**Note:** When uploading files, _curl_ needs an "@" in front of the filename!

### Upload Files with HTTP

It's also possible to fetch a file from an external HTTP server as input audio:

```bash
curl -X POST https://auphonic.com/api/simple/productions.json \
    -H "Authorization: bearer {api_key}" \
    -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
    -F "title=My First HTTP Production" \
    -F "input_file=https://your_server.com/somefile.mp3" \
    -F "action=start"
```

### Upload Files with External Services

When fetching files from SFTP, FTP, Dropbox, Amazon S3, SoundCloud or other [External Services](https://auphonic.com/help/web/services.html#web-external-services):

```bash
curl -X POST https://auphonic.com/api/simple/productions.json \
    -H "Authorization: bearer {api_key}" \
    -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
    -F "service=pmefeNCzkyT4TbRbDmoCDf" \
    -F "input_file=my_dropbox_file.mp3" \
    -F "title=My First Dropbox Production" \
    -F "action=start"
```

Parameters:
- `service`: the UUID of your registered external service
- `input_file`: the filename of the input file (e.g. on the Dropbox server)
- `preset`: the UUID of the preset you want to use

### Detailed Audio Metadata

Various [Metadata Tags](https://auphonic.com/help/web/production.html#production-basic-metadata) can be set:

```bash
curl -X POST https://auphonic.com/api/simple/productions.json \
    -H "Authorization: bearer {api_key}" \
    -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
    -F "title=My First Production" \
    -F "artist=The Artist" \
    -F "album=Our Album" \
    -F "track=1" \
    -F "subtitle=Our subtitle" \
    -F "append_chapters=true" \
    -F "summary=Our very long summary." \
    -F "genre=Podcast" \
    -F "year=2012" \
    -F "publisher=that's me" \
    -F "url=https://auphonic.com" \
    -F "license=Creative Commons Attribution 3.0 Austria" \
    -F "license_url=http://creativecommons.org/licenses/by/3.0/at/" \
    -F "tags=podcast, auphonic api, metadata" \
    -F "image=@/home/user/your_image.jpg" \
    -F "input_file=@/home/user/your_audio_or_video_file.mp3" \
    -F "action=start"
```

### Adding Chapter Marks

You can upload an additional text file with [Chapter Marks](https://auphonic.com/help/web/production.html#production-chapter-marks):

```bash
curl -X POST https://auphonic.com/api/simple/productions.json \
    -H "Authorization: bearer {api_key}" \
    -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
    -F "title=Production with Chapters" \
    -F "input_file=@/home/user/your_audio_or_video_file.mp3" \
    -F "chapters=@/home/user/chapters.txt" \
    -F "action=start"
```

The text file must contain the start time in _HH:MM:ss[.mmm]_ and a `title` for each chapter. Example:

```
00:00:00.000 Intro1 <http://url1.com>
00:00:10.000 Baby prepares to rock
00:00:30.000 Baby rocks the house
00:01:00.000 One Minute
00:01:30.000 End <http://url2.at/ha>
```

### Audio Algorithms

Audio algorithms can be enabled, disabled and parameters can be set:

```bash
curl -X POST https://auphonic.com/api/simple/productions.json \
    -H "Authorization: bearer {api_key}" \
    -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
    -F "title=Production with Detailed Algorithms" \
    -F "input_file=@/home/user/your_audio_or_video_file.mp3" \
    -F "filtering=true" \
    -F "leveler=false" \
    -F "normloudness=true" \
    -F "loudnesstarget=-24" \
    -F "maxpeak=-2" \
    -F "denoise=false" \
    -F "denoiseamount=100" \
    -F "silence_cutter=true" \
    -F "filler_cutter=true" \
    -F "cough_cutter=true" \
    -F "action=start"
```

---

## JSON API: Complex Operations

Our [JSON](http://en.wikipedia.org/wiki/JSON)-based API can be used for **complex workflows** and **detailed control**.

Multiple steps might be necessary to create and start an audio post production:

**Step 1:** Create a production and reference the preset you want to use.
**Step 2 (optional):** Upload your local input file and add it to the newly created production.
**Step 3 (optional):** Start the audio post production on our servers.

### Start a Production and Upload a Local File

**Step 1:** Create a new [Production](https://auphonic.com/help/web/production.html#web-production):

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "preset": "ceigtvDv8jH6NaK52Z5eXH",
            "metadata": { "title": "My first Production" }
        }'
```

This returns JSON data containing the UUID of the newly created production:

```json
{
    "status_code": 200,
    "form_errors": {},
    "error_code": null,
    "error_message": "",
    "data": {
        "uuid": "KKw7AxpLrDBQKLVnQCBtCh"
    }
}
```

**Step 2:** Upload a local input file:

```bash
curl -X POST https://auphonic.com/api/production/KKw7AxpLrDBQKLVnQCBtCh/upload.json \
    -H "Authorization: bearer {api_key}" \
    -F "input_file=@/home/user/your_audio_file.mp3"
```

**Step 3:** Start the audio post production:

```bash
curl -X POST https://auphonic.com/api/production/KKw7AxpLrDBQKLVnQCBtCh/start.json \
    -H "Authorization: bearer {api_key}"
```

### Upload Files with HTTP

The local file upload step is not necessary if you fetch a file from an external HTTP server:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "preset": "ceigtvDv8jH6NaK52Z5eXH",
            "input_file": "https://your_server.com/somefile.mp3",
            "metadata": { "title": "My first HTTP Production" },
            "action": "start"
        }'
```

### Adding Chapters

Create a new production with [Chapter Marks](https://auphonic.com/help/web/production.html#production-chapter-marks):

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "preset": "ceigtvDv8jH6NaK52Z5eXH",
            "input_file": "http://your_server.com/somefile.mp3",
            "metadata": { "title": "My first Production with Chapters" },
            "chapters": [
                {"start": "00:00:00", "title": "Start Chapter", "url": "http://auphonic.com"},
                {"start": "00:02:18", "title": "Second Chapter"},
                {"start": "00:04:41", "title": "Chapter with Image", "image": "https://auphonic.com/static/images/logo.png"}
            ]
        }'
```

---

## API Details and Advanced Features

### Create a Production with Detailed Audio Metadata

Audio [Metadata](https://auphonic.com/help/web/production.html#production-basic-metadata) can be set using the `metadata` parameter:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "metadata": {
                "title": "Production Title",
                "artist": "The Artist",
                "album": "Our Album",
                "track": 1,
                "subtitle": "Our subtitle",
                "append_chapters": true,
                "summary": "Our very long summary.",
                "genre": "Podcast",
                "year": 2012,
                "publisher": "that's me",
                "url": "https://auphonic.com",
                "license": "Creative Commons Attribution 3.0 Austria",
                "license_url": "http://creativecommons.org/licenses/by/3.0/at/",
                "tags": ["podcast", "auphonic api", "metadata"],
                "location": {
                    "latitude": "47.070",
                    "longitude": "15.439"
                }
            }
        }'
```

### Output Files

Add [Output Files](https://auphonic.com/help/web/production.html#production-output-files) to an existing production:

```bash
curl -H "Content-Type: application/json" -X POST \
    https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "output_files": [
                {"format":"mp3", "bitrate":"96"},
                {"format":"aac", "bitrate":"64"},
                {"format":"flac"}
            ]
        }'
```

Available output formats include:
- `mp3`, `mp3-vbr` (MP3 with variable bitrate)
- `vorbis` (Ogg Vorbis), `opus` (Opus Codec)
- `aac` (MP4/M4A), `flac`, `alac` (MP4/M4A)
- `wav` (16-bit PCM), `wav-24bit` (24-bit PCM)
- `video` (same format as input)
- `audiogram` (Waveform Video)

### Setting Filenames

Control how output filenames are constructed:

```json
{"format":"mp3", "bitrate":"48", "filename":"MyFilename.mp3", "mono_mixdown":true}
```

### Audio Algorithms

Setting [Audio Algorithms](https://auphonic.com/help/web/production.html#production-audio-algorithms) parameters:

```bash
curl -H "Content-Type: application/json" -X POST \
    https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "algorithms": {
                "leveler": true, "normloudness": true, "loudnesstarget": -23,
                "filtering": true, "denoise": false, "denoiseamount": 0
            }
        }'
```

### Adding Intro and Outro

Add an [Intro and Outro](https://auphonic.com/help/web/production.html#production-intro-and-outro):

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "multi_input_files": [
                {
                    "service": "pmefeNCzkyT4TbRbDmoCDf",
                    "input_file": "my_dropbox_file.mp3",
                    "type": "intro"
                }
            ]
        }'
```

### Adding Speech Recognition

Add speech recognition to a production:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "speech_recognition": {
                "language": "en",
                "keywords": ["keyword1", "keyword2"],
                "shownotes": true
            }
        }'
```

### Set all Details in one Request

Example of setting all details in a single request:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "metadata": {
                "title": "Production Title",
                "artist": "The Artist",
                "album": "Our Album",
                "track": 1,
                "summary": "Our very long summary.",
                "genre": "Podcast",
                "year": 2012,
                "tags": ["podcast", "auphonic api", "metadata"]
            },
            "output_files": [
                {"format": "mp3", "bitrate": "96"},
                {"format": "flac"}
            ],
            "algorithms": {
                "filtering": true, "leveler": true,
                "normloudness": true, "denoise": true,
                "loudnesstarget": -23, "denoiseamount": 12
            },
            "input_file": "http://your_server.com/somefile.mp3",
            "action": "start"
        }'
```

---

## Query Data

### Query Production Status

```bash
curl https://auphonic.com/api/production/{uuid}/status.json \
    -H "Authorization: bearer {api_key}"
```

### Get Production Details

```bash
curl https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}"
```

### List All Productions

```bash
curl https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}"
```

Parameters:
- `limit`: Limits the number of returned productions
- `offset`: List starts at the defined number
- `uuids_only`: only return UUIDs
- `minimal_data`: only minimal data for each production

### Query External Services

```bash
curl https://auphonic.com/api/services.json \
    -H "Authorization: bearer {api_key}"
```

### Download Result Files

All result files are listed with download URLs in the API response:

```bash
curl https://auphonic.com/api/download/audio-result/{uuid}/filename.mp3 \
    -H "Authorization: bearer {api_key}" -L
```

---

## Update and Delete Resources

### Update Production Metadata

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}" \
    -d '{"metadata": {"title": "New Title", "track": 2}}'
```

### Delete a Production

```bash
curl -X DELETE https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}"
```

### Stop a Production

```bash
curl -X POST https://auphonic.com/api/production/{uuid}/stop.json \
    -H "Authorization: bearer {api_key}"
```

### Reset Production Data

Reset a production to its initial state while keeping the input file:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/production/{uuid}.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "reset_data": true,
            "metadata": {"title": "New Title"},
            "output_files": [{"format":"mp3"}]
        }'
```

---

## Multitrack Productions

An Auphonic Multitrack post production takes **multiple parallel input audio tracks/files**, analyzes and processes them individually as well as combined and creates the final mixdown automatically.

### Create Multitrack Production

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "multi_input_files": [
                {"type": "multitrack", "id": "speech track"},
                {"type": "multitrack", "id": "music track"}
            ],
            "metadata": { "title": "My first Multitrack Production" },
            "output_files": [{"format": "mp3"}],
            "is_multitrack": true
        }'
```

Parameters:
- `is_multitrack`: must be set to true
- `type`: must be set to `multitrack` for each track
- `id`: a readable identifier for each track

### Upload Audio Files for Tracks

```bash
curl -X POST https://auphonic.com/api/production/{uuid}/upload.json \
    -H "Authorization: bearer {api_key}" \
    -F "speech track=@/home/user/file-for-track1.wav" \
    -F "music track=@/home/user/file-for-track2.wav"
```

### Multitrack Audio Algorithm Settings

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "multi_input_files": [
                {
                    "type": "multitrack", "id": "speech track",
                    "algorithms": {"denoise": true}
                },
                {
                    "type": "multitrack", "id": "music track", 
                    "algorithms": {"filtering": false, "backforeground": "background"}
                }
            ],
            "algorithms": {
                "loudnesstarget": -23,
                "leveler": true,
                "gate": true,
                "crossgate": true
            },
            "is_multitrack": true
        }'
```

### Export Individual Tracks

To get back the individual, processed tracks:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "output_files": [
                {"format": "tracks", "ending": "wav.zip"},
                {"format": "tracks", "ending": "flac.zip"}
            ],
            "is_multitrack": true
        }'
```

---

## Authentication

### API Key Authentication

Add the API Key to the authorization header:

```bash
curl https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}"
```

Or as a GET parameter:

```bash
curl https://auphonic.com/api/productions.json?bearer_token={api_key}
```

### HTTP Basic Authentication

```bash
curl https://auphonic.com/api/productions.json -u username:password
```

### OAuth 2.0 Authentication

**Step 1:** Register an Auphonic App at the [Auphonic Apps Page](https://auphonic.com/api/apps/)

**Step 2:** Redirect user to confirmation page:
```
https://auphonic.com/oauth2/authorize/?client_id={client_id}&redirect_uri={redirect_uri}&response_type=code
```

**Step 3:** Obtain access token:

```bash
curl -X POST https://auphonic.com/oauth2/token/ \
    -F "client_id={client_id}" \
    -F "client_secret={client_secret}" \
    -F "redirect_uri={redirect_uri}" \
    -F "grant_type=authorization_code" \
    -F "code={grant_code}"
```

**Step 4:** Use access token:

```bash
curl https://auphonic.com/api/productions.json \
    -H "Authorization: Bearer {access_token}"
```

---

## Webhooks

Webhooks are HTTP POST callbacks to automate the interaction with Auphonic after processing is finished.

### Add Webhook to Production

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "preset": "iWfe3DUoKLFxF7pJzoq5qa",
            "input_file": "http://auphonic.com/media/audio-examples/file.m4a",
            "webhook": "https://your-server.com/callback"
        }'
```

### Webhook Request Details

After production is finished, Auphonic calls your webhook URI with:

Parameters sent:
- `uuid`: the uuid of your production
- `status_string`: either `Done` or `Error`  
- `status`: 3 for `Done` or 2 for `Error`

Example webhook call:
```bash
curl -X POST https://your-web-hook.com/endpoint \
    -d "status=3&status_string=Done&uuid=zeigtvDv8jH6NaK52Z5eXH"
```

---

## External Service Integration

Integrate your service as an [External Service](https://auphonic.com/help/web/services.html#web-external-services) within Auphonic.

### 1. OAuth App Setup

Setup an **Auphonic OAuth app** following the [OAuth 2.0 Authentication Flow for Web Apps](https://auphonic.com/help/api/authentication.html#web-flow).

### 2. External Service Creation Call

```bash
curl -X POST \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer {access_token_of_your_user}" \
    https://auphonic.com/api/service/connect.json \
    -d '{
            "display_name": "My Family Podcast",
            "webhook_url": "https://your-service.fm/userXY/webhook/token/",
            "custom_data": "any-data-string-you-want"
        }'
```

### 3. Webhook after Production

Auphonic will invoke your webhook URL using **JWT-encoded** POST request:

```bash
curl -X POST -H "Content-Type: application/jwt" \
    https://your-service.fm/userXY/webhook/token/ \
    -d 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...'
```

Decode using your OAuth client_secret:

```python
import jwt
data = jwt.decode(
    jwt_token,
    your_oauth_client_secret, 
    algorithms=["HS256"]
)
# Returns: {"uuid": "production_uuid", "custom_data": "your_data"}
```

---

## API Examples

### Simple JavaScript File Upload (CORS)

```javascript
function createCORSRequest(method, url) {
  var xhr = new XMLHttpRequest();
  if ("withCredentials" in xhr) {
    xhr.open(method, url, true);
  } else if (typeof XDomainRequest != "undefined") {
    xhr = new XDomainRequest();
    xhr.open(method, url);
  } else {
    xhr = null;
  }
  return xhr;
}

function createProduction() {
  var xhr = new createCORSRequest("POST", "https://auphonic.com/api/simple/productions.json");
  xhr.setRequestHeader("Authorization", "Bearer XXXXXXX");

  var formData = new FormData();
  formData.append("title", "javascript upload test");
  formData.append("artist", "me");
  formData.append("loudnesstarget", -23);
  formData.append("action", "start");
  formData.append("input_file", document.querySelector('#files').files[0]);

  xhr.send(formData);
}

document.getElementById('files').addEventListener('change', createProduction, false);
```

HTML form:
```html
<input type="file" id="files" name="files[]" multiple />
```

### Complex JavaScript Upload with Progress

```javascript
function createCORSRequest(method, url) {
  var xhr = new XMLHttpRequest();
  if ("withCredentials" in xhr) {
    xhr.open(method, url, true);
  } else if (typeof XDomainRequest != "undefined") {
    xhr = new XDomainRequest();
    xhr.open(method, url);
  } else {
    xhr = null;
  }
  return xhr;
}

function get_token() {
  return 'XXXXXXX'; // your OAuth2 bearer token
}

// Create production using JSON API
var xhr = createCORSRequest("POST", "https://auphonic.com/api/productions.json");
xhr.setRequestHeader("Content-Type", "application/json");
xhr.setRequestHeader("Authorization", "Bearer " + get_token());
xhr.onload = function(e) {
  console.log("Production: created");

  var response = JSON.parse(e.target.response);
  var production_uuid = response.data.uuid;
  var file = document.querySelector('#files').files[0];

  if (file) {
    console.log("File Upload: started");

    var url = 'https://auphonic.com/api/production/{uuid}/upload.json'.replace('{uuid}', production_uuid);
    var xhr2 = createCORSRequest("POST", url);
    xhr2.setRequestHeader("Authorization", "Bearer " + get_token());

    xhr2.upload.addEventListener("progress", function(e) {
      console.log((e.loaded / e.total) * 100);
    }, false);

    xhr2.onload = function(e) {
      console.log("File Upload: Done");
    };

    var formData = new FormData();
    formData.append('input_file', file);
    xhr2.send(formData);
  }
};

xhr.send(JSON.stringify({"metadata":{"title": "test upload 2"}}));
```

---

## Common Use Cases

### Batch Processing Multiple Files

Using Simple API for batch processing:

```bash
#!/bin/bash
for file in *.mp3; do
    curl -X POST https://auphonic.com/api/simple/productions.json \
        -H "Authorization: bearer {api_key}" \
        -F "preset=ceigtvDv8jH6NaK52Z5eXH" \
        -F "title=$file" \
        -F "input_file=@$file" \
        -F "action=start"
done
```

### Podcast Publishing Workflow

Complete workflow with metadata and publishing:

```bash
curl -X POST -H "Content-Type: application/json" \
    https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{
            "metadata": {
                "title": "Episode 42: The Answer",
                "artist": "My Podcast",
                "album": "Season 2",
                "track": 42,
                "genre": "Podcast",
                "summary": "In this episode we discuss the meaning of life.",
                "tags": ["philosophy", "life", "answers"]
            },
            "output_files": [
                {"format": "mp3", "bitrate": "128"},
                {"format": "aac", "bitrate": "64"}
            ],
            "outgoing_services": [
                {"uuid": "your_podcast_host_service_uuid"}
            ],
            "algorithms": {
                "leveler": true,
                "normloudness": true,
                "loudnesstarget": -16,
                "filtering": true
            },
            "input_file": "https://your-server.com/raw-episode-42.wav",
            "webhook": "https://your-cms.com/auphonic-webhook",
            "action": "start"
        }'
```

### Error Handling and Status Monitoring

Monitor production status:

```bash
#!/bin/bash
UUID="your_production_uuid"

while true; do
    STATUS=$(curl -s "https://auphonic.com/api/production/$UUID/status.json" \
        -H "Authorization: bearer {api_key}" | \
        jq -r '.data.status_string')
    
    echo "Status: $STATUS"
    
    if [ "$STATUS" = "Done" ] || [ "$STATUS" = "Error" ]; then
        break
    fi
    
    sleep 30
done

echo "Production finished with status: $STATUS"
```

---

## Best Practices

### API Rate Limits and Performance

- Use webhooks instead of polling for status updates
- Batch operations when possible using the Simple API
- Cache preset UUIDs to avoid repeated lookups
- Use appropriate timeouts for long-running operations

### Error Handling

Always check HTTP status codes and parse error responses:

```bash
response=$(curl -w "%{http_code}" -s -o response.json \
    -X POST https://auphonic.com/api/productions.json \
    -H "Authorization: bearer {api_key}" \
    -d '{"invalid": "data"}')

if [ "$response" -eq 200 ]; then
    echo "Success"
else
    echo "Error: HTTP $response"
    cat response.json
fi
```

### Security Considerations

- Always use HTTPS for API requests
- Store API keys securely, never in client-side code
- Use OAuth 2.0 for third-party applications
- Validate webhook signatures for external service integrations
- Implement proper authentication for your webhook endpoints

### File Format Recommendations

- Use lossless formats (WAV, FLAC) for input when possible
- Choose appropriate bitrates for output formats:
  - MP3: 96-128 kbps for podcasts, 192+ for music
  - AAC: 64-96 kbps for podcasts, 128+ for music
  - Opus: 48-64 kbps for podcasts (most efficient)

### Metadata Best Practices

- Always include title, artist, and genre
- Use consistent naming conventions
- Include episode numbers in track field for podcasts
- Add descriptive summaries for better discoverability
- Use tags for categorization and search

---

## Troubleshooting

### Common Error Codes

- **400 Bad Request**: Invalid JSON or missing required parameters
- **401 Unauthorized**: Invalid or missing API key/token
- **403 Forbidden**: Insufficient permissions or credits
- **404 Not Found**: Invalid UUID or resource doesn't exist
- **413 Payload Too Large**: File size exceeds limits
- **429 Too Many Requests**: Rate limit exceeded

### File Upload Issues

- Ensure file format is supported (MP3, WAV, M4A, FLAC, etc.)
- Check file size limits (varies by account type)
- Use correct Content-Type headers for requests
- Verify @ prefix for local files in curl commands

### Authentication Problems

- Verify API key is active and has sufficient permissions
- Check OAuth token expiration
- Ensure proper Authorization header format: `Bearer {token}`
- Test with HTTP Basic auth if other methods fail

### Webhook Debugging

- Verify webhook URL is accessible from external sources
- Check that endpoint accepts POST requests
- Implement proper error handling and return appropriate HTTP codes
- Log incoming webhook data for debugging

---

## API Reference Summary

### Base URLs
- Production: `https://auphonic.com/api/`
- Simple API: `https://auphonic.com/api/simple/`

### Authentication Headers
```
Authorization: Bearer {api_key}
Authorization: Bearer {access_token}  # OAuth
```

### Content Types
- JSON requests: `Content-Type: application/json`
- File uploads: `multipart/form-data` (automatic with curl -F)
- Webhooks: `application/x-www-form-urlencoded` or `multipart/form-data`

### Key Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/productions.json` | Create production |
| GET | `/productions.json` | List productions |
| GET | `/production/{uuid}.json` | Get production details |
| POST | `/production/{uuid}/upload.json` | Upload files |
| POST | `/production/{uuid}/start.json` | Start processing |
| DELETE | `/production/{uuid}.json` | Delete production |
| GET | `/services.json` | List external services |
| GET | `/presets.json` | List presets |
| POST | `/simple/productions.json` | Simple API endpoint |

This completes the comprehensive Auphonic API documentation. The API provides powerful audio processing capabilities with flexible integration options for various workflows and applications.
