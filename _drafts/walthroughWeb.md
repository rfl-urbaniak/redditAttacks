blah
================

 We conduct a large scale data-driven analysis of the effects of online personal attacks on social media user activity. First, we perform a thorough overview of the literature on the influence of social media on user behavior, especially on the impact that negative and aggressive behaviors, such as harassment and cyberbullying, have on users’ engagement in online media platforms. The majority of previous research were small-scale self-reported studies, which is their limitation. This motivates our data-driven study. We perform a large-scale analysis of messages from Reddit, a discussion website, for a period of two weeks, involving 182,528 posts or comments to posts by 148,317 users. To efficiently collect and analyze the data we apply a high-precision personal attack detection technology. We analyze the obtained data from three perspectives: (i) classical statistical methods, (ii) Bayesian estimation, and (iii) model-theoretic analysis. The three perspectives agree: personal attacks decrease the victims’ activity. The results can be interpreted as an important signal to social media platforms and policy makers that leaving personal attacks unmoderated is quite likely to disengage the users and in effect depopulate the platform. On the other hand, application of cyberviolence detection technology in combination with various mitigation techniques could improve and strengthen the user community. As more of our lives is taking place online, keeping the virtual space inclusive for all users becomes an important problem which online media platforms need to face.

Remark. In what follows, we sometimes display key pieces of code and explain what it does. Some not too fascinating pieces of code are supressed, but the reader can look them up in the associated .Rmd file and compile their own version.

# Technology applied for personal attack detection

For the need of this research we define personal attack as any kind of abusive remark made in relation to a person (ad hominem) rather than to the content of the argument expressed by that person in a discussion. The definition of ‘personal attack’ subsumes the use of specific terms which compare other people to animals or objects or making nasty insinuations without providing evidence. Three examples of typical personal attacks are as follows.

-   *You are legit mentally retarded homie.*
-   *Eat a bag of dicks, fuckstick.*
-   *Fuck off with your sensitivity you douche.*

The detection of personal attacks was performed using Samurai, a proprietary technology of Samurai Labs.[1]

The following figure illustrates how the input text ("ccant believ he sad ur an id10+...!") is processed step-by-step utilizing both statistical and symbolic methods.

In practice, it means that a whole variety of constructions can be detected without the need to construct a fixed list of dictionary words defined . Due to utilizing symbolic components that oversee statistical components, {Samurai} recognizes complex linguistic phenomena (such as indirect speech, rhetorical figures or counter-factual expressions) to distinguish personal attacks from normal communication, greatly reducing the number of false alarms as compared to others systems used for violence detection. An example of comparison can be seen in Figure , and a full benchmark was presented in (Ptaszyński et al., 2018).

The detection models utilized in this research were designed to detect personal attacks targeted against a second person (e.g. interlocutor, original author of a post) and a third person/group (e.g., other participants in the conversation, people not involved in the conversation, social groups, professional groups), except public figures (e.g. politicians, celebrities). With regards to symbolic component of the system, by "models" we mean separate rules (such as, specifying a candidate for the presence of personal attack, such as the aggressive word "idiot," which is further disambiguated with a syntactic rule of citation, e.g., "\[he|she|they\] said \[SUBJECT\] \[PREDICATE\]") or sets of rules, as seen in Figure , e.g. normalization model contains rules for transcription normalization, citation detection model contains rules for citation, etc. With regards to the statistical component, by "models" refer to machine learning models trained on large data to classify an entry into one of the categories (e.g., true personal attack, or false positive).

