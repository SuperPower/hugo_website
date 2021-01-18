---
title: "This Website How to"
description: "How to Contribute to the documentation"
draft: false
weight: 3
---

This documentation is created with [hugo](https://gohugo.io/) a static website generator

## install hugo
Installing hugo is quite simple, as it's a standalone binary that can be downloaded and added to the system path

{{< new_button href="https://gohugo.io/getting-started/installing/" text="hugo getting-started : installing" >}} 


## clone this repo
Note the recusive clone to add the git submodules as well

    >git clone --recursive https://github.com/SuperPower/hugo_website.git
    >cd hugo_website

## test locally
no generation is required to test locally, hugo takes care of generating and serving

    >hugo server

## deploy
generate the website in the `public` folder

    >hugo

make sure you commit and push the `public` submodule before the top repo

## doc support

* for questions or issues related to how to use or contribute to the documentation


{{< new_button href="https://github.com/SuperPower/hugo_website/issues" text="website repo / issues" >}} 

## doc maintainer

* for any other questions

{{< new_button href="https://github.com/wassfila" text="github.com/wassfila" >}} 
