## Metadata Extraction Methodology

The metadata ( tables, columns, measures and relationships) was extracted directly from the Power BI model using the
DAX functions `INFO.VIEW.TABLES()`, `INFO.VIEW.COLUMNS()`, and
`INFO.VIEW.RELATIONSHIPS()`, ensuring the documentation stays accurate to
the model's actual structure rather than being maintained by hand. The
queries used are available in `extract_schema.dax`.
INFO.VIEW is one of the powerful features and among the newer DAX functions introduced in Power BI in june 2026, enabling direct and seamless, in-model metadata extraction.
