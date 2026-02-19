# 🎨 Manhwa (Korean Webtoon) Translator

# Intro

A powerful batch processing pipeline designed to automatically translate Korean manhwa (comics) into English. This tool handles the entire process from speech bubble detection to translated text rendering, preserving the original comic structure.

This tool only translates raw chapters. You need to download raw chapters yourself and arrange these images in manner described in **Installation and Usage** Part. I used [Mihon](https://github.com/mihonapp/mihon) Mobile App to download Raw Chapters.

**NOTE:** It should be noted that this tool is created using only free resources without using any external api's so translation quality may not be that much excellent.

# Translation Samples

| Raw | Translated |
|------|-----------------|
| <img src="https://github.com/user-attachments/assets/13242a62-f122-4ab5-a9d1-b1f3410f1de4" width="300"> | <img src="https://github.com/user-attachments/assets/f641ce2b-61a4-4509-b4c0-d0826d45bea0" width="300"> |
| <img src="https://github.com/user-attachments/assets/066766fb-4733-4ad5-bb7d-8c995570c335" width="300"> | <img src="https://github.com/user-attachments/assets/c47f5015-abc4-4076-b426-466cfadedf1e" width="300"> |
| <img src="https://github.com/user-attachments/assets/0c016a07-82bf-4fb5-9592-30de2d658ceb" width="300"> | <img src="https://github.com/user-attachments/assets/680bf313-f858-4384-a0a0-95f4e01c671e" width="300"> |

# 📦 Installation & Usage

This project is optimized for **Google Colab**.

**NOTE:** To ensure the batch processing pipeline runs smoothly, please organize your raw chapters using the structure below before uploading:
```
📦 Your_Upload_File.zip
 ┃
 ┣━━ 📖 Manhwa_Chapter_01.cbz
 ┃    ┣━━ 🖼️ 001.jpg
 ┃    ┣━━ 🖼️ 002.jpg
 ┃    ┗━━ 🖼️ 003.jpg
 ┃
 ┣━━ 📖 Manhwa_Chapter_02.cbz
 ┃    ┣━━ 🖼️ 001.png
 ┃    ┣━━ 🖼️ 002.png
 ┃    ┗━━ 🖼️ 003.png
 ┃
 ┗━━ 📖 Manhwa_Chapter_03.cbz
      ┗━━ ...
```

1.  Open the `Colab Notebook.ipynb` notebook in Google Colab or a Jupyter environment with GPU support.

2.  Install dependencies (automatically handled in the first cell):
    ```bash
    !pip install ultralytics easyocr transformers accelerate opencv-python pillow sentencepiece
    ```

3.  Run the cells sequentially.

4.  When prompted, upload a ZIP file containing your `.cbz` manhwa files.

5.  Wait for processing to complete.

6.  The final translated ZIP file will be automatically downloaded.

# 🧠 How it Works

### 1. Input and Extraction
The pipeline accepts a bulk .zip archive containing multiple .cbz files. It automatically deconstructs the comic archives into a structured image processing queue, ensuring page order is strictly maintained.

### 2. Speech Bubble Detection
**Model:** [YOLOv8m](https://huggingface.co/ogkalu/comic-text-and-bubble-detector) (Fine-tuned for Comic Bubbles)

**Function:** Identifies and locates speech balloons, thought bubbles, and text boxes. By using a medium-sized YOLOv8 model, we achieve a high balance between detection speed and accuracy for dense manhwa layouts.

### 3. OCR (Text Extraction)
**Model:** [EasyOCR](https://github.com/JaidedAI/EasyOCR)

**Function:** Extracts raw Hangul (Korean) text from the bounding boxes provided by the detection model. This targeted approach prevents the OCR from incorrectly reading background art or sound effects (SFX).

### 4. Text Translation
**Model:** [NLLB-200](https://huggingface.co/NHNDQ/nllb-finetuned-ko2en) (Fine-tuned Ko2En)

**Function:** Translates the extracted text into English. We utilize Meta’s No Language Left Behind model, specifically fine-tuned for Korean-to-English dialogue to better handle the nuances of manhwa speech patterns.

### 5. Inpainting and Rendering
**Cleaning:** Original Korean text is masked using a "White-out" technique based on the precise coordinates of the detected text.

**Typesetting:** The translated English text is dynamically wrapped and rendered inside the original bubbles. The system automatically calculates font size to ensure the text fits the bubble geometry.

### 6. Post Processing and Packaging
**CBZ Creation:** Processed images are grouped back into their original chapter sets.

**Final Export:** The chapters are converted back into .cbz format and bundled into a single .zip file for a one-click download experience.

# Acknowledgement

This project leverages state-of-the-art AI models for each stage of the pipeline:

| Task | Model / Library | Reference |
|------|-----------------|-----------|
| **Speech Bubble Detection** | YOLOv8m (Fine-tuned) | [ogkalu/comic-speech-bubble-detector-yolov8m](https://huggingface.co/ogkalu/comic-text-and-bubble-detector) |
| **OCR (Text Extraction)** | EasyOCR | [JaidedAI/EasyOCR](https://github.com/JaidedAI/EasyOCR) |
| **Translation** | NLLB-200 (Fine-tuned Ko2En) | [NHNDQ/nllb-finetuned-ko2en](https://huggingface.co/NHNDQ/nllb-finetuned-ko2en) |
