# Let's Read!

Goal for this project is to make it easier for kids to read to grandparents over a video call. 

<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777864369_Gemini_Generated_Image_v3wkzsv3wkzsv3wk.jpg" width="50%" alt="Gemini generated - grandparent teaching child to read, style of Monet" title="Gemini generated - grandparent teaching child to read, style of Monet"/>

Kids should be able to share the book they are reading over the call, and grandparents should be able to follow along

Issues with standard video calls:
* resolution is sacrificed due to bandwidth limits making the page unreadable
* even at full resolution the page is too small on a mobile screen to read the text - need to pan/zoom to read the text.

I bought a stand to hold phone like a document camera, and explored two ideas to address the problem resolution/pan/zoom problems.

<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777864518_20260503_185525.jpg" width="80%" />

## Idea 1 - complicated but fun

Children are taught to follow the words with their finger as they read. Why not track the finger and use that to automatically pan/zoom the camera feed?

<figure>
<video controls width="60%"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777870914_lets_read_finger_tracking.mp4" type="video/mp4"></video>
<figcaption> Concept demo output. This would be displayed to the grandparent on a smartphone screen. Top panel shows the page zoomed around the tracked finger, bottom shows whole page for context. It works OK, although a bit jumpy.
</figcaption>
</figure>


<figure>
<video controls width="100%"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777867056_vokoscreenNG-2026-03-29_20-17-33.mp4" type="video/mp4">Your browser does not support the video tag. </video>
<figcaption>Under the hood: finger skeletons in blue, pointing finger detected as red dot, text regions detected in green. Camera focuses on the pointing finger, with a future idea to zoom on the text regions closest to the pointing finger, for more stable zooms.
</figcaption>
</figure>
<br> 


## Idea 2 - simple but effective

Forget all that finger tracking stuff, just provide a decent resolution video stream with an easy interface for the viewer to pan/zoom.

Implementation:

* Running [VDO.Ninja](https://docs.vdo.ninja/ ) on laptop to setup the video call, web client on phone to send the video.
* [Custom wrapper](https://github.com/tim-fan/vdo_ninja_zoom_feed) over the VDO.ninja feed to display with [panzoom controls](https://github.com/anvaka/panzoom).

Result is a high-res live stream of the book that can be zoomed/panned for easy reading:

<video controls width="60%"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777865412_Screen_Recording_20260503_185302_Chrome.mp4" type="video/mp4"></video>

## Conclusions

I'll user-test idea 2 for feedback. If it works well I could think about packaging it conveniently, avoiding the steps of setting up a VDO.ninja room and editing links etc.

I'm also tempted to develop the finger tracking idea further, a next step would be to try it as an android app leveraging built in APIs for [hand tracking](https://ai.google.dev/edge/mediapipe/solutions/vision/hand_landmarker/android) and [text detection](https://developers.google.com/ml-kit/vision/text-recognition/v2/android).