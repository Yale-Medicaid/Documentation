# Coding Best Practices

## Respectful Engineering Practices

!!! warning
    Engineering in embedded environments should prioritize **respect** toward the external systems and our partners that manage them. Avoid intensive computing, clever work-arounds, and unfamiliar code as much as possible.

Our team offers flexible workflows based on individual preferences (e.g., Python or R for analysis). However, in embedded systems, we have the privilege&mdash;and responsibility&mdash;to work respectfully in external environments. "Respectful" coding practices typically involve foregoing some flexibility to be good partners. This includes...

1. **Avoid resource-intensive computation**
    - Avoid multi-hour queries without proper oversight and alignment.
    - Avoid creating large datasets (>500 MB) without proper oversight and alignment. An example for checking table size in Oracle is [available below](#oracle-table-size).
2. **Avoid implementing clever work-arounds**
    - Avoid overriding administrative or security constraints to run programs.
    - Avoid installing new software as much as possible.
    - Avoid command line processes needed to run a program.
3. **Avoid using unfamiliar code**
    - Avoid copy-pasting code from online (e.g., StackOverflow or ChatGPT).
    - Avoid running code from colleagues without proper instruction.
4. **Avoid cluttering shared resources**
    - Remove temporary datasets or outdated database tables.
    - Use the shared schema (e.g., `YALE`) rather than your user schema, which is harder to track and manage as a team.

While the list above may not be comprehensive, it should orient you to the mindset needed while programming within embedded systems. If&mdash;after adopting this mindset&mdash;any engineering activities give you pause, then **do not hesitate to reach out to research supervisors with questions.**

## Masking Small Cell Sizes

As discussed elsewhere, when working in embedded (administrative) systems, we must be cautious about sensitive datasets (PII/PHI). To protect the privacy and well-being of individuals in these systems, we need to avoid "re-identification risks" when reporting information, even once it's been aggregated. Specifically, we opt to **mask data when reporting info for groups with fewer than 11 individuals**. We present an example below:

=== "Before masking"
	
	Consider a geographic analysis at the town-level. When we initially pull the data, we find that some of the reporting categories have fewer than 11 data points (Pomfret).

	| Town | Members | Total Paid |
	| --- | --- | --- |
	| Hartford | 93 | \$87,913 |
	| New Haven | 127 | \$132,333 |
	| Pomfret | 9 | \$9,171 |
	| Windsor | 21 | \$23,930 |
	
=== "After masking"
	
	To avoid any risks in re-identifying members in Pomfret, we opt to mask the details related to this subgroup. While we can still mention that the town was considered (i.e., included in the "Town" column), we remove the details *for reporting purposes*. This data may remain in subsequent aggregate analyses.
	
	| Town | Members | Total Paid |
	| --- | --- | --- |
	| Hartford | 93 | \$87,913 |
	| New Haven | 127 | \$132,333 |
	| Pomfret | *NA* | *NA* |
	| Windsor | 21 | \$23,930 |

We provide some sample code to perform this masking process, either in R or Python. These functions can be repurposed, edited, and updated as seen fit to accommodate your project needs.

=== "R"
	
	``` r
	# When reporting aggregates, mask all numeric columns in a dataframe (df) based on a
	# count column (mask_based_on) that falls below a certain threshold (mask_threshold)
	# >>> Example usage: data |> mask_small_cells(mask_based_on = member_count_col) |> ...
	mask_small_cells <- function(df, mask_based_on, mask_threshold = 10) {
		dplyr::mutate(
			df,
			dplyr::across(dplyr::where(is.numeric),
			\(x) ifelse({{mask_based_on}} <= mask_threshold, NA, x))
		)
	}
	```

=== "Python"
	
	```python
	# [Requires pandas and numpy (np)]
	# When reporting aggregates, mask all numeric columns in a pandas dataframe (df) based on
	# a count column (mask_based_on) that falls below a certain threshold (mask_threshold)
	# >>> Example usage: masked_df = mask_small_cells(df, mask_based_on = member_count_col)
	def mask_small_cells(df, mask_based_on, mask_threshold = 10):
	    data = df.copy()
	    data.loc[
                data[mask_based_on] <= mask_threshold,
                data.select_dtypes(include=np.number).columns.tolist()
	    ] = np.nan
	    return(data)
	```

## Oracle Table Size

The script below reports the size of every table for a schema (e.g., `YALE` below). Among other limitations, this script does **not** report information for temporary tables, which should rarely be used.

```{ .sql }
SELECT
  ALL_TABLES.TABLE_NAME,
  SUM(USER_SEGMENTS.BYTES) / 1024 / 1024 AS MB
FROM
  ALL_TABLES
  INNER JOIN USER_SEGMENTS
    ON ALL_TABLES.TABLE_NAME = USER_SEGMENTS.SEGMENT_NAME
WHERE
  ALL_TABLES.OWNER = 'YALE'
GROUP BY
  ALL_TABLES.TABLE_NAME
ORDER BY
  ALL_TABLES.TABLE_NAME
;
```
