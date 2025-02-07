

# UEH Score Overview Of Graduating Student
Report Link: https://app.fabric.microsoft.com/groups/me/reports/5819b528-96e8-41d6-9c6c-53179c2f94b1/ReportSection?experience=power-bi

![Image](https://github.com/user-attachments/assets/9a36c9c8-73d4-406c-9a34-85adc5688d60)

This is my personal project aimed at analyzing the graduation scores of students from the UEH university

## Project Objectives:

- To compare the correlation between the two graduation periods in 2024
- To evaluate key factors such as the number of students graduating with distinction or excellence, average scores, highest and lowest scores across each graduation period
- To analyze the number of graduates by major
  
These analyses aim to address questions such as:

- Are there significant differences in academic performance between the two graduation periods?
- Which majors consistently produce high-achieving graduates, and - which may need further academic support?

## Project Detail 
The data for this project was obtained from the official publication platform of the University of Economics Ho Chi Minh City (UEH). Specifically, it comprises two PDF files:

<sup>ueh_graduating_score_overview_Dot3_2024.pdf</sup> 

<sup>ueh_graduating_score_overview_Dot4_2024.pdf</sup>




These files contain comprehensive information about the graduation scores of students for the third and fourth graduation periods of 2024, including data on the number of graduates, their academic performance, and their majors

Data Processing Flow: To ensure the data was ready for analysis, the following steps were carried out:
1. Extracting Data from PDFs:
- The two PDF files were read into Python using libraries such as tabula
- Relevant tables containing graduation data were extracted from the PDFs
- The extracted tables were reviewed for completeness and consistency to ensure data integrity
2. Adding labeling columns:
- Added a new column to label each record with the corresponding graduation period (Đợt xét tốt nghiệp).
3. Exporting Processed Data to CSV:
- The cleaned and consolidated DataFrame was exported as a CSV file to enable further analysis
- UTF-8 encoding was applied to ensure compatibility with visualization tools
4. Loading Data into Power BI:
- The exported CSV file was imported into Power BI
- Addressed data inconsistencies, such as missing values, duplicate rows, and formatting issues
- Additional transformations, such as creating calculated columns, defining measures, and applying filters, were performed within Power BI
- The processed data was used to build visualizations that addressed the project’s objectives, such as performance comparisons and trends
- Transformed the data into a structured and standardized format, ensuring proper column headers and consistent data types
5. Visualizing Data and Building DAX Measures
- Developed interactive visualizations, including bar charts, line charts, and KPI indicators, to highlight key graduation trends
- Designed slicers and filters to allow users to drill down into specific graduation periods, departments, and student performance metrics
- Created DAX measures to enhance analytical capabilities, such as:
  
**dtb_sinh_vien_b47**: This DAX formula calculates the average graduation score (ĐTB TN) for students from cohorts earlier than the 47th. It uses CALCULATE to filter data in the fact_graduating_score_overview table, selecting only students from cohorts numbered less than 47, then computes the average ĐTB TN using AVERAGE. Finally, FORMAT is applied to display the result as a decimal number with two decimal places (0.00)
```None
dtb_sinh_vien_b47 = 
FORMAT(
    CALCULATE(
        AVERAGE(fact_graduating_score_overview[ĐTB TN]),
        fact_graduating_score_overview[Số khóa học] < 47
    ), "0.00"
)


```

**tb_sl_sinh_vien**: This DAX formula calculates the average number of students per major. It uses VALUES(dim_major[Ngành]) to retrieve a unique list of majors, then applies AVERAGEX to iterate over each major and compute the average student count using [số_lượng_sinh_viên]
```None
tb_sl_sinh_vien = 
AVERAGEX(
    VALUES(dim_major[Ngành]), 
    [số_lượng_sinh_viên]
)
```
**% sinh viên tốt nghiệp theo đợt**: This DAX formula calculates the percentage of students graduating in each period. It first counts the distinct combinations of student IDs (Mã SV) and majors (Mã ngành) in fact_graduating_score_overview to get the number of graduates for the current context. Then, it uses CALCULATE with ALLEXCEPT to remove all filters except for graduation_period, allowing it to count the total graduates across all periods. The DIVIDE function calculates the percentage by dividing the number of graduates in the current period by the total number of graduates, and multiplies by 100 to express it as a percentag
```None
% sinh viên tốt nghiệp theo đợt = 
DIVIDE(
    COUNTROWS(
        SUMMARIZE(
            fact_graduating_score_overview,
            fact_graduating_score_overview[Mã SV],
            fact_graduating_score_overview[Mã ngành]
        )
    ),
    CALCULATE(
        COUNTROWS(
            SUMMARIZE(
                fact_graduating_score_overview,
                fact_graduating_score_overview[Mã SV],
                fact_graduating_score_overview[Mã ngành]
            )
        ),
        ALLEXCEPT(fact_graduating_score_overview, dim_date[graduation_period])
    ),
    0
) * 100

```

**rank_dtb and rank_dtb_2**: The Score Rank DAX formula calculates rankings for students based on their scores, distinguishing between individual rankings within a major and rankings across all students. First, it defines a variable cn that holds all selected majors using ALLSELECTED(dim_major[Chuyên ngành]), ensuring that ranking calculations are scoped within the currently selected major

Next, it creates a temporary table hs using CALCULATETABLE and SUMMARIZECOLUMNS, which groups students by their major, student ID, and name. The VALUES(dim_major[Chuyên ngành]) function ensures that calculations are limited to the selected major, while ALLSELECTED(dim_student[Mã SV], dim_student[Họ và tên]) ensures that rankings only consider visible students

The formula then calculates two rankings: rnk_hs ranks students within their major using RANKX, sorting scores in descending order with dense ranking, ensuring no gaps in rank values. Similarly, rnk_cn ranks the entire major in the same manner

To determine the correct ranking to return, the formula checks the context. If the report is grouped by major and the major's rank falls within the maximum defined parameter, it returns rnk_cn. If the report is displaying individual student details, it returns rnk_hs. If neither condition is met, the formula returns blank

The second measure, Score Rank 2, ensures that scores are only displayed for students with a valid ranking. It checks if Score Rank is not blank, and if so, it returns the student’s score; otherwise, it returns blank. This prevents unnecessary values from appearing in the report, ensuring a cleaner and more relevant visualization

```None
rank_dtb = 
var cn = ALLSELECTED(dim_major[Chuyên ngành])
var hs = 
CALCULATETABLE(
    SUMMARIZECOLUMNS(
        dim_major[Chuyên ngành],
        dim_student[Mã SV], dim_student[Họ và tên]
    ),
    VALUES(dim_major[Chuyên ngành]),
    ALLSELECTED(dim_student[Mã SV], dim_student[Họ và tên])
)
var rnk_hs =
IF(not ISBLANK([score]), RANKX(hs, [score], , DESC, Dense))
var rnk_cn =
IF(not ISBLANK([score]), RANKX(cn, [score], , DESC, Dense))
RETURN
IF(
    ISINSCOPE(dim_major[Chuyên ngành]) && not ISINSCOPE(dim_student[Họ và tên]) && rnk_cn <= MAX(Parameter[Parameter]), rnk_cn, 
    IF(ISINSCOPE(dim_student[Họ và tên]), rnk_hs,
    BLANK()))
```

```None
rank_dtb_2 = 
IF([Score Rank] <> BLANK(), [score], BLANK())

```
### Related files: 
- Python file: [p1_data_processing](https://github.com/tiendinhquang2104/PowerBIProject/blob/main/UEHScoreOverviewOfGraduatingStudent/DataProcessing/p1_data_processing.ipynb)
- Power BI
