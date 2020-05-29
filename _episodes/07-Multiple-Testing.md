---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 07-Multiple-Testing.md in _episodes_rmd/
title: "Multiple testing"
teaching: 45
exercises: 10
questions:
- ""
keypoints:
- ""

output: html_document
---


## Multiple testing
In ANOVA, if H<sub>0</sub> is rejected, we carry out a __post hoc__ test to 
identify which group(s) are significantly different from the other ones. If 
there are three groups, that means we are performing 3 comparisons:

1. A vs B

2. A vs C

3. B vs C

Each time we perform a t-test, we chose a significance threshold under which we
reject the null hypothesis. A significance level of 0.05 for example means a 5%
chance of making a type I error - incorrectly rejecting H<sub>0</sub>. When 
performing multiple tests, this probability of error accumulates, so the more 
tests performed, the higher likelihood of a type I error.

In our example three-way comparison with signficance threshold of 0.05:
* Prob(making no mistake) = 0.95 x 0.95 x 0.95 = 0.857
* Prob(making a mistake = 1 - 0.857 = 0.143

So rather than a 5% chance of a type I error, we instead have a much higher 
14.3% chance, which is clearly not acceptable.

> ## Challenge 1
>
> Memory test! A type I error is an incorrect rejection of the null hypothesis. 
> Can you remember what a type II error is?
> > ## Solution to Challenge 1
> > 
> > A type II error is a failure to reject the null hypothesis when it is false
> {: .solution}
{: .challenge}

### Correction for multiple testing
To compensate for the effects of multiple testing on expected error rate, we 
need to adjust the p-value according to the number of hypothesis tests 
performed. 

Examples of correction approaches include:
* False Discovery Rate (FDR): controls the type I error rate
* Bonferroni correction (FWER): reduce the probability of even one false 
discovery

In R, the `p.adjust` function can be used to perform multiple-testing correction
on a vector of p-values. 

```r
# Create a vector of multiple p-values to be tested
# Note, most of these individually meet the significance threshold of <0.05
pval <- c(0.02, 0.01, 0.05, 0.04, 0.1, 0.6, 0.5, 0.03, 0.005, 0.0005)
# False Discovery Rate correction - only three tests remain signficant
p.adjust(pval, method = 'fdr')
```

~~~
##  [1] 0.05000000 0.03333333 0.07142857 0.06666667 0.12500000 0.60000000
##  [7] 0.55555556 0.06000000 0.02500000 0.00500000
~~~
{: .output}

```r
# Bonferroni correction - now only one remains significant
p.adjust(pval, method = 'bonferroni')
```

~~~
##  [1] 0.200 0.100 0.500 0.400 1.000 1.000 1.000 0.300 0.050 0.005
~~~
{: .output}

## Summary of relevant statistical tests
This table can be used as a reference to select the appropriate test to use from
a given experimental design
|Analysis required|Normally distributed data|Non-normally distributed data|
|Compare mean or median of one sample group against a known value|One sample t-test|Wilcoxon Rank Sum test|
|Compare means or medians of two sample groups (unpaired data)|Unpaired t-test|Mann-Whitney test|
|Compare means or medians of two sample groups (paired data)|Paired t-test|Wilcoxon Matched Pairs test|
|Compare means or medians of ≥ three sample groups (unpaired data)|ANOVA|Kruskal-Wallis ANOVA|
|Compare means or medians of ≥ three sample groups (paired data)|Repeated measures ANOVA|Friedman test|
|Compare two sample proportions of categorical data (unpaired data/≤5 in one group)| |Fisher exact test|
|Compare > three sample proportions of categorical data| |Chi-square test|
|Compare two sample proportions of categorical data (paired data)| |McNemar’s test|

## Summary of R functions

* Check for normality: `Shapiro.test(var)`
* Correlation (normally distributed) `cor.test(var1,var2,method=“Pearson”)`
* Correlation (Non-normally distributed) `cor.test(var1,var2,method=“Spearman”)`
* Chi-square `chisq.test(table(var1,var2))`
* Fisher’s exact (≤5 counts) `fisher.test(table(var1,var2))`
* Unpaired t-test `t.test(var1~var2)`
* Paired t-test `t.test(var1~var2,paired=T)`
* Wilcoxon Rank Sum test `wilcox.test(var1~var2)`
* Wilcoxon Matched Pairs Test `wilcox.test(var1~var2,paired=T)`
* Compare >2 groups for one factor
* ANOVA `aov(outcome~factor1)`
* Kruskal-Wallis ANOVA `kruskal.test(outcome~factor1)`

## Final practice exercises
> ## Challenge 2
>
> Do patients with multiple gallstones experience more recurrences than those 
> with single stones?
> > ## Solution to Challenge 2
> > 
> > 
> > ```r
> > #create the frequency table
> > table(data$Rec,data$Mult)
> > 
> > # Rename the levels of the variables to make it more readable
> > levels(data$Rec)<-c("NoRecurrence","Recurrence")
> > levels(data$Mult)<-c("Single","Multiple")
> > table(data$Rec,data$Mult)
> > 
> > # Complete Cross-table
> > library(gmodels)
> > CrossTable(data$Rec,data$Mult,format="SPSS",prop.chisq=F,expected=T)
> > 
> > # Visualisation
> > 
> > library(ggplot2)
> > ggplot(data, aes(Rec, fill=Mult)) + 
> >   geom_bar(position="dodge")+
> >   theme(axis.text=element_text(size=18),
> >         legend.text=element_text(size=18),
> >         legend.title=element_text(size=18),
> >         axis.title=element_text(size=18),
> >         plot.title = element_text(size=22, face="bold"))+
> >   ggtitle("Recurrence vs. Mult") 
> > 
> > # Chi-square test performed because more than 5 expected counts in each cell
> > chisq.test(data$Rec,data$Mult)
> > 
> > > {: .solution}
> > {: .challenge}
> > 
> > > ## Challenge 3
> > >
> > > Is the patient's age associated with gallstone recurrence?
> > ## Solution to Challenge 3
> > 
> > # Test normality in both groups
> > shapiro.test(data$Age[which(data$Rec=="NoRecurrence")])
> > shapiro.test(data$Age[which(data$Rec=="Recurrence")])
> > 
> > # Other option
> > by(data$Age,data$Rec,shapiro.test)
> > 
> > # We have to perform a Mann-Whitney test 
> > wilcox.test(data$Age~data$Rec)
> > 
> > # We can compare with a T-test
> > t.test(data$Age~data$Rec)
> > 
> > # Boxplots to visualise
> > plot(data$Age~data$Rec,col=c("red","blue"),ylab="Age",
> >      xlab="Recurrence",cex.lab=1.3)
> > 
> > # Statistical descriptions
> > by(data$Age,data$Rec,median)
> > by(data$Age,data$Rec,IQR)
> > 
> > by(data$Age,data$Rec,mean)
> > by(data$Age,data$Rec,sd)
> > > {: .solution}
> > {: .challenge}
> > 
> > > ## Challenge 4
> > >
> > > Does alcohol consumption influence the time to gallstone dissolution?
> > ## Solution to Challenge 4
> > 
> > # Test normality in each groups
> > by(data$Dis,data$Alcohol.Consumption,shapiro.test)
> > 
> > # If distribution normal in each group
> > result<-aov(data$Dis~data$Alcohol.Consumption)
> > summary(result)
> > 
> > # If distribution not normal
> > kruskal.test(data$Dis~data$Alcohol.Consumption)
> > 
> > # Visualisation
> > plot(data$Dis~data$Alcohol.Consumption,col=2:4)
> > > {: .solution}
> > {: .challenge}
> > 
> > > ## Challenge 5
> > >
> > > Is there any effect of treatment and/or gender on the time to dissolution?
> > ## Solution to Challenge 5
> > 
> > # Visualisation
> > par(mfrow=c(1,2))
> > plot(Dis~Treatment+Gender, data=data)
> > 
> > # Interaction plot to visualise
> > interaction.plot(data$Treatment, data$Gender, data$Dis,
> >                  col=2:3,lwd=3,cex.axis=1.5,cex.lab=1.5)
> > 
> > # anova 2 way with aov function
> > result<-aov(Dis~Treatment+Gender+Treatment*Gender,data=data)
> > summary(result)
> > 
> > # anova 2 way with lm function
> > result<-lm(Dis~Treatment+Gender+Treatment*Gender,data=data)
> > anova(result)
> > 
> > # Checking the assumptions of anova
> > 
> > result<-lm(Dis~Treatment+Gender+Treatment*Gender,data=data)
> > plot(result)
> > > {: .solution}
> > {: .challenge}
> > ```
> > 
> > ```
> > ## Error: <text>:28:1: unexpected '>'
> > ## 27: 
> > ## 28: >
> > ##     ^
> > ```