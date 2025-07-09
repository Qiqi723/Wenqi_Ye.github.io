---
permalink: /
title: "Wenqi Ye"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

I am actively seeking Research Assistant (RA) and Ph.D. positions for Fall 2026!

I am a third-year undergraduate student in the joint-degree program between the University of Electronic Science and Technology of China (UESTC) and the University of Glasgow (UoG), majoring in Electronic Information Engineering.

My research interests are primarily in Next-Generation Wireless Communications (Next-G), Physic-informed nerual network,Computer Vision and Machine Learning. I am particularly passionate about Reconfigurable Intelligent Surfaces (RIS), AI-driven signal processing, and the application of diffusion models in image generation and localization tasks.

I am very fortunate to be advised by the following professors on my research journey:

Prof. Weidong Mei & Prof. Zhi Chen, National Key Laboratory of Science and Technology on Communications, UESTC

Prof. Ruxiang Xu, School of Medicine, UESTC

Research Experience
======

RF-3DGS: Wireless Channel Modeling

Research Assistant | Advisor: Prof. Haijian Sun (University of Georgia) | May 2025 - Present

This research proposes a novel strategy to represent the Radio Frequency Field (RRF) by integrating visual and radio data, aiming to overcome the limitations of using only wireless samples.

The proposed active selection mechanism is shown to benefit Gaussian Splatting (GS) models in both sparse- and dense-view regimes.

5G-Advanced Technology Research

Research Assistant | Advisors: Prof. Weidong Mei, Prof. Zhi Chen (UESTC) | Mar 2024 - Present

Conducted the first modeling of an all-active intelligent reflecting surfaces (AIRS)-assisted wireless network.

Validated theoretical findings through numerical simulations, comparing and demonstrating the performance advantages of AIRS over passive intelligent reflecting surfaces (PIRS) in certain scenarios.

Designed an IRS-user association algorithm for the downlink communication in a multi-IRS aided wireless network, which approximates the optimal solution with polynomial complexity.

Deep Learning Deployment on Brain-Computer Interface

Research Assistant | Advisor: Prof. Ruxiang Xu (UESTC) | Aug 2023 - Jun 2024

Proposed DiffuCell, the first diffusion model applied to cell localization, addressing issues of background noise interference and density loss in traditional density map regression methods, significantly improving the accuracy of cell localization and counting.

Designed the Attention Map Guidance (AG) mechanism, solving the challenge of generating high-quality samples without external guidance in diffusion models, further enhancing the quality of density map generation and localization precision.

Introduced a cell map fusion strategy to address the stochastic nature of diffusion model outputs by fusing multiple density maps, producing more complete localization results and achieving 93.6% precision.

Getting started
======
1. Register a GitHub account if you don't have one and confirm your e-mail (required!)
1. Fork [this template](https://github.com/academicpages/academicpages.github.io) by clicking the "Use this template" button in the top right. 
1. Go to the repository's settings (rightmost item in the tabs that start with "Code", should be below "Unwatch"). Rename the repository "[your GitHub username].github.io", which will also be your website's URL.
1. Set site-wide configuration and create content & metadata (see below -- also see [this set of diffs](http://archive.is/3TPas) showing what files were changed to set up [an example site](https://getorg-testacct.github.io) for a user with the username "getorg-testacct")
1. Upload any files (like PDFs, .zip files, etc.) to the files/ directory. They will appear at https://[your GitHub username].github.io/files/example.pdf.  
1. Check status by going to the repository settings, in the "GitHub pages" section

Site-wide configuration
------
The main configuration file for the site is in the base directory in [_config.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_config.yml), which defines the content in the sidebars and other site-wide features. You will need to replace the default variables with ones about yourself and your site's github repository. The configuration file for the top menu is in [_data/navigation.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_data/navigation.yml). For example, if you don't have a portfolio or blog posts, you can remove those items from that navigation.yml file to remove them from the header. 

Create content & metadata
------
For site content, there is one Markdown file for each type of content, which are stored in directories like _publications, _talks, _posts, _teaching, or _pages. For example, each talk is a Markdown file in the [_talks directory](https://github.com/academicpages/academicpages.github.io/tree/master/_talks). At the top of each Markdown file is structured data in YAML about the talk, which the theme will parse to do lots of cool stuff. The same structured data about a talk is used to generate the list of talks on the [Talks page](https://academicpages.github.io/talks), each [individual page](https://academicpages.github.io/talks/2012-03-01-talk-1) for specific talks, the talks section for the [CV page](https://academicpages.github.io/cv), and the [map of places you've given a talk](https://academicpages.github.io/talkmap.html) (if you run this [python file](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.py) or [Jupyter notebook](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb), which creates the HTML for the map based on the contents of the _talks directory).

**Markdown generator**

The repository includes [a set of Jupyter notebooks](https://github.com/academicpages/academicpages.github.io/tree/master/markdown_generator
) that converts a CSV containing structured data about talks or presentations into individual Markdown files that will be properly formatted for the Academic Pages template. The sample CSVs in that directory are the ones I used to create my own personal website at stuartgeiger.com. My usual workflow is that I keep a spreadsheet of my publications and talks, then run the code in these notebooks to generate the Markdown files, then commit and push them to the GitHub repository.

How to edit your site's GitHub repository
------
Many people use a git client to create files on their local computer and then push them to GitHub's servers. If you are not familiar with git, you can directly edit these configuration and Markdown files directly in the github.com interface. Navigate to a file (like [this one](https://github.com/academicpages/academicpages.github.io/blob/master/_talks/2012-03-01-talk-1.md) and click the pencil icon in the top right of the content preview (to the right of the "Raw | Blame | History" buttons). You can delete a file by clicking the trashcan icon to the right of the pencil icon. You can also create new files or upload files by navigating to a directory and clicking the "Create new file" or "Upload files" buttons. 

Example: editing a Markdown file for a talk
![Editing a Markdown file for a talk](/images/editing-talk.png)

For more info
------
More info about configuring Academic Pages can be found in [the guide](https://academicpages.github.io/markdown/), the [growing wiki](https://github.com/academicpages/academicpages.github.io/wiki), and you can always [ask a question on GitHub](https://github.com/academicpages/academicpages.github.io/discussions). The [guides for the Minimal Mistakes theme](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) (which this theme was forked from) might also be helpful.
