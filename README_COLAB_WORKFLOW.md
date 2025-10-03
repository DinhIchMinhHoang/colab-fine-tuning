# F5-TTS Hybrid Development Workflow

This setup allows you to develop locally in VS Code while training on Google Colab's powerful GPUs.

## 🏗️ Workflow Overview

1. **Local Development** (VS Code) - Code editing, debugging, configuration
2. **Cloud Training** (Google Colab) - GPU-intensive training tasks
3. **Sync via Git** - Keep code synchronized between local and cloud

## 📁 Project Structure

```
colab-fine-tuning/              # Your repository (becomes F5-TTS in Colab)
├── src/f5_tts/                # Source code (develop here)
├── configs/                   # Training configurations
├── data/vietnamese_char/      # Your Vietnamese dataset
│   ├── wavs/                 # Audio files
│   └── sample.csv            # Metadata file
├── ckpts/                    # Downloaded models
├── notebooks/
│   └── colab_training.ipynb  # Colab training notebook
└── README_COLAB_WORKFLOW.md  # This guide
```

## 🚀 Setup Instructions

### 1. Local Setup (VS Code)

1. **Clone and setup your repository:**
   ```bash
   git clone https://github.com/DinhIchMinhHoang/colab-fine-tuning.git
   cd colab-fine-tuning
   pip install -e .
   ```

2. **Make your changes locally:**
   - Edit code in VS Code
   - Test with small datasets
   - Commit and push changes to GitHub

### 2. GitHub Repository Setup

1. **Include your Vietnamese dataset in the repository:**
   ```
   colab-fine-tuning/
   ├── data/vietnamese_char/
   │   ├── wavs/          # Audio files
   │   └── sample.csv     # Metadata file
   ├── notebooks/
   │   └── colab_training.ipynb
   └── README_COLAB_WORKFLOW.md
   ```

2. **Push everything to GitHub:**
   ```bash
   git add data/vietnamese_char/ notebooks/ README_COLAB_WORKFLOW.md
   git commit -m "Add Vietnamese training dataset and Colab notebook"
   git push origin main
   ```

### 3. Colab Setup

1. **Upload the notebook:**
   - Upload `notebooks/colab_training.ipynb` to Google Colab
   - Or open directly from GitHub

2. **Run setup cells:**
   - Cell 1: Check GPU specs
   - Cell 2: Install F5-TTS and dependencies (includes cloning with data)
   - Cell 3: Verify data is available

## 🔄 Development Workflow

### Local Development Cycle:
1. **Edit code** in VS Code (configs, model changes, etc.)
2. **Test locally** with small samples
3. **Commit changes:** `git add . && git commit -m "Your changes"`
4. **Push to GitHub:** `git push origin main`

### Colab Training Cycle:
1. **Open your Colab notebook**
2. **Run Cell 9** to pull latest changes from GitHub
3. **Run training cells** (Cell 7) with updated code
4. **Monitor via TensorBoard** (Cell 6)
5. **Save models** to Google Drive (Cell 8)

## 📋 Key Commands

### Local (VS Code Terminal):
```bash
# Install dependencies
pip install -e .

# Test locally (small dataset)
python src/f5_tts/infer/infer_cli.py --help

# Commit and sync
git add .
git commit -m "Update training config"
git push origin main
```

### Colab:
```python
# Update code from GitHub (run in working directory)
!git pull origin main
!pip install -e . --force-reinstall

# Start training
!accelerate launch --mixed_precision=fp16 src/f5_tts/train/finetune_cli.py ...
```

## 🎯 Optimized Settings for Different GPUs

### T4 (15GB):
```bash
--batch_size_per_gpu 6000
--max_samples 32
--grad_accumulation_steps 2
```

### V100 (16GB):
```bash
--batch_size_per_gpu 8000
--max_samples 48
--grad_accumulation_steps 1
```

### A100 (40GB):
```bash
--batch_size_per_gpu 12000
--max_samples 64
--grad_accumulation_steps 1
```

## 📊 Monitoring Training

1. **TensorBoard in Colab:**
   - Run Cell 6 to start TensorBoard
   - Monitor loss, learning rate, and sample outputs

2. **Save Progress:**
   - Models auto-saved every 10,000 steps
   - Manual backup to Google Drive with Cell 8

## 🔧 Troubleshooting

### Common Issues:

1. **"Numpy not available" error:**
   ```bash
   pip install "numpy<2.0"
   ```

2. **CUDA out of memory:**
   - Reduce `batch_size_per_gpu`
   - Reduce `max_samples`
   - Increase `grad_accumulation_steps`

3. **Code changes not reflected:**
   - Run Cell 9 to pull latest changes
   - Restart runtime if needed

## 💡 Tips

- **Keep sessions alive:** Use Colab Pro to avoid disconnections
- **Regular backups:** Save models to Google Drive frequently
- **Monitor resources:** Check GPU usage in Colab's resource monitor
- **Version control:** Always commit changes before training

## 🔄 Quick Reference

| Task | Location | Command |
|------|----------|---------|
| Code editing | VS Code Local | Edit files normally |
| Testing | VS Code Local | `python -m f5_tts.infer.infer_cli` |
| Sync to cloud | VS Code Terminal | `git push origin main` |
| Training | Google Colab | Run training cells |
| Monitoring | Google Colab | TensorBoard cell |
| Download models | Google Colab | Save to Drive cell |

This workflow gives you the best of both worlds: powerful local development environment and cloud GPU training capabilities!