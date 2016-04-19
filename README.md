IBM Watson Speech Services for Web Browsers
===========================================

[![Build Status](https://travis-ci.org/watson-developer-cloud/speech-javascript-sdk.svg?branch=master)](https://travis-ci.org/watson-developer-cloud/speech-javascript-sdk)
[![npm-version](https://img.shields.io/npm/v/watson-speech.svg)](https://www.npmjs.com/package/watson-speech)

Allows you to easily add voice recognition and synthesis to any web app with minimal code. 

**Warning** This library is still has a few rough edges and may yet see breaking changes.


###  For Web Browsers Only
This library is primarily intended for use in browsers. 
Check out [watson-developer-cloud](https://www.npmjs.com/package/watson-developer-cloud) to use Watson services (speech and others) from Node.js.

However, a server-side component is required to generate auth tokens. 
The examples/ folder includes example Node.js and Python servers, and SDKs are available for [Node.js](https://github.com/watson-developer-cloud/node-sdk#authorization), 
[Java](https://github.com/watson-developer-cloud/java-sdk), 
[Python](https://github.com/watson-developer-cloud/python-sdk/blob/master/examples/authorization_v1.py), 
and there is also a [REST API](http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/doc/getting_started/gs-tokens.shtml).


### Installation - standalone

Pre-compiled bundles are available from on GitHub Releases - just download the file and drop it into your project: https://github.com/watson-developer-cloud/speech-javascript-sdk/releases


### Installation - npm with browserify

This library is built with [browserify](http://browserify.org/) and easy to use in browserify-based projects :

    npm install --save watson-speech


API & Examples
--------------

The basic API is outlined below, see complete API docs at http://watson-developer-cloud.github.io/speech-javascript-sdk/master/

See several examples at https://github.com/watson-developer-cloud/speech-javascript-sdk/tree/master/examples/static/

All API methods require an auth token that must be [generated server-side](https://github.com/watson-developer-cloud/node-sdk#authorization). 
(See https://github.com/watson-developer-cloud/speech-javascript-sdk/tree/master/examples/ for a couple of basic examples in Node.js and Python.)

## [`WatsonSpeech.TextToSpeech`](http://watson-developer-cloud.github.io/speech-javascript-sdk/master/module-watson-speech_text-to-speech.html)

### [`.synthesize({text, token})`](http://watson-developer-cloud.github.io/speech-javascript-sdk/master/module-watson-speech_text-to-speech_synthesize.html) -> `<audio>`

Speaks the supplied text through an automatically-created `<audio>` element. 
Currently limited to text that can fit within a GET URL (this is particularly an issue on [Internet Explorer before Windows 10](http://stackoverflow.com/questions/32267442/url-length-limitation-of-microsoft-edge)
where the max length is around 1000 characters after the token is accounted for.)

Options: 
* text - the text to transcribe // todo: list supported languages
* voice - the desired playback voice's name - see .getVoices(). Note that the voices are language-specific.
* autoPlay - set to false to prevent the audio from automatically playing


## [`WatsonSpeech.SpeechToText`](http://watson-developer-cloud.github.io/speech-javascript-sdk/master/module-watson-speech_speech-to-text.html)


### [`.recognizeMicrophone({token})`](http://watson-developer-cloud.github.io/speech-javascript-sdk/master/module-watson-speech_speech-to-text_recognize-microphone.html) -> [`RecognizeStream`][RecognizeStream]

Options: 
* `keepMic`: if true, preserves the MicrophoneStream for subsequent calls, preventing additional permissions requests in Firefox
* Other options passed to [RecognizeStream]
* Other options passed to [WritableElementStream] if `options.outputElement` is set

Requires the `getUserMedia` API, so limited browser compatibility (see http://caniuse.com/#search=getusermedia) 
Also note that Chrome requires https (with a few exceptions for localhost and such) - see https://www.chromium.org/Home/chromium-security/prefer-secure-origins-for-powerful-new-features

Pipes results through a [FormatStream] by default, set `options.format=false` to disable.

Known issue: Firefox continues to display a microphone icon in the address bar after recording has ceased. This is a browser bug.


### [`.recognizeFile({data, token})`](http://watson-developer-cloud.github.io/speech-javascript-sdk/master/module-watson-speech_speech-to-text_recognize-file.html) -> [`RecognizeStream`][RecognizeStream]

Can recognize and optionally attempt to play a [File](https://developer.mozilla.org/en-US/docs/Web/API/File) or [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
(such as from an `<input type="file"/>` or from an ajax request.)

Options: 
* `data`: a `Blob` or `File` instance. 
* `play`: (optional, default=`false`) Attempt to also play the file locally while uploading it for transcription 
* Other options passed to [RecognizeStream]
* Other options passed to [WritableElementStream] if `options.outputElement` is set

`play`requires that the browser support the format; most browsers support wav and ogg/opus, but not flac.) 
Will emit a `playback-error` on the RecognizeStream if playback fails. 
Playback will automatically stop when `.stop()` is called on the RecognizeStream.

Pipes results through a [TimingStream] by if `options.play=true`, set `options.realtime=false` to disable.

Pipes results through a [FormatStream] by default, set `options.format=false` to disable.


## Changelog

### v0.15
* Removed `SpeechToText.recognizeElement()` due to quality issues
* Added `options.element` to TextToSpeech.synthesize() to support playing through exiting elements
* Fixed a couple of bugs in the TimingStream
* Added support for STT profanity_filter &  documented keywords and words_alternatives options.

### v0.14
* Moved getUserMedia shim to a [standalone library](https://www.npmjs.com/package/get-user-media-promise)
* added a python token server example

### v0.13
* Fixed bug where `continuous: false` didn't close the microphone at end of recognition
* Added `keepMic` option to `recognizeMicrophone()` to prevent multiple permission popups in firefox

### v0.12
* Added `autoPlay` option to `synthesize()`
* Added proper parameter filtering to `synthesize()`

### v0.11
* renamed `recognizeBlob` to `recognizeFile` to make the primary usage more apparent
* Added support for `<input>` and `<textarea>` elements when using the `targetElement` option (or a `WritableElementStream`)
* For objectMode, changed defaults for `word_confidence` to `false`, `alternatives` to `1`, and `timing` to off unless required for `realtime` option. 
* Fixed bug with calling `.promise()` on `objectMode` streams
* Fixed bug with calling `.promise()` on `recognizeFile({play: true})`

### v0.10
* Added ability to write text directly to targetElement, updated examples to use this
* converted examples from jQuery to vanilla JS (w/ fetch pollyfill when necessary)
* significantly improved JSDoc

### v0.9
* Added basic text to speech support

### v0.8
* deprecated `result` events in favor of `objectMode`.
* renamed the `autoplay` option to `autoPlay` on `recognizeElement()` (capital P)

### v0.7
* Changed `playFile` option of `recognizeBlob()` to just `play`, corrected default
* Added `options.format=true` to `recognize*()` to pipe text through a FormatStream
* Added `options.realtime=options.play` to `recognizeBlob()` to automatically pipe results through a TimingStream when playing locally
* Added `close` and `end` events to TimingStream
* Added `delay` option to `TimingStream`
* Moved compiled binary to GitHub Releases (in addition to uncompiled source on npm).
* Misc. doc and internal improvements

## todo

* Solidify API
* break components into standalone npm modules where it makes sense
* run integration tests on travis (fall back to offline server for pull requests)
* more tests in general
* better cross-browser testing (IE, Safari, mobile browsers - maybe saucelabs?)
* update node-sdk to use current version of this lib's RecognizeStream (and also provide the FormatStream + anything else that might be handy)
* move `result` and `results` events to node wrapper (along with the deprecation notice)
* improve docs
* consider a wrapper to match https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html
* support a "hard" stop that prevents any further data events, even for already uploaded audio, ensure timing stream also implements this.
* look for bug where single-word final results may omit word confidence (possibly due to FormatStream?)
* fix bug where TimingStream shows words slightly before they're spoken
* support jquery objects for element and targetElement

[RecognizeStream]: http://watson-developer-cloud.github.io/speech-javascript-sdk/master/RecognizeStream.html
[TimingStream]: http://watson-developer-cloud.github.io/speech-javascript-sdk/master/TimingStream.html
[FormatStream]: http://watson-developer-cloud.github.io/speech-javascript-sdk/master/FormatStream.html
[WritableElementStream]: http://watson-developer-cloud.github.io/speech-javascript-sdk/master/WritableElementStream.html
