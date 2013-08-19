libKeyFinder can be used to estimate the musical key of digital recordings.

It is the basis of the KeyFinder GUI app, which is available as a binary download for Mac OSX and Windows at www.ibrahimshaath.co.uk/keyfinder

For the most basic use case, do something like this:
  
```c++
// Static because it retains useful resources for repeat use
static KeyFinder::KeyFinder k;

// Build an empty audio object
KeyFinder::AudioData a;

// Prepare the object for your audio stream
a.setFrameRate(yourAudioStream.framerate);
a.setChannels(yourAudioStream.channels);
a.addToSampleCount(yourAudioStream.length);

// Copy your audio into the object
while (int i = 0; i < yourAudioStream.length; i++) {
  a.setSample(i, yourAudioStream[i]);
}

// Run the analysis
KeyFinder::KeyDetectionResult r =  k.keyOfAudio(a);

// And do something with the result!
doSomethingWith(r.globalKeyEstimate);
```

Alternatively, you can transform a stream of audio into a chromatic representation, and make progressive estimates of the key:

```c++
// Run the analysis
KeyFinder::KeyDetectionResult r =  k.keyOfAudio(a);

// And do something with the result!
doSomethingWith(r.globalKeyEstimate);

KeyFinder::AudioData a;
a.setFrameRate(yourAudioStream.framerate);
a.setChannels(yourAudioStream.channels);
a.addToSampleCount(yourAudioStream.packetLength);

static KeyFinder::KeyFinder k;
// the workspace holds the memory allocations for analysis of a single track
KeyFinder::Workspace w;

while (yourPacket = newAudioPacket()) {
  while (int i = 0; i < newAudioPacket.length; i++) {
    a.setSample(i, newAudioPacket[i]);
  }

  k.progressiveChromagram(a, w);

  // if you want to grab progressive key estimates...
  KeyFinder::KeyDetectionResult r = k.keyOfChromagram(w);
  doSomethingWithMostRecentKeyEstimate(r.globalKeyEstimate);

}

// if you want to squeeze every last bit of audio from the working buffer...
k.finalChromagram(w)

// and finally...
KeyFinder::KeyDetectionResult r = k.keyOfChromagram(w);

doSomethingWithFinalKeyEstimate(r.globalKeyEstimate);
```
