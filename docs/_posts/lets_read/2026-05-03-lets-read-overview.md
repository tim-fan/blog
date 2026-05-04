# Let's Read!

Goal for this project is to make it easier for kids to read to grandparents over a video call. 

<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777864369_Gemini_Generated_Image_v3wkzsv3wkzsv3wk.jpg" width="50%" />

Kids should be able to share the book they are reading over the call, and grandparents should be able to follow along

Issues with standard video calls:
* resolution is sacrificed due to bandwidth limits making the page unreadable
* even without resolution loss, the page is too small on a mobile screen to read the text - need to pan/zoom to read the text

## Implementation

Stand to hold phone like a document camera:

<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777864518_20260503_185525.jpg" width="80%" />

Running [VDO.Ninja](https://docs.vdo.ninja/ ) on laptop to setup the video call, web client on phone to send the video.

[Custom wrapper](https://github.com/tim-fan/vdo_ninja_zoom_feed) over the VDO.ninja feed to display with [panzoom controls](https://github.com/anvaka/panzoom).

Result is a high-res live stream of the book that can be zoomed/panned for easy reading:

<video controls width="60%"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1777865412_Screen_Recording_20260503_185302_Chrome.mp4" type="video/mp4"></video>

