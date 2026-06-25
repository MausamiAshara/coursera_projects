<h1>Project Overview:</h1>

Subscription platforms thrive on customer retention. This project focuses on building a predictive framework that scores a subscriber's likelihood to cancel their service.
By identifying high-risk users proactively, data-driven customer success interventions can be precisely deployed to the correct audience partitions.
<h4>Dataset Profile:</h4>
<ul>
  <li>Training Set: 243,787 customer observations (70%)</li>
  <li>Testing Set: 104,480 customer observations (30%)</li>
  <li>Target Feature: Churn (Binary: 1 for churned, 0 for retained)</li>
  <li>Class Distribution Check: ->Retained (0): 199,605 (~81.9%)</li>
  <li>Churned (1): 44,182 (~18.1%)</li>
</ul>

<strong>Note:A distinct 4.5:1 class imbalance is present, requiring deliberate model calibration.</strong>

<h4>Key Technical Challenges Addressed</h4>
<h5>Eliminating Dimensional Mismatch (ValueError):</h5>
<p>A common issue during deployment is when structural feature encoding splits create a mismatch between data horizons (e.g., OneHotEncoder producing dynamic column counts on test variants).</br>
<h5>The Solution:</h5> Implemented a robust alignment sequence that merges training and testing matrices temporarily during feature transformation, converting text types via a deterministic LabelEncoder to guarantee identical column topology across execution environments.
</p>
<h5>Overcoming Majority-Class Bias:</h5>
<p>Standard classifiers focus purely on accuracy, often ignoring the minority class in skewed datasets.
<h5>The Solution:</h5> Configured the ensemble with a balanced class weighting penalty matrix, forcing individual branches to heavily penalize errors made on actual churners.</p>

<h4>Pipeline Architecture</h4>
The workflow is contained within a clean, single-execution framework:</br>
[Raw Ingestion (Train/Test)] ──> [Drop Unique Identifiers (CustomerID)]</br>
│</br>
[Tuned Random Forest Ensemble] <── [Unified Label Encoding Alignment]</br>
│</br>
├──> [Validation Stratified Split Evaluation] ──> ROC AUC: 0.7470</br>
└──> [Test Probabilities Export] ──> 'prediction_submission.csv'</br>

<h4>Feature Engineering & Optimization</h4>
<ul>
  <li><h5>Tracking Filters:</h5> High-cardinality sequence IDs (CustomerID) were stripped prior to structural evaluation to prevent over-fitting.</li>
  <li><h5>Feature Importance Insights:</h5> Tree branch evaluations highlighted AccountAge, AverageViewingDuration, ContentDownloadsPerMonth, and ViewingHoursPerWeek as the primary drivers of subscriber retention variance.</li>
</ul>
<h4>Model Hyperparameters</h4>
The pipeline leverages a deep RandomForestClassifier ensemble optimized to map highly non-linear decision boundaries:
<ul>
  <li>python</li>
  <li>model = RandomForestClassifier(</br>
  <b>n_estimators=200</b>,&emsp; &emsp; &emsp; 200 distinct tree estimators to reduce variance</br>
  <b>max_depth=15</b>, &emsp; &emsp; &emsp; &emsp; Capped to eliminate localized deep noise</br>
  <b>min_samples_leaf=10</b>,  &emsp; &nbsp; &nbsp; Regularization barrier to prevent overfitting</br>
  <b>class_weight='balanced'</b>, &nbsp; &nbsp; Counteracts the 4.5:1 target class skew</br>
  <b>random_state=42</b>, &emsp; &emsp; &emsp; Ensures reproducible splits</br>
  <b>n_jobs=-1</b> &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; Multi-threaded across all available CPU cores</br>
  )</li>
</ul>
<h4>Evaluation & Benchmarks:</h4>
The framework utilizes a local 80/20 Stratified Validation Split to ensure the validation slice perfectly mimics real-world class proportions.</br>
<strong>Validation ROC AUC Score Achieved: 0.7470</strong></br>
This foundational accuracy provides a solid baseline for downstream <b> <i>gradient-boosting experiments (LightGBM/XGBoost) </b> </i> or complex feature interaction layers.</br>

<h4>Project Directory Structure:</h4>
text</br>
├── ChurnPrediction.ipynb      &emsp; &emsp; Main development and execution notebook</br>
├── data_descriptions.csv      &emsp; &emsp; &nbsp; Meta-documentation for feature mapping</br>
├── train.csv                  &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;  Labeled subscription history</br>
├── test.csv                   &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &nbsp;  Unlabeled target subscription records</br>
└── prediction_submission.csv  &emsp; Final exported probability scores for evaluation</br>
