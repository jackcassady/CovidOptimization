# CovidOptimization
A project to maximize the flow of Covid-19 patients through treatment while minimizing costs.

### CS/ECE/ISyE 524 &mdash; Introduction to Optimization &mdash; Summer 2020 ###

#  Saving the World: Global Pandemic Edition #

#### Rashelle Pershman (rpershman@wisc.edu), Jack Cassady (jacassady@wisc.edu), and Mackenzie Dahlquist (mjdahlquist@wisc.edu)

### Table of Contents

1. [Introduction](#1.-Introduction)
1. [Mathematical Model](#2.-Mathematical-model)
    1. [Minimum Cost Objective](#2.A.-Minimum-Cost-Objective)
        1. [Variables](#2.A.i.-Variable-Definitions)
        1. [Expressions](#2.A.ii.-Expressions-Used)
        1. [Other Variables](#2.A.iii.-Other-Variables-Used)
        1. [Model](#2.A.iv.-Minimum-Cost-Model)
    1. [Max Flow Objective](#2.B.-Max-Flow-Objective)
        1. [Variables](#2.B.i.-Variable-Definitions)
        1. [Others](#2.B.ii.-Other-Definitions)
        1. [Model](#2.B.iii.-Maximum-Flow-Model)
1. [Solution](#3.-Solution)
    1. [Set Up](#3.A.-Set-Up)
    1. [Inventories](#3.A.i.-Beginning-Inventories)
    1. [Patient Data](#3.A.ii.-Setting-Up-Patient-Data)
    1. [Code](#3.B.-Solving)
1. [Results and Discussion](#4.-Results-and-discussion)
    1. [Max Flow](#4.A.-Max-Flow-Results)
    1. [Min Cost](#4.B.-Min-Cost-Results)
1. [Conclusion](#5.-Conclusion)
1. [References](#6.-References)

## 1. Introduction ##

As of March 2020, the World Health Organization declared a mysterious Wuhan epidemic to be an international pandemic. Borders have closed, travel is limited, and most of the public in America has been given a “stay-at-home” order at least once. Many citizens feel that life as they know it is forever changed. The virus, known as Coronavirus or Covid-19, can cause fever, coughing, sore throat, shortness of breath, severe fatigue, and a plethora of other symptoms. Those at highest risk tend to be immunocompromised, such as individuals with diabetes, asthma, liver disease, and more.  

During this pandemic, hospitals have been overflowing with Coronavirus patients, and many are running out of supplies and/or room. Unrelated surgeries and appointments are being postponed, and hospital staff are feeling overwhelmed, sometimes to the point of mental breakdown.  

Because this virus is so new, there aren’t any prepared regulations and processes to optimize treating these patients. To alleviate the stress and chaos of current events within hospitals, as well as give higher-level treatment and minimize casualties, our team has developed a surface-level program to determine the supplies and staff necessary for the average amount of patients in hospitals in the south central region of Wisconsin, depending on the severity of the patients’ health.  

The problem data is pseudo randomly generated by code based on the total supplies and staff in hospitals in the south central region of Wisconsin (Wisconsin Department of Health Services). More severe virus patients will require more care and resources than those with mild symptoms. Logically, treating severe patients first would serve this goal. A multiperiod planning linear program with an objective of minimizing cost is used to display the constants, variables, etc. necessary for the overall problem, along with the levels of severity a patient can have, which has been simplified to four levels: no Coronavirus symptoms/symptoms not requiring hospitalization, mild symptoms that require fluids and monitoring the patient in the hospital, moderate symptoms that require more attention and a ventilator, and severe symptoms that require a ventilator and has additional life-threatening complications. Afterwards, a max flow linear program is used to see the capacity of each treatment path possible. The overall model takes into account the following assumptions:

* CDC data on hospitalization applies to the south central region of Wisconsin.
* The probability that a Covid patient requires mechanical ventilation in all of Wisconsin is the same for the south central region of Wisconsin.
* There are no deaths.
* No patient discharges from the hospital besides severity 1 patients.
* No patient changes severity during the observed week.

Our network flow for our patient treatment can be seen in the image below:

![Network Flow](network_flow.png "Logo Title Text 1")

## 2. Mathematical model ##

Building our math model, we wanted to be able to find the maximum number of Covid-19 patients that our hospital could treat. As well as minimizing the cost to treat these patients. As a result we have two math models that can be represented as such:

## 2.A. Minimum Cost Objective ##

**Type of Model:** Multiperiod Planning Linear Program

### 2.A.i. Variable Definitions ###

Variable | Definition
--- | ---
$Gown_t$ | Number of patient gowns in inventory on day t  
$Gown\_Order_t$ | Number of patient gowns ordered on day t  
$Staff\_Gown_t$ |  Number of staff gowns in inventory on day t  
$Staff\_Gown\_Order_t$ | Number of staff gowns ordered on day t  
$Glove_t$ | Number of gloves in inventory on day t  
$Glove\_Order_t$ | Number of gloves ordered on day t  
$N95\_Mask_t$ | Number of N95 masks in inventory on day t  
$N95\_Mask\_Order_t$ | Number of N95 masks ordered on day t  
$Surg\_Mask_t$ | Number of surgical masks in inventory on day t  
$Surg\_Mask\_Order_t$ | Number of surgical masks ordered on day t  
$Shield_t$ | Number of face shields in inventory on day t  
$Shield\_Order_t$ | Number of face shields ordered on day t  
$Vent_t$ | Number of ventilators in inventory on day t  
$Vent\_Order_t$ | Number of ventilators ordered on day t  
$Fluid_t$ | Milliliters of fluid in inventory on day t  
$Fluid\_Order_t$ | Milliliters of fluid ordered on day t  
$N_t$ | Number of Nurses working on day t  
$D_t$ | Number of Doctors working on day t  
$N\_On_t$ | Number of Nurses called in on day t  
$D\_On_t$ | Number of Doctors called in on day t  
$N\_Off_t$ | Number of Nurses called off on day t  
$D\_Off_t$ | Number of Doctors called off on day t  

### 2.A.ii. Expressions Used ###

Expression | Definition
--- | ---
$Staff_t$ | Total staff on day t
$Tot\_Pat_t$ | Total number of patients seen on day t
$Adm\_Pat_t$ | Number of admitted patients seen on day t
$Gown\_Use_t$ | Number of patient gowns used on day t
$Staff\_Gown\_Use_t$ | Number of staff gowns used on day t
$Glove\_Use_t$ | Number of pairs of gloves used on day t
$N95\_Mask\_Use_t$ | Number of N95 masks used on day t
$Surg\_Mask\_Use_t$ | Number of surgical masks used on day t
$Shield\_Use_t$ | Number of face shields used on day t
$Fluid\_Use_t$ | Milliliters of fluid used on day t
$Vent\_Use_t$ | Number of ventilators used on day t

### 2.A.iii. Other Variables Used ###

Variable | Definition
--- | ---
$Pat\_4_t$ | Number of Severity 4 patients
$Pat\_3_t$ | Number of Severity 3 patients
$Pat\_2_t$ | Number of Severity 2 patients
$Pat\_1_t$ | Number of Severity 1 patients


### 2.A.iv. Minimum Cost Model ###
$$\begin{aligned} \underset{ \mathbb{}}{\text{minimize}}\qquad& \sum_{t \in days} 393.69*D_t \space + 180.47*N_t + 6.00*N95\_Mask\_Use_t + 0.50*Surg\_Mask\_Use_t + 25,000.00*Vent\_Use_t \\ & + 0.25*Glove\_Use_t + 4.15*Gown\_Use_t + 5.00*Shield\_Use_t + 5.00*Fluid\_Use_t + 2.00*Staff\_Gown\_Use_t\\ \text{subject to:}\qquad& Pat\_4_t \le 14D_t, \forall \space t \in days\\ & Pat\_3_t \le 14D_t, \forall \space t \in days\\ & Pat\_2_t \le 20D_t, \forall \space t \in days\\ & Pat\_4_t \le 2N_t, \forall \space t \in days\\ & Pat\_3_t \le 2N_t, \forall \space t \in days\\ & Pat\_2_t \le 4N_t, \forall \space t \in days\\ & Pat\_1_t \le 4N_t, \forall \space t \in days\\ & D_t = D_{t-1} + On\_D_t - Off\_D_t, \forall \space t \in days\\ & N_t = N_{t-1} + On\_N_t - Off\_N_t, \forall \space t \in days\\ \\ & Staff_t = D_t + N_t, \forall \space t \in days\\ & Tot\_Pat_t = Pat\_4_t + Pat\_3_t + Pat\_2_t + Pat\_1_t, \forall \space t \in days\\ & Adm\_Pat_t = Pat\_4_t + Pat\_3_t + Pat\_2_t, \forall \space t \in days\\ \\ & Adm\_Pat_t \le 102, \forall \space t \in days\\ & Gown\_Use_t = 20*Adm\_Pat_t, \forall \space t \in days\\ & Staff\_Gown\_Use_t = 20*Tot\_Pat_t, \forall \space t \in days\\ & Glove\_Use_t = 250*Tot\_Pat_t, \forall \space t \in days\\ & N95\_Mask\_Use_t = 6*Tot\_Pat_t, \forall \space t \in days\\ & Surg\_Mask\_Use_t = 10*Tot\_Pat_t, \forall \space t \in days\\ & Shield\_Use_t = Staff_t, \forall \space t \in days\\ & Fluid\_Use_t = 2,179*Adm\_Pat_t, \forall \space t \in days\\ & Vent\_Use_t = Pat\_4_t + Pat\_3_t, \forall \space t \in days\\ \\ & Gown_{t-1} + Gown\_Order_t = Gown\_Use_t + Gown_t, \forall \space t \in days\\ & Staff\_Gown_{t-1} + Staff\_Gown\_Order_t = Staff\_Gown\_Use_t + Staff\_Gown_t, \forall \space t \in days\\ & Glove_{t-1} + Glove\_Order_t = Glove\_Use_t + Glove_t, \forall \space t \in days\\ & N95\_Mask_{t-1} + N95\_Mask\_Order_t = N95\_Mask\_Use_t + N95\_Mask_t, \forall \space t \in days\\ & Surg\_Mask_{t-1} + Surg\_Mask\_Order_t = Surg\_Mask\_Use_t + Surg\_Mask_t, \forall \space t \in days\\ & Shield_{t-1} + Shield\_Order_t = Shield\_Use_t + Shield_t, \forall \space t \in days\\ & Fluid_{t-1} + Fluid\_Order_t = Fluid\_Use_t + Fluid_t, \forall \space t \in days\\ & Vent_{t-1} + Vent\_Order_t = Vent\_Use_t + Vent_t, \forall \space t \in days\\ \\& Gown_0 = 3,120\\ & Staff\_Gown_0 = 3,120\\ & Glove_0 = 39,000\\ & N95\_Mask_0 = 936\\ & Surg\_Mask_0 = 1560\\ & Shield_0 = 69\\ & Fluid_0 = 339,924\\ & Vent_0 = 16\\ & D_0 = 80\\ & N_0 = 160\\ \\ & Gown_t, Gown\_Order_t, Staff\_Gown_t, Staff\_Gown\_Order_t, Glove_t, Glove\_Order_t, N95\_Mask_t, N95\_Mask\_Order_t, \\ & Surg\_Mask_t, Surg\_Mask\_Order_t, Shield_t, Shield\_Order_t, Vent_t, Vent\_Order_t, Fluid_t, Fluid\_Order_t, N_t, D_t, N\_On_t, \\ & D\_On_t, N\_Off_t, D\_Off_t \ge 0, \forall \space t \in days\\ \end{aligned}$$

## 2.B. Max Flow Objective ##

**Type of Model**: Max Flow Linear Program

### 2.B.i. Variable Definitions ###

* $x_{ij}$: The total flow of patients through our treatment network

### 2.B.ii. Other Definitions ###

Data | Definition
--- | ---
Nodes | Our set of treatment nodes
Paths | Set of treatment paths that patients can take
$cap_{ij}$ | Capacity of each path
$c_{ij}$ | Cost of each node

### 2.B.iii. Maximum Flow Model ###
$$\begin{aligned} \underset{ \mathbb{}}{\text{minimize}}\qquad& -\sum_{i \in Paths}\sum_{j \in Paths} c_{ij} * x_{ij}\\ \text{subject to:}\qquad& \sum_{j \in Nodes}x_{kj} = \sum_{i \in Nodes}x_{ik} && \forall \space k \in Nodes\\ & x_{ij} \le cap_{ij} && \forall \space (i,j) \in Paths\\ & x_{ij} \ge 0 && \forall \space (i,j) \in Paths\\ \end{aligned}$$

## 3. Solution ##

### 3.A. Set Up ###
### 3.A.i. Beginning Inventories ###

Using the data from the Johns Hopkins Center for Health(11), we find the following numbers for the use of consumables:

PPE | Requirement (Per Person Per Day)
--- | ---------
Gloves | 250
Gowns | 20
Staff Gowns | 20
N95 Mask | 6
Surgical Mask | 10

Then using our current number of Covid-19 patients admitted at our hospital plus our average daily increase in cases we can use this number to find an approximation of the amount of each consumable needed each day:  

PPE | Need (Per Day)
--- | ---------
Gloves | 13,000 
Gowns | 1,040 
Staff Gowns | 1,040
N95 Mask | 312 
Surgical Mask | 520 

Next using our assumption that we have 3 days of inventory we find our beginning inventory levels for Gloves, Gowns, Staff Gowns, N95 Masks, and Surgical Masks:  

PPE | Inventory at Day 0
--- | ---------
Gloves | 39,000 
Gowns | 3120 
Staff Gowns | 3120 
N95 Mask| 936 
Surgical Mask | 1560

Determining the number of Face Shields required each day we need to use the requirement of doctors and nurses for each severity level of patient since we know that only 1 Face Shield is needed for each staff member per day. Using the data we found on the number of doctors and nurses required for each level of patient(12)(13)(14) we find:  
* Severity 4 => 1/2 Nurses and 1/14 Doctors
* Severity 3 => 1/2 Nurses and 1/14 Doctors
* Severity 2 => 1/4 Nurses and 1/20 Doctors
* Severity 1 => 1/4 Nurses

So using approximate numbers for the number of each type patient we have:
* Severity 4 => 11 cases = 7 Shields
* Severity 3 => 11 cases = 7 Shields
* Severity 2 => 23 cases = 7 Shields
* Severity 1 => 8 cases = 2 Shields

Thus, we need roughly 23 Face Shields per day and a starting inventory level of 69 Face Shields.  

Finding the number of Fluids needed each day we use the data from the International Fluid Academy(15), that shows that we need approximately 27 mL/kg/day. To find the amount required per patient per day we will use the assumption that every patient is the average weight of an adult in North America of 80.7 kg(16) giving us the requirement of 2,179 mL/patient/day. Subsequently, we use our number of current admitted patients plus the average number of positive cases per day to find our daily need of 113,308 mL and as a result our starting inventory of 339,924 mL.

Finally we calculate the number of ventilators and beds on hand by finding the total number of hospitals in the South Central region of Wisconsin which is 23(10). Then using the number of ventilators in the region of 366(17) we find that we have approximately 16 ventilators per hospital. Furthermore, we know that the number of beds available in the region is 2346 and using the same process we find that we have 102 beds. Our number of beds will be used as a constant in our model for the number of patients we can treat so it will not be included in our inventory. 

This gives us our overall per patient per day requirements of each consumable of:

PPE | Requirement (Per Person Per Day)
--- | ---------
Gloves | 250
Gowns | 20
Staff Gowns | 20
N95 Mask | 6
Surgical Mask | 10
Face Shield | 1 (Per Medical Staff)
Fluids | 2,179 mL
Ventilators | 1

And provides our beginning inventory of:

PPE | Inventory at Day 0
--- | ---------
Gloves | 39,000 
Gowns | 3120 
Staff Gowns | 3120 
N95 Mask| 936 
Surgical Mask | 1560
Face Shield | 69
Fluids | 339,924 mL
Ventilators | 16


### 3.A.ii. Setting Up Patient Data ###

Our project looks at 7 days of treating covid patients, using the most recent data on positive covid cases in Dane county we find the following information(18):  

Date | Number of Positive Cases
---- | ------------------------
Jul 27 | 8
Jul 28 | 7
Jul 29 | 14
Jul 30 | 8
Jul 31 | 16
Aug 1 | 6
Aug 2 | 18

Breaking these numbers down we have that:  
* Average: 11
* Standard Deviation: 4.504

We used the average $\pm$ 1σ in order to find the upper and lower bounds for our new case generator, rounding the lower bound down to the nearest integer and rounding the upper bound up to the nearest integer giving us the bounds of $ 6 \le x \le 16 $ with x being the number of new cases each day.

Then to break down the number of patients into their severities, we began by multiplying the number of daily cases by the percentage of Covid cases that require hospitalization. According the the CDC, the rate of hospitalization for Covid-19 is 130.1 per 100,000 population(19). Using the population of Dane County which is 529,843 we can estimate that roughly 689.33 people required hospitalization in Dane County and then dividing this by the number total number of cases in Dane county, 4,198(2). We find that our percentage of positive tests that require hospitalization is 16.42%.

We can use this percentage to find the number of our generated daily cases that require hospitalization. The patients that do not require hospitalization are classified as severity 1 and can be found by finding the difference between the number of cases and the number hospitalized. 

Next to break our Admitted Patients into severities 2, 3, and 4 we first use the information that there are 7 patients currently in Intensive Care with Covid-19 which we will classify as Severity 4 in our case. With 41 patients currently receiving care for Covid in our hospital this means that 17.07% of our admitted patients are severity 4. Since severities 2, 3, and 4 cannot occur at the same time this means that they are mutually exclusive and collectively exhaustive and thus must make up 100% of of our Admitted Patients this means that 82.93% must be severity 2 or 3. 

In order to find the probabilities that an admitted patient has severity 2 or 3 we must use the information that only severities 3 and 4 require mechanical ventilation. To find the probability that a patient of Covid-19 requires mechanical ventilation we will use data for the state of Wisconsin and assume that this probability is the same in Dane county, there have been 4,732 people to ever be hospitalized due to covid, and with 18% of those being active cases we can say that there are roughly 852 people hospitalized in Wisconsin. With 321 patients currently using mechanical ventilation in Wisconsin(17) the probability that a Covid patient requires mechanical ventilation and thus, the probability of severity 3 and 4 is 37.68%. Since the probabilities of severities 3 and 4 must add up to 37.68% and the probability of a patient being severity 4 is 17.07% then the probability that a patient is severity 3 must be 20.61%. Once again since the total probability of admitted patients is the sum of the probabilities of severities 2, 3, and 4 then the probability that a patient is severity 2 is 62.32%. Thus our probabilities are as follows:

x | P(x)
---|----
2 | 0.6232
3 | 0.2061
4 | 0.1707

![Admitted Patients Distribution](Admitted Patients Distribution.png "Logo Title Text 1")
