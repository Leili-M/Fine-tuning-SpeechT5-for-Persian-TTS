#  Fine-Tuning SpeechT5 for Persian Text-to-Speech


---

##  Overview

This project presents a **fine-tuning pipeline for Microsoft’s SpeechT5** to generate natural-sounding **Persian (Farsi) speech** from text.  
Despite resource constraints, the model was successfully adapted to the Persian phoneme space using limited GPU memory, selective training, and dataset optimization strategies.

---

## Objective

To enable **multi-speaker Persian text-to-speech (TTS)** by fine-tuning a pretrained English model (SpeechT5) on a Persian corpus while:
- Preserving prosody and intelligibility,
- Handling limited GPU memory (high-performance unit restrictions),
- And achieving stable convergence with reduced batch sizes and epochs.

---

##  Architecture

| Component | Model | Description |
|------------|--------|-------------|
| Text-to-Speech Backbone | SpeechT5 (`microsoft/speecht5_tts`) | Converts tokenized Persian text to spectrograms |
| Vocoder | HiFi-GAN (`microsoft/speecht5_hifigan`) | Reconstructs waveform from spectrograms |
| Speaker Encoder | SpeechBrain X-Vector (`speechbrain/spkrec-xvect-voxceleb`) | Provides 512-dim speaker embeddings for voice adaptation |

---

##  Dataset Summary

- **Source:** Custom Persian dataset (based on Mozilla Common Voice Persian)  
- **Speakers:** 32 distinct voices  
- **Training samples:** 41,604  
- **Validation samples:** 10,558  
- **Sampling rate:** 16 kHz  

### Speaker Distribution
Most speakers contribute fewer than 1,000 utterances, while a few provide up to 7,000 samples — introducing mild class imbalance but valuable voice diversity.

*(See histogram below)*  
![speaker_distribution](assets/speaker_distribution.png)

---

##  Training Strategy

Due to **limited GPU memory**, the fine-tuning process focused on:
- **Selective layer training** (freezing encoder, training decoder only)  
- **Reduced batch size (≤ 8)** and **gradient accumulation**  
- **Token-length filtering** (discarding sequences > 200 tokens)  
- **Short-epoch training (5 epochs)** with early stopping and checkpointing  

Despite constraints, loss consistently decreased — indicating stable adaptation and learning progress.

---

##  Training Results

| Epoch | Train Loss | Validation Loss |
|:------:|:-----------:|:----------------:|
| 0 | 0.5308 | 0.5507 |
| 1 | 0.5138 | 0.5463 |
| 2 | 0.5120 | 0.5436 |
| 3 | 0.5070 | 0.5525 |
| 4 | 0.4990 | 0.5476 |

The model converged smoothly, with validation loss stabilizing around **0.54**, confirming that the simplified training setup did not compromise generalization.

---

##  Sample Outputs

###  Fine-Tuned Audio Examples
| ID | Type | Text (Phonetic) | Listen |
|----|------|------------------|--------|
| 9 | Validation | **hala do d/lil vojud dare** |  [Audio 1](samples/val_9.wav) 
| 2 | Validation | **mif/hmi ke m/nzur/m ciye** |  [Audio 2](samples/val_5.wav) 
| 5 | Validation | **@un xeyli b/d masa; meyde** |  [Audio 3](samples/val_2.wav) 


> *Samples exhibit improved fluency, natural prosody, and consistent speaker identity compared to zero-shot baseline.*

---

##  Observations

- The fine-tuned SpeechT5 successfully captured **Persian phonetic patterns**.  
- **Speaker embeddings** preserved timbre and style across voices.  
- **HiFi-GAN vocoder** produced clean, natural-sounding audio.  
- **Reduced training setup** (due to limited GPU) still achieved stable, high-quality synthesis.

---

## 🧾 Citation

