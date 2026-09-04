** A direct manuscript excerpt from the publication of the research that resulted in the LNVSI algorithm explaining the mathematical theory in full detail **

* Design and Deployment of the LNVSI Algorithm *

The LNVSI algorithm is a small, modular, and multivariate Sequential Analysis Pipeline (SAP) and classical machine learning (ML) algorithm written in Python. Microsoft Visual Studio Code’s intra-workspace GitHub Copilot (mostly with Anthropic’s Claude models) assisted with some debugging, frontend, documentation (namely as some sections of the repository README.md file) , and code cohesivity during development. No artificial intelligence was used for designing the mathematics, pipeline workflow, or conceptual aspects of the algorithm. The algorithm runs several Scikit-learn (sklearn) statistical tests across the UV-Vis-NIR spectrophotometer dataset for each Neogastropod venomic sample cuvette. These computations compare the experimental datasets against the known conotoxin positive control dataset. These tests are classical vector computation, regression, and dimensionality reduction-based ML implementations that serve to determine whether any of the analyte samples are quantitatively conotoxin-like. To that end, following the subtraction of the y-axis absorbance datapoint values of the negative control group from every corresponding datapoint along the x-axis in every relevant dataset, they compare such spectral resonance datasets of the analytes to the positive control dataset. These absorbance datasets are stored as one-dimensional vector arrays, and the comparison logic between the positive control and each analyte always occurs using tuples consisting of those two absorbance arrays paired along the same x-axis wavelength data.

However, before any sklearn ML logic can be called in the algorithm, the aforementioned arrays are preprocessed rigorously. The LNVSI algorithm runs, for each of them, Savitsky-Golay filtering with Bayesian-optimized polynomial orders and window sizes contextual to each smoothed datapoint. The Savitsky-Golay filtering logic also takes a hardcoded bias factor value of 1.2. This preprocessing is done for data normalization and the removal of dataset artifacts that could create errors during downstream ML computation, such as machine imprecision and noise. The smoothed datasets are then automatically saved to the user’s device in CSV format. To preface the ML pipeline, the algorithm uses the lmfit algorithm to deconvolve the dataset curves using the Voigt Model alongside a Nonlinear Least Squares regression (Newville et al., 2025). After spectral deconvolution, Dynamic Time Warping (DTW) is applied to align the peak-related data in each tuple to common positions along the shared x-axis. This alignment ensures that spectral resonance features are compared based on their prominence, rather than their exact location on the x-axis. By doing so, DTW improves the accuracy of feature comparison, focusing on the mathematical intensity and shape of the spectral features rather than their precise wavelength positions.

The actual sklearn computations consist of Pearson Correlation Coefficient (PCC) computation, Principal Component Analysis (PCA), an intelligent Partial Least Squares (PLS) regression, and Cosine Similarity Vector Analysis (CSVA). The sklearn.Preprocessing StandardScaler library is also used to further normalize the analyte and positive control tuples mid-pipeline before running PCA for better results accuracy. Once all ML logic has been run on the tuples, the sub-results from each step in the SAP are combined into a Venomic Composite Similarity Score (VCSS) between each analyte venomic peptidome and the positive control. This score is calculated by averaging six normalized metrics: cosine similarity, Pearson correlation, Inverse Euclidean Distance (IED), normalized AUC difference, normalized PCA component difference, and PLS regression score. The IED value for each array tuple is computed between the processed control and sample curves, then normalized as detailed below to ensure that smaller distances yield higher similarity.

Let:

P = positive control absorbance curve 
S  = sample absorbance curve
The subscript 2 refers to the Euclidean norm (also called the L2 norm)
$$( \frac{1}{1 + |P - S|_2} )$$ 

The full final formula for determining the VCSS is, with “clip” referring to a NumPy function that restricts a value to be never less than 0 and never greater than 1:
$$\text{VCSS} = 100 \times \text{clip}\left( \frac{1}{6} \sum_{i=1}^{6} m_i, 0, 1 \right)$$ 

Where:
* $$m_1 = \text{CSVA}(C, S)$$
    ** CSVA result between control (C) and sample (S) **
* $$m_2 = \frac{\text{PCC}(C, S) + 1}{2}$$
    ** PCC, normalized specifically to [0, 1] **
* $$m_3 = \frac{1}{1 + |C - S|_2}$$
    ** Inverse Euclidean distance (L2 norm) between control and sample **
* $$m_4 = 1 - \frac{|\text{AUC}(C) - \text{AUC}(S)|}{\text{AUC}(C)}$$
   ** Normalized difference in AUC **
* $$m_5 = 1 - |\text{PCA}_1(C) - \text{PCA}_1(S)|$$
    ** Difference in first PCA values represented as subscript 1, normalized. **
* $$m_6 = \max(\text{PLS}(C, S), 0)$$
    ** PLS regression score, clipped at zero **

The composite score quantifies the overall similarity between the sample and control spectra, with the Euclidean distance directly contributing as one of its six core metrics.

Next, the datasets are run through a custom permutation test function. Its logic first calculates the observed composite similarity score $$\text{VCSS}$$ between the control and sample curves, using six metrics, including Euclidean distance. The function is specifically designed for similarity significance testing, avoiding any possible issues that could arise from using a statistical significance test designed for testing for significant divergence patterns as opposed to convergence patterns in scientific data. Next, the sample curve is randomly permuted a large number of times, represented below using the variable $${\text{P}}$$ as $$n_\text{P}$$. For each permutation, $${\text{P}}$$, from the number of permutations, $$n_\text{P}$$, a new permutation similarity score, given by the variable $${\text{PSS}}$$, is computed using the same formula. The test then counts the number of permutations where $$\text{PSS}\geq \text{VCSS}$$. The final p-value for the tuple in question is finally determined as the fraction of such permutations, given by the equation below:

$$p = \frac{\text{count} + 1}{n_{\text{P}} + 1}$$

After p-values are obtained for each post-analysis tuple, False Discovery Rate (FDR) correction using the Benjamini-Hochberg Procedure (BHP) is applied to account for multiple comparisons. This BHP adjustment ensures that the expected proportion of false positives among the analytes deemed significant remains below the hardcoded threshold, based on the standard statistical threshold for statistical significance in science and mathematics of $$ p  \lt  0.05$$.

