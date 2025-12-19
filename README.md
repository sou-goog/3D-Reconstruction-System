# AI-Powered 3D Reconstruction System
## Cross-Platform Single-Image 3D Object Reconstruction Using LRM and TripoSR

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svg)](https://pytorch.org/)
[![Flask](https://img.shields.io/badge/Flask-3.0+-green.svg)](https://flask.palletsprojects.com/)

---

## 📋 Quick Navigation

- [Overview](#-overview)
- [Features](#-features)  
- [Installation](#-installation)
- [Usage](#-usage)
- [Complete Deep Dive](#complete-deep-dive-3d-reconstruction-system)
  - [Part 1: System Architecture](#part-1-system-architecture--core-technologies)
  - [Part 2: Flask Application](#part-2-flask-application-deep-dive)
  - [Part 3: Background Processing](#part-3-background-processing--ai-pipeline)
  - [Part 4: 3D Viewer & API](#part-4-server-sent-events-3d-viewer--api-architecture)

---

## 🎯 Overview

This is an **AI-powered 3D reconstruction application** that converts a single 2D image into a complete 3D model using deep learning, computer vision, and 3D graphics rendering.

**Input:** Single 2D photo (e.g., chair.jpg)

**Output:**
- `mesh.obj` - 3D geometry file
- `mesh.stl` - 3D printing format
- `mesh.mtl` - Material definition
- `mesh_texture.png` - Texture map
- `render.mp4` - 360° rotation video
- 30 individual render frames

---

## ✨ Features

- 🤖 **AI-Powered** - TripoSR transformer-based neural network
- 🎨 **Auto Background Removal** - U²-Net segmentation
- 📹 **360° Video** - Automatic rotation animation
- 🖼️ **Interactive Viewer** - WebGL-based 3D visualization
- 📦 **Multiple Formats** - OBJ, STL, MTL exports
- ⚡ **GPU Accelerated** - CUDA support for 10x speedup
- 🔄 **Real-time Progress** - Server-Sent Events streaming

---

## 📦 Installation

```bash
# Clone repository
git clone https://github.com/sou-goog/AI-Powered-3D-Reconstruction-System.git
cd AI-Powered-3D-Reconstruction-System

# Install dependencies
pip install -r requirements.txt

# Run application
python app.py
```

**Access:** `http://localhost:5000`

---

## 🎮 Usage

### Web Interface
1. Upload image (drag & drop or browse)
2. Watch real-time progress updates
3. View interactive 3D model
4. Download files (OBJ/STL/MP4)

### Command Line
```bash
python run.py input.jpg --device cuda --output ./output
```

### Python API
```python
from tsr.system import TSR
model = TSR.from_pretrained("stabilityai/TripoSR")
mesh = model.extract_mesh(scene_codes)[0]
```

---

# Complete Deep Dive: 3D Reconstruction System

## Part 1: System Architecture & Core Technologies

---

### 1.1 What This System Does

This is an **AI-powered 3D reconstruction application** that converts a single 2D image into a complete 3D model. The system uses:
- Deep learning (neural networks)
- Computer vision
- 3D graphics rendering
- Web technologies

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

### 1.2 High-Level Architecture

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

### 1.3 Complete Technology Stack

#### **1.3.1 Backend (Python)**

| Library | Version | Purpose | Deep Explanation |
|---------|---------|---------|------------------|
| **Flask** | 3.0.0 | Web Framework | Lightweight WSGI web application framework. Handles HTTP requests/responses, routing, templating. WSGI = Web Server Gateway Interface (Python standard for web apps). |
| **PyTorch** | Latest | Deep Learning | Facebook's machine learning library. Provides tensor operations, automatic differentiation (autograd), GPU acceleration via CUDA. Used to run the TripoSR neural network. |
| **rembg** | Latest | Background Removal | Uses U²-Net neural network (trained on 15k images) to segment foreground/background. Returns RGBA image with transparent background. |
| **PIL (Pillow)** | Latest | Image Processing | Python Imaging Library. Loads/saves images, resize, format conversion (PNG/JPG/etc), color space operations (RGB/RGBA), image filtering. |
| **NumPy** | Latest | Array Operations | Numerical Python. Provides N-dimensional arrays, mathematical operations, broadcasting. Used for image arrays (H×W×C), matrix math, vectorized operations. |
| **trimesh** | 4.0.5+ | 3D Mesh Processing | Loads/saves 3D files (OBJ/STL/PLY), mesh operations (repair, simplify), ray tracing, texture baking via ColorVisuals.to_texture(). |
| **imageio-ffmpeg** | Latest | Video Encoding | Python wrapper for FFmpeg. Converts image sequences to MP4/AVI. Uses H.264 codec for web-compatible videos. |
| **flask-cors** | Latest | CORS Headers | Cross-Origin Resource Sharing. Allows requests from different domains (needed for Android app API calls). Adds `Access-Control-Allow-Origin` headers. |

#### **1.3.2 AI Model Architecture**

**TripoSR** (Stability AI):
- **Type:** Transformer-based 3D reconstruction
- **Input:** Single RGB image (512×512)
- **Output:** 3D triplane representation (3 feature planes)
- **Training Data:** Objaverse dataset (800k+ 3D models)
- **Model Size:** ~1.5 GB
- **Inference Time:** ~10-30 seconds on GPU, 60-120 seconds on CPU

**Components:**
1. **Image Tokenizer:** Converts image to tokens (like words in NLP)
2. **Transformer Backbone:** Processes tokens, learns 3D structure
3. **TriPlane Decoder:** Converts tokens to 3 orthogonal feature planes
4. **NeRF-like Renderer:** Queries triplanes to render novel views
5. **Marching Cubes:** Extracts mesh surface from density field

#### **1.3.3 Frontend (JavaScript)**

| Library | Version | Purpose | Deep Explanation |
|---------|---------|---------|------------------|
| **Three.js** | 0.155.0 | 3D Graphics | WebGL wrapper. Provides scene graph, cameras, lights, materials, geometries. Abstracts raw WebGL API. Uses GPU for real-time rendering. |
| **OBJLoader** | Three.js addon | OBJ Parser | Parses Wavefront OBJ file format. Reads vertices (`v`), normals (`vn`), texture coords (`vt`), faces (`f`). Creates Three.js geometry. |
| **MTLLoader** | Three.js addon | Material Parser | Parses MTL (Material Template Library). Reads ambient (Ka), diffuse (Kd), specular (Ks), texture maps (map_Kd). Creates Three.js materials. |
| **OrbitControls** | Three.js addon | Camera Control | Mouse/touch controls for 3D camera. Left-drag = rotate, right-drag = pan, scroll = zoom. Uses spherical coordinates. Auto-rotation support. |
| **Bootstrap** | 5.3.0 | UI Framework | CSS framework with grid system, components (buttons, cards, modals), responsive design, utility classes. |
| **Font Awesome** | 6.4.0 | Icon Library | Vector icons (SVG/font). Used for UI icons (download, rotate, wireframe, etc). Scalable and customizable. |

---

### 1.4 System Requirements

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

**Browser:**
- Modern browser with WebGL 2.0 support
- Chrome 90+, Firefox 88+, Safari 15+

---

### 1.5 Performance Metrics

| Operation | Time (GPU) | Time (CPU) | Memory |
|-----------|-----------|-----------|--------|
| Model Loading | 5-10 sec | 5-10 sec | 1.5 GB |
| Background Removal | 1-2 sec | 3-5 sec | 500 MB |
| Scene Code Generation | 2-5 sec | 20-40 sec | 2 GB |
| Rendering 30 Views | 3-8 sec | 30-60 sec | 1 GB |
| Mesh Extraction | 2-4 sec | 5-10 sec | 500 MB |
| Texture Baking | 1-2 sec | 1-2 sec | 200 MB |
| **Total** | **10-30 sec** | **60-120 sec** | **4-5 GB peak** |

---

## Part 2: Flask Application Deep Dive

This section covers the Flask web framework fundamentals and how the application handles HTTP requests, manages sessions, and coordinates background processing.

### Key Concepts
- WSGI (Web Server Gateway Interface)
- Request/Response handling
- Session management with encrypted cookies
- Background threading for non-blocking processing
- Server-Sent Events (SSE) for real-time updates

---

## Part 3: Background Processing & AI Pipeline

This section details the complete image processing pipeline from upload to 3D model generation.

### Pipeline Stages
1. **Image Loading & Preprocessing** - Resize to 512×512
2. **Background Removal** - U²-Net neural network
3. **AI Model Inference** - TripoSR transformer
4. **3D Rendering** - NeRF-style volume rendering
5. **Mesh Extraction** - Marching Cubes algorithm (256³ grid)
6. **Texture Baking** - UV unwrapping with xatlas
7. **File Export** - OBJ, STL, MTL, PNG, MP4

---

## Part 4: Server-Sent Events, 3D Viewer & API Architecture

This section covers real-time communication and 3D visualization.

### Technologies
- **Server-Sent Events (SSE)** - One-way server→client streaming
- **Three.js** - WebGL-based 3D rendering
- **OrbitControls** - Interactive camera manipulation
- **REST API** - Programmatic access to all features

---

## 🔌 API Reference

### Endpoints

```http
POST /                    # Upload image
GET /progress/{id}        # SSE progress stream  
GET /result/{folder}      # View results
GET /output/{folder}/{file}  # Download files
GET /gallery              # View all results
```

### Example: Upload & Process

```bash
curl -X POST http://localhost:5000/ \
  -F "image=@chair.jpg"
```

---

## 📝 License

MIT License - see [LICENSE](LICENSE) file

---

## 🙏 Acknowledgments

- **[Stability AI](https://stability.ai/)** - TripoSR model
- **[HuggingFace](https://huggingface.co/)** - Model hosting
- **[Three.js](https://threejs.org/)** - 3D visualization

---

<div align="center">

**⭐ Star this repo if you find it useful!**

</div>
