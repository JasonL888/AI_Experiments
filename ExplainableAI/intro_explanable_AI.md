---
marp: true
author: Jason Lau
size: 16:9
theme: dracula
paginate: true
transition: fade
class: 
- lead
header: Introduction to eXpanable AI (XAI)
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
    h1 {
        font-size: 1.35em;
    }
    li > strong {
        color: var(--dracula-orange);
    }
    li {
      font-size: 0.8em;
    }
    strong {
        color: var(--dracula-orange);
    }
    blockquote {
        font-size: 0.8em;
        font-style: italic;
    }
    img[alt~="center"] {
        display: block;
        margin: 0 auto;
    }
    .fa-twitter { color: aqua; }
    .fa-mastodon { color: purple; }
    .fa-linkedin { color: blue; }
    .fa-window-maximize { color: skyblue; }
    .fa-arrow-alt-circle-right {color: #8be9fd }
    @import 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.3.0/css/all.min.css'

---

# <br><br>Introduction to eXplanable AI (XAI)
- Addressing trust and interpretability in modern AI


![bg right w:80%](../Images/SophiArch_Logo.png)

---
# The AI Transparency Crisis

- **Modern AI models** 
    - increasingly complex and opaque
- **Deep learning networks** 
    - can have millions of parameters
- **Critical question**: 
    - How do these models make decisions?
- **The "Black Box"**: 
    - Input → ??? → Output

> It doesn't matter if you're a cat or a dog if no one can explain why

---

# Why Explainability Matters Now More Than Ever


<div class="columns">
<div>

- **AI Adoption Growth**:
    - Healthcare diagnostics
    - Autonomous vehicles
    - Financial lending
    - Legal sentencing
    - Hiring processes
</div>
<div>

- **Consequences of Unexplainable AI**:
    - Bias amplification
    - Safety risks
    - Legal liability
    - Regulatory compliance issues
</div>
</div>

---

# Key Concepts: Interpretability vs. Explainability

- Model Interpretability
    - **Intrinsic transparency**
    - Simple models (linear regression, decision trees)
    - Naturally understandable

- Model Explainability
    - **Post-hoc explanations**
    - Complex models (neural networks, ensembles)
    - Additional techniques to explain decisions

> Think
> - **Interpretable** = Glass box
> - **Explainable** = Black box with a manual

---

# Common XAI Techniques

| Global Methods | Local Methods | Visualization |
|----------------|---------------|---------------|
| Feature Importance | LIME | Saliency Maps |
| Partial Dependence | SHAP | Activation Maps |
| Model Distillation | Counterfactuals | Attention Maps |

---

# Feature Importance
<div class="columns">
<div>

- **What:**
    - Identifies which features most influence model predictions globally

- **How:**
    - Permutation importance
    - Gini importance (tree-based)
    - Coefficient magnitude (linear models)

</div>
<div>

- **Use cases:** 
    - Tabular data, structured data analysis

```python
# Example with scikit-learn
from sklearn.inspection import permutation_importance
result = permutation_importance(model, X_test, y_test, n_repeats=10)
```
</div>
</div>

---
# Hands-On Part 1: Feature Importance for Tabular Data
<div class="columns">
<div>

- Applying XAI to your existing classifier:
```python
import shap
import matplotlib.pyplot as plt

# Load your pre-trained model
# model = your_trained_classifier

# Create explainer
explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_test)

# Visualize feature importance
shap.summary_plot(shap_values, X_test)            
```
</div>
<div>

- Expected Output:
    - Feature importance ranking
    - Impact direction (positive/negative)
    - Global model behavior
</div>
</div>

---
# Saliency Maps for Image Classification
- **What**: 
    - Highlights pixels most influential to the prediction
- **How**:
    - Gradient-based methods
    - Class activation maps (Grad-CAM)
    - Guided backpropagation
- **For "cat vs. dog" classifier**:
    - Which pixels made model say "cat"?
    - Which features considered "dog-like"?

![bg right](https://images.unsplash.com/photo-1543852786-1cf6624b9987?ixlib=rb-4.0.3&auto=format&fit=crop&w=1000&q=80)

---
# Hands-On Part 2: Saliency Maps for Images
```python
import tensorflow as tf
import matplotlib.pyplot as plt

def create_saliency_map(model, image, class_index):
    image = tf.cast(image, tf.float32)
    
    with tf.GradientTape() as tape:
        tape.watch(image)
        prediction = model(image)
        class_score = prediction[:, class_index]
    
    gradients = tape.gradient(class_score, image)
    saliency = tf.reduce_max(tf.abs(gradients), axis=-1)
    return saliency[0]

# Apply to your image classifier
# saliency = create_saliency_map(model, test_image, predicted_class)                              
```

---

# SHAP: SHapley Additive exPlanations
- **Game theory approach**: 
    - Fairly distributes "credit" among features
- **Key properties**:
    - **Local accuracy**: Explanation matches model output
    - **Missingness**: Features with no effect get zero importance
    - **Consistency**: Better features get more credit

```python
import shap

# Model-agnostic explainer
explainer = shap.KernelExplainer(model.predict, X_train)
shap_values = explainer.shap_values(X_test_instance)

# Force plot for individual prediction
shap.force_plot(explainer.expected_value, shap_values, X_test_instance)                               
```

---

# LIME: Local Interpretable Model-agnostic Explanations
<div class="columns">
<div>

- **Core idea**: Approximate complex model locally with simple, interpretable model
- **Process**:
    1. Take single prediction to explain
    2. Perturb input data slightly
    3. Train simple model on perturbations
    4. Use simple model as local explanation
</div>
<div>


```python
import lime
import lime.lime_tabular

explainer = lime.lime_tabular.LimeTabularExplainer(
    training_data=X_train.values,
    feature_names=feature_names,
    mode='classification'
)

exp = explainer.explain_instance(
    X_test.iloc[0], 
    model.predict_proba, 
    num_features=5
)
exp.show_in_notebook(show_table=True)                       
```
</div>
</div>


---
# Critical Applications: Healthcare
<div class="columns">
<div>

- **Why explainability matters**:
    - **Diagnostic accuracy**: 
        - Doctors need to verify AI reasoning
    - **Treatment decisions**: 
        - Life-or-death consequences
    - **Liability**: 
        - Who's responsible for wrong diagnoses?
    - **Regulatory compliance**: 
        - FDA requires transparency
</div>
<div>

![center width:550](https://images.unsplash.com/photo-1559757148-5c350d0d3c56?ixlib=rb-4.0.3&auto=format&fit=crop&w=1000&q=80)
</div>
</div>

> Model detected cancer because of these specific tissue patterns ...

---
# Critical Applications: Finance
<div class="columns">
<div>

- **Loan approval systems**:
    - **Legal requirement**: 
        - Equal Credit Opportunity Act
    - **Bias detection**: 
        - Identify discriminatory patterns
    - **Customer trust**: 
        - "Why was my loan denied?"
    - **Risk management**: 
        - Understand model limitations
</div>
<div>

- **Trading algorithms**:
    - **Regulatory scrutiny**: 
        - SEC compliance
    - **Risk assessment**: 
        - Understanding failure modes
    - **Strategy validation**: 
        - Do the reasons make financial sense?
</div>
</div>


---
# Critical Applications: Law & Justice
<div class="columns">
<div>

- **Criminal sentencing risk assessment**:
    - **Due process**: 
        - Right to confront evidence
    - **Bias amplification**: 
        - Perpetuating historical inequalities
    - **Judicial oversight**: 
        - Judges must understand recommendations
    - **Appeal process**: 
        - Unexplainable decisions can't be properly appealed
</div>
<div>

![center width:550](https://images.unsplash.com/photo-1589391886645-d51941baf7fb?ixlib=rb-4.0.3&auto=format&fit=crop&w=1000&q=80)
</div>
</div>

> Algorithmic transparency is a matter of justice, not just technology

---

# The Trust Question
- **Can we ever fully trust an unexplainable model?**
- Arguments **FOR** trust:
    - Proven track record of accuracy
    - Human experts often can't explain their intuition
    - Performance matters more than process in some domains
- Arguments **AGAINST** trust:
    - Unexplained failures are inevitable
    - No way to verify reasoning is sound
    - Vulnerable to adversarial attacks
    - Ethical and legal requirements unmet

---

# XAI Limitations & Challenges
- **Technical challenges**:
    - Explanation accuracy vs. model accuracy trade-off
    - Computational complexity
    - "Explanation hacking" - models learning to game explanations

- **Human factors**:
    - Explanation comprehension varies
    - Risk of over-trusting explanations
    - "Illusion of understanding"
> Fundamental question: Are we explaining the model or our perception of the model?

---
# Best Practices for XAI Implementation
- **Match explanation to audience** (technical vs. non-technical)
- **Use multiple explanation methods** for cross-validation
- **Validate explanations** with domain experts
- **Document limitations** of both model and explanations
- **Consider the human-in-the-loop** workflow

> Remember: Explainability is a means to an end, not the end itself.

---
# Hands-On Exercise Plan
<div class="columns">
<div>

##### Part A: Tabular Data (30 mins)
- Load your pre-trained classifier
- Apply SHAP to generate global feature importance
- Analyze individual predictions with force plots
- Compare with LIME explanations
</div>
<div>

##### Part B: Image Data (30 mins)
- Use your CNN image classifier
- Generate saliency maps for test images
- Compare Grad-CAM with simple gradient methods
- Discuss what features the model actually learned
</div>
</div>






---
# Discussion Questions
- **In healthcare**, would you trust an AI diagnosis that's 95% accurate but unexplainable over a human doctor who's 90% accurate but can explain their reasoning?

- **What domains** absolutely require explainability vs. where might we accept black boxes?

- **Can good explanations** ever make up for lower model accuracy?

- **How do we balance** the trade-off between model complexity and explainability?

---

# Resources & Next Steps
- **Libraries to explore**:
    - `shap` - SHAP explanations
    - `lime` - LIME explanations
    - `interpret` - Microsoft's interpretability package
    - `captum` - PyTorch model interpretability

- **Further learning**:
    - ["Interpretable Machine Learning" by Christoph Molnar](https://christophm.github.io/interpretable-ml-book/)

---
# Key Takeaways
- **Explainability** is crucial for trust, safety, and ethics
- **Multiple techniques exist** for different data types and needs
- **No one-size-fits-all** - choose methods based on context
- **Explainability enables** responsible AI deployment
- **The field is evolving** - stay current with new developments