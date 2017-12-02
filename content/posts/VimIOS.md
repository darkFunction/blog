---
title: "iOS autocompletion in Vim"
date: 2011-11-19
categories: 
 - "Tools "
---

I’ve been playing around with Vim recently and wanted to get autocompletion for my iOS project working. Vim isn’t great as an IDE replacement for XCode, but it’s nice to be able to jump in and edit code files quickly if you need to.

Firstly, you will need to install clang complete.

Here’s what I needed to add to my .vimrc file:

	let g:clanguseroptions=’-triple i386-apple-macosx10.6.7 -target-cpu yonah -target-linker-version 123.2 -resource-dir /Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/../lib/clang/2.1 -fblocks -x objective-c++ -fblocks -isysroot /Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator4.3.sdk -D__IPHONEOSVERSIONMINREQUIRED=40300 -iquote $PROJ/Classes || exit 0’
	
	let g:clangcompletemacros = 1

Where $PROJ/Classes may be one or more paths to your source files (I actually had quite a few).
