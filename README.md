# README.md

<img src="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/SteeplBanner.png"/>

<a href='https://apps.apple.com/us/app/steepl/id1617553884'>![](https://img.shields.io/itunes/v/1617553884?style=flat-square)</a>
<a href='https://play.google.com/store/apps/details?id=com.lundexsoftware.steepl'>![Custom badge](https://img.shields.io/endpoint?color=green&logo=google-play&logoColor=green&url=https%3A%2F%2Fplayshields.herokuapp.com%2Fplay%3Fi%3Dcom.lundexsoftware.steepl%26l%3DAndroid%26m%3D%24version)</a>

<div style="font-size:16px; margin-top: -4px;">Powered by:</div>

<a href='https://github.com/facebook/react-native'>![React Native](https://img.shields.io/badge/react_native-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)</a>
<a href='https://github.com/reduxjs/redux-toolkit'>![Redux](https://img.shields.io/badge/redux-%23593d88.svg?style=for-the-badge&logo=redux&logoColor=white) </a>
<a href='https://firebase.google.com/'>![Firebase](https://img.shields.io/badge/firebase-%23039BE5.svg?style=for-the-badge&logo=firebase)</a>
<a href='https://developers.google.com/youtube/v3'>![YouTube](https://img.shields.io/badge/Data_API_v3-%23FF0000.svg?style=for-the-badge&logo=YouTube&logoColor=white)</a>

# Overview

## What is Steepl?
Steepl is a mobile app that gives a user access to topic-organized, curated Christian content to provide a meaningful, online substitute or replacement for church. The goal of Steepl goal is to provide a constant, self-driven, free church experience to everyone.

## Key Features
- Content organized by mental states.
- Topic of the Day.
- Content consisting of curated: 
  - Verses (Over a 1000 <u>handpicked</u> for specific topics)
  - Videos (<u>Handpicked</u> for specific topics)
  - User posted Testimonies.
- Favoriting any content to save for later.
- How Are You Feeling feature to help you discover content depending on your general state of mind.

<br>

------------

<br>

# Design Diagrams

## Solution Diagram
#### <a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/SolutionDiagram.png">Diagram Link</a>

<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/SolutionDiagram.png' width=480>

    The challenge of this project is to create a dynamic mobile application using React Native and Component/View design pattern to display data and source files from a database hosted in the cloud with Google Firestore.
</div>

## Usage Flow Chart
#### <a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/flowchart.png">Diagram Link</a>

<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/flowchart.png' width=480>

    This diagram shows the flow of the possible ways a user could interact with the application. This is not a sitemap.
</div>


##

<br>

## Sitemap
#### <a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/sitemap.png">Diagram Link</a>

<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/sitemap.png' width=480>

    This is a sitemap for Steepl. This diagram shows all pages in the app and how they are connected to each other.
</div>



##

<br>

## Schema Design Diagram
#### <a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/er_diagram.png">Diagram Link</a>

<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/er_diagram.png' width=480>

    Steepl uses a NoSQL firestore database. This diagram shows the database collections' and subcollections' structure.
</div>

##

<br>

## Youtube Video Detail Retrieval Diagram
#### <a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/video_diagram1.png?raw=true">Diagram Link</a>
<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/video_diagram1.png' width=480>

    This diagram demonstrates the method behind how Youtube videos and their properties like title, thumbnail, creator, etc. are interacted with by the Steepl application. 
    Firebase stores a Youtube video ID and that is used with the Youtube Data API to return required data about the video’s properties. When a video is clicked the Steepl application will open the Youtube application on the user’s device by combining a YouTube URL with the ID to open the clicked video.

</div>

##

<br>

# Code Snippits

## Topic of the Day Function
```javascript
const topicOTD = async () => {
  var dateObj = new Date();
  //get user's timezone offset
  var timezoneOffset = dateObj.getTimezoneOffset() * 60000;
  //get current date
  var date = new Date(Date.now() - timezoneOffset);
  var month = date.getMonth() + 1;
  var day = date.getDate();
  var year = date.getFullYear();
  var newdate = year + "-" + month + "-" + day;
  //get topic of the day from db
  db.collection("totd").doc(newdate).get().then(function (doc) {
      if (!doc.exists) {
        //If TOTD doesn't already exist, select a new random topic
        db.collection("topics").get().then(function (querySnapshot) {
            var random = Math.floor(Math.random() * querySnapshot.size);
            var randomTopic = querySnapshot.docs[random].id;
            //add new topic to the database
            db.collection("totd").doc(newdate).set({
              topic: randomTopic,
              date: newdate,
            });
          });
      } else {}
    });
};
```
<div align="center">
This function is called everytime the user launches the app. This function checks if a topic of the day is set in the database for the user's current date. If a topic of the day document is not present then this function will select a random topic from the database and set that as the topic of the day for that date.
</div>
<br>

------------

<br>

##

## Youtube Data API Video Retrieval Function
```javascript
async function getVideoDetailsAsJSON(videoIDs) {
  const videoJson = [];
  try {
    //Loop through all video IDs from db and retrieve video details
    for (let i = 0; i < videoIDs.length; i++) {
      await fetch(
          `https://www.youtube.com/oembed?url=http://www.youtube.com/watch?v=${videoIDs[i].videoID}&format=json`
      ).then((response) => response.json()).then((data) => {
            videoJson.push({
            id: videoIDs[i].id,
            videoID: videoIDs[i].videoID,
            ...data,
            });
        });
    }
  } catch (error) { console.log(error); }
  //Return the video details as JSON
  return videoJson;
}
```
<div align="center">
This function takes in the video IDs from the database, fetches the video details for each ID via the YouTube Data API, and stores the retrieved data in JSON format in a returned array.
</div>
<br>

------------

<br>

##

## "How Are You Feeling" Content Randomizer Function
```javascript
useEffect(async () => {
    setLoading(true);
    //Arrays to store values
    const returnedVerses = [];
    const returnedVideos = [];
    const returnedTestimonies = [];
    //For each topic gather a limited number of each type of content and randomize them into arrays
    topics?.forEach(async (topic) => {
      try {
        // Get 8 verses from current topic
        await db.collection("topics").doc(topic).collection("verses").limit(8).get().then((snapshot) => {  
            //insert retrieved verses into array
            snapshot.forEach((doc) => { returnedVerses.push({ id: doc.id, ...doc.data() }); });
            //randomize verses in the array
            let shuffled = returnedVerses
              .map((value) => ({ value, sort: Math.random() })).sort((a, b) => a.sort - b.sort).map(({ value }) => value);
            //set the randomized verses array to the currently displayed verses
            setVerses(shuffled);
          }).then(() => {
            //retrieve 4 videos from current topic
            db.collection("topics").doc(topic).collection("videos").limit(4).get().then((snapshot) => {
                    //insert retrieved videos into array
                    snapshot.forEach((doc) => { returnedVideos.push({ id: doc.id, ...doc.data() });
                });
                getVideoJson(returnedVideos).then((videos) => {
                  //randomize videos in array
                  let shuffled = videos.map((value) => ({ value, sort: Math.random() }))
                  .sort((a, b) => a.sort - b.sort).map(({ value }) => value);
                  //set the randomized videos to the currently displayed videos
                  setVideos(shuffled);
                });
              })
              .then(() => {
                // retrieve 5 testimonies from the current topic
                db.collection("topics").doc(topic).collection("testimonies").limit(5).get()
                  .then((snapshot) => {
                    //insert retrieved testimonies into array
                    snapshot.forEach((doc) => { returnedTestimonies.push({ id: doc.id, ...doc.data() }); });
                    // set the retrieved testimonies to the currently displayed testimonies
                    setTestimonies(returnedTestimonies);
                  });
              });
          });
      } catch (error) { console.log(error); }
      setLoading(false);
    });
  }, [topics]);
```
<div align="center">
This function is called when the user selects a mood from the "How Are You Feeling" options (moods). This function retrieves a set quantity of each content type (verses, videos, and testimonies) from each topic that has the same scale level as the mood selected. The retrieved content is randomized and then returned.
</div>
<br>

------------

<br>

##

# Downloadable Documents

<a href="https://github.com/collinwillis/Steepl_Homepage/blob/main/Assets/Steepl_Project_Proposal.docx?raw=true" download>Project_Proposal.docx</a>

<a href="https://github.com/collinwillis/Steepl_Homepage/blob/main/Assets/Steepl_Project_Requirements.docx?raw=true" download>Project_Requirements.docx</a>

<a href="https://github.com/collinwillis/Steepl_Homepage/blob/main/Assets/Steepl_Design_Spec.docx?raw=true" download>Design_Specifications.docx</a>

<a href="https://github.com/collinwillis/Steepl_Homepage/blob/main/Assets/Steepl_user_stories.xls?raw=true" download>User_Stories.xls</a>

<a href="https://github.com/collinwillis/Steepl_Homepage/blob/main/Assets/Traceability_Matrix.xls?raw=true" download>Tracability_Matrix.xls</a>

<a href="https://github.com/collinwillis/Steepl_Homepage/blob/main/Assets/Steeple_TestCase.xls?raw=true" download>Test_Cases.xls</a>

<br>
<br>

# Diagram Image Links

<a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/SolutionDiagram.png">Solution_Diagram.png</a>

<a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/flowchart.png">Usage_Flow_Diagram.png</a>

<a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/sitemap.png">Sitemap.png</a>

<a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/er_diagram.png">Schema_Design_Diagram.png</a>

<a href="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/video_diagram1.png?raw=true">Youtube_Video_Detail_Retrieval_Diagram.png</a>

<br>
<br>

# Credits/Links
Technologies/Software that power Steepl:

[React Native](https://github.com/facebook/react-native "React Native")

[Redux Toolkit](https://github.com/reduxjs/redux-toolkit "Redux Toolkit")

[Youtube Data API](https://developers.google.com/youtube/v3 "Youtube Data API")

[Firebase](https://firebase.google.com/ "Firebase")