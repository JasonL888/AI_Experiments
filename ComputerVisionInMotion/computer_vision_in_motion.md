---
marp: true
author: Jason Lau
size: 16:9
theme: dracula
paginate: true
transition: fade
class: 
- lead
header: Computer Vision in Motion
footer: "© 2025 SophiArch"
style: |
    .columns {
        display: grid;
        grid-template-columns: repeat(2, minmax(0, 1fr));
        gap: 1rem;
    }
    section.lead h1 {
        text-align: center;
    }
    table {
        font-size: 0.8em;
        width: 100%;
        text-align: center;
    }
    tbody {
        text-align: justify;
    }
    li > strong {
        color: var(--dracula-orange);
    }
    li {
      font-size: 0.9em;
    }
    strong {
        color: var(--dracula-orange);
    }
    img[alt~="center"] {
        display: block;
        margin: 0 auto;
    }
---

# <br><br><br>Computer Vision in Motion


![bg right w:80%](../Images/SophiArch_Logo.png)

---
# <br><br><br>Fundamentals Concepts

![bg right w:80%](../Images/computer_vision.png)

---
# From Static to Dynamic Vision
- Evolution of Computer Vision Tasks
    - **Image Classification**: "What is in this image?"
    - **Object Detection**: "Where are the objects and what are they?"
    - **Image Segmentation**: "Which pixels belong to which objects?"

- **Key Shift**: 
    - Understanding spatial relationships and multiple objects in complex scenes

---

# Object Detection Fundamentals
- **Bounding Boxes**
    - Rectangular regions defining object locations
    - Formats: (x, y, width, height) or (x1, y1, x2, y2)
    - Confidence scores for each detection

- Key Challenges
    - Multiple objects at different scales
    - Real-time processing requirements
    - Occlusion and varying viewpoints

---
# Image Segmentation Types
<div class="columns">
<div>

- Semantic Segmentation
![width:500 height:90](https://b2633864.smushcdn.com/2633864/wp-content/uploads/2022/06/semantic-segmentation.png?lossy=2&strip=1&webp=1)

- Instance Segmentation
![width:500 height:90](https://b2633864.smushcdn.com/2633864/wp-content/uploads/2022/06/instance-segmentation.png?lossy=2&strip=1&webp=1)

- Panoptic Segmentation
![width:250 height:90](https://b2633864.smushcdn.com/2633864/wp-content/uploads/2022/06/panoptic-segmentation-1.png?lossy=2&strip=1&webp=1)![width:250 height:90](https://b2633864.smushcdn.com/2633864/wp-content/uploads/2022/06/panoptic-segmentation-2.png?lossy=2&strip=1&webp=1)
</div>
<div>

- **Semantic** and **Instance** segmentation
    - mature and widely used
- **Panoptic** segmentation
    - newer and growly rapidly for comprehensive scene understanding


> image from https://pyimagesearch.com/
</div>
</div>





---
# Performance Comparision
Task|	Output|	Speed|	Use Cases
----|---------|------|------------
Classification|	Single label|	Fastest|	Content filtering, basic recognition
Object Detection|	Bounding boxes with labels|	Fast|	Surveillance, retail analytics
Segmentation|	Pixel-level masks|	Slowest|	Medical imaging, autonomous driving

---
# Technical Considerations
- Challenges in Real-time Applications
    - Computational Constraints: Balancing accuracy vs. speed
    - Lighting Variations: Robustness to different conditions
    - Scale Variations: Detecting objects at multiple distances
    - Occlusion Handling: Partial object visibility

---
# <br><br><br>Computer Vision Metrics


![bg right w:80%](../Images/computer_vision.png)


---
# Intersection over Union (IoU)
<div class="columns">
<div>

$$
IoU = \frac{\text{Area of Overlap}}{\text{Area of Union}}
$$

- measures how close one prediction box is to the real box
    - 100%: perfect match
    - &nbsp; &nbsp; 0%: no match

</div>
<div>

![width:600](../Images/computer_vision_metrics/computer_vision_metrics.001.jpeg)
</div>
</div>

---
# Average Precision (AP)

<div class="columns">
<div>

$$
AP = \text{Area under Precision-Recall curve}
$$
- measures how well model classifies overall
    - not just the one box
- combines 2 ideas
    - **Precision**: of all predicted
        - how many were correct ?
    - **Recall**: of all the actual
        - how many did the model find ?

</div>
<div>

![width:600](../Images/computer_vision_AP)
</div>
</div>

<!--
AP=1.0 (perfect) - curves stay at top at all recall levels
AP=0.5 (average) - precision drops as recall increases (some missed detections or false positives)
AP=0 (poor) - model fails to detect anything 
-->



---

# Mean Average Precision (mAP)

<div class="columns">
<div>

- Mean AP values for all classes
    - eg. 3 classes (cat, dog, car)
        $$
        mAP = \frac{AP_{cat} + AP_{dog} + AP_{car}}{3}
        $$

- mAP@50-95
    - more strict and modern standard 
        - based on COCO benchmark
    - AP at 10 IoU thresholds
        - 0.50,0.55 ... 0.90,0.95
</div>
<div>

![width:600](../Images/computer_vision_mAP_at_50_95.png)

</div>
</div>

<!--
Here’s a simple visualization showing how AP changes with IoU thresholds:
- mAP@50 → the AP when IoU = 0.5 (lenient match).
- mAP@75 → the AP when IoU = 0.75 (stricter match).
- mAP@50–95 → the average AP across all thresholds from 0.5 to 0.95 (step 0.05), giving a more balanced view of detection quality across easy and hard cases.
-->

---

# <br><br><br>Computer Vision Models

![bg right w:80%](../Images/computer_vision.png)


---
# YOLO: You Only Look Once

- Revolution in Real-time Detection
    - **Single-stage architecture**: 
        - Divides image into grid
        - Predicts bounding boxes and class probabilities simultaneously
        - Extremely fast inference speeds

    - **Advantages**:
        - Real-time performance (45-150 FPS)
        - Global context understanding
        - Simple pipeline

---
# Demo - YOLO
- [Yolo with livestream video](https://githubtocolab.com/JasonL888/AI_Experiments/blob/main/ComputerVisionInMotion/yolo_livestream.ipynb)

```python
from ultralytics import YOLO
import cv2

# Load pre-trained model
model = YOLO('yolo11n.pt')

# Webcam detection
cap = cv2.VideoCapture(0)
while True:
    ret, frame = cap.read()
    results = model(frame)
    annotated_frame = results[0].plot()
    cv2.imshow('YOLO Detection', annotated_frame)                                                 
```

---

# <br><br><br>References

![bg right w:80%](../Images/computer_vision.png)


---
# References
- [Semantic vs Instance vs Panoptic Segmentation](https://pyimagesearch.com/2022/06/29/semantic-vs-instance-vs-panoptic-segmentation/)
- [A Comprehensive Review of YOLO...](https://arxiv.org/html/2304.00501v6/)
- [OpenCV Docs](https://docs.opencv.org/4.x/index.html)