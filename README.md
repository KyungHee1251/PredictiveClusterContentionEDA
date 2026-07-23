### Predicting Cluster Outages and Resource Contention Across Heterogeneous Cloud Environments

KyungHee Kim

#### Executive summary
This project builds and evaluates machine learning models to predict workload failures and severe resource contention in large-scale cluster environments. Using [Google Borg telemetry data](https://www.kaggle.com/datasets/derrickmwiti/google-2019-cluster-sample) as the training baseline, a **Decision Tree model** achieved near-perfect in-domain performance (**0.999 accuracy and recall**). To test cross-environment robustness, the model was validated on the [Alibaba Cluster trace dataset](https://www.kaggle.com/datasets/rohitdurbha/alibaba-cluster-data). While domain shift introduced false positives, the model successfully maintained a 1.000 recall score, proving its viability as a conservative, safety-first alerting tool to prevent unhandled cluster outages.

#### Rationale
In large-scale distributed systems and cloud infrastructure, missing an actual workload failure leads directly to cluster outages, severe service degradation, and negative customer impact. Relying purely on reactive measures is insufficient; deploying proactive predictive models allows engineering teams to catch resource saturation and execution stalls early. However, designing these systems requires balancing high recall against alert fatigue caused by false alarms.

#### Research Question
Can a machine learning model trained on resource utilization and hardware-level telemetry from one cloud infrastructure (Google Borg) successfully generalize to predict workload contention and failures in an entirely different enterprise cluster environment (Alibaba Cluster Trace)?

#### Data Sources
* **Google Borg Cluster Data (2019):** Used for primary model training and in-domain testing, encompassing resource usage metrics, task metadata, start times, and hardware performance counters.
* **Alibaba Cluster Trace:** Used for cross-dataset validation and generalization testing, processed by merging resource usage logs with metadata tables and subsampling 200,000 rows.

#### Methodology
* **Data Preparation & Feature Alignment:** Processed raw telemetry, handled missing values, merged usage and metadata tables, and mapped native features across different cluster schemas.
* **Target Definition:** Established binary target variables representing failures or severe resource contention based on high resource saturation (CPU/Memory > 85%) and severe execution stalls (CPI > 3.0), aligning with standard SRE thresholds.
* **Model Training & Evaluation:** Trained and compared multiple classifiers (Logistic Regression, Decision Tree, Naïve Bayes, and Linear SVC). Prioritized recall to minimize false negatives and evaluated performance using confusion matrices, accuracy, precision, and F1-score.
* **Cross-Dataset Validation:** Applied the trained Google Borg model directly to the Alibaba dataset to evaluate cross-environment generalization and domain shift handling.

#### Results
* **Within-Dataset Performance:** The models evaluated on Google Borg data exhibited distinct performance profiles:
  * **Decision Tree:** Achieved near-perfect in-domain performance (**0.999 accuracy and 0.998 recall**) driven primarily by features such as ```page_cache_memory```, ```start_time```, and ```memory_accesses_per_instruction```.
  * **Logistic Regression & Linear SVC:** Both struggled with high false negatives, missing over 20,000 actual failure cases each, making them unviable for safety-critical outage detection.
  * **Naïve Bayes:** Captured more actual failures but generated an overwhelming number of false positives (33,883), resulting in severe alert fatigue.
* **Cross-Environment Generalization:** When tested on the Alibaba dataset without retraining, the model achieved a perfect **1.000 recall score**, capturing every instance of contention without missing critical failures. 
* **Operational Trade-offs:** While recall was flawless, the cross-application validation revealed high false positives (79,792), highlighting the impact of domain shift and the need for localized threshold calibration to mitigate alert fatigue.

#### Next steps
* **Domain Adaptation:** Implement transfer learning and domain adaptation techniques to adjust decision boundaries for new cluster topologies without starting from scratch.
* **Threshold Calibration:** Fine-tune decision thresholds using Alibaba-specific telemetry to reduce false positives and control operational alert fatigue.
* **Temporal Modeling:** Incorporate sliding-window temporal features to capture sequential degradation patterns over time rather than isolated snapshots.

#### Outline of project

- [Link to notebook: Data Exploration and Preprocessing](https://github.com/KyungHee1251/PredictiveClusterContentionEDA/blob/main/Capstone_Submission_EDA.ipynb)


##### Contact and Further Information
For questions or collaboration inquiries regarding distributed systems performance engineering and predictive observability, please reach out to kyunghee.kim.1251@gmail.com.
