# Arduino ALAC Codec

[![Arduino Library](https://img.shields.io/badge/Arduino-Library-blue.svg)](https://www.arduino.cc/reference/en/libraries/)

The __Apple Lossless Audio Codec__ (ALAC) is an audio codec developed by Apple and supported on iPhone, iPad, most iPods, Mac and iTunes. ALAC is a data compression method which reduces the size of audio files with no loss of information. A decoded ALAC stream is bit-for-bit identical to the original uncompressed audio file.

This Arduino library provides a portable implementation of the ALAC encoder and decoder from [Apple's open source implementation](https://github.com/macosforge/alac).

## Features

- Decode ALAC audio to PCM
- Encode PCM audio to ALAC format
- Support for 16-bit, 24-bit and 32-bit audio
- Support for mono, stereo audio and even more channels
- Compatible with standard ALAC files

## Installation

1. Download the latest release as ZIP
2. In Arduino IDE, go to Sketch > Include Library > Add .ZIP Library
3. Select the downloaded ZIP file

Or you can git clone this project into the Arduino libraries folder e.g. with

```
cd  ~/Documents/Arduino/libraries
git clone https://github.com/pschatzmann/codec-alac.git
```

This project can also be built (e.g. on your desktop) with cmake:


## Important Usage Notes

**The original ALAC API is complex and poorly documented.** After struggling with the direct implementation, I strongly recommend using the simplified wrapper in the [Arduino Audio Tools](https://github.com/pschatzmann/arduino-audio-tools) library instead of using this library directly.

### Key Requirements

1. **Configuration Sharing**: The ALAC decoder **must** receive the exact configuration parameters from the encoder
2. **Frame Size Consistency**: Data buffer sizes must be identical between encoder and decoder
3. **Container Format**: ALAC typically requires a container format (like M4A) to store essential metadata and keep the encoded data frames.

## Using with Arduino Audio Tools (Recommended)

The [Codec API](https://github.com/pschatzmann/arduino-audio-tools/wiki/Encoding-and-Decoding-of-Audio) from the Arduino Audio Tools library provides a much simpler interface for working with ALAC.

### Example: Encoding and Decoding a Sine Wave

Here is a simple example that generates a sine wave, encodes it, decodes it and writes the final result as CSV. We use custom frame size of 1024 samples.

```C++
#include "AudioTools.h"
#include "AudioTools/AudioCodecs/CodecALAC.h"

//SET_LOOP_TASK_STACK_SIZE(16*1024); // 16KB for ESP32

AudioInfo info(44100, 2, 16);
SineWaveGenerator<int16_t> sineWave(32000);  // subclass of SoundGenerator with max amplitude of 32000
GeneratedSoundStream<int16_t> sound(sineWave); // Stream generated from sine wave
CsvOutput<int16_t> out(Serial);
EncoderALAC enc_alac(1024);
DecoderALAC dec_alac(1024);
EncodedAudioStream decoder(&out, &dec_alac); // decode and write as csv
EncodedAudioStream encoder(&decoder, &enc_alac); // encode and write to decoder
StreamCopy copier(encoder, sound);     

void setup() {
  Serial.begin(115200);
  AudioToolsLogger.begin(Serial, AudioToolsLogLevel::Debug);

  // start Output
  Serial.println("starting Output...");
  auto cfgi = out.defaultConfig(TX_MODE);
  cfgi.copyFrom(info);
  out.begin(cfgi);

  // Setup sine wave
  sineWave.begin(info, N_B4);

  // start encoder
  encoder.begin(info);

  /* Optional but important for complex audio:
   * Copy configuration parameters from encoder to decoder.
   * The Audio Tools library handles this automatically in most cases.
   */
  //dec_alac.setCodecConfig(enc_alac.config()); 
  //dec_alac.setCodecConfig(enc_alac.binaryConfig());

  // start decoder (optionally providing the AudioInfo)
  decoder.begin(info);
  
  Serial.println("Test started...");
}

void loop() { 
  copier.copy();
}
```
As you can see above, there are three alternaive ways to synchronize the configuration:

- using dec_alac.setCodecConfig(enc_alac.config()); (= ALACSpecificConfig object)
- using setCodecConfig(enc_alac.binaryConfig()); (= Matical Cookie)
- using the AudioTools AudioInfo and the FrameSize in the constructor

## Direct API Documentation

If you need to use the original API directly (not recommended for beginners):

- [ALACDecoder Class Reference](https://pschatzmann.github.io/codec-alac/html/classALACDecoder.html)
- [ALACEncoder Class Reference](https://pschatzmann.github.io/codec-alac/html/classALACEncoder.html)

## Memory Requirements

ALAC encoding and decoding can be memory intensive. For ESP32 devices, you may need to increase the stack size:

```C++
SET_LOOP_TASK_STACK_SIZE(16*1024); // 16KB for ESP32
```

## License

This library uses code from Apple's open source ALAC project under the Apache License 2.0.

## Contributing

Contributions to improve the library are welcome. Please submit issues or pull requests on the GitHub repository.


