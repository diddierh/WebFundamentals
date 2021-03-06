---
layout: updates/post
title: "Web apps that talk - Introduction to the Speech Synthesis API"
description: "Introduction to the Web Speech API's synthesis feature."
published_on: 2014-01-14
updated_on: 2014-01-14
authors:
  - ericbidelman
tags:
  - news
  - multimedia
  - voice
  - webspeech
  - synthesis
---
The [Web Speech API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html) adds [voice recognition](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#speechreco-section) (speech to text) and [speech synthesis](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html#tts-section) (text to speech) to JavaScript. The post briefly covers the latter, as the API recently landed in Chrome 33 (mobile and desktop). If you're interested in speech recognition, [Glen Shires](http://www.html5rocks.com/en/profiles/#glenshires) had a great writeup a while back on the voice recognition feature, "[Voice Driven Web Apps: Introduction to the Web Speech API](http://updates.html5rocks.com/2013/01/Voice-Driven-Web-Apps-Introduction-to-the-Web-Speech-API)".

## Basics

The most basic use of the synthesis API is to pass the `speechSynthesis.speak()` and utterance:

{% highlight javascript %}
    var msg = new SpeechSynthesisUtterance('Hello World');
    window.speechSynthesis.speak(msg);
{% endhighlight %}

However, you can also alter parameters to effect the volume, speech rate, pitch, voice, and language:

{% highlight javascript %}
var msg = new SpeechSynthesisUtterance();
var voices = window.speechSynthesis.getVoices();
msg.voice = voices[10]; // Note: some voices don't support altering params
msg.voiceURI = 'native';
msg.volume = 1; // 0 to 1
msg.rate = 1; // 0.1 to 10
msg.pitch = 2; //0 to 2
msg.text = 'Hello World';
msg.lang = 'en-US';

msg.onend = function(e) {
  console.log('Finished in ' + event.elapsedTime + ' seconds.');
};

speechSynthesis.speak(msg);
{% endhighlight %}

### Setting a voice

The API also allows you to get a list of voice the engine supports:

{% highlight javascript %}
speechSynthesis.getVoices().forEach(function(voice) {
  console.log(voice.name, voice.default ? '(default)' :'');
});
{% endhighlight %}

Then set a different voice, by setting `.voice` on the utterance object:

{% highlight javascript %}
var msg = new SpeechSynthesisUtterance('I see dead people!');
msg.voice = speechSynthesis.getVoices().filter(function(voice) { return voice.name == 'Whisper'; })[0];
speechSynthesis.speak(msg);
{% endhighlight %}

Note that depending on the platform, Chrome might have to be online for the speech synthesis to work. Examine the `localService` flag of the `voice` object to tell.

## Demo

In my Google I/O 2013 talk, "[More Awesome Web: features you've always wanted](http://www.moreawesomeweb.com/)" ([www.moreawesomeweb.com](http://www.moreawesomeweb.com/)), I showed a Google Now/Siri-like demo of using the Web Speech API's `SpeechRecognition` service with the Google Translate API to auto-translate microphone input into another language:

{% ytvideo N_wTBKMuJis start=1695 %}

DEMO: [http://www.moreawesomeweb.com/demos/speech_translate.html](http://www.moreawesomeweb.com/demos/speech_translate.html)

Unfortunately, it used an undocumented (and unofficial API) to perform the speech synthesis. Well now we have the full Web Speech API to speak back the translation! I've updated the [demo](http://www.moreawesomeweb.com/demos/speech_translate.html) to use the synthesis API.

## Browser Support

Chrome 33 has full support for the Web Speech API, while Safari for iOS7 has partial support.

### Feature detection

Since browsers may support each portion of the Web Speech API separately (e.g. the case with Chromium), you may want to feature detect each feature separately:

{% highlight javascript %}
if ('speechSynthesis' in window) {
 // Synthesis support. Make your web apps talk!
}

if ('SpeechRecognition' in window) {
  // Speech recognition support. Talk to your apps!
}
{% endhighlight %}
