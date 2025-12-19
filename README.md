# AI-Powered 3D Reconstruction System
## Cross-Platform Single-Image 3D Object Reconstruction Using LRM and TripoSR

**University Project - CSB22066**  
*Department of Electronics and Communication Engineering*  
*Tezpur University*

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [System Architecture](#system-architecture)
3. [Technology Stack](#technology-stack)
4. [Features](#features)
5. [Installation](#installation)
6. [Usage](#usage)
7. [Project Structure](#project-structure)
8. [Technical Documentation](#technical-documentation)
9. [Performance Metrics](#performance-metrics)
10. [API Reference](#api-reference)
11. [Contributing](#contributing)
12. [License](#license)

---

## 🎯 Overview

This is an **AI-powered 3D reconstruction application** that converts a single 2D image into a complete 3D model. The system leverages cutting-edge deep learning techniques, computer vision, and 3D graphics rendering to transform ordinary photographs into interactive 3D objects.

**Input:** A single 2D photo (e.g., chair.jpg)

**Output:**
- `mesh.obj` - 3D geometry file (OBJ format)
- `mesh.stl` - 3D printing format (STL format)
- `mesh.mtl` - Material definition file
- `mesh_texture.png` - Texture/color map
- `render.mp4` - 360° rotation video
- `render_000.png` to `render_029.png` - 30 individual frames
- Interactive WebGL viewer in browser

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     USER INTERFACE                          │
│  (Web Browser - HTML/CSS/JavaScript + Three.js)             │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ HTTP Requests
                        ▼
┌─────────────────────────────────────────────────────────────┐
│               FLASK WEB SERVER (app.py)                     │
│  • Routing: Maps URLs to functions                          │
│  • Session Management: Tracks users                         │
│  • File Handling: Upload/Download                           │
│  • Background Threading: Non-blocking processing            │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ Function Calls
                        ▼
┌─────────────────────────────────────────────────────────────┐
│           IMAGE PREPROCESSING PIPELINE                      │
│  • Background Removal (rembg library)                       │
│  • Resize to 512×512 (PIL library)                          │
│  • RGBA → RGB conversion (NumPy)                            │
│  • Image normalization                                      │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ Preprocessed Image
                        ▼
┌─────────────────────────────────────────────────────────────┐
│            TSR AI MODEL (tsr/system.py)                     │
│  • Neural Network: TripoSR (Transformer-based)              │
│  • Framework: PyTorch                                       │
│  • Input: 2D image tensor                                   │
│  • Output: 3D scene codes (latent representation)           │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ 3D Scene Codes
                        ▼
┌─────────────────────────────────────────────────────────────┐
│              3D RENDERING ENGINE                            │
│  • Render 30 views from different camera angles             │
│  • Use scene codes to generate images                       │
│  • Create rotation video                                    │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ Rendered Images
                        ▼
┌─────────────────────────────────────────────────────────────┐
│              MESH EXTRACTION                                │
│  • Marching Cubes Algorithm (extracts surface)              │
│  • Generate vertices and faces                              │
│  • Extract vertex colors                                    │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ 3D Mesh Data
                        ▼
┌─────────────────────────────────────────────────────────────┐
│           EXPORT & TEXTURE BAKING (trimesh)                 │
│  • OBJ Export: Geometry + normals                           │
│  • STL Export: For 3D printing                              │
│  • Texture Baking: Vertex colors → UV texture map           │
│  • MTL Generation: Material definition                      │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        │ Output Files
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                   FILE STORAGE                              │
│  output/{timestamp}/                                        │
│    ├── input.png                                            │
│    ├── mesh.obj                                             │
│    ├── mesh.stl                                             │
│    ├── mesh.mtl                                             │
│    ├── mesh_texture.png                                     │
│    ├── render_000.png ... render_029.png                    │
│    └── render.mp4                                           │
└─────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Technology Stack

### Backend (Python)

| Library | Version | Purpose |
|---------|---------|---------|
| **Flask** | 3.0.0 | Web Framework - Handles HTTP requests/responses, routing, templating |
| **PyTorch** | Latest | Deep Learning - Machine learning library for running TripoSR neural network |
| **rembg** | Latest | Background Removal - Uses U²-Net neural network for foreground/background segmentation |
| **PIL (Pillow)** | Latest | Image Processing - Loads/saves images, resize, format conversion |
| **NumPy** | Latest | Array Operations - N-dimensional arrays, mathematical operations |
| **trimesh** | 4.0.5+ | 3D Mesh Processing - Loads/saves 3D files, texture baking |
| **imageio-ffmpeg** | Latest | Video Encoding - Converts image sequences to MP4 |
| **flask-cors** | Latest | CORS Headers - Cross-Origin Resource Sharing for API |

### AI Model Architecture

**TripoSR** (Stability AI):
- **Type:** Transformer-based 3D reconstruction
- **Input:** Single RGB image (512×512)
- **Output:** 3D triplane representation
- **Training Data:** Objaverse dataset (800k+ 3D models)
- **Model Size:** ~1.5 GB
- **Inference Time:** ~10-30 seconds on GPU, 60-120 seconds on CPU

### Frontend (JavaScript)

| Library | Version | Purpose |
|---------|---------|---------|
| **Three.js** | 0.155.0 | 3D Graphics - WebGL wrapper for real-time 3D rendering |
| **Bootstrap** | 5.3.0 | UI Framework - Responsive design and components |
| **Font Awesome** | 6.4.0 | Icon Library - Vector icons for UI elements |

---

## ✨ Features

- 🤖 **AI-Powered Reconstruction** - Advanced neural networks for accurate 3D generation
- 🎨 **Automatic Background Removal** - Smart foreground extraction using U²-Net
- 📹 **360° Video Generation** - Automatic rotation video creation
- 🖼️ **Interactive 3D Viewer** - Real-time WebGL viewer with rotation and zoom
- 📦 **Multiple Export Formats** - OBJ, STL, MTL with texture support
- ⚡ **GPU Acceleration** - CUDA support for faster processing
- 🔄 **Real-time Progress Updates** - Server-Sent Events for live status
- 📱 **Responsive Design** - Works on desktop and mobile browsers
- 🎯 **3D Printing Ready** - STL format export for direct printing

---

## 📥 Installation

### Prerequisites

**Minimum:**
- Python 3.8+
- 8 GB RAM
- 10 GB disk space
- CPU: 4 cores

**Recommended:**
- Python 3.10+
- 16 GB RAM
- 20 GB disk space
- NVIDIA GPU with 8+ GB VRAM
- CUDA 11.8+

### Setup Instructions

1. **Clone the repository:**
```bash
git clone https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System.git
cd AI-Powered-3D-Reconstruction-System
```

2. **Create virtual environment:**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies:**
```bash
pip install -r requirements.txt
```

4. **Download U²-Net model (for background removal):**
The model will be downloaded automatically on first run to `~/.u2net/u2net.onnx` (~176 MB)

5. **Run the application:**
```bash
python app.py
```

6. **Access the web interface:**
Open your browser and navigate to `http://localhost:5000`

---

## 🎮 Usage

### Web Interface

1. **Upload Image:**
   - Click "Browse Files" or drag and drop an image
   - Supported formats: JPG, PNG, GIF
   - Optimal size: 512×512 to 2048×2048

2. **Processing:**
   - Watch real-time progress updates
   - Processing time: 30-120 seconds depending on hardware

3. **View Results:**
   - Interactive 3D viewer with mouse controls
   - Left-click drag: Rotate model
   - Right-click drag: Pan camera
   - Scroll: Zoom in/out

4. **Download Files:**
   - OBJ: 3D geometry with UV mapping
   - STL: For 3D printing
   - MP4: 360° rotation video
   - PNG: Texture map

### Command Line Interface

```bash
python run.py examples/chair.png --device cuda --output ./output
```

**Arguments:**
- `input`: Path to input image
- `--device`: `cuda` or `cpu` (default: auto-detect)
- `--output`: Output directory (default: `./output`)
- `--no-remove-bg`: Skip background removal
- `--foreground-ratio`: Object size ratio (default: 0.85)

---

## 📁 Project Structure

```
.
├── app.py                          # Main Flask application
├── api.py                          # REST API for Android app
├── requirements.txt                 # Python dependencies
├── README.md                       # This file
│
├── tsr/                            # AI model implementation
│   ├── system.py                   # TSR main class
│   ├── utils.py                    # Helper functions
│   ├── models/                     # Neural network layers
│   │   ├── isosurface.py          # Marching Cubes
│   │   ├── transformer.py         # Transformer blocks
│   │   └── renderer.py            # NeRF-style renderer
│   └── __init__.py
│
├── templates/                      # Jinja2 HTML templates
│   ├── index.html                 # Upload page
│   ├── processing.html            # Progress viewer
│   ├── result.html                # 3D viewer
│   └── gallery.html               # Model gallery
│
├── uploads/                        # Temporary upload storage
├── output/                         # Generated models
│   └── {timestamp}/               # Each session folder
│       ├── input.png
│       ├── mesh.obj
│       ├── mesh.stl
│       ├── mesh.mtl
│       ├── mesh_texture.png
│       ├── render_000.png ... render_029.png
│       └── render.mp4
│
├── examples/                       # Sample images
└── android_app/                    # Android application
```

---

## 📚 Technical Documentation

For detailed technical documentation, please refer to:

- **[Complete Deep Dive](./docs/DEEP_DIVE.md)** - Comprehensive technical documentation
- **[API Reference](./docs/API.md)** - REST API endpoints
- **[Model Architecture](./docs/MODEL.md)** - TripoSR model details

### Quick Technical Overview

#### Key Algorithms

1. **Background Removal:** U²-Net neural network
2. **3D Reconstruction:** TripoSR Transformer
3. **Surface Extraction:** Marching Cubes algorithm
4. **Texture Baking:** UV unwrapping with xatlas
5. **Rendering:** NeRF-style volume rendering

#### Processing Pipeline

```
Image Upload → Background Removal → Resize → AI Inference → 
Rendering (30 views) → Mesh Extraction → Texture Baking →  
Export (OBJ/STL/MTL/PNG/MP4)
```

---

## ⚡ Performance Metrics

| Operation | Time (GPU) | Time (CPU) | Memory |
|-----------|-----------|-----------|--------|
| Model Loading | 5-10 sec | 5-10 sec | 1.5 GB |
| Background Removal | 1-2 sec | 3-5 sec | 500 MB |
| Scene Code Generation | 2-5 sec | 20-40 sec | 2 GB |
| Rendering 30 Views | 3-8 sec | 30-60 sec | 1 GB |
| Mesh Extraction | 2-4 sec | 5-10 sec | 500 MB |
| **Total** | **10-30 sec** | **60-120 sec** | **4-5 GB peak** |

---

## 🔌 API Reference

### REST Endpoints

#### Upload Image
```http
POST /
Content-Type: multipart/form-data

Parameters:
  - image: File (JPG/PNG)

Response:
  - Redirects to /processing/{session_id}
```

#### Progress Stream (SSE)
```http
GET /progress/{session_id}
Accept: text/event-stream

Response:
  data: {"message": "Processing...", "timestamp": "12:34:56"}
  data: {"status": "complete", "folder_id": "1234567890"}
```

#### View Result
```http
GET /result/{folder_id}

Response:
  - HTML page with 3D viewer
```

#### Download Files
```http
GET /output/{folder_id}/{filename}

Files:
  - mesh.obj
  - mesh.stl
  - mesh.mtl
  - mesh_texture.png
  - render.mp4
```

---

## 🤝 Contributing

We welcome contributions! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👥 Team

**Group Project CSB22066**

[Add your team member names here]

**Supervisor:** [Add supervisor name]  
**Department:** Electronics and Communication Engineering  
**Institution:** Tezpur University

---

## 🙏 Acknowledgments

- **Stability AI** - For the TripoSR model
- **HuggingFace** - Model hosting and distribution
- **Three.js Community** - 3D visualization libraries
- **Tezpur University** - Academic support

---

## 📞 Contact

For questions or support, please contact:

- **Email:** [Your university email]
- **GitHub Issues:** [https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System/issues](https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System/issues)

---

## 🔗 Links

- **Live Demo:** [Add demo link if available]
- **Documentation:** [https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System/wiki](https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System/wiki)
- **Report Issues:** [https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System/issues](https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System/issues)

---

<div align="center">

**Made with ❤️ for Academic Excellence**

⭐ Star this repository if you find it helpful!

</div>
