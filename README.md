# Manufacturing-Process-Control-using-SQL

## Project Overview

Manufacturing processes are like solving a puzzle—each part must fit together precisely to ensure quality and efficiency. This project focuses on improving how manufacturing teams monitor and control production using Statistical Process Control (SPC). SPC is a data-driven method that ensures processes remain stable, only requiring adjustments when data points fall outside acceptable limits.

To achieve this, we define control limits using:

- Upper Control Limit (UCL) – Maximum acceptable measurement.

- Lower Control Limit (LCL) – Minimum acceptable measurement.

By analyzing historical manufacturing data with SQL window functions and nested queries, we identify process variations and detect outliers that require corrective actions.

### Project Workflow

1. Data Analysis & Control Limit Calculation

- Extracted manufacturing part data.

- Calculated moving averages and standard deviations for process monitoring.

- Defined UCL and LCL to determine acceptable ranges.

2. Identifying Process Deviations

- Used SQL window functions to segment data by operator.

- Flagged products that fall outside control limits.

- Created alerts to indicate when adjustments are needed.

3. Ensuring Process Stability

- Reviewed flagged deviations to detect trends.

- Provided insights to improve quality control and reduce waste.

- Suggested process adjustments based on SPC results.

### Key SQL Query

```sql
SELECT
    b.*,
    CASE
        WHEN b.height NOT BETWEEN b.lcl AND b.ucl THEN TRUE
        ELSE FALSE
    END AS alert
FROM (
    SELECT
        a.*,
        a.avg_height + 3*a.stddev_height/SQRT(5) AS ucl,
        a.avg_height - 3*a.stddev_height/SQRT(5) AS lcl  
    FROM (
        SELECT
            operator,
            ROW_NUMBER() OVER w AS row_number,
            height,
            AVG(height) OVER w AS avg_height,
            STDDEV(height) OVER w AS stddev_height
        FROM manufacturing_parts
        WINDOW w AS (
            PARTITION BY operator
            ORDER BY item_no
            ROWS BETWEEN 4 PRECEDING AND CURRENT ROW
        )
    ) AS a
    WHERE a.row_number >= 5
) AS b;
```

### Technologies Used

SQL (PostgreSQL/MySQL) – Data querying & statistical analysis

SPC (Statistical Process Control) – Quality monitoring strategy

Power BI – Data visualization for process tracking


### Results & Business Impact

Proactive Quality Control: Detects potential issues before defects occur.

Reduced Waste & Rework: Ensures only high-quality products are produced.

Optimized Process Monitoring: Provides automated alerts for necessary adjustments.
