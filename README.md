lowLag.js
=========
a simple wrapper for low-latency, high-compatibility, html5-friendly audio
--------------------------------------------------------------------------
See the official homepage [lowLag.alienbill.com](http://lowlag.alienbill.com/) for more details.

lowLag.js is a wrapper providing an *extremely* simple interface (initialize, load a sound, play a sound) for 3 different technologies:

*   WebKit Audio (for Chrome and recent/upcoming versions of Safari),
*   the &lt;audio&gt; tag (for Firefox)
*   SoundManager2 (for Flash support in IE, and as an audio tag fallback for everything else)

Download & Installation
-----------------------
You need two files:

*   The .js file lowLag.js
*   The unzipped contents of sm2.zip

I recommend unzipping that into the directory your html file is in. (So that the relative paths sm2/js/ and sm2/swf are available to the script...)

Unzip the sm2.zip file and move things so its contents can be accessed by the html5 page. Do similar for the file lowLag.js.

Add this to the top of your html file:

`<script src="/path/to/lowLag.js"></script>`  
`<script src="/path/to/soundmanager2.js"></script>`  

Finally, if the swf directory is something other than "sm2/swf" relative to your html file, you sill have to set the `sm2url` parameter when you call lowLag.init().

Usage
---------------
Simplest example:

`lowLag.init();`  
`lowLag.load("pluck.mp3");`  
`lowLag.play("pluck.mp3");`  

That would be (pretty much) the whole story, except... Firefox doesn't support MP3s. So lowLag.js borrows the concept of a sound group - a set of identical but different format sound URLs that you label and then play via a tag. All 3 modes support this (more or less: Webkit just plays the first one in the group, but you don't have to change any code.) Sample:

`lowLag.init({"urlPrefix":"sounds/"});`  
`lowLag.load(["pluck.mp3","pluck.ogg"],"pluck");`  
`lowLag.play("pluck");`  
Including an mp3 and ogg version gets you very high percentage coverage across browsers.  (That was also example of using configuration options to `lowLag.init()`.)

Note: lowLag contains a convenience version of the classic jQuery "ready" function. If you have a page without jQuery, you can put your custom function calling `lowLag.init()` and `.load()` into a call to `lowLag.ready()`. 

Command Options
---------------
*`lowLag.init()`* takes one optional argument, a hash of configuration options:

*   `force` -- Value can be 'webkitAudio', 'audioTag', or 'sm2'. With no argument, lowLag.js will pick the best for the current browser (webkit for Browser that support that as a feature, the <audio> tag if it detects Firefox (preferred over SoundManager2 for performance reasons) and finally SoundManager2, which will first try Flash and fallback to the <audio> tag.)
*   `useSuspension` -- When using the Web Audio API or 'webkitAudio' this flag allows the audio context to shut itself down - saving power and addressing issues with certain mobile devices - after `suspendTimeout` milliseconds of disuse.
*   `suspendDelay` -- How many milliseconds to wait before shutting down the audio context if `useSuspension` is in force.
*   `urlPrefix` -- The value of this key will be directly prefixed to URLs when loading sound files. (Default "", i.e. same directory as the html file)
*   `audioTagTimeToLive` -- To allow for simultaneous sounds, <audio> tags are cloned and then destroyed. This value is the time (in millis) before the file is destroyed. Default is 5000, or 5 seconds: if your clips are longer than that you may wish to adjust this value accordingly.
*   `sm2url` -- 
Location of the directory for Soundmanager2's .swf files... defaults to 'sm2/swf/'
*   `debug` -- 
Values can be 'console', 'page', 'both' or 'none'-- determines where information gets sent. Default to 'console'.

`lowLag.load(urls,tag)` -- `urls` can be a string (containing a single sound file URL) or an array (containing a group of the same sound in different file formats) `tag` is optional: if present, it will be used by the "play()" command. If absent, `urls` as a string will be used, or the first entry in the urls array. (Note: webkit Audio will only use the first file in a urls group, so order things accordingly. (i.e. start with .mp3))

`lowLag.play(tag)` -- Play the sound associated with this tag.

Caveats
-------
*   You might have trouble with certain browser/mode combinations if you are trying to read the sound files from your local filesystem, rather than from a server... Google Chrome in particular has some stringent requirements for data, but so does IE... one workaround is to use Python (OSX should have it pre-installed) and run a micro webserver as described here.
*   If your sound clips are longer than 5 seconds, be sure to set audioTagTimeToLive to something more than 5000 in lowLag.init()!
*   Some browsers decline to play audio if it is triggered by the "onLoad()" or similar, without user intervention. You may wish to test across browsers, especially if you are auto-playing something.
