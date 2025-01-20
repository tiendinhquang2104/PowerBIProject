
# UEH Score Overview Of Graduating Student
Report Link: https://app.fabric.microsoft.com/groups/me/reports/5819b528-96e8-41d6-9c6c-53179c2f94b1/ReportSection?experience=power-bi

![Image](https://github.com/user-attachments/assets/9a36c9c8-73d4-406c-9a34-85adc5688d60)

This is my personal project aimed at analyzing the graduation scores of students from the UEH university

## Project Objectives:

- To compare the correlation between the two graduation periods in 2024
- To evaluate key factors such as the number of students graduating with distinction or excellence, average scores, highest and lowest scores across each graduation period.
- To analyze the number of graduates by major
- To identify trends and patterns in student performance across different graduation periods and majors
- To uncover potential disparities or areas of improvement in academic performance
- To provide insights for university management to enhance student support programs and curriculum design.
These analyses aim to address questions such as:

- Are there significant differences in academic performance between the two graduation periods?
- Which majors consistently produce high-achieving graduates, and - which may need further academic support?
- How can the university optimize its resources to improve student outcomes and graduation rates?
## Project Detail 
The data for this project was obtained from the official publication platform of the University of Economics Ho Chi Minh City (UEH). Specifically, it comprises two PDF files:

<sup>ueh_graduating_score_overview_Dot3_2024.pdf</sup> 

<sup>ueh_graduating_score_overview_Dot4_2024.pdf</sup>




These files contain comprehensive information about the graduation scores of students for the third and fourth graduation periods of 2024, including data on the number of graduates, their academic performance, and their majors

Data Preprocessing:
To ensure the data was ready for analysis, the following steps were carried out:
1. Extracting Data from PDFs:
- The two PDF files were read into Python using libraries such as tabula.
- Relevant tables containing graduation data were extracted from the PDFs.
- The extracted tables were reviewed for completeness and consistency to ensure data integrity.
2. Cleaning and Structuring Data:
- Addressed data inconsistencies, such as missing values, duplicate rows, and formatting issues.
- Added a new column to label each record with the corresponding graduation period (Đợt xét tốt nghiệp).
- Transformed the data into a structured and standardized format, ensuring proper column headers and consistent data types.
3. Exporting Processed Data to CSV:
- The cleaned and consolidated DataFrame was exported as a CSV file to enable further analysis.
- UTF-8 encoding was applied to ensure compatibility with visualization tools.
4. Loading Data into Power BI:
- The exported CSV file was imported into Power BI.
- Additional transformations, such as creating calculated columns, defining measures, and applying filters, were performed within Power BI.
- The processed data was used to build visualizations that addressed the project’s objectives, such as performance comparisons and trends
