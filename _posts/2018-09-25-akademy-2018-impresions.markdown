---
layout: post
title:  "Akademy 2018 Impressions"
date:   2018-09-25 13:01:32 +0200
categories: kde akademy update
---

[Akademy](https://akademy.kde.org/) is, according to its web page, the annual
world summit of KDE, one of the largest Free Software communities in the world.
I would add to that is a great opportunity to meet really creative tech people, to
learn and to get more involved into the Free Software world. This was my first
time attending to it, and probably not the last one. I attended as a
[plasma extensions](https://github.com/plasma-extensions/) developer, as
[Nitrux](http://nxos.org/) developer and also as
an [AppImage Project](https://github.com/AppImage/AppImageKit) contributor.

I wasn't able to get presentation ready on time ready but I do had the chance to
discuss about the above mentioned projects with the people around. So I'll share
the a summary.

### Plasma Extensions

Is composed by a set of experimental plasmoids that follow a design goal: bring
the most used and related settings together and upfront.

 1. **[Plasma Widget Quick Controls](https://github.com/plasma-extensions/plasma-widget-quick-controls)**

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
    <div style="display: inline-block;">
        <p>This widget is to group together all the controls that a novice user will require and is meant to replace the network, Bluetooth, Audio and Power plasmoids.</p>
    </div>
    <div style="display: inline-block;">
    <img src="https://cloud.githubusercontent.com/assets/1138094/19288493/4497e330-8fd4-11e6-948f-cf32f76d09aa.png" alt="Plasma Widget Quick Controls"/>
    </div>
</div>


 2. **[Extended Volume Mixer Widget](https://github.com/plasma-extensions/plasma-widget-extended-volume-mixer)**

<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
    <div style="display: inline-block;">
        <p>The goal behind this plasmoid is to make really accessible the configuration of multiple audio devices.
</p>
    </div>
    <div style="display: inline-block;">
    <img src="https://cloud.githubusercontent.com/assets/1138094/19489665/22d42bee-953a-11e6-8f43-2d92539e1c78.png" alt="Extended Volume Mixer"/>
    </div>
</div>


 3. **[Notifications sidebar](https://github.com/plasma-extensions/plasma-widget-notification-sidebar)**


<div style="-webkit-column-count: 2; -moz-column-count: 2; column-count: 2; -webkit-column-rule: 1px dotted #e0e0e0; -moz-column-rule: 1px dotted #e0e0e0; column-rule: 1px dotted #e0e0e0;">
    <div style="display: inline-block;">
        <p>Provides a fancy and easy to access area for your system notifications.</p>
    </div>
    <div style="display: inline-block;">
    <img src="https://cloud.githubusercontent.com/assets/1138094/17712753/59dd61c4-63c5-11e6-98a0-d25cd36267c3.png" alt="Notifications side bar"/>
    </div>
</div>


### Nitrux

 Is a GNU/Linux distribution focused on providing the best user experience possible.
 It combines an elegant design and a set of enhanced plasma widgets that group
 together the mos commonly used functionalities. It's based (at the time writing
 this post) on ubuntu 18.04 and uses AppImages as the main way of distributing user
 applications.

 In Akademy we, the Nitrux team, had the chance to present our work in the
 distributions BoF. Which included the [NX Software Center](https://github.com/Nitrux/nx-software-center)
 which is a Qt-Qml application that makes usage of the plasma qml components
 to achieve a better blending with Plasma based desktops. Also it's distributed
 as an AppImage which makes it a good reference of applications that makes
 use of the plasma qml components and kde frameworks and want to be distributed
 in the same way.


### AppImage

 As active contributor to the AppImage project I was presenting the [AppImage KDE
 Thumbnailer](https://github.com/azubieta/KDE-AppImage-Thumbnailer). Also was
 discussed how to improve AppImages support in Plasma, therefore were requested
 the following features:
 * AppImage files thumbnails in the file manager.
 * Applications menu, mime-types and favorite applications integration.
 * Execution and verification of newly downloaded AppImage files.
 * Discovery newly available AppImages in the file system.

 It was really great to see that our requests were listened and analysed carefully.
 We get to the arrangement that once the basic libs for AppImage manipulation were
 included in the major upstream projects the repositories we could continue working
 on the topic.


#### Conclusions
 After a whole week of really interesting presentations, workshops and high
 tech I got the feeling of being part of something really awesome, the
 creation of a better and free software ecosystem for the world. The people
 there were really nice and the organization was awesome. In general, Akademy
 2018 was a great opportunity to share experiences, results and also to
 make friends.