Moreover, the symbolic component of the system uses two types of symbolic rules, namely "narrow rules" and "wide rules." The former have smaller coverage (e.g., are triggered less often), but detect messages containing personal attacks with high precision. The latter, have wider coverage, but their precision is lower. We decided to set apart the "narrow" and "wide" subgroups of the detection models in order to increase the granularity of the analysis. Firstly, we took only the detection models designed to detect personal attacks targeted against second person. Secondly, we used these models on a dataset of 320,000 Reddit comments collected on 2019/05/06. Thirdly, we randomly picked at most hundred returned results for each low-level model. Low-level models are responsible for detecting low-level categories. Similarly, mid-level models detect mid-level categories, by combining several low-level models, etc. (Some models are triggered very often while others rarely, so using all instances would create too much bias). There were 390 low-level models but many of them returned in less than 100 results. We verified them manually with the help of expert annotators trained in detection of personal attacks and selected only those models that achieved at least 90% of precision. The models with fewer than 100 returned results were excluded from the selection. After this step, the "narrow" subgroup contained 43 out of 390 low-level models. Finally, we tested all of the "narrow" models on a large dataset of 477,851 Reddit comments collected between 2019/06/01 and 2019/08/31 from two subreddits (r/MensRights and r/TooAfraidToAsk). Each result of the "narrow" models was verified manually by a trained annotator and the "narrow" models collectively achieved over 93.3% of precision. We also tested the rest of the "wide" models on random samples of 100 results for each model (from the previous dataset of 320,000 Reddit comments) and we excluded the models that achieved less than 80% precision. The models with fewer than 100 results were not excluded from the "wide" group. In this simple setup we detected 24,251 texts containing "wide" attacks, where:

