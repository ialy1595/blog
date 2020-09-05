---
title: "About Me"
date: 2019-02-04T10:25:27+09:00
---

![](/images/my.jpg#center30)

# Education

 - [**Gyeonggi Science High School (GSHS)**](https://www.gs.hs.kr/) <sm>(Mar 2012 - Feb 2015)</sm>

 - [**Seoul National University (SNU)**](http://www.snu.ac.kr) <sm>(Mar 2015 – Present)</sm>
    - B.S. in Computer Science and Engineering

# Interest Fields

 - **Problem Solving**
    - Algorithm, Data structure
 -  **Human Computer Interaction**
     - UI/UX, Gamification, Human Computing
 - **Design**
    - Game design, project managing

# Projects

## eng2music

When you learn English words or phrases, you may want to know how they are pronounced. eng2music can be helpful in this case. Searching for an English word or phrase shows the phrase that contains the word in the music. Click the YouTube button to repeat the phrase on YouTube.

 - [Web link](http://ialy1595.me/eng2music/)
 - [Repository](https://github.com/ialy1595/eng2music)
 - React & React Hook
 - Dataset from [DALI](https://github.com/gabolsgabs/DALI)
    - Crawling with selenium to check invalid YouTube link.
    - 1071 songs with 33916 phrases are used
 - Incremental search system
   - Use previous results to reduce search time
 - Responsive design
 - Lazy loading for large data

Search | Play YouTube
:-:|:-:
![](/images/about_me/e2m0.png) | ![](/images/about_me/e2m1.png)


# korean-index-of

When you type "코드", the process is "ㅋ", "코", "콛", and "코드", only "코" and "코드" is substring of "코드". For this reason, incremental search with normal string match function provides a poor UX because it repeats whether the results are included or not. The improved `indexOf` function, `koreanIndexOf` in this package can solve this problem because they can determine that "ㅋ", "코", "콛", and "코드" is substring of "코드".

 - npm package ([link](https://www.npmjs.com/package/korean-index-of))
 - [Repository](https://github.com/ialy1595/korean-index-of)
 - You can get all match results in an array at once
 - You can get a range of matching
   - It tells you whether the last letter is onset(초성) or coda(종성)
 - Supply onset search(초성 검색)
 - All functions in this package take a linear time
   - Use Z algorithm

![](/images/about_me/kor_demo0.gif)

## Water Half Torpedo Half

You must avoid approaching torpedoes. There are many games of this genre. However, there is a distinction only in this game: The enemy's information is given **discontinuously**. You can only verify the position of the torpedo when the radar is passing by. You should anticipate and avoid the trajectory of a torpedo through color indicators that tell you the speed. It will allows you to experience that what is natural is not natural.

 - [Repository](https://github.com/ialy1595/Water-Half-Torpedo-Half)
 - Unity with C#
 - Android build
 - Use CCW algorithm for ladar collision check
 - Validated stage clearable
    - Torpedo generated randomly
    - Checked to see if there was a clear path
    - Use flood-fill algorithm
 - Torpedo generated more frequently near submarines.
    - For prevent long waiting in only one place a.k.a 존버
    - Use probability density function

Main | Playing
:-:|:-:
 ![](/images/about_me/torpedo0.jpg) | ![](/images/about_me/torpedo1.jpg)

## NDB

Game management tycoon game. Users can control the game’s popularity by summoning appropriate number of monsters. It has contents like summoning monsters, increasing server, employing bug GMs who handles bug users, buying and using items, regular and emergency inspections. It received 3 votes in Nexon Dream Members workshop.

 - [Repository](https://github.com/ialy1595/NDB.git)
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
 - **C**
    - Linux kernel modification
    - [CMU Labs](http://csapp.cs.cmu.edu/3e/labs.html)
      - Bomb, Attack, Architecture, Cache, Shell, Malloc, Proxy
 - **C#** with Unity
 - **Python**
    - Web crawling
    - Data processing
 - **Processing3**
    - For prototyping

# Awards & Certificates

## Awards

 - **Korea Olympiad in Informatics, Silver Prize** <sm>(Sept 2013)</sm>
 - **Creative Problem Solving Festival, Gold Prize** <sm>(Nov 2013)</sm>
 - **Nexon Dream Members Workshop, 2nd** <sm>(Feb 2016)</sm>
 - **Coder’s High, 13th** <sm>(Jul 2016)</sm>
 - **ACM International Collegiate Programming Contest (ICPC) Korea regional, 10th place** <sm>(Nov 2016)</sm>

## Certificates

 - **Craftsman Bartender** <sm>(Dec 2015)</sm>
 - **Industrial Engineer Information Processing** <sm>(May 2017)</sm>
 
# Scholarship

 - **SNU Merit Based Scholarship** <sm>(Aug 2015, Jan 2016, Feb 2016, Feb 2017, Aug 2017, Feb 2018)</sm>
 - **National Scholarship (Type I)** <sm>(Aug 2016)</sm>
 - **National Scholarship (Type II)** <sm>(Aug 2015, Aug 2016, Dec 2016)</sm>
 - **Yoon Song-Yee and Kim Tack-Jin Scholarship** <sm>(Oct 2016)</sm>
 - **SNU College of Engineering ERF Scholarship** <sm>(June 2017, Sept 2017)</sm>

# Other Activities

 - **Michigan Technological University Youth Program** <sm>(16th July 2012 – 27th July 2012)</sm>
 - **Game designer of reality puzzle game club of GSHS, \<the Genius in GSHS\>** <sm>(Mar 2014 – Feb 2015)</sm>
 - **Staff member of student council of GSHS** <sm>(Mar 2014 – Feb 2015)</sm>
 - **Seminar member of cocktail club of SNU, \<Hurim\>** <sm>(Mar 2016 – present)</sm>
 - **Regular member of server management club of SNU, \<Bacchus\>** <sm>(Aug 2016 – July 2018)</sm>