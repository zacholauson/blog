---
layout: post
title: "Pairing Tour: Day 1"
date: 2014-07-21 21:18

---
Today I started my pairing tour, which is 2 weeks of pairing with craftsmen in the company. Today I was pairing with Taryn. We started out the day by working on a spike to determine the amount of work behind making an iOS app designed for iPhone work for iPads as well. I've never done iOS development before, but this was a fun problem to work through.

Based on what we had been reading, changing the layout to universal, it should have just expanded to fit the screen of the iPad. Unfortunately the layout stuck to the side, the size of an iPhone, on the iPad.

Taryn had read about naming the `.xib` files to reflect what device they were for.
`SomethingView~iphone.xib` and `SomethingView~ipad.xib`. 
We tested this by making a change on one xib file and making sure its only shown on the matching device, which seemed to work.
We still had one problem though. Even though we changed the screen size on the `_ipad.xib` file, it wasn't shown any bigger on the device. It turned out this was because we also needed device specific storyboards.
We didn't continue to go too far down this route, as it was just a spike.

We ended up finding out a few things though.     

  - `Shift+Command+S` will duplicate the file you have selected in XCode.

  - Rename to duplicate storyboard to `StoryboardName_iPad.storyboard`.

     I think you could also rename the original storyboard with the `_iPhone.storyboard` suffix. I doubt it would make a difference, as these get pointed to in the `info.plist`.

- The iPad storyboard will need to be edited to target the iPad.
   
  Right click the iPad storyboard and open as source code and make the following changes.

{% codeblock %}
targetRuntime="iOS.CocoaTouch"                             -> targetRuntime="iOS.CocoaTouch.iPad"
<simulatedScreenMetrics key="destination" type="retina4"/> -> <simulatedScreenMetrics key="destination"/>
{% endcodeblock %}

- Lastly, you will need to update the `info.plist` to link to the new default storyboard(iPhone), and link to the iPad storyboard.


#####A few links that I found helpful while researching for this spike

http://stackoverflow.com/questions/8465769/converting-storyboard-from-iphone-to-ipad

http://blogs.innovationm.com/convert-iphone-application-to-universal-application/

https://developer.apple.com/library/ios/documentation/userexperience/conceptual/mobilehig/LaunchImages.html


To finish off the day we reviewed pull requests for the Clojure and iOS projects.
