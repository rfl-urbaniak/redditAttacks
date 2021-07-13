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


\textbf{Abstract.} We conduct a large scale data-driven analysis of the effects of online personal attacks on social media user activity. First, we perform a thorough overview of the literature on the influence of social media on user behavior, especially on the impact that negative and aggressive behaviors, such as harassment and cyberbullying, have on users’ engagement in online media platforms. The majority of previous research were  small-scale self-reported studies, which is their limitation. This motivates our data-driven study. We perform a large-scale analysis of messages from Reddit, a discussion website, for a period of two weeks, involving 182,528 posts or comments to posts by 148,317 users. To efficiently collect and analyze the data we apply a high-precision personal attack detection technology. We analyze the obtained data from three perspectives: (i) classical statistical methods, (ii) Bayesian estimation, and (iii) model-theoretic analysis. The three perspectives agree: personal attacks decrease the victims’ activity.
The results can be interpreted as an important signal to social media platforms and policy makers that leaving personal attacks unmoderated is quite likely to disengage the users and in effect depopulate the platform. On the other hand, application of cyberviolence detection technology in combination with various mitigation techniques could improve and strengthen the user community. As more of our lives is taking place online, keeping the virtual space inclusive for all users becomes an important problem which online media platforms need to face.



\textbf{Remark}. In what follows, we sometimes display key pieces of code and explain what it does. Some not too fascinating pieces of code are supressed, but the reader can look them up in the associated .Rmd file and compile their own version.



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

The detection of personal attacks was performed using Samurai, a proprietary technology of Samurai Labs.[1]


### References {-}

Ptaszyński, M., Leliwa, G., Piech, M., & Smywiński-Pohl, A. (2018). Cyberbullying detection–technical report 2/2018, Department of Computer Science AGH, University of Science and Technology. *arXiv Preprint arXiv:1808.00926*.

Wroczynski, M., & Leliwa, G. (2019). *System and method for detecting undesirable and potentially harmful online behavior*. Google Patents.

[1] <https://www.samurailabs.ai/>, described in (Ptaszyński, Leliwa, Piech, & Smywiński-Pohl, 2018; Wroczynski & Leliwa, 2019).
