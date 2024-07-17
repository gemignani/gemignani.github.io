---
title: "Brazilian Interactive DTV Applications"
date: 2014-04-11T08:47:11+01:00
draft: false
---

Brazilian DTV standard for interactive applications "Ginga" is now being followed by other South America countries. The standard is usually implemented by big electronics companies and setup-box manufacturers as a middleware service inside their platforms.

Applications can be built using a special version of [Java TV](http://www.oracle.com/technetwork/java/javame/tech/javatv-136131.html) or [NCL](http://www.ncl.org.br/en) (Nested Context Language), the [specification](http://www.abnt.org.br/imagens/Normalizacao_TV_Digital/ABNTNBR15601_2007Vc_2008.pdf) covers a wide range of concepts, so the developer should read the ABNT NBR standard before further adventures.

Ginga NCL language is a declarative language for hypermedia documents, it also supports [LUA scripts](http://www.lua.org) and a simple HTML document, so the design is pretty simple. 

The actual documentation is widespread, available in ABNT NBR documents as mentioned above, [ITU-T H.761](https://www.itu.int/rec/dologin_pub.asp?lang=e&id=T-REC-H.761-201106-I!!ZPF-E&type=items) , some univerysity works like [this from PUC](http://www.ncl.org.br/documentos/NCL3.0-DTV.pdf) and [this too](http://www.ncl.org.br/documentos/NCL3.0-EC.pdf).


How to run
==========

We have the following options for running our "application":

* Use a DTV / STB which already have the stack running
* [Ginga4Windows](http://www.gingancl.org.br/sites/gingancl.org.br/files/ferramentas/ginga-v0.13.5-win32.exe) a simple Windows Emulator which would be enough for getting started 
* [Ubuntu VM](http://www.gingancl.org.br/sites/gingancl.org.br/files/ferramentas/ubuntu-server10.10-ginga-v.0.12.4-i386.zip) which should behave the same way as the Windows version
* [DTV Emulator](http://tvd.lifia.info.unlp.edu.ar/ginga.ar/index.php/wari) from Lifia lab from Argentina

Simple NCL application
======================

We will develop a simple application, so we just need to create a NCL document and it will run in any emulated system. If we want to deploy it to a real system, it should go into a DSMCC carrousel section inside a transport stream (TS).

So, let's go directly to the code, a simple NCL document would be:

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<ncl id="exemplo01" xmlns="http://www.ncl.org.br/NCL3.0/EDTVProfile">
	<head>
		<regionBase>
			<region height="100%" id="rgVideo1" left="0" top="0" width="100%"/>
		</regionBase>
		<descriptorBase>
			<descriptor id="dVideo1" region="rgVideo1"/>
		</descriptorBase>
	</head>
	<body>
		<port component="video1" id="pEntrada"/>
		<media descriptor="dVideo1" id="video1" src="media/sample.mp4"/>
	</body>
</ncl>
```

It looks like an HTML/XML and the code is written to reproduce the *<media>* tag, which would play the *dVideo1* described by *<descriptorBase>* tag and placed at a region from *<regionBase>*. It is important to note that every code 

Working with LUA scripts
========================

The LUA language is very popular, it provides a flexible meta-features that can be extended as needed. The base language is light – the full reference interpreter is only about 180 kB compiled, it is a dynamically typed language, it also implements a small set of advanced features such as first-class functions, garbage collection, closures, proper tail calls, coercion (automatic conversion between string and number values at run time), coroutines (cooperative multitasking) and dynamic module loading.

There is also a special API done for DTV in LUA, which can handle useful information like current channel, volume.

Ok, so let's start by defining a LUA media inside our NCL document like this:
```
<media id="lua1" src="main.lua" descriptor="dLua" />
```

Then we can just create the main.lua file and create a funtion to handle this event.

```
function handler (evt)
	if (evt.class == 'key') then
		print (">> evt.key = <"..evt.key..">")
	end
	if evt.key == 'ENTER' or evt.key == 'YELLOW' then
		event.post('out', { class='ncl', type='presentation', action='stop'} )
		return true
	end
end
```

Lua is so powerful that it can be used for drawing too like this:

```
function drawLua()
	canvas:attrColor('black')
	canvas:attrFont('vera',25,nil)
	canvas:drawText(10, 10, "Lua sample") 
	--let's flush!
	canvas:flush()
```

Further example would be drawing rectangles, circles or loading images. 

There are many available examples and additional materials at [Clube NCL](http://clube.ncl.org.br/) which are great for getting started!

Working example
==============

It is possible to find awsome material in previous links, but I would like to share some gems!

* [Sokoban](/sokoban.zip) application, great way to learn
* [Sochi](/sochi.tar.gz) application from [Record](http://rederecord.r7.com).