---
title: "Cisco University Challenge"
author: "Awen"
date:  2017-10-30 14:30 0000
layout: post
categories: hackathons
---

Cisco University Challenge is an annual hackathon hosted by Cisco UK aimed at university teams. For the uninitated, a hackathon is an event where teams of developers compete to build products that address a set of challenges. This year, Royal Holloway sent two teams of 5 students to Feltham to take part.

## Our Team

![Team 1]({{ "/resources/cisco/team1.jpg" | absolute_url }})
Royal Holloway Team 1: Megan Goh, Hugh Wells, Dulcie Jackson, Awen Saunders, Shaurya Puri

Our team, lovingly referred to as *Team Shark*, was composed of hackathon veterans and newbies alike, with me acting as project manager; Dulcie, Hugh, and Shaurya as software engineers; and Megan as our business analyst. 

## The Challenge

The challenge that our team decided on was related to Cisco's Project SWIFT, a programme aimed at improving IT throughout the rail network in the UK. Other teams worked on various challenges ranging from shopping to VR, and of course other train-related endeavours.

The hackathon itself was a fun yet grueling affair lasting from 09:00 on Thursday to 14:00 on Friday when the awards ceremony finished. The hacking time lasted just short of 24 hours, and the team worked tirelessly through the night completing TrainShark.

## Our Solution: TrainShark

![Mock up of application]({{ "/resources/cisco/mockup.png" | absolute_url }})

TrainShark is a platform that allows train passengers to determine which carriage is least crowded prior to boarding. This is made possible by leveraging existing WiFi access points to generate a relative busyness score. This score is represented in an easy-to-understand traffic light model, showing customers which carriage to board.

The platform aggregates two major sources of information into a RESTful API, namely Network Rail's OpenLDBWS live train information, and information from the Cisco Meraki APIs. Our technology demonstrator frontend is written in Vue.js, and retrieves real time train information from our platform.

In order for this system to work, the trains would have to be equipped with compatible WiFi APs. Our platform would be marketed as an added value product to enhance the attractiveness of programmes like project SWIFT.

## Conclusion

After two rounds of presentation, the judges concluded that we were the champions of this year's challenge, and generously handed out our prizes. We were awarded an entire lab's worth of Meraki equipment, including 6 APs, a firewall, two switches, and an IP camera. Our individual prizes were an Amazon Echo Dot and a smartwatch each.

![Our team looking tired after a long night of hacking]({{ "/resources/cisco/tired.jpg" | absolute_url }})
Our team looking very tired after a long night of hacking

![Back on campus to distribute the spoils]({{ "/resources/cisco/spoils.jpg" | absolute_url }})
Back on campus to distribute our new Meraki equipment

![Celebrating with team 2]({{ "/resources/cisco/celebrating.jpg" | absolute_url }})
Celebrating our win with Royal Holloway's other team on stage


## Promo Video

<iframe width="640" height="360" src="https://www.youtube.com/embed/NywU7Lws6aA" frameborder="0" gesture="media" allowfullscreen></iframe>

Shaurya made us this excellent teaser video which we played prior to our presentation.
