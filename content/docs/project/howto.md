---
title: "Doc How to"
description: "How to Contribute to the documentation"
draft: false
weight: 3
---

This documentation is created with [hugo](https://gohugo.io/) a static website generator

## install hugo
Installing hugo is quite simple, as it's a standalone binary that can be downloaded and added to the system path

* [hugo getting-started : installing](https://gohugo.io/getting-started/installing/)

## clone this repo
Note the recusive clone to add the git submodules as well

    >git clone --recursive https://github.com/Informaticore/SuperPower
    >cd SuperPower
    >cd website_work_dir

## test locally
no generation is required to test locally, hugo takes care of generating and serving

    >hugo server

## deploy
generate the website in the `public` folder

    >hugo

make sure you commit and push the `public` submodule before the top repo

## doc maintainer

Please contact the documentation maintainer [wassfila](https://github.com/wassfila)
* for questions or issues related to how to use or contribute to the documentation
* you notice any typo or error that you'd like simply to help by notifying without having to create a ticket
