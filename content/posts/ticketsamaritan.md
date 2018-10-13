---
title: "Ticket Samaritan"
date: 2018-10-13
tags: [ "swift", "vapor" ]
---

Built a website and API backend (https://www.ticketsamaritan.uk/) using Vapor after attending [Serverside.swift conference](https://www.serversideswift.info/) in Berlin. I explored serverless technologies offered by IBM but ultimately Vapor turned out to be a great option and Vapor Cloud especially makes for easy deployment and database hosting.

The hardest part was getting presigned forms working for user image uploads to Amazon S3. I released a [tiny util](https://github.com/darkfunction/s3policy) for generating form policies.

The site is for travellers to share free tickets and has received [postive responses](https://www.reddit.com/r/Edinburgh/comments/9nkbck/lots_of_posts_about_spare_rail_tickets_here/) on Reddit. I think it needs a companion app to be really useful (notifications are a key requirement for this project) but since the API is done this is something that can be put together pretty quickly in the future.


