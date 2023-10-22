Business Intelligence Project
================

- [Business Intelligence Lab Submission
  Markdown](#business-intelligence-lab-submission-markdown)
- [Student Details](#student-details)
- [Step 1: Setup Chunk](#setup-chunk)
- [STEP 2 Loading the Student Performance
  Dataset](#loading-the-student-performance-dataset)
  - [STEP 3 Apply a Scale Data
    Transform}](#step-3-apply-a-scale-data-transform)
  - [STEP 4 Apply a Centre Data
    Transform](#step-4-apply-a-centre-data-transform)
  - [STEP 5. Apply a Standardize Data Transform
    —-](#step-5-apply-a-standardize-data-transform--)
  - [STEP 6. Apply a Normalize Data Transform
    —-](#step-6-apply-a-normalize-data-transform--)
  - [STEP 7. Apply a Box-Cox Power Transform
    —-](#step-7-apply-a-box-cox-power-transform--)
  - [STEP 8. Apply a Yeo-Johnson Power Transform
    —-](#step-8-apply-a-yeo-johnson-power-transform--)
  - [STEP 9.a. PCA Linear Algebra Transform for Dimensionality Reduction
    —-](#step-9a-pca-linear-algebra-transform-for-dimensionality-reduction--)
  - [STEP 9.b. PCA Linear Algebra Transform for Feature Extraction
    —-](#step-9b-pca-linear-algebra-transform-for-feature-extraction--)
  - [STEP 10. ICA Linear Algebra Transform for Dimensionality Reduction
    —-](#step-10-ica-linear-algebra-transform-for-dimensionality-reduction--)

# Business Intelligence Lab Submission Markdown

- [Student Details](#student-details)
- [Setup Chunk](#setup-chunk)
- [Loading the Student Performance
  Dataset](#loading-the-student-performance-dataset)
  - [Description of the Dataset](#description-of-the-dataset)
- [\<You can Provide Another Appropriate Title Here if you
  wish\>](#you-can-provide-another-appropriate-title-here-if-you-wish)
  - [\<You Can Have a Sub-Title Here if you
    wish\>](#you-can-have-a-sub-title-here-if-you-wish)
  - [\<You Can Have Another Sub-Title Here if you
    wish\>](#you-can-have-another-sub-title-here-if-you-wish)

# Student Details

<table style="width:99%;">
<colgroup>
<col style="width: 38%" />
<col style="width: 36%" />
<col style="width: 15%" />
<col style="width: 8%" />
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Student ID Numbers and Names of Group Members</strong></td>
<td><p>| 1. 134564 - A - Cynthia</p>
<p>| 2. 100230 - A - Richard</p>
<p>| 3. 134982 - A - Austin</p>
<p>| 4.</p>
<p>| 5.</p></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td></td>
<td><strong>GitHub Classroom Group Name</strong></td>
<td>Luminosity</td>
<td></td>
</tr>
<tr class="odd">
<td><strong>Course Code</strong></td>
<td>BBT4206</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Course Name</strong></td>
<td>Business Intelligence II</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td><strong>Program</strong></td>
<td>Bachelor of Business Information Technology</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td><strong>Semester Duration</strong></td>
<td>16<sup>th</sup> October 2023 to 28<sup>th</sup> November 2023 |</td>
<td></td>
<td></td>
</tr>
</tbody>
</table>

# Step 1: Setup Chunk

We start by installing all the required packages

``` r
# language server package
if (require("languageserver")) {
  require("languageserver")
} else {
  install.packages("languageserver", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# STEP 1. Install and Load the Required Packages 
## mlbench ----
if (require("mlbench")) {
  require("mlbench")
} else {
  install.packages("mlbench", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## readr ----
if (require("readr")) {
  require("readr")
} else {
  install.packages("readr", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## caret ----
if (require("caret")) {
  require("caret")
} else {
  install.packages("caret", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## e1071 ----
if (require("e1071")) {
  require("e1071")
} else {
  install.packages("e1071", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## factoextra ----
if (require("factoextra")) {
  require("factoextra")
} else {
  install.packages("factoextra", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## FactoMineR ----
if (require("FactoMineR")) {
  require("FactoMineR")
} else {
  install.packages("FactoMineR", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

------------------------------------------------------------------------

**Note:** the following “*KnitR*” options have been set as the defaults
in this markdown:  
`knitr::opts_chunk$set(echo = TRUE, warning = FALSE, eval = TRUE, collapse = FALSE, tidy.opts = list(width.cutoff = 80), tidy = TRUE)`.

More KnitR options are documented here
<https://bookdown.org/yihui/rmarkdown-cookbook/chunk-options.html> and
here <https://yihui.org/knitr/options/>.

``` r
knitr::opts_chunk$set(
    eval = TRUE,
    echo = TRUE,
    warning = FALSE,
    collapse = FALSE,
    tidy = TRUE
)
```

------------------------------------------------------------------------

**Note:** the following “*R Markdown*” options have been set as the
defaults in this markdown:

> output:
>
> github_document:  
> toc: yes  
> toc_depth: 4  
> fig_width: 6  
> fig_height: 4  
> df_print: default
>
> editor_options:  
> chunk_output_type: console

# STEP 2 Loading the Student Performance Dataset

``` r
library(readr)
student_data <- read_csv("data/BI1-BBIT4-1-StudentPerformanceDataset.csv", col_types =
                           readr::cols(
                             class_group =
                               readr::col_factor(levels = c("A", "B", "C")),
                             gender = readr::col_factor(levels = c("1", "0")),
                             YOB = readr::col_date(format = "%Y"),
                             regret_choosing_bi =
                               readr::col_factor(levels = c("1", "0")),
                             drop_bi_now =
                               readr::col_factor(levels = c("1", "0")),
                             motivator =
                               readr::col_factor(levels = c("1", "0")),
                             read_content_before_lecture =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             anticipate_test_questions =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             answer_rhetorical_questions =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             find_terms_I_do_not_know =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             copy_new_terms_in_reading_notebook =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             take_quizzes_and_use_results =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             reorganise_course_outline =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             write_down_important_points =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             space_out_revision =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             studying_in_study_group =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             schedule_appointments =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             goal_oriented =
                               readr::col_factor(levels =
                                                   c("1", "0")),
                             spaced_repetition =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4")),
                             testing_and_active_recall =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4")),
                             interleaving =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4")),
                             categorizing =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4")),
                             retrospective_timetable =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4")),
                             cornell_notes =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4")),
                             sq3r = readr::col_factor(levels =
                                                        c("1", "2", "3", "4")),
                             commute = readr::col_factor(levels =
                                                           c("1", "2",
                                                             "3", "4")),
                             study_time = readr::col_factor(levels =
                                                              c("1", "2",
                                                                "3", "4")),
                             repeats_since_Y1 = readr::col_integer(),
                             paid_tuition = readr::col_factor(levels =
                                                                c("0", "1")),
                             free_tuition = readr::col_factor(levels =
                                                                c("0", "1")),
                             extra_curricular = readr::col_factor(levels =
                                                                    c("0", "1")),
                             sports_extra_curricular =
                               readr::col_factor(levels = c("0", "1")),
                             exercise_per_week = readr::col_factor(levels =
                                                                     c("0", "1",
                                                                       "2",
                                                                       "3")),
                             meditate = readr::col_factor(levels =
                                                            c("0", "1",
                                                              "2", "3")),
                             pray = readr::col_factor(levels =
                                                        c("0", "1",
                                                          "2", "3")),
                             internet = readr::col_factor(levels =
                                                            c("0", "1")),
                             laptop = readr::col_factor(levels = c("0", "1")),
                             family_relationships =
                               readr::col_factor(levels =
                                                   c("1", "2", "3", "4", "5")),
                             friendships = readr::col_factor(levels =
                                                               c("1", "2", "3",
                                                                 "4", "5")),
                             romantic_relationships =
                               readr::col_factor(levels =
                                                   c("0", "1", "2", "3", "4")),
                             spiritual_wellnes =
                               readr::col_factor(levels = c("1", "2", "3",
                                                            "4", "5")),
                             financial_wellness =
                               readr::col_factor(levels = c("1", "2", "3",
                                                            "4", "5")),
                             health = readr::col_factor(levels = c("1", "2",
                                                                   "3", "4",
                                                                   "5")),
                             day_out = readr::col_factor(levels = c("0", "1",
                                                                    "2", "3")),
                             night_out = readr::col_factor(levels = c("0",
                                                                      "1", "2",
                                                                      "3")),
                             alcohol_or_narcotics =
                               readr::col_factor(levels = c("0", "1", "2", "3")),
                             mentor = readr::col_factor(levels = c("0", "1")),
                             mentor_meetings = readr::col_factor(levels =
                                                                   c("0", "1",
                                                                     "2", "3")),
                             `Attendance Waiver Granted: 1 = Yes, 0 = No` =
                               readr::col_factor(levels = c("0", "1")),
                             GRADE = readr::col_factor(levels =
                                                         c("A", "B", "C", "D",
                                                           "E"))),
                         locale = readr::locale())
View(student_data)
```

## STEP 3 Apply a Scale Data Transform}

``` r
# We use the "preProcess()" function in the caret package

# BEFORE
summary(student_data)

#getting all numerics
library(dplyr)

numerical_col <- student_data %>% 
  select_if(is.numeric)


#Repeats
student_data_repeats <- as.numeric(unlist(student_data[, 28]))
hist(student_data_repeats, main = names(student_data)[28])
model_of_the_transform <- preProcess(student_data, method = c("scale"))
print(student_data)
student_data_scale_transform <- predict(model_of_the_transform, student_data)

#avgs
student_data_avg_eval <- as.numeric(unlist(student_data[, 75]))
hist(student_data_avg_eval, main = names(student_data)[75])
model_of_the_transform <- preProcess(student_data, method = c("scale"))
student_data_scale_transform <- predict(model_of_the_transform, student_data)

student_data_avg_learning <- as.numeric(unlist(student_data[, 76]))
hist(student_data_avg_learning, main = names(student_data)[76])
model_of_the_transform <- preProcess(student_data, method = c("scale"))
student_data_scale_transform <- predict(model_of_the_transform, student_data)

student_data_avg_peda <- as.numeric(unlist(student_data[, 77]))
hist(student_data_avg_peda, main = names(student_data)[77])
model_of_the_transform <- preProcess(student_data, method = c("scale"))
student_data_scale_transform <- predict(model_of_the_transform, student_data)

#absenteism
student_data_abs <- as.numeric(unlist(student_data[, 96]))
hist(student_data_abs, main = names(student_data)[96], xlim = c(0,60))
model_of_the_transform <- preProcess(student_data, method = c("scale"))
student_data_scale_transform <- predict(model_of_the_transform, student_data)

#coursework
student_data_coursework <- as.numeric(unlist(student_data[, 97]))
hist(student_data_coursework, main = names(student_data)[97])
model_of_the_transform <- preProcess(student_data, method = c("scale"))
student_data_scale_transform <- predict(model_of_the_transform, student_data)

student_data_exam <- as.numeric(unlist(student_data[, 98]))
hist(student_data_exam, main = names(student_data)[98])
model_of_the_transform <- preProcess(student_data, method = c("scale"))
student_data_scale_transform <- predict(model_of_the_transform, student_data)


student_data_tot <- as.numeric(unlist(student_data[, 99]))
hist(student_data_tot, main = names(student_data)[99], xlim= c(0,100))
model_of_the_transform <- preProcess(student_data, method = c("scale"))
print(student_data)
student_data_scale_transform <- predict(model_of_the_transform, student_data)

#after
student_data_repeats <- as.numeric(unlist(student_data_scale_transform[, 28]))
hist(student_data_repeats, main = names(student_data_scale_transform)[28])

student_data_avg_eval <- as.numeric(unlist(student_data_scale_transform[, 75]))
hist(student_data_avg_eval, main = names(student_data_scale_transform)[75])

student_data_avg_learning <- as.numeric(unlist(student_data_scale_transform[, 76]))
hist(student_data_avg_learning, main = names(student_data_scale_transform)[76])

student_data_avg_peda <- as.numeric(unlist(student_data_scale_transform[, 77]))
hist(student_data_avg_peda, main = names(student_data_scale_transform)[77])

student_data_coursework <- as.numeric(unlist(student_data_scale_transform[, 97]))
hist(student_data_coursework, main = names(student_data_scale_transform)[97])

student_data_exam <- as.numeric(unlist(student_data_scale_transform[, 98]))
hist(student_data_exam, main = names(student_data_scale_transform)[98])

student_data_tot <- as.numeric(unlist(student_data_scale_transform[, 99]))
hist(student_data_tot, main = names(student_data_scale_transform)[99])
```

## STEP 4 Apply a Centre Data Transform

``` r
# The centre data transform calculates the mean of an attribute and subtracts
# it from each value.

# BEFORE
summary(student_data)
boxplot(student_data[, 1], main = names(student_data)[1])
boxplot(student_data[, 3], main = names(student_data)[3])
boxplot(student_data[, 7], main = names(student_data)[7])
boxplot(student_data[, 8], main = names(student_data)[8])
boxplot(student_data[, 9], main = names(student_data)[9])
boxplot(student_data[, 10], main = names(student_data)[10])
boxplot(student_data[, 11], main = names(student_data)[11])
boxplot(student_data[, 12], main = names(student_data)[12])
boxplot(student_data[, 13], main = names(student_data)[13])
boxplot(student_data[, 28], main = names(student_data)[28])
boxplot(student_data[, 75], main = names(student_data)[75])
boxplot(student_data[, 76], main = names(student_data)[76])
boxplot(student_data[, 77], main = names(student_data)[77])
boxplot(student_data[, 97], main = names(student_data)[97])
boxplot(student_data[, 98], main = names(student_data)[98])
boxplot(student_data[, 99], main = names(student_data)[99])
boxplot(student_data[, 10], main = names(student_data)[100])



model_of_the_transform <- preProcess(student_data, method = c("center"))
print(model_of_the_transform)
student_data_center_transform <- predict(model_of_the_transform, # nolint
                                           student_data)

# AFTER
summary(student_data_center_transform)
boxplot(student_data_center_transform[, 1], main = names(student_data_center_transform)[1])
boxplot(student_data_center_transform[, 3], main = names(student_data_center_transform)[3])
boxplot(student_data_center_transform[, 7], main = names(student_data_center_transform)[7])
boxplot(student_data_center_transform[, 8], main = names(student_data_center_transform)[8])
boxplot(student_data_center_transform[, 9], main = names(student_data_center_transform)[9])
boxplot(student_data_center_transform[, 10], main = names(student_data_center_transform)[10])
boxplot(student_data_center_transform[, 11], main = names(student_data_center_transform)[11])
boxplot(student_data_center_transform[, 12], main = names(student_data_center_transform)[12])
boxplot(student_data_center_transform[, 13], main = names(student_data_center_transform)[13])
boxplot(student_data_center_transform[, 28], main = names(student_data_center_transform)[28])
boxplot(student_data_center_transform[, 75], main = names(student_data_center_transform)[75])
boxplot(student_data_center_transform[, 76], main = names(student_data_center_transform)[76])
boxplot(student_data_center_transform[, 77], main = names(student_data_center_transform)[77])
boxplot(student_data_center_transform[, 97], main = names(student_data_center_transform)[97])
boxplot(student_data_center_transform[, 98], main = names(student_data_center_transform)[98])
boxplot(student_data_center_transform[, 99], main = names(student_data_center_transform)[99])
boxplot(student_data_center_transform[, 10], main = names(student_data_center_transform)[100])
```

## STEP 5. Apply a Standardize Data Transform —-

``` r
# Standardize Data Transform ----
## STEP 5. Apply a Standardize Data Transform ----

### The Standardize Basic Transform on the BI 2 ----
# BEFORE
summary(student_data)

sapply(student_data[, 96:99], function(x) sd(x, na.rm = TRUE))

model_of_the_transform <- preProcess(student_data, method = c("scale", "center"))
print(model_of_the_transform)
student_data_standardize_transform <- predict(model_of_the_transform, # nolint
                                                student_data)

# AFTER
summary(student_data_standardize_transform)
sapply(student_data_standardize_transform[, 96:99], function(x) sd(x, na.rm = TRUE))
```

## STEP 6. Apply a Normalize Data Transform —-

``` r
# Normalize Data Transform ----

## STEP 6. Apply a Normalize Data Transform ----
# Normalizing a dataset implies ensuring the numerical data are
# between [0, 1] (inclusive).


### The Normalize Transform on the BI dataset ----
summary(student_data)
model_of_the_transform <- preProcess(student_data, method = c("range"))
print(model_of_the_transform)
student_data_normalize_transform <- predict(model_of_the_transform, # nolint
                                              student_data)
summary(student_data_normalize_transform)
```

## STEP 7. Apply a Box-Cox Power Transform —-

``` r
## STEP 7. Apply a Box-Cox Power Transform ----
# BEFORE
summary(student_data)

#Calculate the skewness before the Box-Cox transform
sapply(student_data_standardize_transform[, 75:99],  skewness, type = 2)

#Plot a histogram to view the skewness before the Box-Cox transform
boxplot(student_data_standardize_transform[, 75], main = names(student_data_standardize_transform)[75])
boxplot(student_data_standardize_transform[, 76], main = names(student_data_standardize_transform)[76])
boxplot(student_data_standardize_transform[, 77], main = names(student_data_standardize_transform)[77])
boxplot(student_data_standardize_transform[, 78], main = names(student_data_standardize_transform)[78])
boxplot(student_data_standardize_transform[, 79], main = names(student_data_standardize_transform)[79])
boxplot(student_data_standardize_transform[, 80], main = names(student_data_standardize_transform)[80])
boxplot(student_data_standardize_transform[, 81], main = names(student_data_standardize_transform)[81])
boxplot(student_data_standardize_transform[, 82], main = names(student_data_standardize_transform)[82])
boxplot(student_data_standardize_transform[, 83], main = names(student_data_standardize_transform)[83])
boxplot(student_data_standardize_transform[, 84], main = names(student_data_standardize_transform)[84])
boxplot(student_data_standardize_transform[, 85], main = names(student_data_standardize_transform)[85])
boxplot(student_data_standardize_transform[, 86], main = names(student_data_standardize_transform)[86])
boxplot(student_data_standardize_transform[, 87], main = names(student_data_standardize_transform)[87])
boxplot(student_data_standardize_transform[, 88], main = names(student_data_standardize_transform)[88])
boxplot(student_data_standardize_transform[, 89], main = names(student_data_standardize_transform)[89])
boxplot(student_data_standardize_transform[, 90], main = names(student_data_standardize_transform)[90])
boxplot(student_data_standardize_transform[, 91], main = names(student_data_standardize_transform)[91])
boxplot(student_data_standardize_transform[, 92], main = names(student_data_standardize_transform)[92])
boxplot(student_data_standardize_transform[, 93], main = names(student_data_standardize_transform)[93])
boxplot(student_data_standardize_transform[, 94], main = names(student_data_standardize_transform)[94])
boxplot(student_data_standardize_transform[, 95], main = names(student_data_standardize_transform)[95])
boxplot(student_data_standardize_transform[, 96], main = names(student_data_standardize_transform)[96])
boxplot(student_data_standardize_transform[, 97], main = names(student_data_standardize_transform)[97])
boxplot(student_data_standardize_transform[, 98], main = names(student_data_standardize_transform)[98])
boxplot(student_data_standardize_transform[, 99], main = names(student_data_standardize_transform)[99])



model_of_the_transform <- preProcess(student_data_standardize_transform, method = c("BoxCox"))
print(model_of_the_transform)
student_data_box_cox_transform <- predict(model_of_the_transform, # nolint
                                            student_data_standardize_transform)

# AFTER
summary(student_data_box_cox_transform)

# Calculate the skewness after the Box-Cox transform
sapply(student_data_box_cox_transform[, 75:99],  skewness, type = 2)

#Plot a histogram to view the skewness after the Box-Cox transform
boxplot(student_data_box_cox_transform[, 75], main = names(student_data_box_cox_transform)[75])
boxplot(student_data_box_cox_transform[, 76], main = names(student_data_box_cox_transform)[76])
boxplot(student_data_box_cox_transform[, 77], main = names(student_data_box_cox_transform)[77])
boxplot(student_data_box_cox_transform[, 78], main = names(student_data_box_cox_transform)[78])
boxplot(student_data_box_cox_transform[, 79], main = names(student_data_box_cox_transform)[79])
boxplot(student_data_box_cox_transform[, 80], main = names(student_data_box_cox_transform)[80])
boxplot(student_data_box_cox_transform[, 81], main = names(student_data_box_cox_transform)[81])
boxplot(student_data_box_cox_transform[, 82], main = names(student_data_box_cox_transform)[82])
boxplot(student_data_box_cox_transform[, 83], main = names(student_data_box_cox_transform)[83])
boxplot(student_data_box_cox_transform[, 84], main = names(student_data_box_cox_transform)[84])
boxplot(student_data_box_cox_transform[, 85], main = names(student_data_box_cox_transform)[85])
boxplot(student_data_box_cox_transform[, 86], main = names(student_data_box_cox_transform)[86])
boxplot(student_data_box_cox_transform[, 87], main = names(student_data_box_cox_transform)[87])
boxplot(student_data_box_cox_transform[, 88], main = names(student_data_box_cox_transform)[88])
boxplot(student_data_box_cox_transform[, 89], main = names(student_data_box_cox_transform)[89])
boxplot(student_data_box_cox_transform[, 90], main = names(student_data_box_cox_transform)[90])
boxplot(student_data_box_cox_transform[, 91], main = names(student_data_box_cox_transform)[91])
boxplot(student_data_box_cox_transform[, 92], main = names(student_data_box_cox_transform)[92])
boxplot(student_data_box_cox_transform[, 93], main = names(student_data_box_cox_transform)[93])
boxplot(student_data_box_cox_transform[, 94], main = names(student_data_box_cox_transform)[94])
boxplot(student_data_box_cox_transform[, 95], main = names(student_data_box_cox_transform)[95])
boxplot(student_data_box_cox_transform[, 96], main = names(student_data_box_cox_transform)[96])
boxplot(student_data_box_cox_transform[, 97], main = names(student_data_box_cox_transform)[97])
boxplot(student_data_box_cox_transform[, 98], main = names(student_data_box_cox_transform)[98])
boxplot(student_data_box_cox_transform[, 99], main = names(student_data_box_cox_transform)[99])
```

## STEP 8. Apply a Yeo-Johnson Power Transform —-

``` r
# Yeo-Johnson Power Transform ----

## STEP 8. Apply a Yeo-Johnson Power Transform ----
# Similar to the Box-Cox transform, the Yeo-Johnson transform reduces the
# skewness by shifting the distribution of an attribute and making the
# attribute have a more Gaussian-like distribution. The difference is that the
# Yeo-Johnson transform can handle zero and negative values, unlike the Box-Cox
# transform.

# BEFORE
summary(student_data)
student_data_95 <- student_data[, -95]
student_data_numerics <- student_data_95[, 75:98]
student_data_numerics[is.na(student_data_numerics)] <- 0
View(student_data_numerics)

sapply(student_data_numerics,  skewness, type = 2)

# Plot a histogram to view the skewness before the Yeo-Johnson transform
student_data_numerics <- as.data.frame(lapply(student_data_numerics, as.numeric))

par(mfrow = c(1, 4))
for (i in 1:24) {
  if (is.numeric(student_data_numerics[, i])) {
    hist(student_data_numerics[, i], main = names(student_data_numerics)[i])
  }
}



model_of_the_transform <- preProcess(student_data_numerics, method = c("YeoJohnson"))
print(model_of_the_transform)
student_data_numerics_yeo_johnson_transform <- predict(model_of_the_transform, student_data_numerics) # nolint

#after
summary(student_data_numerics_yeo_johnson_transform)
# Calculate the skewness after the Yeo-Johnson transform
sapply(student_data_numerics_yeo_johnson_transform,  skewness, type = 2)

# Plot a histogram to view the skewness after the Yeo-Johnson transform
student_data_numerics_yeo_johnson_transform <- as.data.frame(lapply(student_data_numerics_yeo_johnson_transform, as.numeric))

par(mfrow = c(1, 4))
for (i in 1:24) {
  hist(student_data_numerics_yeo_johnson_transform[, i],
       main = names(student_data_numerics_yeo_johnson_transform)[i])
}
```

## STEP 9.a. PCA Linear Algebra Transform for Dimensionality Reduction —-

``` r
## STEP 9.a. PCA Linear Algebra Transform for Dimensionality Reduction ----
summary(student_data_numerics)

model_of_the_transform <- preProcess(student_data_numerics, method =
                                       c("scale", "center", "pca"))

print(model_of_the_transform)
student_data_numerics_pca_dr <- predict(model_of_the_transform, student_data_numerics)

summary(student_data_numerics_pca_dr)
```

## STEP 9.b. PCA Linear Algebra Transform for Feature Extraction —-

``` r
## STEP 9.b. PCA Linear Algebra Transform for Feature Extraction ----

# We use the `princomp()` function is used to perform PCA on a correlation
# matrix.

student_data_numerics_pca_fe <- princomp(cor(student_data_numerics))
summary(student_data_numerics_pca_fe_pca_fe)

#### Scree Plot ----
# The Scree Plot shows that the 1st 2 principal components can cumulatively
# explain 92.8% of the variance, i.e., 87.7% + 5.1% = 92.8%.
factoextra::fviz_eig(student_data_numerics_pca_fe, addlabels = TRUE)

# The loading values for each variable in the 1st 2 principal components are
# shown below:
student_data_numerics_pca_fe$loadings[, 1:2]

factoextra::fviz_cos2(student_data_numerics_pca_fe, choice = "var", axes = 1:2)


factoextra::fviz_pca_var(student_data_numerics_pca_fe, col.var = "cos2",
                         gradient.cols = c("red", "orange", "green"),
                         repel = TRUE)
```

## STEP 10. ICA Linear Algebra Transform for Dimensionality Reduction —-

``` r
## STEP 10. ICA Linear Algebra Transform for Dimensionality Reduction ----

# Independent Component Analysis (ICA) transforms the data to return only the
# independent components. The n.comp argument is required to specify the
# desired number of independent components. This also results in a list of
# attributes that are uncorrelated.

if (!is.element("fastICA", installed.packages()[, 1])) {
  install.packages("fastICA", dependencies = TRUE)
}
require("fastICA")

summary(student_data_numerics)

model_of_the_transform <- preProcess(student_data_numerics,
                                     method = c("scale", "center", "ica"),
                                     n.comp = 8)
print(model_of_the_transform)
student_data_numerics_ica_dr <- predict(model_of_the_transform, student_data_numerics)

summary(student_data_numerics_ica_dr)
```

**etc.** as per the lab submission requirements. Be neat and communicate
in a clear and logical manner.
