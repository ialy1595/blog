---
title: "About Me"
date: 2019-02-04T10:25:27+09:00
---

![](/images/about_me/my.jpg#center50)

# Education

 - [**Gyeonggi Science High School (GSHS)**](https://www.gs.hs.kr/) <sm>(Mar 2012 - Feb 2015)</sm>

 - [**Seoul National University (SNU)**](http://www.snu.ac.kr) <sm>(Mar 2015 – Present)</sm>
    - B.S. in Computer Science and Engineering

# Interest Fields

 - **Problem Solving**
    - Algorithm, Data structure
 -  **Human-Computer Interaction**
     - UI/UX, Gamification, Human computing
 - **Design**
    - Game design, Project managing

# Projects

## korean-index-of [(npm)](https://www.npmjs.com/package/korean-index-of) [(repository)](https://github.com/ialy1595/korean-index-of)

When you type "코드", the process is "ㅋ", "코", "콛", and "코드", but only "코" and "코드" is a substring of "코드". For this reason, incremental search with a normal string match function provides a poor UX in Korean because it repeats whether the results are included or not. The improved `indexOf` function, `koreanIndexOf` in this package can solve this problem because they can determine that "ㅋ", "코", "콛", and "코드" is a substring of "코드".

 - You can get all match results in an array at once
 - You can get a range of matching
   - It tells you whether the last letter is onset(초성) or coda(종성)
 - Supply onset search(초성 검색)
 - All functions in this package take a linear time
   - Use Z algorithm

![](/images/about_me/kor_demo1.gif#center100)

## eng2music [(web)](http://ialy1595.me/eng2music/) [(repository)](https://github.com/ialy1595/eng2music)

When you learn English words or phrases, you may want to know how they are pronounced. eng2music can be helpful in this case. Searching for an English word or phrase shows the phrase that contains the word in the music. Click the YouTube button to repeat the phrase on YouTube.

 - React & React Hook
 - Dataset from [DALI](https://github.com/gabolsgabs/DALI)
    - Crawling with selenium to check invalid YouTube links.
    - 1071 songs with 33916 phrases are used
 - Incremental search system
   - Use previous results to reduce search time
 - Responsive design
 - Lazy loading for large data

Search | Play YouTube
:-:|:-:
![](/images/about_me/e2m0.png) | ![](/images/about_me/e2m1.png)

## Water Half Torpedo Half [(repository)](https://github.com/ialy1595/Water-Half-Torpedo-Half)

You must avoid approaching torpedoes. There are many games of this genre. However, there is a **distinction only in this game: The enemy's information is given discontinuously**. You can only verify the position of the torpedo when the radar is passing by. You should anticipate and avoid the trajectory of a torpedo through color indicators that tell you the speed. It will allow you to experience that what is natural is not natural.

 - Unity with C#
 - Android build
 - Use CCW algorithm for radar collision check
 - Validated stage clearable
    - Torpedo generated randomly
    - Checked to see if there was a clear path
    - Use flood-fill algorithm
 - Torpedo is generated more frequently near submarines.
    - For preventing long waiting in only one place a.k.a 존버
    - Use probability density function

![](/images/about_me/torpedo_demo1.gif#center30)

## NDB [(repository)](https://github.com/ialy1595/NDB.git)

Game management tycoon game. Users can control the game’s popularity by summoning an appropriate number of monsters. It has contents like summoning monsters, increasing server, employing bug GMs who handle bug users, buying and using items, regular and emergency inspections. It received 3 votes in the Nexon Dream Members workshop.

 - Unity, C#
 - Team project (6 members)
 - Undertaken roles
    - Implementation
        - Animation logic
        - Image Design
        - Bug User/ Bug GM system
    - Game design
        - Round/ Stage structure
        - Risk factor

# Technical Skills

 - **C++**
   - Algorithm contest
 - **Javascript**
   - React, Vue, Node, React-native
 - **Python**
    - Web crawling
    - Data processing
 - **C**
    - Linux kernel modification
    - [CMU Labs](http://csapp.cs.cmu.edu/3e/labs.html)
      - Bomb, Attack, Architecture, Cache, Shell, Malloc, Proxy
 - **Java**
    - Spring
 - **C#** with Unity
 - **Processing3**
    - For prototyping

# Experiences

 - **Intern, Human-Computer Interaction Lab, SNU** <sm>(Oct 2017 - Jun 2018)</sm>
    - Design empty space of mobile interface
 - **Intern, Samsung Electronics** <sm>(Jan 2021 - Feb 2021)</sm>
    - Develop bearer packet trace debugging tool
    - Design flexible self wealth accumulation application

# Awards

 - **Korea Olympiad in Informatics, Silver Prize** <sm>(Sep 2013)</sm>
 - **Creative Problem Solving Festival, Gold Prize** <sm>(Nov 2013)</sm>
 - **Nexon Dream Members Workshop, 2nd** <sm>(Feb 2016)</sm>
 - **Coder’s High, 13th** <sm>(Jul 2016)</sm>
 - **ACM ICPC, Korea regional, 11th place** <sm>(Nov 2016)</sm>
 - **SCPC, Onsite Final** <sm>(Jul 2019)</sm>
 - **Google Code Jam, Round 2** <sm>(May 2020)</sm>


# Certificates

 - **Craftsman Bartender** <sm>(Dec 2015)</sm>
 - **Industrial Engineer Information Processing** <sm>(May 2017)</sm>
 
# Other Activities

 - **Seminar member of cocktail club of SNU, [\<Hurim\>](https://www.facebook.com/snuHURIM/)** <sm>(Mar 2016 – present)</sm>
 - **Regular member of server management club of SNU, [\<Bacchus\>](https://bacchus.snucse.org/)** <sm>(Aug 2016 – July 2018)</sm>
 - **Speaker of IT Conference in ROKAF, [\<계룡 전산 한마당\>](http://www.sportsseoul.com/news/read/777410)** <sm>(Aug 2016 – July 2018)</sm>
 - **Minor contribution on [Mobx](https://mobx.js.org/README.html) ([#2684](https://github.com/mobxjs/mobx/commit/5e2e7e7e8e02538fca8f83670a01d6c0a73f1f53))**