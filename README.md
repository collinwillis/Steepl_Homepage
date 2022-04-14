# Editor.md

<img src="https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/SteeplBanner.png"/>


<a href='https://www.google.com'>![](https://img.shields.io/itunes/v/1617553884?style=flat-square)</a>

<a href='https://www.google.com'>![Custom badge](https://img.shields.io/endpoint?color=green&logo=google-play&logoColor=green&url=https%3A%2F%2Fplayshields.herokuapp.com%2Fplay%3Fi%3Dcom.lundexsoftware.steepl%26l%3DAndroid%26m%3D%24version)
<br>

![React Native](https://img.shields.io/badge/react_native-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)
![Redux](https://img.shields.io/badge/redux-%23593d88.svg?style=for-the-badge&logo=redux&logoColor=white) 
![Firebase](https://img.shields.io/badge/firebase-%23039BE5.svg?style=for-the-badge&logo=firebase)
![YouTube](https://img.shields.io/badge/Data_API_v3-%23FF0000.svg?style=for-the-badge&logo=YouTube&logoColor=white)


------------

# Overview

## What is Steepl?
Steepl is a mobile app that gives a user access to topic-organized Christian content to provide a meaningful, online substitute or replacement for church. The goal of Steepl goal is to provide a constant, self-driven, free church experience to everyone.

------------

## Key Features
- Content organized by mental states.
- Topic of the Day.
- Content consisting of: 
 - Verses (Over a 1000 <u>handpicked</u> for specific topics)
 - Videos (<u>Handpicked</u> for specific topics)
 - User posted Testimonies.
- Favoriting any content to save for later.
- How Are You Feeling feature to help you discover content depending on your general state of mind.

<br>

# Design Diagrams
------------
## Usage Flow Chart
<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/flowchart.png' width=480>
</div>

##
------------

<br>

## Sitemap
<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/sitemap.png' width=480>
</div>

------------

<br>

## Schema Design Diagram
<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/er_diagram.png' width=480>
</div>

------------

<br>

## Video Retrieval Diagram
<div align="center">
    <img src='https://raw.githubusercontent.com/collinwillis/Steepl_Homepage/main/Assets/video_diagram1.png' width=480>
</div>

------------

<br>

# Code Snippits
------------
<br>

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
------------
<br>

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
------------
<br>

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


------------

# Credits/Links
Technologies/Software that power Steepl:

[React Native](https://github.com/facebook/react-native "React Native")

[Redux Toolkit](https://github.com/reduxjs/redux-toolkit "Redux Toolkit")

[Youtube Data API](https://developers.google.com/youtube/v3 "Youtube Data API")

[Firebase](https://firebase.google.com/ "Firebase")
