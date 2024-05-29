---
author: ['Seah Shao Xuan']
title: 'Do You Speak My Language? A Panel Data Study'
date: 2021-10-21
draft: false
cover: 
    image: "images/cover_language.jpg"
description: "Understanding the Impact of English Proficiency on Employment Outcomes"
summary: "Understanding the Impact of English Proficiency on Employment Outcomes"
tags: ["panel data", "regression analysis", "causal inference", "instrument variables", "2SLS", "confounders", "economics", "education"]
ShowToc: true
TocOpen: false
---

## Language as a Birth Endowment

Imagine navigating your entire education and career in a language that isn’t your first. For many in the United States, this is a daily reality. English reigns supreme in the business world, and mastering it can be a ticket to better job prospects and higher wages. But what happens when you struggle with the language?

Students who aren't fluent in English often find themselves lagging behind academically and socially. This isn't due to a lack of effort but rather the challenge of adjusting to a language-dominant environment that doesn't reflect their cultural roots. While White students rarely encounter this barrier, minority students frequently do, as their household languages may differ significantly from English.

The journey to English proficiency varies widely, influenced by factors such as learning opportunities and personal aptitude. Research frequently explores the link between English skills and future earnings for second-language speakers. However, these studies often face challenges due to unobserved variables that can skew the results.

In this exploration, I utilize the primary language spoken at home as a measure to better understand its impact on English proficiency, aiming to reduce biases related to learning motivation. Similar research conducted internationally has shown how language proficiency in the dominant language affects the wages of students who speak different first languages.

## Unpacking the Language-Proficiency Wage Link

Understanding the impact of language proficiency on earnings has been a focal point in economic research for years. Various studies have employed innovative methods to uncover this relationship, each offering unique insights.

### The Spanish Context

