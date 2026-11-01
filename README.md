# Edge Smart CCTV: Dynamic Storage Compression

A C++ / OpenCV program that reduces CCTV storage by recording at **full FPS only when motion is detected**, and only **about 1 frame per second** when the scene is static.

## Features

- Motion detection using MOG2 background subtraction
- Noise filtering (wind, rain, grain) with morphological operations
- Dynamic frame-dropping policy (ACTIVE vs STATIC)
- Live preview with bounding boxes and a motion mask window
- Prints the final storage reduction percentage

## How It Works

1. Reads a video file frame by frame.
2. MOG2 background subtraction marks the moving areas.
3. Thresholding removes shadows, and morphological OPEN/CLOSE removes noise and fills holes.
4. Connected components find each moving blob and measure its area.
5. If any blob is larger than 30 pixels, the state is **ACTIVE** and every frame is saved.
6. Otherwise the state is **STATIC** and only one frame per second is saved.
7. At the end, the program reports:

```
Storage Reduction = (frames read - frames stored) / frames read x 100
```

## Quick Start (Windows, no installation)

1. Go to the **Releases** page and download `CCTV_Portable.zip`.
2. Extract the zip.
3. Keep your video in the same folder and name it `test_input.mp4`.
4. Double-click `Micro Project.exe`.
5. Press **q** to stop early.
6. The result is saved as `output_compressed.mp4` in the same folder.

You can also drag any video file onto the exe, or run it from a terminal:

```
"Micro Project.exe" myvideo.mp4 result.mp4
```

If Windows reports a missing DLL, install the free **Microsoft Visual C++ Redistributable (x64)**.

## Build From Source

**Requirements**

- Windows with Visual Studio (Desktop development with C++)
- OpenCV 5.0 (extracted, for example, to `C:\opencv`)

**Steps**

1. Create a new C++ Console App project and paste in `src/main.cpp`.
2. Set the configuration to **Release | x64**.
3. In **Project > Properties**:
   - C/C++ > General > Additional Include Directories: `C:\opencv\build\include`
   - Linker > General > Additional Library Directories: `C:\opencv\build\x64\vc16\lib`
   - Linker > Input > Additional Dependencies: `opencv_world500.lib`
4. Add `C:\opencv\build\x64\vc16\bin` to your system PATH.
5. Build with **Ctrl + Shift + B** and run with **Ctrl + F5**.

**Note:** this code uses `connectedComponentsWithStats` instead of `findContours`, `contourArea` and `boundingRect`, because those functions are not available in the same headers in OpenCV 5.0.

## Project Structure

```
CCTV-Smart-Compression/
├── src/
│   └── main.cpp
├── README.md
├── .gitignore
└── (Releases) CCTV_Portable.zip
```

## Tunable Settings

| Setting | Default | Effect |
|---|---|---|
| `minObjectArea` | 30 | Higher ignores more noise, lower detects smaller objects |
| MOG2 `history` | 500 | Number of frames used to learn the background |
| MOG2 `varThreshold` | 25 | Motion sensitivity |
| Static sampling | 1 frame/sec | How many frames are kept when nothing moves |

## Notes

- The output plays faster during static periods, like a timelapse, because frames were removed.
- Storage saving is measured in frame count. Actual file size also depends on the codec (`mp4v`).
- MOG2 needs a short warm-up, so the first few frames may show false motion.

## Tech Stack

C++, OpenCV 5.0, MOG2 background subtraction, morphological image processing

## Author

Shubh Yadav,MITS,B.tech


