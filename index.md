---
title:
layout: page
toc: true
#title:
output:
  md_document:
variant: markdown_github
preserve_yaml: true
---


### Description

- Exploration of the effects of online personal attacks on victims' activity on social media (Reddit).

-   First large-scale (150K users who generated more than 180K comments), behavioral analysis that uses a high-precision Artificial Intelligence system and is not based on self-reported data.

-  Data analysis in R conducted from three perspectives: (1)~classical statistical methods, (2)~Bayesian estimation, and (3)~model-theoretic analysis with hurdle and zero-inflated models.

-  Personal attacks received online significantly decrease victims' online activity.


_Abstract._ We conduct a large scale data-driven analysis of the effects of online personal attacks on social media user activity. First, we perform a thorough overview of the literature on the influence of social media on user behavior, especially on the impact that negative and aggressive behaviors, such as harassment and cyberbullying, have on users’ engagement in online media platforms. The majority of previous research were  small-scale self-reported studies, which is their limitation. This motivates our data-driven study. We perform a large-scale analysis of messages from Reddit, a discussion website, for a period of two weeks, involving 182,528 posts or comments to posts by 148,317 users. To efficiently collect and analyze the data we apply a high-precision personal attack detection technology. We analyze the obtained data from three perspectives: (i) classical statistical methods, (ii) Bayesian estimation, and (iii) model-theoretic analysis. The three perspectives agree: personal attacks decrease the victims’ activity.
The results can be interpreted as an important signal to social media platforms and policy makers that leaving personal attacks unmoderated is quite likely to disengage the users and in effect depopulate the platform. On the other hand, application of cyberviolence detection technology in combination with various mitigation techniques could improve and strengthen the user community. As more of our lives is taking place online, keeping the virtual space inclusive for all users becomes an important problem which online media platforms need to face.



_Remark_. In what follows, we sometimes display key pieces of code and explain what it does. Some not too fascinating pieces of code are supressed, but the reader can look them up in the associated .Rmd file and compile their own version.



### Resources

