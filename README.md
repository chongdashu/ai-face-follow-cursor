# Face Follow Mouse Cursor

> A proof-of-concept web application that makes portrait images "follow" your cursor using depth-based 2.5D parallax effects.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.5-blue.svg)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React-18.3-61dafb.svg)](https://reactjs.org/)
[![Three.js](https://img.shields.io/badge/Three.js-0.169-black.svg)](https://threejs.org/)


> **As Seen On the YouTube Video:** [Building an AI app with BOTH Claude Code & Cursor... without a single line of code](https://www.youtube.com/watch?v=jXc9zL2GPUI)


**[🚀 Want more resources? Check it out at BuilderPack.ai](https://build.aioriented.dev/?utm_source=github&utm_medium=readme&utm_campaign=face_follow_cursor)**

## 🎮 Demo Previews

## 🎯 Demo

[**Live Demo**](https://face-follow-mouse-cursor.vercel.app)*

<!-- Add a demo GIF or screenshot here -->

## ✨ Features

- **Upload & Process**: Drag-and-drop portrait images with automatic depth map generation
- **Real-time Parallax**: Smooth 3D parallax effect powered by Three.js and custom WebGL shaders
- **ML-Powered Depth**: On-device depth estimation using ONNX Runtime (WASM/WebGL/WebGPU)
- **Smooth Cursor Tracking**: EMA smoothing, dead zones, and angle clamping for natural motion
- **Adjustable Controls**: Real-time intensity and smoothing sliders
- **Performance Optimized**: Automatic mesh subdivision adjustment for different devices
- **No Backend Required**: Runs entirely in the browser, suitable for static hosting

## 🚀 Quick Start

### Prerequisites

- Node.js 18+ and npm

### Installation

```bash
# Clone the repository
git clone https://github.com/chongdashu/ai-face-follow-cursor.git
cd ai-face-follow-cursor

# Install dependencies
npm install

# Start development server
npm run dev
```

Visit `http://localhost:5173` and upload a portrait image to see the effect!

### Optional: Add Depth Model

For better depth estimation, download an ONNX depth model:

```bash
# Create models directory
mkdir -p public/models

# Download model (235 MB, recommended)
# Download from: https://huggingface.co/onnx-community/depth-anything-v2-large/resolve/main/onnx/model_q4f16.onnx
# Place in: public/models/depth-anything-v2-small.onnx
```

**Note**: Without a model, the app uses a simple fallback depth map (radial gradient) which still demonstrates the effect.

## 🛠️ Tech Stack

- **Frontend**: React 18 + TypeScript + Vite
- **3D Rendering**: Three.js with custom vertex/fragment shaders
- **ML Inference**: ONNX Runtime Web (WebGPU/WebGL/WASM backends)
- **Research**: [Vectal.ai](https://vectal.ai) - AI-powered research and productivity
- **Deployment**: Vercel (with optional serverless functions)

## 📖 Usage

1. **Upload Image**: Drag and drop a portrait image or click to browse
2. **Wait for Processing**: Depth map is generated automatically (~1-3 seconds)
3. **Move Cursor**: Watch the portrait follow your cursor in real-time
4. **Adjust Controls**: Use sliders to fine-tune intensity and smoothing
5. **Try Atlas Mode**: *(Optional)* Generate multiple gaze-directed images via Replicate API

### Atlas Mode (Optional)

For more realistic gaze tracking, you can generate a grid of pre-rendered gaze images:

1. Get a [Replicate API token](https://replicate.com/account/api-tokens)
2. Copy environment file: `cp .env.example .env.local`
3. Add your token: `REPLICATE_API_TOKEN=r8_your_token_here`
4. Restart dev server and use the "Generate Atlas" feature

## ⚙️ Configuration

Edit `src/config.ts` to customize:

- **Rotation Limits**: Default ±12° (yaw), ±8° (pitch)
- **Smoothing**: EMA alpha 0.1-0.35 (default 0.2)
- **Dead Zone**: 8% of viewport (cursor tracking threshold)
- **Depth Scale**: 0.01-0.025 (parallax intensity)
- **Mesh Subdivisions**: 128 (vertex density)
- **Performance Thresholds**: Auto-adjust quality on slow devices

## 🏗️ Project Structure

```
src/
├── components/
│   ├── Upload.tsx          # Image upload with drag-and-drop
│   ├── Viewer.tsx          # Main Three.js scene orchestrator
│   ├── Controls.tsx        # UI sliders and debug panel
│   ├── AtlasGenerator.tsx  # Atlas image generation UI
│   └── AtlasViewer.tsx     # Atlas rendering mode
├── lib/
│   ├── cursor/             # Cursor-to-rotation mapping
│   ├── depth/              # ONNX depth inference
│   ├── three/              # Three.js scene setup
│   └── atlas/              # Atlas mode utilities
├── shaders/                # GLSL vertex/fragment shaders
└── config.ts               # Configuration constants
```

## 🚢 Deployment

### Vercel (Recommended)

```bash
npm install -g vercel
vercel
```

Or connect your GitHub repository to Vercel for automatic deployments.

**Environment Variables** (optional, for Atlas Mode):
- `REPLICATE_API_TOKEN`: Your Replicate API token

### Static Hosting

The app works on any static hosting platform:

```bash
npm run build
# Deploy the `dist/` directory to:
# - Netlify
# - GitHub Pages
# - Cloudflare Pages
# - AWS S3 + CloudFront
# - etc.
```

## 🎓 How It Works

1. **Image Upload**: User uploads a portrait image
2. **Depth Estimation**: ONNX model generates a depth map (or uses fallback)
3. **3D Scene Setup**: Three.js creates a subdivided plane mesh
4. **Vertex Displacement**: Custom shader displaces vertices based on depth and cursor position
5. **Cursor Tracking**: Cursor position is mapped to rotation angles with smoothing
6. **Animation Loop**: Scene re-renders at 60fps with updated rotations

### Key Techniques

- **Depth-Based Displacement**: Vertices are displaced proportionally to their depth value
- **Sine-Wave Rotation**: Displacement uses `sin(yaw)` and `sin(pitch)` for smooth motion
- **Dead Zone + EMA**: Cursor tracking uses dead zones and exponential moving average for natural feel
- **Provider Fallback**: ONNX Runtime tries WebGPU → WebGL → WASM → CPU for maximum compatibility

## 📚 Depth Models

The app supports any ONNX depth estimation model with these requirements:

- **Input**: RGB image, shape `[1, 3, H, W]`, normalized to `[0, 1]`
- **Output**: Depth map (single or multi-channel)

### Recommended Models

**Depth Anything V2** - Best quality and speed

| Model | Size | Quality | Speed | License |
|-------|------|---------|-------|---------|
| [model_q4f16.onnx](https://huggingface.co/onnx-community/depth-anything-v2-large/resolve/main/onnx/model_q4f16.onnx) | 235 MB | ⭐⭐⭐⭐ | ⚡⚡⚡ | CC-BY-NC-4.0 (non-commercial) |
| [model_fp16.onnx](https://huggingface.co/onnx-community/depth-anything-v2-large/resolve/main/onnx/model_fp16.onnx) | 669 MB | ⭐⭐⭐⭐⭐ | ⚡⚡ | CC-BY-NC-4.0 (non-commercial) |
| [Depth Anything V2 Small](https://huggingface.co/onnx-community/depth-anything-v2-small) | ~100 MB | ⭐⭐⭐ | ⚡⚡⚡ | Apache 2.0 (commercial OK) |

**Installation**:
1. Download model file
2. Place in `public/models/depth-anything-v2-small.onnx`
3. Or update path in `src/components/Viewer.tsx`

**CDN Hosting**: Models can be served from Hugging Face CDN (no download needed):
```typescript
// In src/config.ts
modelPath: 'https://huggingface.co/onnx-community/depth-anything-v2-small/resolve/main/onnx/model.onnx'
```

## 🐛 Troubleshooting

### Model won't load
- Check browser console for errors
- Verify file path is correct (case-sensitive)
- Ensure model is in `public/models/`, not `src/`
- Try CDN URL instead of local file

### Slow performance
- Use quantized model (`model_q4f16.onnx`)
- Reduce `meshSubdivisions` in `src/config.ts`
- Check which ONNX provider is active (WebGPU > WebGL > WASM)
- Lower `maxImageWidth` to reduce inference time

### Parallax effect not smooth
- Increase `emaAlpha` in controls (more smoothing)
- Adjust `deadZone` size
- Check FPS in debug panel (should be 60fps)
- Reduce `meshSubdivisions` if FPS is low

## 🤝 Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Development

```bash
# Run development server
npm run dev

# Run linter
npm run lint

# Build for production
npm run build

# Preview production build
npm run preview
```

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🙏 Credits

- **Vectal.ai**: [vectal.ai](https://vectal.ai) - Thank you for sponsoring the YouTube video!
- **Wes Bos**: Special thanks for the [shoutout](https://x.com/wesbos/status/1987976884920217831)!
- **Depth Anything V2**: [Depth-Anything-V2](https://github.com/DepthAnything/Depth-Anything-V2)
- **Face Looker**: [kylan02/face_looker](https://github.com/kylan02/face_looker) (atlas approach inspiration)
- **Three.js**: [threejs.org](https://threejs.org/)
- **ONNX Runtime**: [onnxruntime.ai](https://onnxruntime.ai/)
- **Replicate**: [replicate.com](https://replicate.com/) (gaze image generation)

## ⭐ Show Your Support

If you find this project interesting, please consider giving it a star on GitHub!

---

**Made with ❤️ using React, TypeScript, and Three.js**