The sum exceeds 100% because some of the comments contained personal attacks against both second person and third person / groups. For example, a comment \`\`Fu\*\* you a\*\*hole, you know that girls from this school are real bit\*\*es" contains both types of personal attack.

Additionally, from the original data of 320,000 Reddit posts we extracted and annotated 6,769 Reddit posts as either a personal attack (1) or not (0). To assure that the extracted additional dataset contains a percentage of Personal Attacks sufficient to perform the evaluation, the Reddit posts were extracted with an assumption that each post contains at least one word of a general negative connotation. Our dictionary of such words contains 6,412 instances, and includes a wide range of negative words, such as nouns (e.g., "racist", "death", "idiot", "hell"), verbs (e.g., "attack", "kill", "destroy", "suck"), adjectives (e.g., "old", "thick", "stupid", "sick"), or adverbs (e.g., "spitefully", "tragically", "disgustingly"). In the 6,769 additionally annotated Reddit samples there were 957 actual Personal Attacks (14%), from which Samurai correctly assigned 709 (true positives) and missed 248 (false negatives), which accounts for 74% of the Recall rate. Finally, we performed another additional experiment in which, we used Samurai to annotate completely new 10,000 samples from Discord messages that did not contain Personal Attacks but contained vulgar words. The messages were manually checked by two trained annotators and one additional super-annotator. The result of Samurai on this additional dataset was a 2% of false positive rate, with exactly 202 cases misclassified as personal attacks. This accounts for specificity rate of 98%.

The raw datasets used have been obtained by , who were able to collect posts and comments without moderation or comment removal. All content was downloaded from the data stream provided by which enabled full data dump from Reddit in real-time. The advantage of using it was access to unmoderated data. Further, deployed their personal attacks recognition algorithms to identify personal attacks.

In the study, experimental manipulation of the crucial independent variables (personal attacks of various form) to assess their effect on the dependent variable (users’ change in activity) would be unethical and against the goal of Samurai Labs, which is to detect and *prevent* online violence. While such a lack of control is a weakness as compared to typical experiments in psychology, our sample was both much larger and much more varied than the usual WEIRD (western, educated, and from industrialized, rich, and democratic countries) groups used in psychology. Notice, however, that the majority of Reddit users are based in U.S. For instance, (Wise, Hamman, & Thorson, 2006) examined 59 undergraduates from a political science class at a major Midwestern university in the USA, (Zong, Yang, & Bao, 2019) studied 251 students and faculty members from China who are users of WeChat, and (Valkenburg, Peter, & Schouten, 2006) surveyed 881 young users (10-19yo.) of a Dutch SNS called CU2.

Because of the preponderance of personal attacks online, we could use the real-life data from Reddit and use the following study design:

1.  All the raw data, comprising of daily lists of posts and comments (some of which were used in the study) with time-stamps and author and target user names, have been obtained by Samurai Labs, who also applied their personal attack detection algorithm to them, adding two more variables: narrow and wide. These were the raw datasets used in further analysis.

2.  Practical limitations allowed for data collection for around two continuous weeks (day 0 ± 7 days). First, we randomly selected one weekend day and one working day. These were June 27, 2020 (Saturday, S) and July 02, 2020 (Thursday, R). The activity on those days was used to assign users to groups in the following manner. We picked one weekend and one non-weekend day to correct for activity shifts over the weekend (the data indeed revealed slightly higher activity over the weekends, no other week-day related pattern was observed). We could not investigate (or correct for) monthly activity variations, because the access to unmoderated data was limited.

3.  For each of these days, a random sample of 100,000 posts or comments have been drawn from all content posted on Reddit. Each of these datasets went through preliminary user-name based bots removal. This is a simple search for typical phrases included in user names, such as "Auto", "auto", "Bot", or "bot".

For instance, for our initial thursdayClean datased, this proceeds like this:

``` r
thursdayClean <- thursdayClean[!grepl("Auto", thursdayClean$author,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("auto", thursdayClean$author,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("Auto", thursdayClean$receiver,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("auto", thursdayClean$receiver,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("bot", thursdayClean$receiver,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("Bot", thursdayClean$receiver,
    fixed = TRUE), ]
```

1.  In some cases, content had been deleted by the user or removed by Reddit --- in such cases the dataset only contained information that some content had been posted but was later removed; since we could not access the content of such posts or comments and evaluate them for personal attacks, we also excluded them from the study.

Again, this was a fairly straightforward use of grepl:

``` r
thursdayClean <- thursdayClean[!grepl("none", thursdayClean$receiver,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("None", thursdayClean$receiver,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("<MISSING>", thursdayClean$receiver,
    fixed = TRUE), ]
thursdayClean <- thursdayClean[!grepl("[deleted]", thursdayClean$receiver,
    fixed = TRUE), ]
```

1.  This left us with 92,943 comments or posts by 75,516 users for and 89,585 comments by 72,801 users for . While we didn't directly track whether content was a post or a comment, we paid attention as to whether a piece of content was a reply to a post or not (the working assumption was that personal attacks on posts might have different impact than attacks on comments). Quite consistently, 46% of content were comments on posts on both days.

2.  On these two days respectively, 1359 users (1.79%) received at least one attack, 35 of them received more than one (0.046%). 302 of users (0.39%) received at least one attack and 3 of them more than one on that day (0.003%). These numbers are estimates for a single day, and therefore if the chance of obtaining at least one attack in a day is 1.79%, assuming the binomial distribution, the estimated probability of obtaining at least one attack in a week is 11.9% in a week and 43% in a month.

``` r
100  * round(1-dbinom(0,7,prob = 1359/75516),3)` #week
100 * round(1-dbinom(0,31,prob = 1359/75516),3)` #month
```

1.  To ensure a sufficient sample size, we decided not to draw a random sub-sample from the or class comprising 340 users, and included all of them in the Thursday treatment group (). Other users were randomly sampled from and added to , so that the group count was 1000.

2.  An analogous strategy was followed for . 1338 users belonged to , 27 to , 329 to and 3 to . The total of 344 or users was enriched with sampled users to obtain the group of 1000 users.

3.  The preliminary / groups of 1500 users each were constructed by sampling 1500 users who posted comments on the respective days but did not receive any recognized attacks. The group sizes for control groups are higher, because after obtaining further information we intended to eliminate those who received any attacks before the group selection day (and for practical reasons we could only obtain data for this period after the groups were selected).

4.  For each of these groups new dataset was prepared, containing all posts or comments made by the users during the period of ±7 days from the selection day (337,015 for , 149,712 for , 227,980 for and 196,999 for ) and all comments made to their posts or comments (621,486 for , 170,422 for , 201,614 for and 204,456 for ), after checking for uniqueness these jointly were 951,949 comments for , 318,542 comments for , 404,535 comments for , and 380,692 comments for ). The need to collect all comments to the content posted by our group members was crucial. We needed this information because we needed to check all such comments for personal attacks to obtain an adequate count of attacks received by our group members. In fact, this turned out to be the most demanding part of data collection.

5.  All these were wrangled into the frequency form, with (1) numbers of attacks as recognized by or algorithm (in the dataset we call these and respectively), (2) distinction between and ), and (3) activity counts for each day of the study, (4) with added joint counts for the e and periods. Frequency data for users outside of the control or treatment groups were removed.

6.  With the frequency form at hand, we could look at outliers. We used a fairly robust measure. For each of the weekly counts of and we calculated the interquartile range (), as the absolute distance between the first and the third quartile and identified as outliers those users which landed at least 1.5 × IQR from the respective mean. These resulted in a list of 534 "powerusers" which we suspected of being bots (even though we already removed users whose names suggested they were bots) --- all of them were manually checked by . Those identified as bots (only 15 of them) or missing (29 of them) were removed. It was impossible to establish whether the missing users were bots; there are also two main reasons why a user might be missing: (a) account suspended, and (b) user deleted. We decided not to include the users who went missing in our study, because they would artificially increase the activity drop during the period and because we didn't suspect any of the user deletions to be caused by personal attacks directed against them (although someone might have deleted the account because they were attacked, these were power-users who have a high probability of having been attacked quite a few times before, so this scenario is unlikely).

7.  The frequency form of the control sets data was used to remove those users who were attacked in the period (894 out of 1445 for , and 982 out of 1447 for remained).

8.  A few more unusual data points needed to be removed, because they turned out to be users whose comments contained large numbers of third-person personal attacks which in fact supported them. Since we were interested in the impact of personal attacks directed against a user on the user's activity, such unusual cases would distort the results. Six were authors of posts or comments which received more than 60 attacks each. Upon inspection, all of them supported the original users. For instance, two of them were third-person comments about not wearing a mask or sneezing in public, not attacks on these users. Another example is a female who asked for advice about her husband: the comments were supportive of her and critical of the husband. Two users with weekly activity count change higher than 500 were removed -- they did not seem to be bots but very often they posted copy-pasted content and their activity patterns were highly irregular with changes most likely attributable to some other factors than attacks received. The same holds for a young user we removed from the study who displayed activity change near 1000. She commented on her own activity during that period as very unusual and involving 50 hrs without sleeping. Her activity drop afterwards is very likely attributable to other factors than receiving a personal attack.

9.  86 users who did not post anything in the period were also removed.

10. In the end, and were aligned, centering around the selection day (day 8) and the studied group comprised 3673 users.

``` r
# note we load the data here
data <- read.csv("../datasets/quittingFinalAnon.csv")[, -1]
table(data$group)
```

    ## 
    ##   Rcontrol Rtreatment   Scontrol Streatment 
    ##        875        935        942        921

A few first lines of the resulting anonymized dataset from which we removed separate day counts. Note that in the code "low" corresponds to "wide" (for "low precision") and "high" to "narrow" attacks (for "high precision"). The variables are: (low attacks, high attacks, low attacks on posts, how attacks on posts, authored content posted) and retained summary columns.

-    contains anonymous user numbers.
-    contains the sum of attacks in days 1-7. the sum of (attacks in the same period.
-    and code and attacks on posts (we wanted to verify the additional sub-hypothesis that attacks on a post might have more impact than attacks on comments).

-    and count comments or posts during days seven days before and seven days after. The intuition is, these shouldn't change much if personal attacks have no impact on activity.

-    and include information about which group a user belongs to.

``` r
dataDisp <- data[, c(1, 77:85)]
head(dataDisp)
```

    ##   user sumLowBefore sumHighBefore sumPlBefore sumPhBefore activityBefore
    ## 1    1            1             0           1           0              2
    ## 2    2            5             4           0           0            106
    ## 3    3            2             1           0           0             29
    ## 4    4            6             4           0           0            180
    ## 5    5            5             2           0           0            116
    ## 6    6            2             0           0           0            124
    ##   activityAfter activityDiff      group treatment
    ## 1             0           -2 Rtreatment         1
    ## 2            80          -26 Rtreatment         1
    ## 3            31            2 Rtreatment         1
    ## 4            92          -88 Rtreatment         1
    ## 5            95          -21 Rtreatment         1
    ## 6           104          -20 Rtreatment         1

First, we visually explore our dataset by looking at the relationship between the number of received attacks vs. the activity change counted as the difference of weekly counts of posts or comments authored in the second () and in the first week (). We do this for attacks (Fig. ), attacks (Fig. ), where a weaker, but still negative impact, can be observed, and then we take a look at the impact of those attacks which were recognized as (Fig. ). The distinction between wide and narrow pertains only to the choice of attack recognition algorithm and does not directly translate into how offensive an attack was, except that attacks also include third-person ones. Here, the direction of impact is less clear: while the tendency is negative for low numbers of attacks, non-linear smoothing suggests that higher numbers mostly third-person personal attacks seem positively correlated with activity change. This might suggest that while being attacked has negative impact on a user's activity, having your post "supported" by other users' third-person attacks has a more motivating effect. We will look at this issue in a later section, when we analyze the dataset using regression.

The visualisations in Figure should be understood as follows. Each point is a user. The *x*-axis represents a number of attacks they received in the period (so that, for instance, users with 0 wide attacks are the members of the control group), and the *y*-axis represents the difference between their activity count and . We can see that most of the users received 0 attacks before (these are our control group members), with the rest of the group receiving 1, 2, 3, etc. attacks in the period with decreasing frequency. The blue line represents linear regression suggesting negative correlation. The gray line is constructed using generalized additive mode (gam) smoothing, which is a fairly standard smoothing method for large datasets (it is more sensitive to local tendencies and yet avoids overfitting). The parameters of the gam model (including the level of smoothing) are chosen by their predictive accuracy. Shades indicate the 95% confidence level interval for predictions from the linear model.

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

Ptaszyński, M., Leliwa, G., Piech, M., & Smywiński-Pohl, A. (2018). Cyberbullying detection–technical report 2/2018, Department of Computer Science AGH, University of Science and Technology. *arXiv Preprint arXiv:1808.00926*.

Valkenburg, P. M., Peter, J., & Schouten, A. P. (2006). Friend networking sites and their relationship to adolescents’ well-being and social self-esteem. *CyberPsychology & Behavior*, *9*(5), 584–590. <https://doi.org/10.1089/cpb.2006.9.584>

Wise, K., Hamman, B., & Thorson, K. (2006). Moderation, response rate, and message interactivity: Features of online communities and their effects on intent to participate. *Journal of Computer-Mediated Communication*, *12*(1), 24–41. <https://doi.org/10.1111/j.1083-6101.2006.00313.x>

Wroczynski, M., & Leliwa, G. (2019). *System and method for detecting undesirable and potentially harmful online behavior*. Google Patents.

Zong, W., Yang, J., & Bao, Z. (2019). Social network fatigue affecting continuance intention of social networking services. *Data Technologies and Applications*. <https://doi.org/10.1108/dta-06-2018-0054>

[1] <https://www.samurailabs.ai/>, described in (Ptaszyński, Leliwa, Piech, & Smywiński-Pohl, 2018; Wroczynski & Leliwa, 2019).
