# Arduino ALAC Codec

[![arduino-library-badge](https://www.ardu-badge.com/badge/Arduino%20ALAC.svg?)](https://www.ardu-badge.com/Arduino%20ALAC)

The Apple Lossless Audio Codec (ALAC) is an audio codec developed by Apple and supported on iPhone, iPad, most iPods, Mac and iTunes. ALAC is a data compression method which reduces the size of audio files with no loss of information. A decoded ALAC stream is bit-for-bit identical to the original uncompressed audio file.

This Arduino library provides a portable implementation of the ALAC encoder and decoder from [Apple's open source implementation](https://github.com/macosforge/alac).

## Features

- Decode ALAC audio to PCM
- Encode PCM audio to ALAC format
- Support for 16-bit and 24-bit audio
- Support for mono and stereo audio
- Compatible with standard ALAC files

## Installation

### Arduino IDE

1. Open the Arduino IDE
2. Go to Sketch > Include Library > Manage Libraries
3. Search for "Arduino ALAC"
4. Click Install

### Manual Installation

1. Download the latest release as ZIP
2. In Arduino IDE, go to Sketch > Include Library > Add .ZIP Library
3. Select the downloaded ZIP file