- Github folder with all the files is available [here](https://github.com/rfl-urbaniak/redditAttacks).

- The [datasets subfolder](https://github.com/rfl-urbaniak/redditAttacks/tree/main/datasets) includes the dataset (quittingFinalAnon.csv) and the bayesian chains resulting from the bayesian analysis.
-




**Contents**
* TOC
{:toc}


###  Technology applied for personal attack detection

For the need of this research we define personal attack as any kind of abusive remark made in relation to a person (ad hominem) rather than to the content of the argument expressed by that person in a discussion. The definition of ‘personal attack’ subsumes the use of specific terms which compare other people to animals or objects or making nasty insinuations without providing evidence. Three examples of typical personal attacks are as follows.

-   *You are legit mentally retarded homie.*
-   *Eat a bag of dicks, fuckstick.*
-   *Fuck off with your sensitivity you douche.*

The detection of personal attacks was performed using Samurai, a proprietary technology of Samurai Labs.[1] The following figure illustrates how the input text ("ccant believ he sad ur an id10+...!") is processed step-by-step utilizing both statistical and symbolic methods.


<img src="https://rfl-urbaniak.github.io/redditAttacks/images/example.png" width="100%" style="display: block; margin: auto;" />


In practice, it means that a whole variety of constructions can be detected without the need to construct a fixed list of dictionary words defined . Due to utilizing symbolic components that oversee statistical components, Samurai recognizes complex linguistic phenomena (such as indirect speech, rhetorical figures or counter-factual expressions) to distinguish personal attacks from normal communication, greatly reducing the number of false alarms as compared to others systems used for violence detection. The full benchmark was presented in (Ptaszyński et al., 2018).


The detection models utilized in this research were designed to detect personal attacks targeted against a second person (e.g. interlocutor, original author of a post) and a third person/group (e.g., other participants in the conversation, people not involved in the conversation, social groups, professional groups), except public figures (e.g. politicians, celebrities). With regards to symbolic component of the system, by "models" we mean separate rules (such as, specifying a candidate for the presence of personal attack, such as the aggressive word "idiot," which is further disambiguated with a syntactic rule of citation, e.g.,

``` r
"\[he|she|they\] said \[SUBJECT\] \[PREDICATE\]")
```

 or sets of rules. The normalization model contains rules for transcription normalization, citation detection model contains rules for citation, etc. With regards to the statistical component, by "models" refer to machine learning models trained on large data to classify an entry into one of the categories (e.g., true personal attack, or false positive).




Moreover, the symbolic component of the system uses two types of symbolic rules, namely "narrow rules" and "wide rules." The former have smaller coverage (e.g., are triggered less often), but detect messages containing personal attacks with high precision. The latter, have wider coverage, but their precision is lower. We decided to set apart the "narrow" and "wide" subgroups of the detection models in order to increase the granularity of the analysis. Firstly, we took only the detection models designed to detect personal attacks targeted against second person. Secondly, we used these models on a dataset of 320,000 Reddit comments collected on 2019/05/06. Thirdly, we randomly picked at most hundred returned results for each low-level model. Low-level models are responsible for detecting low-level categories. Similarly, mid-level models detect mid-level categories, by combining several low-level models, etc. (Some models are triggered very often while others rarely, so using all instances would create too much bias). There were 390 low-level models but many of them returned in less than 100 results. We verified them manually with the help of expert annotators trained in detection of personal attacks and selected only those models that achieved at least 90% of precision. The models with fewer than 100 returned results were excluded from the selection. After this step, the "narrow" subgroup contained 43 out of 390 low-level models. Finally, we tested all of the "narrow" models on a large dataset of 477,851 Reddit comments collected between 2019/06/01 and 2019/08/31 from two subreddits (r/MensRights and r/TooAfraidToAsk). Each result of the "narrow" models was verified manually by a trained annotator and the "narrow" models collectively achieved over 93.3% of precision. We also tested the rest of the "wide" models on random samples of 100 results for each model (from the previous dataset of 320,000 Reddit comments) and we excluded the models that achieved less than 80% precision. The models with fewer than 100 results were not excluded from the "wide" group. In this simple setup we detected 24,251 texts containing "wide" attacks, where:



-   5,717 (23.6%) contained personal attacks against second person detected by the "narrow" models,
-   8,837 (36.4%) contained personal attacks against second person detected by "wide" models,
-   10,023 (41.3%) contained personal attacks against third persons / groups.


The sum exceeds 100% because some of the comments contained personal attacks against both second person and third person / groups. For example, a comment \`\`Fu\*\* you a\*\*hole, you know that girls from this school are real bit\*\*es" contains both types of personal attack.





Additionally, from the original data of 320,000 Reddit posts we extracted and annotated 6,769 Reddit posts as either a personal attack (1) or not (0). To assure that the extracted additional dataset contains a percentage of Personal Attacks sufficient to perform the evaluation, the Reddit posts were extracted with an assumption that each post contains at least one word of a general negative connotation. Our dictionary of such words contains 6,412 instances, and includes a wide range of negative words, such as nouns (e.g., "racist", "death", "idiot", "hell"), verbs (e.g., "attack", "kill", "destroy", "suck"), adjectives (e.g., "old", "thick", "stupid", "sick"), or adverbs (e.g., "spitefully", "tragically", "disgustingly"). In the 6,769 additionally annotated Reddit samples there were 957 actual Personal Attacks (14%), from which Samurai correctly assigned 709 (true positives) and missed 248 (false negatives), which accounts for 74% of the Recall rate. Finally, we performed another additional experiment in which, we used Samurai to annotate completely new 10,000 samples from Discord messages that did not contain Personal Attacks but contained vulgar words. The messages were manually checked by two trained annotators and one additional super-annotator. The result of Samurai on this additional dataset was a 2% of false positive rate, with exactly 202 cases misclassified as personal attacks. This accounts for specificity rate of 98%.



The raw datasets used have been obtained by Samurai, who were able to collect Reddit posts and comments without moderation or comment removal. All content was downloaded from the data stream provided by which enabled full data dump from Reddit in real-time. The advantage of using it was access to unmoderated data (As of August 20th, the service is not available. For now, one possible way is to use an API provided by Reddit with a constraint:  Reddit allows for 600 requests every 10 minutes. 10 minutes might be a short time in the case of moderators' reaction, but is enough for AutoModerator or other automated moderation to exert their force, thus leading to a moderated, and therefore incomplete dataset.). Further, deployed their personal attacks recognition algorithms to identify personal attacks.




In the study, experimental manipulation of the crucial independent variables (personal attacks of various form) to assess their effect on the dependent variable (users’ change in activity) would be unethical and against the goal of Samurai Labs, which is to detect and *prevent* online violence. While such a lack of control is a weakness as compared to typical experiments in psychology, our sample was both much larger and much more varied than the usual WEIRD (western, educated, and from industrialized, rich, and democratic countries) groups used in psychology. Notice, however, that the majority of Reddit users are based in U.S. For instance, (Wise, Hamman, & Thorson, 2006) examined 59 undergraduates from a political science class at a major Midwestern university in the USA, (Zong, Yang, & Bao, 2019) studied 251 students and faculty members from China who are users of WeChat, and (Valkenburg, Peter, & Schouten, 2006) surveyed 881 young users (10-19yo.) of a Dutch SNS called CU2.



Because of the preponderance of personal attacks online, we could use the real-life data from Reddit and use the following study design:

1.  All the raw data, comprising of daily lists of posts and comments (some of which were used in the study) with time-stamps and author and target user names, have been obtained by Samurai Labs, who also applied their personal attack detection algorithm to them, adding two more variables: narrow and wide. These were the raw datasets used in further analysis.

2.  Practical limitations allowed for data collection for around two continuous weeks (day 0 ± 7 days). First, we randomly selected one weekend day and one working day. These were June 27, 2020 (Saturday, S) and July 02, 2020 (Thursday, R). The activity on those days was used to assign users to groups in the following manner. We picked one weekend and one non-weekend day to correct for activity shifts over the weekend (the data indeed revealed slightly higher activity over the weekends, no other week-day related pattern was observed). We could not investigate (or correct for) monthly activity variations, because the access to unmoderated data was limited.

3.  For each of these days, a random sample of 100,000 posts or comments have been drawn from all content posted on Reddit. Each of these datasets went through preliminary user-name based bots removal. This is a simple search for typical phrases included in user names, such as "Auto", "auto", "Bot", or "bot".

For instance, for our initial thursdayClean datased, this proceeds like this:
``` r
thursdayClean <- thursdayClean[!grepl("Auto", thursdayClean$author, fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("auto", thursdayClean$author, fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("Auto", thursdayClean$receiver, fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("auto", thursdayClean$receiver,
fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("bot", thursdayClean$receiver,
fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("Bot", thursdayClean$receiver,
fixed = TRUE), ]
```



4. In some cases, content had been  deleted by the user or removed by Reddit  --- in such cases the dataset only contained information that some content had been  posted but was later removed; since we could not access the content  of such posts or comments and evaluate them for personal attacks, we also excluded them from the study.

Again, this was a  straightforward use of grepl:
``` r
thursdayClean <- thursdayClean[!grepl("none", thursdayClean$receiver, fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("None", thursdayClean$receiver, fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("<MISSING>", thursdayClean$receiver,
 fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("[deleted]", thursdayClean$receiver,
 fixed = TRUE), ]
```

 5. This left us with  92,943 comments or posts by 75,516 users  for R and 89,585 comments by 72,801 users  for S. While we didn't directly track whether content was a post or a comment, we paid attention as to whether a piece of content was a reply to a post or not (the working assumption was that personal attacks on posts might have different impact than attacks on comments). Quite consistently, $46\%$ of content were comments on posts on both days.


 6.  On these two  days respectively,  1359 R users ($1.79\%$) received at least one narrow attack, 35 of them received more than one ($0.046\%$). 302 of S users ($0.39\%$) received at least one narrow attack and 3 of them more than one narrow on that day ($0.003\%$). These numbers  are estimates for a single day, and therefore if the chance of obtaining at least one narrow attack in a day is $1.79\%$, assuming the binomial distribution, the estimated probability of obtaining at least one narrow attack in a week is 11.9\% in a week and 43\% in a month.
``` r
100  * round(1-dbinom(0,7,prob = 1359/75516),3)` #week
100 * round(1-dbinom(0,31,prob = 1359/75516),3)` #month
```


7.  To ensure a sufficient sample size, we decided not to draw a random sub-sample from the or class comprising 340 users, and included all of them in the Thursday treatment group (). Other users were randomly sampled from and added to , so that the group count was 1000.

8.  An analogous strategy was followed for . 1338 users belonged to , 27 to , 329 to and 3 to . The total of 344 or users was enriched with sampled users to obtain the group of 1000 users.

9.  The preliminary / groups of 1500 users each were constructed by sampling 1500 users who posted comments on the respective days but did not receive any recognized attacks. The group sizes for control groups are higher, because after obtaining further information we intended to eliminate those who received any attacks before the group selection day (and for practical reasons we could only obtain data for this period after the groups were selected).

10.  For each of these groups new dataset was prepared, containing all posts or comments made by the users during the period of ![\\pm 7](https://latex.codecogs.com/png.latex?%5Cpm%207 "\pm 7") days from the selection day (337,015 for , 149,712 for , 227,980 for and 196,999 for ) and all comments made to their posts or comments (621,486 for , 170,422 for , 201,614 for and 204,456 for ), after checking for uniqueness these jointly were 951,949 comments for , 318,542 comments for , 404,535 comments for , and 380,692 comments for ). The need to collect all comments to the content posted by our group members was crucial. We needed this information because we needed to check all such comments for personal attacks to obtain an adequate count of attacks received by our group members. In fact, this turned out to be the most demanding part of data collection.

11.  All these were wrangled into the frequency form, with (1) numbers of attacks as recognized by or algorithm (in the dataset we call these and respectively), (2) distinction between and ), and (3) activity counts for each day of the study, (4) with added joint counts for the e and periods. Frequency data for users outside of the control or treatment groups were removed.

12.  With the frequency form at hand, we could look at outliers. We used a fairly robust measure. For each of the weekly counts of and we calculated the interquartile range (), as the absolute distance between the first and the third quartile and identified as outliers those users which landed at least ![1.5\\times \`IQR`}](https://latex.codecogs.com/png.latex?1.5%5Ctimes%20%5Ctextsf%7BIQR%7D "1.5\times `IQR`}") from the respective mean. These resulted in a list of 534 "powerusers" which we suspected of being bots (even though we already removed users whose names suggested they were bots) --- all of them were manually checked by . Those identified as bots (only 15 of them) or missing (29 of them) were removed. It was impossible to establish whether the missing users were bots; there are also two main reasons why a user might be missing: (a) account suspended, and (b) user deleted. We decided not to include the users who went missing in our study, because they would artificially increase the activity drop during the period and because we didn't suspect any of the user deletions to be caused by personal attacks directed against them (although someone might have deleted the account because they were attacked, these were power-users who have a high probability of having been attacked quite a few times before, so this scenario is unlikely).

13.  The frequency form of the control sets data was used to remove those users who were attacked in the period (894 out of 1445 for , and 982 out of 1447 for remained).

14.  A few more unusual data points needed to be removed, because they turned out to be users whose comments contained large numbers of third-person personal attacks which in fact supported them. Since we were interested in the impact of personal attacks directed against a user on the user's activity, such unusual cases would distort the results. Six were authors of posts or comments which received more than 60 attacks each. Upon inspection, all of them supported the original users. For instance, two of them were third-person comments about not wearing a mask or sneezing in public, not attacks on these users. Another example is a female who asked for advice about her husband: the comments were supportive of her and critical of the husband. Two users with weekly activity count change higher than 500 were removed -- they did not seem to be bots but very often they posted copy-pasted content and their activity patterns were highly irregular with changes most likely attributable to some other factors than attacks received. The same holds for a young user we removed from the study who displayed activity change near 1000. She commented on her own activity during that period as very unusual and involving 50 hrs without sleeping. Her activity drop afterwards is very likely attributable to other factors than receiving a personal attack.

15.  86 users who did not post anything in the period were also removed.

16. In the end, and were aligned, centering around the selection day (day 8) and the studied group comprised 3673 users.


``` r
# note we load the data here
data <- read.csv("../datasets/quittingFinalAnon.csv")[, -1]
table(data$group)
```

##
##   Rcontrol Rtreatment   Scontrol Streatment
##875935942921



  A few first lines of the resulting anonymized dataset from which we removed separate day counts. Note that in the code "low" corresponds to "wide" (for "low precision") and "high" to "narrow" attacks (for "high precision"). The variables are: (low attacks, high attacks, low attacks on posts, how attacks on posts, authored content posted) and retained summary columns.



``` r
dataDisp <- data[, c(1, 77:85)]
head(dataDisp)
```

  ##   user sumLowBefore sumHighBefore sumPlBefore sumPhBefore activityBefore
  ## 111 0   1   0  2
  ## 225 4   0   0106
  ## 332 1   0   0 29
  ## 446 4   0   0180
  ## 555 2   0   0116
  ## 662 0   0   0124
  ##   activityAfter activityDiff  group treatment
  ## 1 0   -2 Rtreatment 1
  ## 280  -26 Rtreatment 1
  ## 3312 Rtreatment 1
  ## 492  -88 Rtreatment 1
  ## 595  -21 Rtreatment 1
  ## 6   104  -20 Rtreatment 1


-  `user` contains anonymous user numbers.
-  `sumLowBefore` contains the sum of `wide`  attacks in days 1-7. `sumHighBefore` the sum of `narrow` (attacks in the same period.
-  `Pl* and *Ph* code *wide* and `narrow` attacks on posts (we wanted to verify the additional sub-hypothesis that  attacks on a post might have more impact than attacks on comments).

-  `activityBefore` and `activityAfter` count comments or posts during days seven days before and seven days after. The intuition is, these shouldn't change much if personal attacks have  no impact on activity.

-  `group` and `treatment` include information about which group a user belongs to.




### Exploration


First, we visually explore our dataset by looking at the relationship between the number of received attacks vs. the activity change counted as the difference of weekly counts of posts or comments authored  in the second (`after`) and in the first  week (`before`). We do this for `narrow` attacks , `wide` attacks, where a weaker, but still negative impact, can be observed, and  then we   take a look at the impact of those attacks which were recognized as `wide only`. The distinction between wide and narrow pertains only to the choice of attack recognition algorithm and does not directly translate into how offensive an attack was, except that `wide` attacks also include third-person ones.  Here, the direction of impact is less clear: while the tendency is negative for low numbers of attacks, non-linear smoothing suggests that  higher numbers mostly third-person personal attacks seem positively correlated with activity change. This might suggest that while being attacked has negative impact on a user's activity, having your post "supported" by other users' third-person attacks has a more motivating effect. We will look at this issue in a later section, when we analyze the dataset using regression.



The visualisations  should be understood as follows. Each point is a user. The $x$-axis represents a number of attacks they received in the `before` period (so that, for instance, users with 0 wide attacks are the members of the control group), and  the $y$-axis represents the difference between their activity count `before` and `after`. We can see that most of the users received 0 attacks before (these are our control group members), with the rest of the group receiving 1, 2, 3, etc. attacks in the `before` period with decreasing frequency. The blue line represents linear regression suggesting negative correlation. The gray line is constructed using generalized additive mode (gam) smoothing, which is  a fairly standard smoothing method for large datasets (it is more sensitive to local tendencies and yet avoids overfitting). The parameters of the gam model (including the level of smoothing) are chosen by their predictive accuracy. (See  the [documentation](\url{https://www.rdocumentation.org/packages/mgcv/versions/1.8-33/topics/gam) of `gam` of the `mgcv` package for details.)  Shades indicate the $95\%$ confidence level interval for predictions from the linear model.




``` r
library(ggthemes)
th <- theme_tufte()
highPlot <- ggplot(data, aes(x = sumHighBefore, y = activityDiff)) +
geom_jitter(size = 0.8, alpha = 0.3) + geom_smooth(method = "lm",
color = "skyblue", fill = "skyblue", size = 0.7, alpha = 0.8) +
scale_x_continuous(breaks = 0:max(data$sumHighBefore), limits = c(-1,
max(data$sumHighBefore))) + ylim(c(-300, 300)) + geom_smooth(color = "grey",
size = 0.4, lty = 2, alpha = 0.2) + xlab("narrow attacks before") +
ylab("activity change after") + labs(title = "Impact of narrow attacks on activity",
subtitle = "weekly counts, n=3673") + geom_segment(aes(x = -1,
y = -100, xend = 9, yend = -100), lty = 3, size = 0.1, color = "gray71",
alpha = 0.2) + geom_segment(aes(x = -1, y = 100, xend = 9,
yend = 100), lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = -1, y = -100, xend = -1, yend = 100),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = 9, y = -100, xend = 9, yend = 100),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
th


highPlotZoomed <- ggplot(data, aes(x = sumHighBefore, y = activityDiff)) +
geom_jitter(size = 1, alpha = 0.2) + geom_smooth(method = "lm",
color = "skyblue", fill = "skyblue", size = 0.7, alpha = 0.8) +
th + scale_x_continuous(breaks = 0:max(data$sumHighBefore),
limits = c(-1, 9)) + ylim(c(-100, 100)) + geom_smooth(color = "grey",
size = 0.4, lty = 2, alpha = 0.2) + xlab("narrow attacks before") +
ylab("activity change after") + labs(title = "Impact of narrow attacks on activity",
subtitle = "weekly counts, zoomed in") + geom_hline(yintercept = 0,
col = "red", size = 0.2, lty = 3)
```


``` r
highPlot
```


<img src="https://rfl-urbaniak.github.io/redditAttacks/images/highPlot-1.png" width="100%" style="display: block; margin: auto;" />




``` r
highPlotZoomed
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/highPlotZoomed-1.png" width="100%" style="display: block; margin: auto;" />

``` r
lowPlot <- ggplot(data, aes(x = sumLowBefore, y = activityDiff)) +
geom_jitter(size = 0.8, alpha = 0.3) + geom_smooth(method = "lm",
color = "skyblue", fill = "skyblue", size = 0.7, alpha = 0.8) +
th + geom_smooth(color = "grey", size = 0.4, lty = 2, alpha = 0.2) +
xlab("wide attacks before") + ylab("activity change after") +
labs(title = "Impact of wide attacks on activity", subtitle = "weekly counts, n=3673") +
geom_segment(aes(x = -1, y = -150, xend = 15, yend = -150),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = -1, y = 150, xend = 15, yend = 150),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = -1, y = -150, xend = -1, yend = 150),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = 15, y = -150, xend = 15, yend = 150),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
xlim(c(-1, max(data$sumLowBefore)))



lowPlotZoomed <- ggplot(data, aes(x = sumLowBefore, y = activityDiff)) +
geom_jitter(size = 1, alpha = 0.2) + geom_smooth(method = "lm",
color = "skyblue", fill = "skyblue", size = 0.7, alpha = 0.8) +
th + scale_x_continuous(breaks = 0:max(data$sumLowBefore),
limits = c(-1, 15)) + ylim(c(-150, 150)) + geom_smooth(color = "grey",
size = 0.4, lty = 2, alpha = 0.2) + xlab("wide attacks before") +
ylab("activity change after") + labs(title = "Impact of wide attacks on activity",
subtitle = "weekly counts, zoomed in") + geom_hline(yintercept = 0,
col = "red", size = 0.2, lty = 3)
```

``` r
lowPlot
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/lowPlot-1.png" width="100%" style="display: block; margin: auto;" />

``` r
lowPlotZoomed
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/lowPlotZoomed-1.png" width="100%" style="display: block; margin: auto;" />




``` r
lowOnlyPlot <- ggplot(data, aes(x = (sumLowBefore - sumHighBefore),
y = activityDiff)) + geom_jitter(size = 0.8, alpha = 0.3) +
geom_smooth(method = "lm", color = "skyblue", fill = "skyblue",
size = 0.7, alpha = 0.8) + th + geom_smooth(color = "grey",
size = 0.4, lty = 2, alpha = 0.2) + xlab("wide only attacks before") +
ylab("activity change after") + labs(title = "Impact of wide only attacks on activity",
subtitle = "weekly counts, n=3673") + geom_segment(aes(x = -1,
y = -150, xend = 15, yend = -150), lty = 3, size = 0.1, color = "gray71",
alpha = 0.2) + geom_segment(aes(x = -1, y = 150, xend = 15,
yend = 150), lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = -1, y = -150, xend = -1, yend = 150),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
geom_segment(aes(x = 15, y = -150, xend = 15, yend = 150),
lty = 3, size = 0.1, color = "gray71", alpha = 0.2) +
xlim(c(-1, max(data$sumLowBefore)))


lowOnlyPlotZoomed <- ggplot(data, aes(x = (sumLowBefore - sumHighBefore),
y = activityDiff)) + geom_jitter(size = 1, alpha = 0.2) +
geom_smooth(method = "lm", color = "skyblue", fill = "skyblue",
size = 0.7, alpha = 0.8) + th + scale_x_continuous(breaks = 0:max(data$sumLowBefore),
limits = c(-1, 15)) + ylim(c(-150, 150)) + geom_smooth(color = "grey",
size = 0.4, lty = 2, alpha = 0.2) + xlab("wide only attacks before") +
ylab("activity change after") + labs(title = "Impact of wide only attacks on activity",
subtitle = "weekly counts, zoomed in") + geom_hline(yintercept = 0,
col = "red", size = 0.2, lty = 3)
```

``` r
lowOnlyPlot
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/lowOnlyPlot-1.png" width="100%" style="display: block; margin: auto;" />

``` r
lowOnlyPlotZoomed
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/lowOnlyPlotZoomed-1.png" width="100%" style="display: block; margin: auto;" />




The above visualises the  activity change in terms of weekly counts. However, arguably, a change of -20 for a user who posts 500 times a week has different weight than for a user who posts 30 times. For this reason, we  also need to  look at changes in proportion, calculated by the following formula:

\begin{align}
\mathsf{activityScore} = & \frac{\mathsf{activityDifference}}{\mathsf{activityBefore}}
\end{align}

We zoom in to densely populated areas of the plot for `activityScore` as a function of the three types of attacks. The impact is still negative, more so for `narrow` attacks, and less so for other types. Note that for mathematical reasons the score minimum is -1 (user activity cannot drop more than 100\%), and so the graph looks  assymetric around the horizontal line.


``` r
rescale <- function(diff, act) {
diff/act
}
data$activityScore <- rescale(data$activityDiff, data$activityBefore)

propPlotHigh <- ggplot(data, aes(x = sumHighBefore, y = activityScore)) +
geom_jitter(size = 0.8, alpha = 0.3) + geom_smooth(method = "lm",
color = "skyblue", fill = "skyblue", size = 0.7, alpha = 0.8) +
th + xlab("narrow attacks before") + ylab("proportional activity change") +
labs(title = "Impact of narrow attacks on proportional activity",
subtitle = "n=3673") + scale_y_continuous(limits = c(-1,
10)) + geom_hline(yintercept = 0, col = "red", size = 0.2,
lty = 3) + scale_x_continuous(breaks = 1:15, limits = c(-1,
10)) + geom_smooth(color = "grey", size = 0.4, lty = 2, alpha = 0.2)

propPlotLow <- ggplot(data, aes(x = sumLowBefore, y = activityScore)) +
geom_jitter(size = 0.8, alpha = 0.3) + geom_smooth(method = "lm",
color = "skyblue", fill = "skyblue", size = 0.7, alpha = 0.8) +
th + xlab("wide attacks before") + ylab("proportional activity change") +
labs(title = "Impact of wide attacks on proportional activity",
subtitle = "n=3673") + scale_y_continuous(limits = c(-1,
10)) + geom_hline(yintercept = 0, col = "red", size = 0.2,
lty = 3) + scale_x_continuous(breaks = 1:15, limits = c(-1,
10)) + geom_smooth(color = "grey", size = 0.4, lty = 2, alpha = 0.2)


propPlotLowOnly <- ggplot(data, aes(x = sumLowBefore - sumHighBefore,
y = activityScore)) + geom_jitter(size = 0.8, alpha = 0.3) +
geom_smooth(method = "lm", color = "skyblue", fill = "skyblue",
size = 0.7, alpha = 0.8) + th + xlab("wide only attacks before") +
ylab("proportional activity change") + labs(title = "Impact of wide only attacks on proportional activity",
subtitle = "n=3673") + scale_y_continuous(limits = c(-1,
10)) + geom_hline(yintercept = 0, col = "red", size = 0.2,
lty = 3) + scale_x_continuous(breaks = 1:15, limits = c(-1,
10)) + geom_smooth(color = "grey", size = 0.4, lty = 2, alpha = 0.2)
```

``` r
propPlotHigh
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/propPlotHigh-1.png" width="100%" style="display: block; margin: auto;" />

``` r
propPlotLow
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/propPlotLow-1.png" width="100%" style="display: block; margin: auto;" />

``` r
propPlotLowOnly
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/propPlotLowOnly-1.png" width="100%" style="display: block; margin: auto;" />






### Classical estimation



Next, we focus on the uncertainties involved. One standard way  to estimate them is to use a one-sample t-tests to estimate the means and $95\%$ confidence intervals for `activityDifference` for different numbers of attacks (this would be  equivalent to running a paired t-test for activity before and activity after).


Here is the analysis for narrow attacks. There were not enough observations  for attacks above 8 (3, 2, and 2 for 9, 10 and 11 attacks, and single observations for a few higher non-zero counts)  for a t-test to be useful, so  we run the t-test for up to 8 attacks.






``` r
counts <- t(table(data$sumHighBefore))
counts <- rbind(counts, counts)
counts[1, ] <- as.numeric(colnames(counts))
colnames(counts) <- NULL
rownames(counts) <- c("no. of attacks", "count")
counts
```
    ##                [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
    ## no. of attacks    0    1    2    3    4    5    6    7    8     9    10    11
    ## count          2831  530  147   61   35   22   17    8    4     3     2     2
    ##                [,13] [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21]
    ## no. of attacks    12    13    14    16    17    19    25    26    27
    ## count              1     1     1     3     1     1     1     1     1




We proceed as follows. We list 9  options of the number of attacks and initiate vectors to which we will save the confidence interval limits (`low`, `high`), the estimated mean and the $p$-value. We use the same approach to analyze  the other types of attacks.


``` r
attacks <- 0:8
max <- max(attacks)
low <- numeric(max + 1)
high <- numeric(max + 1)
m <- numeric(max + 1)
p <- numeric(max + 1)
t <- list()
for (attacks in attacks) {
t[[attacks + 1]] <- t.test(data[data$sumHighBefore == attacks,]$activityDiff)
  low[attacks + 1] <- t[[attacks + 1]]$conf.int[1]
  high[attacks + 1] <- t[[attacks + 1]]$conf.int[2]
  m[attacks + 1] <- t[[attacks + 1]]$estimate
  p[attacks + 1] <- t[[attacks + 1]]$p.value
  }
highTable <- as.data.frame(round(rbind(0:8, low, m, high, p),3))
rownames(highTable) <- c("attacks", "CIlow", "estimated m", "CIhigh",
"p-value")
```


``` r
highTableLong <- round(data.frame(attacks = 0:8, low, m, high,p), 3)
highTableBar <- ggplot(highTableLong) + geom_bar(aes(x = attacks,
  y = m), stat = "identity", fill = "skyblue", alpha = 0.5) +
  geom_errorbar(aes(x = attacks, ymin = low, ymax = high),
  width = 0.4, colour = "seashell3", alpha = 0.9, size = 0.3) +
  th + xlab("narrow attacks") + ylab("mean activity change") +
  geom_text(aes(x = attacks, y = low - 20, label = p), size = 2) +
  labs(title = "Mean impact of narrow attacks on  weekly activity",
  subtitle = "with 95% confidence intervals and p-values") +
  scale_x_continuous(labels = 0:8, breaks = 0:8)

highTableBar6 <- ggplot(highTableLong[highTableLong$attacks <
  6, ]) + geom_bar(aes(x = attacks, y = m), stat = "identity",
  fill = "skyblue", alpha = 0.5) + geom_errorbar(aes(x = attacks,
  ymin = low, ymax = high), width = 0.4, colour = "seashell3",
  alpha = 0.9, size = 0.3) + th + xlab("narrow attacks") +
  ylab("mean activity change") + geom_text(aes(x = attacks,
  y = low - 20, label = p), size = 2) + labs(title = "Mean impact of narrow attacks <6 on  weekly activity",
  subtitle = "with 95% confidence intervals and p-values") +
  scale_x_continuous(labels = 0:5, breaks = 0:5)

highTableBar3 <- ggplot(highTableLong[highTableLong$attacks <
  3, ]) + geom_bar(aes(x = attacks, y = m), stat = "identity",
  fill = "skyblue", alpha = 0.5) + geom_errorbar(aes(x = attacks,
  ymin = low, ymax = high), width = 0.4, colour = "seashell3",
  alpha = 0.9, size = 0.3) + th + xlab("narrow attacks") +
  lab("mean activity change") + geom_text(aes(x = attacks,
  y = low - 5, label = p), size = 2) + labs(title = "Mean impact of narrow attacks <3 on  weekly activity",
  subtitle = "with 95% confidence intervals and p-values") +
  scale_x_continuous(labels = 0:2, breaks = 0:2)
```

``` r
attacks <- 0:8
max <- max(attacks)
lowL <- numeric(max + 1)
highL <- numeric(max + 1)
mL <- numeric(max + 1)
pL <- numeric(max + 1)
tL <- list()

for (attacks in attacks) {
  tL[[attacks + 1]] <- t.test(data[data$sumLowBefore == attacks,]$activityDiff)

  lowL[attacks + 1] <- t[[attacks + 1]]$conf.int[1]
  highL[attacks + 1] <- t[[attacks + 1]]$conf.int[2]
  mL[attacks + 1] <- t[[attacks + 1]]$estimate
  pL[attacks + 1] <- t[[attacks + 1]]$p.value
  }
lowTable <- as.data.frame(round(rbind(0:8, lowL, mL, highL, pL),3))
ownames(lowTable) <- c("attacks", "CIlow", "estimated m", "CIhigh","p-value")

attacks <- 0:8
max <- max(attacks)
lowLo <- numeric(max + 1)
highLo <- numeric(max + 1)
mLo <- numeric(max + 1)
pLo <- numeric(max + 1)
tLo <- list()

for (attacks in attacks) {
tLo[[attacks + 1]] <- t.test(data[data$sumLowBefore - data$sumHighBefore == attacks, ]$activityDiff)
lowLo[attacks + 1] <- t[[attacks + 1]]$conf.int[1]
highLo[attacks + 1] <- t[[attacks + 1]]$conf.int[2]
mLo[attacks + 1] <- t[[attacks + 1]]$estimate
pLo[attacks + 1] <- t[[attacks + 1]]$p.value
}

lowOnlyTable <- as.data.frame(round(rbind(0:8, lowLo, mLo, highLo, pLo), 3))
rownames(lowTable) <- c("attacks", "CIlow", "estimated m", "CIhigh",
"p-value")
```

``` r
lowTableLong <- round(data.frame(attacks = 0:8, lowL, mL, highL, pL), 3)
lowTableBar <- ggplot(lowTableLong) + geom_bar(aes(x = attacks, y = m),
stat = "identity", fill = "skyblue", alpha = 0.5) +
geom_errorbar(aes(x = attacks, ymin = low, ymax = high),
width = 0.4, colour = "seashell3", alpha = 0.9, size = 0.3) +
th + xlab("wide attacks") + ylab("mean activity change") +
geom_text(aes(x = attacks, y = low - 20, label = round(p,
3)), size = 2) + labs(title = "Mean impact of wide attacks on  weekly activity",
subtitle = "with 95% confidence intervals and p-values") +
scale_x_continuous(labels = 0:8, breaks = 0:8)
lowOnlyTableLong <- round(data.frame(attacks = 0:8, lowLo, mLo, highLo, pLo), 3)

lowOnlyTableBar <- ggplot(lowOnlyTableLong) + geom_bar(aes(x = attacks,
y = m), stat = "identity", fill = "skyblue", alpha = 0.5) +
geom_errorbar(aes(x = attacks, ymin = low, ymax = high),
width = 0.4, colour = "seashell3", alpha = 0.9, size = 0.3) +
th + xlab("wide only attacks") + ylab("mean activity change") +
geom_text(aes(x = attacks, y = low - 20, label = round(p,
3)), size = 2) + labs(title = "Mean impact of wide only attacks on  weekly activity",
subtitle = "with 95% confidence intervals and p-values") +
scale_x_continuous(labels = 0:8, breaks = 0:8)\
```

T-test based estimates for activity change divided by numbers of narrow attacks received:

    ##                 V1      V2      V3      V4      V5       V6       V7       V8
    ## attacks      0.000   1.000   2.000   3.000   4.000    5.000    6.000    7.000
    ## CIlow       -3.154 -12.658 -23.390 -45.991 -94.861 -108.030 -169.527 -108.555
    ## estimated m -2.140  -8.251 -12.646 -25.607 -59.400  -60.864  -80.882  -46.125
    ## CIhigh      -1.125  -3.844  -1.902  -5.222 -23.939  -13.697    7.762   16.305
    ## p-value      0.000   0.000   0.021   0.015   0.002    0.014    0.071    0.124
    ##                   V9
    ## attacks        8.000
    ## CIlow       -144.273
    ## estimated m  -46.750
    ## CIhigh        50.773
    ## p-value        0.225

T-test based estimates for activity change divided by numbers of wide attacks received:

    ##                 V1      V2      V3      V4      V5       V6       V7       V8
    ## attacks      0.000   1.000   2.000   3.000   4.000    5.000    6.000    7.000
    ## CIlow       -3.154 -12.658 -23.390 -45.991 -94.861 -108.030 -169.527 -108.555
    ## estimated m -2.140  -8.251 -12.646 -25.607 -59.400  -60.864  -80.882  -46.125
    ## CIhigh      -1.125  -3.844  -1.902  -5.222 -23.939  -13.697    7.762   16.305
    ## p-value      0.000   0.000   0.021   0.015   0.002    0.014    0.071    0.124
    ##                   V9
    ## attacks        8.000
    ## CIlow       -144.273
    ## estimated m  -46.750
    ## CIhigh        50.773
    ## p-value        0.225

T-test based estimates for activity change divided by numbers of wide only attacks received:

    ##            V1      V2      V3      V4      V5       V6       V7       V8
    ##         0.000   1.000   2.000   3.000   4.000    5.000    6.000    7.000
    ## lowLo  -3.154 -12.658 -23.390 -45.991 -94.861 -108.030 -169.527 -108.555
    ## mLo    -2.140  -8.251 -12.646 -25.607 -59.400  -60.864  -80.882  -46.125
    ## highLo -1.125  -3.844  -1.902  -5.222 -23.939  -13.697    7.762   16.305
    ## pLo     0.000   0.000   0.021   0.015   0.002    0.014    0.071    0.124
    ##              V9
    ##           8.000
    ## lowLo  -144.273
    ## mLo     -46.750
    ## highLo   50.773
    ## pLo       0.225





We represent this information in barplots. Two of them are restricted to different numbers of attacks for legibility. The printed values below bars represent $p$-values, not the estimated means.



``` r
highTableBar
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/highTableBar-1.png" width="100%" style="display: block; margin: auto;" />

``` r
highTableBar6
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/highTableBar6-1.png" width="100%" style="display: block; margin: auto;" />

``` r
highTableBar3
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/highTableBar3-1.png" width="100%" style="display: block; margin: auto;" />

``` r
lowTableBar
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/lowTableBar-1.png" width="100%" style="display: block; margin: auto;" />

``` r
lowOnlyTableBar
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/lowOnlyTableBar-1.png" width="100%" style="display: block; margin: auto;" />


Now, let's take a look at power calculations for higher numbers of attacks.

``` r
h6 <- data[data$sumHighBefore == 6, ]
h7 <- data[data$sumHighBefore == 7, ]
h8 <- data[data$sumHighBefore == 8, ]

# power for 6 attacks
a <- mean(data$activityDiff)
s <- sd(h7$activityDiff)
n <- 8
error <- qt(0.975, df = n - 1) * s/sqrt(n)
left <- a - error
right <- a + error
assumed <- a - 80
tleft <- (left - assumed)/(s/sqrt(n))
tright <- (right - assumed)/(s/sqrt(n))
p <- pt(tright, df = n - 1) - pt(tleft, df = n - 1)
power6 <- 1 - p
power6
```

    ## [1] 0.7369795

``` r
# power for 7 attacks
a <- mean(data$activityDiff)
s <- sd(h7$activityDiff)
n <- 8
error <- qt(0.975, df = n - 1) * s/sqrt(n)
left <- a - error
right <- a + error
assumed <- a - 80
tleft <- (left - assumed)/(s/sqrt(n))
tright <- (right - assumed)/(s/sqrt(n))
p <- pt(tright, df = n - 1) - pt(tleft, df = n - 1)
power7 <- 1 - p
power7
```

    ## [1] 0.7369795

``` r
# power for 8 attacks
a <- mean(data$activityDiff)
s <- sd(h8$activityDiff)
n <- 4
error <- qt(0.975, df = n - 1) * s/sqrt(n)
left <- a - error
right <- a + error
assumed <- a - 80
tleft <- (left - assumed)/(s/sqrt(n))
tright <- (right - assumed)/(s/sqrt(n))
p <- pt(tright, df = n - 1) - pt(tleft, df = n - 1)
power8 <- 1 - p
power8
```

    ## [1] 0.3088534








Note fairly wide confidence intervals for higher number of attacks. These arise because attacks are quite rare, so the sample sizes for 5, 6, 7 and 8 narrow attacks are  22, 17,  8  and  4 respectively. This might be also the reason why $p$-values are not too low (although still below the usual significance thresholds). In fact, power analysis shows that if the real activity difference for those groups equals to the mean for `narrow = 6`, that is, -80, the probabilities that this effect would be discovered by a single sample t-test for 6, 7, and 8 attacks are  0.737, 0.737, 0.309, and so tests for higher numbers of attacks are underpowered.




We run single t-tests on different groups to estimate different means and we don't use t-test for hypothesis testing. To alleviate concerns about multiple testing and increased risk of type I error, we also performed an ANOVA tests, which strongly suggest non-random correlation between the numbers of attacks and activity change. Furthermore, 80 comparison rows in Tukey's Honest Significance Test (Tukey, 1949) have conservatively adjusted p-value below 0.05.






``` r
highAnova <- aov(activityDiff ~ as.factor(sumHighBefore), data = data)
lowAnova <- aov(activityDiff ~ as.factor(sumLowBefore), data = data)
lowOnlyAnova <- aov(activityDiff ~ as.factor(sumLowBefore - sumHighBefore),
    data = data)

library(descr, quietly = TRUE)
library(pander, quietly = TRUE)
library(papeR, quietly = TRUE)
sh <- xtable(summary(highAnova))
rownames(sh) <- c("narrow", "residuals")
sh
```



|           | Df   | Sum Sq     | Mean Sq  | F value | Pr($>$F) |
|-----------|------|------------|----------|---------|----------|
| narrow    | 20   | 785495.95  | 39274.80 | 25.03   | 0.0000   |
| residuals | 3652 | 5730212.39 | 1569.06  |         |          |


``` r
sl <- xtable(summary(lowAnova))
rownames(sl) <- c("narrow", "residuals")
sl
```


|           | Df   | Sum Sq     | Mean Sq  | F value | Pr($>$F) |
|-----------|------|------------|----------|---------|----------|
| narrow    | 42   | 1103365.53 | 26270.61 | 17.62   | 0.0000   |
| residuals | 3630 | 5412342.81 | 1491.00  |         |          |




``` r
so <- xtable(summary(lowOnlyAnova))
rownames(so) <- c("narrow", "residuals")
so
```

|           | Df   | Sum Sq     | Mean Sq  | F value | Pr($>$F) |
|-----------|------|------------|----------|---------|----------|
| narrow    | 35   | 1106625.41 | 31617.87 | 21.26   | 0.0000   |
| residuals | 3637 | 5409082.93 | 1487.24  |         |          |





We used t-tests, which one might think assumes normality, to estimate means  for distributions, which in fact  are not normal. However, what t-test assumes is the normality of the sampling distribution, and this condition is much easier to satisfy thanks to the central limit theorem.
See for instance the following figure:

``` r
means <- numeric(10000)
for (run in 1:10000) {
    means[run] <- mean(sample(data[data$sumHighBefore == 2, ]$activityDiff,
        30))
}
distr <- ggplot(data[data$sumHighBefore == 2, ], aes(x = activityDiff)) +
    geom_histogram(bins = 100) + th + ggtitle("Distribution of activityDiff for narrow attacks before = 2")


sampDistr <- ggplot() + geom_histogram(aes(x = means), bins = 100) +
    th + ggtitle("Simulated sampling distribution for the same  with n=30 and 10 000 runs")
```






``` r
distr
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/distr-1.png" style="display: block; margin: auto;" />

``` r
sampDistr
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/sampDistr-1.png" style="display: block; margin: auto;" />









There are, however, some reasons to be concerned with classical methods:


- $p$-values and confidence intervals are hard to interpret intuitively. For instance, a $95\%$ confidence interval being $(x,y)$ does not mean that the true mean is within $(x,y)$ with probability $0.95$, and the estimated mean being $z$ with $p$-value $0.01$ does not  mean that the true
population mean is $z$ with probability $0.99$.

-   *p*-values and confidence intervals are sensitive to undesirable factors, such as stopping intention in experiment design (Kruschke, 2015).

- Classical hypothesis tests require several assumptions to hold which sometimes do not hold in reality, and the choice of  significance thresholds is arbitrary.

-  Crucially, probabilities reported in classical analysis are probabilities of the data on the assumption of the null hypothesis (e.g., that the true mean is 0), not the posterior probabilities of the true parameter values given the data. To obtain these, we used Bayesian analysis and studied the impact of skeptical prior probabilities on how the data  impacts the posterior distribution of the parameters at hand.




### Bayesian estimation



We used Markov Chain Monte Carlo methods (using the [Bayesian Estimation Supersedes the t-Test](https://www.rdocumentation.org/packages/BEST/versions/0.5.2)  package) to estimate the posterior probability distribution for mean changes in activity
in different groups. Note you have to have JAGS installed on your computer to run the simulations, and be aware that
 some of the computations are pretty demanding and will take time.
We did this for  three different fairly skeptical normal prior distributions (which we call
`wide, informed`, and `fit`), whose means and standard deviations are respectively $(0,50), (-1.11,44.47)$ and $(-1.11,7.5)$ (read on for an explanation why).



``` r
library(BEST)
priorsWide <- list(muM = 0, muSD = 50)
priorsInformed <- list(muM = -1.11, muSD = 44.47)
priorsFit <- list(muM = -1.11, muSD = 7.5)
```


These are all normal distributions with different parameters. We follow the usual practice of presenting a Bayesian analysis with a range of options: the reader is to judge with prior seems most plausible to them, and then modify their beliefs according to the impact the data has on their prior convictions. The wide prior assumes that the most likely difference is 0, but the standard deviation is quite large, the informed prior takes the mean and standard deviation of the whole study group, whereas the fit prior follows fairly closely the actual empirical distribution of activity change values. All of them are fairly skeptical because they expect the change to be the same for every user, and they expect this change to be near 0.  We did not use  a completely uninformed uniform prior, because it is  not sufficiently skeptical, being more easily impacted by the data, and because it has some undesirable mathematical properties. See [Don’t Use Uniform Priors. They statistically don’t make sense](https://towardsdatascience.com/stop-using-uniform-priors-47473bdd0b8a) for an accessible explanation. Our priors look as follows  (note that the fitted prior looks similar when the $x$ scale is different to make the fitting more clearly visible; we also visualise the fitted prior in the same scale as the other priors).   Here's the visualisation code for the wide priors, code for the other priors is analogous.



``` r
priorWide <- ggplot(data = data.frame(x = c(-200, 200)), aes(x)) +
    stat_function(fun = dnorm, args = list(mean = 0, sd = 50)) +
    ylab("") + scale_y_continuous(breaks = NULL) + th + xlab("expected activity change") +
    labs(title = "Wide prior", subtitle = "Normal prior with m = 0, sd = 50")
priorWide
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/priorWide-1.png" width="100%" style="display: block; margin: auto;" />

``` r
priorFit <- ggplot(data = data.frame(x = c(-100, 100)), aes(x)) +
    stat_function(fun = dnorm, args = list(mean = -1.11, sd = 7.5)) +
    ylab("") + scale_y_continuous(breaks = NULL) + th + xlab("expected activity change") +
    labs(title = "Fitted prior, zoomed in", subtitle = "Normal prior with m = 0, sd = 10, empirical distribution in gray") +
    geom_density(data = data, aes(x = activityDiff), col = "grey",
        alpha = 0.6) + xlim(c(-80, 80))
priorFit
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/priorFit-1.png" width="100%" style="display: block; margin: auto;" />

``` r
priorInformative <- ggplot(data = data.frame(x = c(-200, 200)),
    aes(x)) + stat_function(fun = dnorm, args = list(mean = -1.11,
    sd = 44.47)) + ylab("") + scale_y_continuous(breaks = NULL) +
    th + xlab("expected activity change") + labs(title = "Informative prior",
    subtitle = "Normal prior with m = -1.11 and sd = 44.47")
priorInformative
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/priorInformative-1.png" width="100%" style="display: block; margin: auto;" />

``` r
priorFitted <- ggplot(data = data.frame(x = c(-100, 100)), aes(x)) +
    stat_function(fun = dnorm, args = list(mean = -1.11, sd = 7.5)) +
    ylab("") + scale_y_continuous(breaks = NULL) + th + xlab("expected activity change") +
    labs(title = "Fitted prior", subtitle = "Normal prior with m = 0, sd = 10, empirical distribution in gray") +
    geom_density(data = data, aes(x = activityDiff), col = "grey",
        alpha = 0.6) + xlim(c(-200, 200))
priorFitted
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/priorFitted-1.png" width="100%" style="display: block; margin: auto;" />







First, it will be convenient to split and extract the data (we will do the Bayesian analysis for narrow attacks:


``` r
sh0 <- data[data$sumHighBefore == 0, ]$activityDiff
sh1 <- data[data$sumHighBefore == 1, ]$activityDiff
sh2 <- data[data$sumHighBefore == 2, ]$activityDiff
sh3 <- data[data$sumHighBefore == 3, ]$activityDiff
sh4 <- data[data$sumHighBefore == 4, ]$activityDiff
sh5 <- data[data$sumHighBefore == 5, ]$activityDiff
sh6 <- data[data$sumHighBefore == 6, ]$activityDiff
sh7 <- data[data$sumHighBefore == 7, ]$activityDiff
sh8 <- data[data$sumHighBefore == 8, ]$activityDiff
```



As an example, we go over in detail over the estimation of the impact of data for three narrow attacks on the wide prior. We   built the sample of simulated posterior probabilities and plotted the t-distributions of 30 random steps in the approximation process together with a histogram of the data, so that we can estimate how the simulations converged. Actually, since BEST simulations are computationally heavy, we have already run them separately and loaded the file with the results. However, normally you would use a line like the one listed (these simulations take the more time the larger the dataset).



``` r
mc3w <- BESTmcmc(sh3, priors = priorsWide)
plotPostPred(mc3w)
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/unnamed-chunk-28-1.png" width="100%" style="display: block; margin: auto;" />




``` r
print(mc3w)
```

    ## MCMC fit results for BEST analysis:
    ## 100002 simulations saved.
    ##          mean     sd  median    HDIlo  HDIup  Rhat n.eff
    ## mu    -24.476  9.063 -24.327 -42.4760 -6.859 1.000 48767
    ## nu      9.626 12.988   5.275   0.9163 33.326 1.001  2867
    ## sigma  61.398 11.533  61.081  39.6685 84.249 1.000  9530
    ##
    ## 'HDIlo' and 'HDIup' are the limits of a 95% HDI credible interval.
    ## 'Rhat' is the potential scale reduction factor (at convergence, Rhat=1).
    ## 'n.eff' is a crude measure of effective sample size.




     The simulation, among other things, produces out a sample of 100002 simulated posterior distributions of the mean, \textsf{mu}. Below we briefly describe how the algorithm works.

- Potential parameter  candidates are "locations"  that can be visited, and as the algorithm "travels", it writes down the  visited ones.
- The algorithm starts with a random candidate $\theta_1$ for a parameter, say, that the real mean activity change is 5. It writes down $\theta_1$ in the list of "places visited".
- It calculates the probability (density) for the observations  on the assumption that $\theta_1$ indeed is the real mean activity change. This results in $p(D\vert \theta_1)$.
-  However, what is important is $p(\theta_1\vert D)$, the extent to which the data supports the claim that $\theta_1$ is the right parameter. This, by Bayes' Theorem, is related to $p(D\vert \theta_1)$ by means of  Bayes' theorem:
\begin{align}\label{eq:bayes}
p(\theta_1\vert D) & = \frac{p(D\vert \theta_1)p(\theta)}{p(D)}
\end{align}
- Now, $p(D)$ is notoriously difficult to calculate in many cases. The general problem is that  for the continuous case we have $p(D) = \int d \theta p(D\vert \theta) p (\theta)$ and this integral can be analytically unsolvable.
However,  what the simulation needs is something proportional to the right-hand side above, and so what is used is simply $p(D\vert \theta_1)p(\theta)$: the likelihood times the prior. We call the result $p'(\theta_1)$.
- Next, the algorithm considers another randomly drawn potential parameter $\theta_2$ in the neighborhood of $\theta_1$ and calculates $p'(\theta_2)$. If it is greater than $p'(\theta_1)$, it moves its location to $\theta_2$ with certainty, otherwise, it decides to move there with some non-extreme probability only (its value is a meta-parameter chosen to optimize the algorithm convergence).
- Then it draws randomly another potential parameter in the neighborhood of wherever it ended up, and proceeds as before. This is done for a large number of steps.
- The procedure, repeated multiple  times, yields a long list of potential parameters visited, and the frequency with which the algorithm visits certain range of potential parameters is proportionate to the probability of these parameters being the true parameters given the data. After normalization,  a probability density for the list is obtained: this is the estimated posterior probability distribution  for the parameter in question.





For instance, it is possible to  look at which potential parameters the simulation for three attacks visited.  Let's look at  the first 100 steps in the full simulation and parameters visited at every 50th step (because a plot of all 100000 steps would not be clearly readable).

``` r
ggplot() + geom_line(aes(x = 1:length(mc3w$mu[1:100]), y = mc3w$mu[1:100]),
    alpha = 0.7) + th + xlab("initial steps in the chain") +
    ylab("potential parameter") + labs(title = "Convergence plot for first 100 steps in MCMC",
    subtitle = "Wide prior, three attacks")
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/convergencePlot-1.png" width="100%" style="display: block; margin: auto;" />




``` r
conv <- mc3w$mu[seq(1, length(mc3w$mu), by = 50)]
ggplot() + geom_line(aes(x = 1:length(conv), y = conv), alpha = 0.7) +
    th + xlab("steps in the selection") + ylab("potential parameter") +
    labs(title = "Convergence plot for every 50th step in MCMC",
        subtitle = "Wide prior, three attacks")
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/chainDiagnostics-1.png" width="100%" style="display: block; margin: auto;" />





We can collapse the plot, ignoring time, with default \textsf{BESTmcmc} features and obtain the following plot:

``` r
plot(mc3w)
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/collapsed-1.png" width="100%" style="display: block; margin: auto;" />



The histogram illustrates the distribution of the outcome. HDI with limits is displayed at the bottom, and the information in green represents the  proportion of the sample that is below 0.

`Rhat` is a convergence measure, if the process goes well it should be around 1, and `n.eff` is the effective sample size - which is often lower than the actual full sample because of autocorrelation (in simulations next guess for a parameter depends to some extent on what the previous guess was, and the calculation of `n.eff` corrects for this).



Visual inspection of Figure reveals that the most visited locations (potential mean activity drops) center around slightly less than minus twenty. In fact, the mean of those visited potential means is -24.476 (although this does not have to be the mean of the sample, which in our case is -25.6065574; rather it is the result of a compromise between the data and the prior). The median is very close. The new elements are HDIlo and HDIup, the limits of the *Highest Density Inverval*: the range of values that are most credible and cover 95% of the distribution. The Values within the 95% HDI are more credible than values outside the HDI, and the values inside it have a total probability of $95\%$. Crucially, these can be interpreted as posterior probabilities of various mean candidates being the population means based on the data, which makes HDI much unlike standard confidence intervals.


Having  gone over a detailed example, we present the results of the Bayesian analysis for 0-9 narrow attacks (and a simulated prior) for three types of priors and the barplot of the means of posterior distributions (not the means of the data, but a result of a compromise between the  priors and the data) with HDIs.






``` r
# prepare data and plot densities
bayesianWideDF <- data.frame(a0 = mc0w$mu, a1 = mc1w$mu, a2 = mc2w$mu,
    a3 = mc3w$mu, a4 = mc4w$mu, a5 = mc5w$mu, a6 = mc6w$mu, a7 = mc7w$mu,
    a8 = mc8w$mu)
bayesianWideDF$prior <- rnorm(nrow(bayesianWideDF), mean = 0,
    sd = 50)
BayesianWideDFLong <- gather(bayesianWideDF)
BayesianWideDFLong$key <- as.factor(BayesianWideDFLong$key)
BayesianWideDensities <- ggplot(BayesianWideDFLong, aes(x = value,
    group = key, color = key, fill = key)) + geom_density(alpha = 0.2) +
    th + xlab("activity change") + scale_fill_discrete(name = "group",
    labels = c("0 attacks", "1 attack", "2 attacks", "3 attacks",
        "4 attacks", "5 attacks", "6 attacks", "7 attacks", "8 attacks",
        "prior")) + guides(color = FALSE, size = FALSE) + ylim(c(0,
    0.11)) + xlim(c(-90, 60)) + labs(title = "Impact of data on wide prior",
    subtitle = "narrow attacks vs. activity change")

# extract means and HDI limits from the mcmc objects
wideMeans <- numeric(9)
wideLow <- numeric(9)
wideHigh <- numeric(9)
for (i in 1:9) {
    wideMeans[i] <- eval(parse(text = paste("summary(mc", i -
        1, "w)[1]", sep = "")))
    wideLow[i] <- eval(parse(text = paste("summary(mc", i - 1,
        "w)[21]", sep = "")))
    wideHigh[i] <- eval(parse(text = paste("summary(mc", i -
        1, "w)[26]", sep = "")))
}

# order the data and make the barplot
wideBayesTable <- round(data.frame(attacks = 0:8, wideLow, wideMeans,
    wideHigh), 3)
wideBayesBar <- ggplot(wideBayesTable) + geom_bar(aes(x = attacks,
    y = wideMeans), stat = "identity", fill = "skyblue", alpha = 0.5) +
    geom_errorbar(aes(x = attacks, ymin = wideLow, ymax = wideHigh),
        width = 0.4, colour = "seashell3", alpha = 0.9, size = 0.3) +
    th + xlab("narrow attacks") + ylab("activity change") + geom_text(aes(x = attacks +
    0.24, y = wideMeans - 3, label = round(wideMeans, 1)), size = 3) +
    scale_x_continuous(labels = 0:8, breaks = 0:8)
```

``` r
bayesianFittedDF <- data.frame(a0 = mc0f$mu, a1 = mc1f$mu, a2 = mc2f$mu,
    a3 = mc3f$mu, a4 = mc4f$mu, a5 = mc5f$mu, a6 = mc6f$mu, a7 = mc7f$mu,
    a8 = mc8f$mu)
bayesianFittedDF$prior <- rnorm(nrow(bayesianFittedDF), mean = -1.11,
    sd = 7.5)
BayesianFittedDFLong <- gather(bayesianFittedDF)
BayesianFittedDFLong$key <- as.factor(BayesianFittedDFLong$key)
BayesianFittedDensities <- ggplot(BayesianFittedDFLong, aes(x = value,
    group = key, color = key, fill = key)) + geom_density(alpha = 0.2) +
    th + xlab("activity change") + scale_fill_discrete(name = "group",
    labels = c("0 attacks", "1 attack", "2 attacks", "3 attacks",
        "4 attacks", "5 attacks", "6 attacks", "7 attacks", "8 attacks",
        "prior")) + guides(color = FALSE, size = FALSE) + ylim(c(0,
    0.12)) + xlim(c(-35, 20)) + labs(title = "Impact of data on fitted prior",
    subtitle = "narrow attacks vs. activity change")

fittedMeans <- numeric(9)
fittedLow <- numeric(9)
fittedHigh <- numeric(9)

for (i in 1:9) {
    fittedMeans[i] <- eval(parse(text = paste("summary(mc", i -
        1, "f)[1]", sep = "")))
    fittedLow[i] <- eval(parse(text = paste("summary(mc", i -
        1, "f)[21]", sep = "")))
    fittedHigh[i] <- eval(parse(text = paste("summary(mc", i -
        1, "f)[26]", sep = "")))
}

fittedBayesTable <- round(data.frame(attacks = 0:8, fittedLow,
    fittedMeans, fittedHigh), 3)
fittedBayesBar <- ggplot(fittedBayesTable) + geom_bar(aes(x = attacks,
    y = fittedMeans), stat = "identity", fill = "skyblue", alpha = 0.5) +
    geom_errorbar(aes(x = attacks, ymin = fittedLow, ymax = fittedHigh),
        width = 0.4, colour = "seashell3", alpha = 0.9, size = 0.3) +
    th + xlab("narrow attacks") + ylab("activity change") + geom_text(aes(x = attacks +
    0.25, y = fittedMeans - 1, label = round(fittedMeans, 1)),
    size = 3) + scale_x_continuous(labels = 0:8, breaks = 0:8)


# ______________________________
bayesianInformativeDF <- data.frame(a0 = mc0i$mu, a1 = mc1i$mu,
    a2 = mc2i$mu, a3 = mc3i$mu, a4 = mc4i$mu, a5 = mc5i$mu, a6 = mc6i$mu,
    a7 = mc7i$mu, a8 = mc8i$mu)
bayesianInformativeDF$prior <- rnorm(nrow(bayesianInformativeDF),
    mean = -1.11, sd = 44.47)
BayesianInformativeDFLong <- gather(bayesianInformativeDF)
BayesianInformativeDFLong$key <- as.factor(BayesianInformativeDFLong$key)
BayesianInvormativeDensities <- ggplot(BayesianInformativeDFLong,
    aes(x = value, group = key, color = key, fill = key)) + geom_density(alpha = 0.2) +
    th + xlab("activity change") + scale_fill_discrete(name = "group",
    labels = c("0 attacks", "1 attack", "2 attacks", "3 attacks",
        "4 attacks", "5 attacks", "6 attacks", "7 attacks", "8 attacks",
        "prior")) + guides(color = FALSE, size = FALSE) + ylim(c(0,
    0.11)) + xlim(c(-90, 50)) + labs(title = "Impact of data on Informative prior",
    subtitle = "narrow attacks vs. activity change")


InformativeMeans <- numeric(9)
InformativeLow <- numeric(9)
InformativeHigh <- numeric(9)

for (i in 1:9) {
    InformativeMeans[i] <- eval(parse(text = paste("summary(mc",
        i - 1, "i)[1]", sep = "")))
    InformativeLow[i] <- eval(parse(text = paste("summary(mc",
        i - 1, "i)[21]", sep = "")))
    InformativeHigh[i] <- eval(parse(text = paste("summary(mc",
        i - 1, "i)[26]", sep = "")))
}

InformativeBayesTable <- round(data.frame(attacks = 0:8, InformativeLow,
    InformativeMeans, InformativeHigh), 3)

InformativeBayesBar <- ggplot(InformativeBayesTable) + geom_bar(aes(x = attacks,
    y = InformativeMeans), stat = "identity", fill = "skyblue",
    alpha = 0.5) + geom_errorbar(aes(x = attacks, ymin = InformativeLow,
    ymax = InformativeHigh), width = 0.4, colour = "seashell3",
    alpha = 0.9, size = 0.3) + th + xlab("narrow attacks") +
    ylab("activity change") + geom_text(aes(x = attacks + 0.25,
    y = InformativeMeans - 3, label = round(InformativeMeans,
        1)), size = 3) + scale_x_continuous(labels = 0:8, breaks = 0:8)
```

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/BayesianWideDensities-1.png" width="100%" style="display: block; margin: auto;" />

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/wideBayesBar-1.png" width="100%" style="display: block; margin: auto;" />

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/BayesianFittedDensities-1.png" width="100%" style="display: block; margin: auto;" />

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/fittedBayesBar-1.png" width="100%" style="display: block; margin: auto;" />

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/BayesianInvormativeDensitie-1.png" width="100%" style="display: block; margin: auto;" />

<img src="https://rfl-urbaniak.github.io/redditAttacks/images/InformativeBayesBar-1.png" width="100%" style="display: block; margin: auto;" />




























### References

Ptaszyński, M., Leliwa, G., Piech, M., & Smywiński-Pohl, A. (2018). Cyberbullying detection–technical report 2/2018, Department of Computer Science AGH, University of Science and Technology. *arXiv Preprint arXiv:1808.00926*.

Wroczynski, M., & Leliwa, G. (2019). *System and method for detecting undesirable and potentially harmful online behavior*. Google Patents.

[1] <https://www.samurailabs.ai/>, described in (Ptaszyński, Leliwa, Piech, & Smywiński-Pohl, 2018; Wroczynski & Leliwa, 2019).
