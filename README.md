# Abandoned_Cart_Audience_Segmentation
This project builds a practical framework for segmenting users who abandoned their shopping carts, applying MECE (Mutually Exclusive, Collectively Exhaustive) principles and producing prioritized audience segments for marketing or retention strategies.
Problem We’re Solving

E-commerce platforms often struggle to prioritize users who abandon carts. Not all abandoners are equal — some are high-value customers, some are engaged but low-value, and others may not be worth pursuing.
This project solves the problem by:
Defining a clear universe of users.
Creating MECE partitions that avoid overlap.
Enforcing size constraints to ensure actionable segments.
Computing multi-dimensional scores to rank segments.
Outputting an interpretable strategy file for marketers.

Dataset Fields
The dataset contains the following fields:
user_id
cart_abandoned_date
last_order_date
avg_order_value
sessions_last_30d
num_cart_items
engagement_score
profitability_score

Quickstart
Open the provided Google Colab notebook.
Run all cells in order:
Generates a synthetic dataset with required fields.
Defines the universe (users who abandoned carts in last 7 days).
Creates MECE segments using AOV, engagement, and profitability rules.
Applies constraints (min/max segment sizes).
Computes audience scores across multiple dimensions.
Outputs a strategy file in CSV/JSON format.
Produces plots (bar charts, scatterplots).

Logic & Rules Applied
1. Define the Universe
Users who abandoned carts in the last 7 days are selected as the target universe.

2. Create MECE Segments (Decision-Tree Inspired)
We partitioned users into fine-grained, non-overlapping groups:
High AOV Abandoners → avg_order_value > 3000
Mid AOV Engaged → 1000 < avg_order_value <= 3000 AND engagement_score > 0.5
High Profitability → profitability_score > 0.7
Other Bucket → All remaining users
This ensures every user falls into exactly one segment, without overlap.

3. Apply Constraints
Enforced segment size bounds: 500 ≤ size ≤ 20,000.
Segments below threshold were merged into Other Bucket.
Segments above threshold were also folded back into Other Bucket, ensuring practicality.

4. Compute Audience Scores
For each segment, we computed multiple dimensions:
Conversion Potential = engagement × recency proxy (via AOV ranking)
Lift vs Control = randomized uplift simulation
Size Normalized = segment size ÷ max size
Profitability = average profitability_score
Strategic Fit = mean of engagement and profitability

An overall score was computed as:
0.3 * Conversion Potential +
0.2 * Lift vs Control +
0.2 * Size Normalized +
0.3 * Strategic Fit

5. Output Strategy
Final outputs include a CSV/JSON table with:
Segment Name
Rules Applied
Size
Dimension Scores
Overall Score
Validity Flag (whether it respects constraints)

We also produced:
Barplot of Overall Scores by Segment
Scatterplot of Profitability vs Engagement (bubble size = segment size)

Example Output:
| Segment             | Rules Applied                        | Size   | Conv\_Pot | Profitability | Overall Score | Valid |
| ------------------- | ------------------------------------ | ------ | --------- | ------------- | ------------- | ----- |
| High AOV Abandoners | AOV > 3000                           | 3,200  | 0.72      | 0.81          | 0.76          | Yes   |
| Mid AOV Engaged     | 1000 < AOV ≤ 3000 & Engagement > 0.5 | 5,000  | 0.65      | 0.60          | 0.62          | Yes   |
| Other Bucket        | Else                                 | 12,000 | 0.50      | 0.55          | 0.52          | Yes   |


Why Partitions Were Chosen
AOV is a strong indicator of customer value.
Engagement captures likelihood to return and convert.
Profitability Score ensures focus on customers that drive margin.
“Other Bucket” ensures collective exhaustiveness.

How MECE Was Enforced
Explicit decision rules ensure no overlaps (mutually exclusive).
“Other Bucket” captures all remaining users (collectively exhaustive).
Constraints applied to rebalance segment sizes.

Limitations
Thresholds for AOV/engagement/profitability are heuristic; may need tuning.
Lift vs Control scores are simulated, not based on actual experiments.
Recency is only partially reflected (through cart_abandoned_date cutoff).
Real business context may require more nuanced rules (e.g., geography, device).

Potential Improvements
Replace simulated metrics with actual A/B test results.
Dynamic thresholds via quantiles instead of fixed cutoffs.
Use decision trees or clustering for automated segmentation.
Incorporate time-to-next-order prediction for stronger recency signals.
Expand scoring framework with marketer-driven weights.

