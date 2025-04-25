# Salient Object Segmentation: U2Net vs. SAM

This project compares two state-of-the-art models for salient object segmentation: **U2Net** and **SAM (Segment Anything Model)**. The evaluation is designed to be fully automated and includes both quantitative and qualitative analysis. A novel silent prompt technique was implemented to adapt SAM for batch segmentation.

## Project Structure

├── models/ │ ├── u2net.onnx # Pre-trained U2Net model (ONNX) │ └── sam_vit_h_4b8939.pth # Pre-trained SAM model checkpoint ├── DUT-OMRON-image/ # Input images ├── DUT-OMRON-mask/ # Ground truth masks ├── u2net_result/ # Output masks and visuals from U2Net ├── sam_result/ # Output masks and visuals from SAM ├── compare_scores.csv # IoU results ├── SAM-Vs-U2NET-Salient-Segment-Evaluate.ipynb # Evaluation notebook └── README.md

## Models Compared

### U2Net
U2Net is a deep neural network designed for salient object detection. It provides accurate and lightweight predictions and is well-suited for binary segmentation tasks. In this project, it is run using ONNX for fast inference.

### SAM (Segment Anything Model)
SAM is a versatile segmentation model developed by Meta AI. It typically relies on user-provided prompts (such as points or boxes) to guide segmentation. In this project, SAM is adapted for automatic use by generating prompts from ground truth masks.

## Silent Segmentation Trick

To automate SAM's interactive segmentation pipeline:

- The function `get_random_points_from_mask` selects up to 10 random foreground pixels from the ground truth mask to use as prompts.
- If no foreground is found, the center point is used as fallback.
- These prompts are passed to the SAM predictor, which generates masks.
- Multiple mask outputs are merged using pixel-wise union.

This trick enables SAM to be used in a fully automated batch evaluation loop.

## Evaluation Method

### Metric: Intersection over Union (IoU)

\[
\text{IoU} = \frac{|A \cap B|}{|A \cup B|}
\]

IoU measures the overlap between predicted and ground truth masks. For each image:

1. Both U2Net and SAM produce binary masks.
2. These are compared with the ground truth mask.
3. IoU is calculated for each prediction and saved to CSV.
4. Final results include average IoU per model.

### Quantitative Results

| Model   | Average IoU |
|---------|-------------|
| U2Net   | 0.6151      |
| SAM     | 0.8544      |

SAM outperformed U2Net on the DUT-OMRON dataset in terms of segmentation accuracy.

### Qualitative Results

Each output includes:

- `*_mask.png` – Binary segmentation mask
- `*_foreground.png` – Extracted object
- `*_background.png` – Background only

These visualizations are saved under `u2net_result/` and `sam_result/`.