[Budria and Swedberg (2015)](https://econpapers.repec.org/bookchap/aecieed10/10-42.htm) tackled this question in Spain, examining how proficiency in Spanish influences immigrant earnings. They used important information: whether the immigrant arrived in Spain before the age of 10, whether they have children fluent in Spanish, and whether they plan to stay in Spain for the next five years. These features help isolate the effect of language skills from other factors that might influence earnings.

### Insights from Australia

[Chiswick and Miller (1995)](https://econpapers.repec.org/article/ucpjlabec/v_3a13_3ay_3a1995_3ai_3a2_3ap_3a246-88.htm) explored a similar terrain in Australia. They focused on determinants of English fluency, such as age at arrival and country of birth. Although they didn't provide a specific coefficient for the impact of language ability on wages, their regression analysis highlighted the importance of these variables in understanding language acquisition and its economic implications.

### The U.S. Experience

In the United States, [Bleakley and Chin (2004)](https://www.jstor.org/stable/3211642) delved into the earnings of immigrants from both English-speaking and non-English-speaking countries. They found that older immigrants struggled more with English, affecting their economic outcomes. By comparing young and adult immigrants, they showed how crucial language proficiency is for economic success, especially for those from non-English-speaking backgrounds.

### A New Perspective

While these studies offer valuable insights, they haven't yet explored one potentially powerful instrument: the primary language used by students at home. 

By focusing on the language spoken with family members, we can get a more accurate estimate of how English proficiency affects future wages, minimizing biases related to learning motivation and other unobserved factors.

## Causal Inference and Confounders

Understanding the true impact of English proficiency on employment outcomes is not just an academic exercise — it has profound real-world implications. 

Causal inference, the process of identifying whether one variable directly affects another, is crucial in this context. Without establishing causality, we risk basing policies and educational strategies on misleading correlations.

### The Challenge of Confounders

One of the primary challenges in making causal inferences is the presence of confounders. These are variables that influence both the independent variable (English proficiency) and the dependent variable (future wages and economic prosperity). 

For instance, a student’s socioeconomic status or intrinsic motivation might affect both their ability to learn English and their future earnings. If we don't account for these confounders, we might incorrectly attribute the effect of these other factors to English proficiency.

{{< image-grid 
    columns="1"
    srcs="images/confounders.png"
>}}

### A Solution: Instrumental Variables

Instrumental variables (IVs) provide a powerful method to address this issue. An IV is a variable that is correlated with the independent variable (English proficiency) but not directly with the dependent variable (future wages), except through its effect on the independent variable. 

{{< image-grid 
    columns="1"
    srcs="images/instrumental_variables.png"
>}}

In this case, it's useful to imagine that a student's English proficiency has several factors, of which some are motivation-dependent and others are motivation-independent.

By using whether English is spoken at home as a proxy, these independent components can be extracted, and therfore be used to predict future wages. This breaks the link, where any non-causal correlations are erased away.

By using IVs, researchers can isolate the variation in the independent variable that is not confounded by other factors, allowing for a clearer estimation of its causal effect.

You can find more about IVs [here](https://en.wikipedia.org/wiki/Instrumental_variables_estimation).

## Diving into the Data

Exploring the relationship between English proficiency and future earnings requires robust data. One excellent source is the National Education Longitudinal Study of 1988 [(NELS:88)](https://nces.ed.gov/surveys/nels88/). 

This dataset tracks a cohort of eighth graders starting in 1988 and follows their educational and career progress through several key years: 1990, 1992, 1994, and 2000.

### The Focus of the Analysis

When working with this dataset, it's important to zero in on relevant features. In this case, the focus is on students' language abilities and other significant determinants of future wages. 

The primary dependent variable is ```lwage```, which represents the logarithmic transformation of a student’s wage after graduation. This logarithmic transformation normalizes the data, making patterns and relationships more evident.

The key independent variable here is ```english_fluency```, a composite binary measure. It is set to 1 if a student reports that they understand, speak, read, and write English either "pretty well" or "very well." This variable helps distinguish between different levels of English proficiency.

### Ensuring Robustness

To ensure a thorough analysis, it's crucial to include a variety of control variables. These account for other factors that might influence future wages, ensuring that the analysis captures the true effect of English proficiency. 

As with any data-related project, the most important step is in visualising the nature of data that we're working with. Here are some simple summary statistics for reference:

| Variable              | Description                                 | Mean  | SD    | Min   | Max   |
|-----------------------|---------------------------------------------|-------|-------|-------|-------|
| ```lwage```           | log of student’s wage                       | 8.528 | 0.974 | 4.605 | 11.00 |
| ```english_fluency``` | binary; student reports “pretty well” understanding, speaking, reading, writing = 1 | 0.913 | 0.281 |       |       |
| ```lap```             | binary; student has ever attended lang asst progs = 1 | 0.205 | 0.404 |       |       |
| ```byhmlang```        | degree to which student speaks English at home | 2.199 | 0.715 | 1     | 4     |
| ```sex```             | binary; male = 1                   | 0.451 | 0.497 |       |       |
| ```experience```      | number of years worked in last job          | 3.002 | 2.158 | 0.333 | 13.58 |
| ```race_api```        | binary; asian or pacific islander = 1 | 0.317 | 0.465 |       |       |
| ```race_hispanic```   | binary; hispanic = 1               | 0.616 | 0.486 |       |       |
| ```race_black```      | binary; black = 1                  | 0.040 | 0.196 |       |       |
| ```byfaminc```        | categorical; based on family’s recent income |       |       | 1     | 15    |
| ```birthyr```         | categorical; based on birth year   |       |       | 72    | 75    |

The original NELS:88 dataset includes 12,144 students. For a more focused analysis, the sample was narrowed to 3,366 students from non-White ethnic backgrounds. This approach helps better understand the impact of English proficiency within minority groups. 

After accounting for missing data, the final sample size stands at approximately 1,100 students. While this reduction raises concerns about potential bias due to selective attrition, there is no substantial evidence to suggest significant impact on the findings.

It's also important to match the number of variables in the regression models to the sample size. This step helps prevent inflated standard errors and maintains the robustness of the results.

By leveraging this comprehensive dataset, we can get clearer insights into how English proficiency shapes economic opportunities, especially for students from diverse backgrounds. This understanding can in turn shape educational policies and targeted interventions to support those who face language barriers.

## Approaching the Problem

Understanding how English proficiency influences future earnings requires a robust methodology that accounts for potential biases and confounding factors. 

Here's the approach:

### Baseline OLS Regression

A simple Ordinary Least Squares (OLS) regression of ```lwage``` on ```english_fluency``` and ```lap``` (whether the student participated in a language assistance program) is sufficient to establish a starting point.

To address confounders, a second OLS regression can include student and family characteristics as control variables.

### Addressing Bias and Unobserved Characteristics

OLS estimates may be biased due to unobserved characteristics, such as student motivation, which can influence both English proficiency and future wages. 

For example, motivated students may recognize the importance of English proficiency for future income and thus could put more effort into improving their language skills, leading to a positive selection bias.

To control for this bias, we could use our handy instrument variables as a control. ```byhmlang``` is a good instrument candidate, and represents the degree of English used by students in their household. 

This instrument can hopefully help isolate the sole effect of English proficiency on wages. Additionally, family income, gender, race, experience, and birth year were included as exogenous control variables ($\mathbf{W}$).

### The Two Stage Least Squares Model

How can we incorporate our instruments into our analysis?

Two-stage least squares (2SLS) is a statistical technique that leverages instrumental variables to address potential biases in regression analysis. 

In the first stage, the instrumental variables are used to predict the endogenous variable of interest (in this case, English proficiency) while controlling for other exogenous variables. 

These new predicted values are then used in the second stage regression analysis as if they were observed values, effectively replacing the original endogenous variable. 

This approach allows researchers to isolate the variation in the endogenous variable that is not influenced by other unobserved factors, thus providing more accurate estimates of its causal effect on the outcome variable (in this case, future wages). 

By employing instrumental variables in this way, 2SLS helps mitigate issues related to endogeneity and unobserved heterogeneity, leading to more reliable and interpretable results.

$$ \text{english_fluency} = (\text{byhmlang}) \beta + \mathbf{W} \theta + \epsilon_1 $$
$$ \text{lwage} = \hat{(\text{english_fluency})} \mu + \mathbf{W} \gamma + \epsilon_2 $$

Notice that in the second equation, we use $\hat{\text{english_fluency}}$ instead of $\text{english_fluency}$, where any effect due to unobserved confounders are eliminated.

We can obtain $\hat{\text{english_fluency}}$ from

$$ \hat{\text{english_fluency}} = (\text{byhmlang}) \beta + \mathbf{W} \theta $$

### Ensuring Validity of the Instrument

For the instrument to be valid, two conditions must be satisfied: 

1. Relevance: ```byhmlang``` is likely relevant, meaning that the extent to which a student speaks English at home could influence their English proficiency.
2. Exogeneity: ```byhmlang``` could be exogenous, meaning that the ability to converse in English with family members might depend more on family language practices than on the student's motivation to learn English, if at all.

The degree of which we meet these conditions affects the validity of our instrument and the ability to generate more accurate estimates of the causal effect of English proficiency on future wages.

## Unveiling the Impact of English Proficiency on Future Wages

Any modern day statistical package or software can perform the 2SLS regression with no issues. Here's what I found:

{{< image-grid 
    columns="1"
    srcs="images/results.png"
>}}

### What Does This Mean?

In quick summary:

1. The baseline Ordinary Least Squares (OLS) regression estimates a 19.7% increase in future wages associated with fluency in English, although this coefficient is only significant at the 10% level. 
2. Additionally, participating in at least one year of language assistance in school improves future earnings by 14.7%, significant at the 5% level. 
3. When observed student and family characteristics were included in the OLS regression, the coefficient on English fluency rose to 33.8% and was significant at the 1% level. However, the effect of the language assistance program fell to 7.26%.

As a sanity check, the coefficients on the other control variables are consistent with existing literature, with positive coefficients on gender and work experience. 

From these results, it's clear that the estimate of 33.8% for English fluency is likely biased upwards due to positive selection bias from confounders such as student motivation.

### Relevancy Considerations

In the 2SLS regression, the instrument ```byhmlang``` was used to generate an estimate for English fluency. The results of the first stage regression suggest that ```byhmlang``` is relevant, with a *t*-statistic of 7.21 and a *p*-value of less than 0.001. Furthermore, the first stage *F*-statistic is 52.0, indicating that ```byhmlang``` is a strong instrument.

### Exogeneity Considerations

While it is difficult to determine the exact level of exogeneity of byhmlang, it is likely that there is at best only weak correlation between the instrument and future wages. The spoken language at home is often decided from birth and is also highly dependent on whether other members of family speak fluent English. 

It's very unlikely that someone would change these practices to improve their English proficiency, given that there are several other avenues to do so, such as opting for additional English lessons. 

On the contrary, possible sources of endogeniety could stem from Parents of students who understand the importance of English competency, and may elect to pressure their children to use English at home, concurrently involving themselves in the improvement of their children’s grades. 

Consequently, this could lead to a positively biased coefficient since an improvement in future wages could be attributed to parenting efforts, rather than one’s fluency in English.

Ultimately, it's extremely difficult to pinpoint any sources of influence based on the context.

### Comparison with OLS Estimates

The 2SLS regression with control variables estimates that the effect of English fluency generates a 9.8% increase in future wages, which is lower than that estimated in the OLS regressions. This is consistent with the predicted direction of selection bias in the OLS estimates due to omitted confounders as explained earlier. 

While the 2SLS estimates may not be fully unbiased, the evidence suggests a positive selection bias due to omitted variables in the OLS-estimated coefficient.

## The Nuances in Establishing Causality

In exploring the link between English proficiency and future earnings for non-White students, this study highlights the importance of careful analysis. While both standard regression techniques and more advanced instrumental variable approaches show a positive association between English skills and wages, the depth of this relationship varies.

Interestingly, while the initial findings using standard regression techniques suggest a substantial boost in wages associated with English proficiency, a closer examination using instrumental variables reveals a more tempered effect. 

This discrepancy sheds light on the potential biases inherent in traditional analysis methods, emphasizing the need for nuanced approaches when interpreting such relationships.

For policymakers and educators, these insights carry significant implications. When crafting language assistance programs aimed at supporting students with English language challenges, it's crucial to consider the nuanced nature of the language-wage relationship. 

By accounting for factors like student motivation and background, these programs can be tailored more effectively to meet the diverse needs of students.

Looking ahead, future research could delve deeper into understanding the mechanisms through which English proficiency shapes economic outcomes. 

Exploring aspects such as workplace dynamics, career progression, and social integration could provide further clarity on how language skills impact earnings potential.

Ultimately, by leveraging these insights and adopting more robust analytical methods, we can better equip students with the language skills they need to thrive and to promote a more equitable world.