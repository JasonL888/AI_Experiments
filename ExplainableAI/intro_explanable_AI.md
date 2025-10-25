---
marp: true
author: Jason Lau
size: 16:9
theme: dracula
paginate: true
transition: fade
class: 
- lead
header: Introduction to eXpainable AI (XAI)
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
        width: 100%;
    }
    theader {
        font-size: 0.7em;
         text-align: center;       
    }
    tbody {
        font-size: 0.6em;
        text-align: left;
    }
    h1 {
        font-size: 1.3em;
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

# <br><br>Introduction to eXplainable AI (XAI)
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

- Both aim to make model behavior understandable, 
    - but differ in **focus** and **methods**
- Critical for trust, debugging, regulatory compliance, and responsible AI

---
# Interpretability

- **Definition:** Directly understanding how a model transforms inputs into outputs
- **Intrinsic:** Model is transparent by design (e.g., linear models, decision trees)
- **Types:**
  - **Global:** Entire model logic
  - **Local:** Specific prediction path

---
# Explainability

- **Definition:** Understanding why a model made a specific decision
- **Post hoc:** Requires external tools (e.g., SHAP, LIME, saliency maps)
- **Useful for:** Complex or "black-box" models (deep neural networks)
- **Primarily local** (explains single predictions)

---
# Key Differences

| Aspect            | Interpretability           | Explainability             |
|-------------------|---------------------------|----------------------------|
| Focus             | Model logic (“**how**”)        | Prediction reason (“**why**”)  |
| Method            | Intrinsic                  | Post hoc                   |
| Typical Models    | Simple, transparent        | Complex, opaque            |
| Granularity       | Global & Local             | Mainly Local               |
| Goal              | Technical transparency     | User-facing justifications |

---

# Common Explainable AI (XAI) Techniques

|Technique|Method|Key Features|Use Case
|-------------|------|------------|--------
|Intrinsically Interpretable Models|Linear, Logistic Regression|Transparent coefficients, feature impact|Regression, binary classication
|             |Decision Trees & Rule-based Systems|Traceable rules and branches|Simple tree models, rule learners
|Post-hoc Explanation Methods|LIME| Local surrogate method for specific predictions|Explaining individual predictions
|                            |SHAP|Shapley value-based feature importance|Global and local feature attribution
|Visualization-Based Techniques|Grad-CAM|Image region heatmaps from deep models|Analyzing CNN image decisions
|                            |Saliency Maps|Input pertubations for visual explanations|Vision, model debugging


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
```
</div>
</div>

---
# SHAP: SHapley Additive exPlanations
<div class="columns">
<div>

- **Game theory approach**: 
    - Fairly distributes "credit" among features to outcome prediction
- **Key properties**:
    - **Local accuracy**: Explanation matches model output
    - **Missingness**: Features with no effect get zero importance
    - **Consistency**: Better features get more credit
</div>
<div>


```python
import shap

# Load your pre-trained model
# model = your_trained_classifier

# Create explainer
explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_test)

# Visualize feature importance
shap.summary_plot(shap_values, X_test)                 
```
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
# Saliency Maps for Images
```python
from tf_keras_vis.saliency import Saliency
# We use the built-in Saliency class.
from tf_keras_vis.utils.scores import CategoricalScore
# We also define a score function to tell the library which class prediction to optimize for (in our case, the predicted class).
if predicted_class_idx is not None:
    # 1. Define the score function (the class to maximize/get gradient for)
    score = CategoricalScore([predicted_class_idx])

    # 2. Create the Saliency object
    # The 'clone=True' ensures the model is safely copied for gradient computation
    saliency = Saliency(model, model_modifier=None, clone=True)

    # 3. Generate the saliency map
    # The result has shape (1, 224, 224, 3). We only need the first batch item and max over channels.
    saliency_map_vanilla = saliency(score, test_img_tensor) 
                           
```

---
# Demo
- [Hands-on Part A - SHAP and LIME - python notebook](https://githubtocolab.com/JasonL888/AI_Experiments/blob/main/ExplainableAI/hands_on_part_A_SHAP_LIME.ipynb)
- [Hands-on Part B - Saliency Map and Grad-CAM - python notebook](https://githubtocolab.com/JasonL888/AI_Experiments/blob/main/ExplainableAI/hands_on_part_B_Saliency_Grad-CAM.ipynb))


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
# Discussion Questions
- **In healthcare**, would you trust an AI diagnosis that's 95% accurate but unexplainable over a human doctor who's 90% accurate but can explain their reasoning?

- **What domains** absolutely require explainability vs. where might we accept black boxes?

- **Can good explanations** ever make up for lower model accuracy?

<!--
Healthcare 
- 95% accurate but unexplainable AI might outperform a 90% accurate human diagnostician on paper
- patients and healthcare systems generally favor transparent, interpretable AI–human collaboration over opaque automation.

Domains
- explainability is mandatory wherever human rights, safety, or fairness are at stake (eg. healthcare, justice/law, HR recruitment, autonomous driving)
- optional where performance gains outweigh interpretive clarity and the impact of incorrect or opaque decisions is minimal (eg. entertainment & recommendations, industrial optimiation - eg. production scheduling, network optimization)

Good explanations vs lower model accuracy
- yes 
    - only in context where human trust, accountability or safety-critical decisions are more important than marginal gains in predictive performance
        - eg. healthcare, finance or product cost estimation
    - high-volume, low-risk user-facing system (eg. youtube/shopping recommendation) accuracy typically dominates as it affects performance outcome, users are less concern with understanding model

--->

---

# Resources & Next Steps
- **Libraries to explore**:
    - `shap` - [SHAP docs](https://shap.readthedocs.io/en/latest/index.html)
    - `lime` - [lime github](https://github.com/marcotcr/lime)

- **Further learning**:
    - ["Interpretable Machine Learning" by Christoph Molnar](https://christophm.github.io/interpretable-ml-book/)

---
# Key Takeaways
- **Explainability** is crucial for trust, safety, and ethics
- **Multiple techniques exist** for different data types and needs
- **No one-size-fits-all** - choose methods based on context
- **Explainability enables** responsible AI deployment
- **The field is evolving** - stay current with new developments