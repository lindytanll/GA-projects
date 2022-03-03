# ![] Project 1: Standardized Test Analysis


### Problem Statement

As an employee of the SAT College Board and part of a team to monitor statewide participation, this study aims to:
* Explore trends in the participation of SAT, as well as ACT, over years from 2017-2019; and
* Identify and provide recommendations on the states which we can focus our resources to improve SAT participation rates.

Target Audience: College Board (i.e. the organization that administers the SAT)..


---

### Datasets

|Feature|Type|Dataset|Description|
|---|---|---|---|
|state|object|ACT/SAT|The states in US (a total of 51 states).|
|year|datetime|ACT/SAT|The year which students took the ACT or SAT.|
|sat_participation|float|SAT|Percentage of students in the state who took the SAT.|
|act_participation|float|ACT|Percentage of students in the state who took the ACT.|
|sat_reading_writing|int|SAT|Average score for Reading, Writing and Language in SAT for the state. Reading and Writing sections are combined to provide a final Evidence-Based Reading and Writing (EBRW) score on a scale of 200-800.|
|sat_math|int|SAT|Average score for Math in SAT for the state. Math section is scored on a scale of 200-800.|
|sat_total|int|SAT|Average overall score for SAT for the state . Overall score combines the Reading/Writing and Math section and has score range of 400-1600, with 1600 being a perfect score.|
|act_english|float|ACT|Average scaled score for English section in ACT for the state. Scaled score range from 1-36, translated from raw score of 0-75.|
|act_math|float|ACT|Average scaled score for Math section in ACT for the state. Scaled score range from 1-36, translated from raw score of 0-60.|
|act_reading|float|ACT|Average scaled score for Reading section in ACT for the state. Scaled score range from 1-36, translated from raw score of 0-40.|
|act_science|float|ACT|Average scaled score for Science section in ACT for the state. Scaled score range from 1-36, translated from raw score of 0-40.|
|act_composite|float|ACT|Average overall score of the state for ACT. Overall composite score is a simple average of four area scores (i.e. English, Math, Reading and Science) with score range of 1-36.|
|exist|object|-|Indicator on whether the data is present from SAT or ACT dataset.|

					

---

### Background

* SAT and ACT are standardized tests that many colleges and universities in the United States require for their admissions process. This score is part of the assessment to determine whether or not a potential student will be accepted to the university.

* SAT and ACT are made mandatory for public school juniors in some states by their respective State Boards of Education, as the results can be used to determine statewide scholastic achievement ([*source*](https://testive.com/state-sat-act/)).

    - States that require the ACT:

        * Alabama
        * Hawaii
        * Idaho
        * Kentucky
        * Louisiana
        * Mississippi
        * Missouri
        * Montana
        * Nebraska
        * Nevada
        * North Carolina
        * North Dakota
        * Ohio
        * South Carolina
        * Tennessee
        * Utah
        * Wisconsin
        * Wyoming

    - States that require the SAT:

        * Colorado
        * Connecticut
        * Delaware
        * District of Columbia
        * Idaho
        * Illinois
        * Maine
        * Michigan
        * New Hampshire
        * Ohio
        * Tennessee

* Preference for one standardized test over another tends to be largely geographical in nature. For this reason, states with high SAT participation are likely to have much lower ACT participation, and vice versa ([*source*](https://blog.collegevine.com/here-are-the-average-sat-scores-by-state/)).


---

### Brief summary of analysis

* States with high SAT participation are those which made SAT mandatory while those with low participation are those which made ACT mandatory.

* States with low participation generally had high SAT score and high participation had low score. Similarly for ACT.
    - This is likely due to statewide requirements. When students do not self select and take test as part of a requirement and only option, their scores tend to be lower ([*source*](https://blog.collegevine.com/here-are-the-average-sat-scores-by-state/)). 
    - In contrast, students would voluntarily choose the test which they believe they would score higher on.

* States with 100% participation for SAT typically continue to have high participation thereafter, due to it being made mandatory. Similarly for ACT. 

    - For SAT:
        - Michigan had 100% participation since 2017. Colorado, Idaho, Illinois, and Rhode Island sustained close to 100% participation rate since 2018.
        - This is due to SAT being state-administered in these states (e.g. Illinois and Rhode Island made SAT mandatory in 2018, Colorado changed mandatory from ACT to SAT in 2017).

    - For ACT:
        - Amongst 19 states, 16 states had less than 10% year-to-year change in participation rate.
        - This is also due to ACT being state-administered in these states.

* Amongst states with >50% participation for both tests, Florida and Gerogia are currently not mandated to take either test. 
    - From 2017-2019, Hawaii, North Carolina and South Carolina, which are mandated to take ACT, had more than 80% participation for ACT and about 50-60% participation for SAT.
    - For Florida and Gerogia, the participation rates are comparable between SAT and ACT but inversely related (e.g. Georgia's participation increased from 60% to 70% for SAT while decreased from 55% to 50% for ACT from 2017-2019).

* Median participation rate for SAT was relatively low (~55%), as compared to ACT (~65%), suggesting that there is room for improvement.
    - There are more extreme participation for SAT (about one-third of the states have either 100% or close to 0% participation).
    - ACT is more widely adopted by states (about one-third of the states having 100% participation).

* Since it is difficult to increase SAT participation for states which have adopted ACT, and states which have adopted SAT are likely to continue having high participation, we excluded states with mandatory tests, and recommend focusing resources on the other states to improve SAT participation.

---

### Conclusions/Recommendations

* Excluding states with mandatory ACT and SAT, we recommend focusing resoures on the following states to improve SAT participation:
    * *Focus first*: States with relatively high SAT score, as compared to ACT score). As their relative overall score is higher for SAT, we can use this as a basis to propose to the Boards of Education in these states to make SAT mandatory in their states:
        - Arizona
        - Alaska
        - Oregon
        - Florida
    * *Focus next*:States with relatively high participation rate, as compared to ACT participation:
        - Vermont
        - Virginia
        - Massachusetts
        - New Jersey
        - Pennsylvania
