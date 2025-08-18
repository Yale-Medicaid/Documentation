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

## Oracle Table Size

The script below reports the size of every table for a schema (e.g., `YALE` below). Among other limitations, this script does **not** report information for temporary tables, which should rarely be used.

```{sql}
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
