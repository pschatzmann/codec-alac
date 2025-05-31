# Arduino ALAC Codec

[![Arduino Library](https://img.shields.io/badge/Arduino-Library-blue.svg)](https://www.arduino.cc/reference/en/libraries/)

The __Apple Lossless Audio Codec__ (ALAC) is an audio codec developed by Apple and supported on iPhone, iPad, most iPods, Mac and iTunes. ALAC is a data compression method which reduces the size of audio files with no loss of information. A decoded ALAC stream is bit-for-bit identical to the original uncompressed audio file.

This Arduino library provides a portable implementation of the ALAC encoder and decoder from [Apple's open source implementation](https://github.com/macosforge/alac).

## Features

- Decode ALAC audio to PCM
- Encode PCM audio to ALAC format
- Support for 16-bit and 24-bit audio
- Support for mono and stereo audio
- Compatible with standard ALAC files

## Installation

1. Download the latest release as ZIP
2. In Arduino IDE, go to Sketch > Include Library > Add .ZIP Library
3. Select the downloaded ZIP file

## Class Documentation

- [Decoder](https://pschatzmann.github.io/codec-alac/html/classALACDecoder.html)
- [Encoder](https://pschatzmann.github.io/codec-alac/html/classALACEncoder.html)

## Usage Instructions

The standard API is a nightmare and it is quite impossible to get the parameters right. The missing documentation details and example code does not help either and then there are the all the AI tools that provide just wrong information! So I suggest not to bother with these classes but I recommend to use the [Codec API](https://github.com/pschatzmann/arduino-audio-tools/wiki/Encoding-and-Decoding-of-Audio) provided by the [Arduino Audio Tools](https://github.com/pschatzmann/arduino-audio-tools).


Here is a simple Arduino Example sketch that encodes and decodes a sine wave and prints it as CSV to the console:

```C++

#include "AudioTools.h"
#include "AudioTools/AudioCodecs/CodecALAC.h"

//SET_LOOP_TASK_STACK_SIZE(16*1024); // 16KB

AudioInfo info(44100, 2, 16);
SineWaveGenerator<int16_t> sineWave( 32000);  // subclass of SoundGenerator with max amplitude of 32000
GeneratedSoundStream<int16_t> sound( sineWave); // Stream generated from sine wave
CsvOutput<int16_t> out(Serial);
EncoderALAC enc_alac;
DecoderALAC dec_alac;
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

  // optionally copy config from encoder to decoder
  // since decoder already has audio info and frames
  //dec_alac.setCodecConfig(enc_alac.config()); 
  //dec_alac.setCodecConfig(enc_alac.binaryConfig());

  // start decoder
  decoder.begin(info);
  
  Serial.println("Test started...");
}


void loop() { 
  copier.copy();
}

```