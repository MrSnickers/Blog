March 30 2014

Happy World Back Up Day Eve!  I was trying to back up the photos I had taken on my iPhone onto an external hard-drive.  The problem is that I relied on iPhoto to do it and the external hard drive is formatted for a Windows machine.  iPhoto is a bit of a roach motel for photos and once they were in there they were segregated in over 200 different files in a Byzantine file structure.  However Unix can save the day!

After changing directory into the root of the iPhoto library it is possible to use a find command for all items of type file and to execute the command copy and send them to a folder on the desktop like so:

find . -type f -exec cp {} /Users/Sterling/Desktop/Photos \;


March 25 2014

I'm talking today at Hack and Tell about the parser I wrote to convert the NYC Restaurant Inspection results into a useful and hostable JSON file.
New York City has a very progressive policy of publicly making available all data that is acquired with taxpayer money.  They have some great and easy to use stuff up there like hosted JSON of all the greenspaces in the city that would make a very nice dynamic map.
What is not so nice is the Restaurant Inspection data provided by the Department of Health and Mental Hygiene.  It is only available as a download of a nearly 1 Gigabyte plain text file.  It's supposedly in a CSV format, but according to complaints on the boards it can't be opened properly as there are commas and double quotes in titles which disrupts the format.  I wanted it in JSON anyhow.

This is what a bit of it looks like:

"CAMIS","DBA","BORO","BUILDING","STREET","ZIPCODE","PHONE","CUISINECODE","INSPDATE","ACTION","VIOLCODE","SCORE","CURRENTGRADE","GRADEDATE","RECORDDATE"
"40280083","INTERCONTINENTAL THE BARCLAY","1","111       ","EAST   48 STREET                                                                                    ","10017","2129063134","03","2014-02-07 00:00:00","D","10F","4","A","2014-02-07 00:00:00","2014-03-20 06:01:11.660000000"
"40356483","WILKEN'S FINE FOOD","3","7114","AVENUE U","11234","7184443838","27","2014-01-14 00:00:00","D","10F","10","A","2014-01-14 00:00:00","2014-03-20 06:01:11.660000000"
"40362869","SHASHEMENE INT'L RESTAURA","3","195","EAST   56 STREET","11203","3474300871","17","2013-05-08 00:00:00","D","10B","7","A","2013-05-08 00:00:00","2014-03-20 06:01:11.660000000"
"50008280","WILD ORCHID BAR & LOUNGE INC.","4","111-48    ","LEFFERTS BOULEVARD                                                                                  ","11420","3479609997","99","1900-01-01 00:00:00","","","","","","2014-03-20 06:01:19.813000000"
"50008281","Arcade Bakery","1","220       ","CHURCH STREET                                                                                       ","10013","3472765061","99","1900-01-01 00:00:00","","","","","","2014-03-20 06:01:19.813000000"
"50008282","Coffee 11238 Ltd.","3","995A      ","FULTON STREET                                                                                       ","11238","7186831583","99","1900-01-01 00:00:00","","","","","","2014-03-20 06:01:19.813000000"
"50008285","Uncle Benny's Pizza","1","1811","1 AVENUE","10128","2012812553","99","1900-01-01 00:00:00","","","","","","2014-03-20 06:01:19.813000000"
"50008286","Espinal Restaurant","3","1039","BELMONT AVENUE","11208","7188275230","99","1900-01-01 00:00:00","","","","","","2014-03-20 06:01:19.813000000"
"81642687","FAMOUS RAY'S RESTAURANT CORP.","1","582","WEST  207 STREET","10034","8624525735","99","1900-01-01 00:00:00","","","","","","2014-03-20 06:01:19.813000000"

You may notice the fanciful use of white space and capslock.

My first idea was to take each line, which is a restaurant, and split it into an array of data elements.  I knew from the column headings which data elements I wanted and planned to populate a temporary array with the desired elements.  Then I would run that in parallel with my known column headings to make a temporary hash for each restaurant that then can be made into a JSON object.

<pre>
  desired_data = [1, 3, 4, 5, 7, 8, 10, 12]
  column_names = [:name, :street_address, :zip, :cuisine, :inspection_date, :violation, :current_grade]

  ##stuff happens here

  temp_array.each_with_index {|item, index| temp_hash[column_names[index]] = item}
</pre>

I wanted to do as little stuff as possible though, as there are over 53,000 records to process, many of which are out of date or incomplete.  I wrote in logic to have a completeness flag that would flip if any of my required data elements were missing so that I could exit the loop, not save that record, and move on as quickly as possible.

<pre>
  element_array.each_with_index do |data_element, index|
    is_complete = false if (data_element == "" && index != 10)
    ## processing code

    temp_array << data_element unless index == 4
    end
  formatted_array << temp_hash if is_complete
</pre>

But this is where things really started to balloon.  I thought I'd be able to just shovel items from the restaurant array into one half of the hash zipper right away, but more and more things needed processing.  Dates had to be checked to be sure they were current, codes were converted to human readable text, and the crazy caps lock situation on names was resolved with a specific method.

<pre>
  def namify(element)
    element.split(" ").each{|word| word.capitalize!}.join(' ')
  end
</pre>

It became evident that nearly everything needed to be processed so I had to consider if it would be more efficient to remove the iteration and select items to go into the hash directly.
For this version, I pulled all the data processing logic into its own method that contained many ternary statements that returned from the method if data was missing.

<pre>
  ## stuff before ..
  element_array[1] ? temp_hash[:name] = namify(element_array[1]) : return
  ## further processing
</pre>

I wanted to know what the difference in potential optimization was between the two approaches.  I couldn't use the "time" command for benchmarking in the Rails console, so I created a Time.now object at the beginning of the parser and another at the end.  Subtracting the two gives me the total time required to run the parser in seconds so I didn't need to sit there with a stop watch and could read Zooborns.com instead.

Surprisingly the old style parser was consistently, if only slightly, faster than the new version implying that removing iteration did not give me the hoped for optimization as I had traded it for more searching.

March 24 2014

I've been working a lot on Project Euler problems.  I try and push code to Github every day, and these little code snacks have been really fun to do between larger projects.
What I enjoy a lot about the Project Euler questions is that you can pretty much always get some kind of naive answer and the real satisfaction of the project is to optimize it all Ugly-Duckling style until it is faster, shorter, and much more snappy.

Many of the problems hinge on REALLY big numbers to make it challenging.  Since it's super boring to sit and watching your terminal grind while it calculates prime factors or what not, benchmarking is important in optimization and the Unix time command is a sweet way of knowing how much processing you have whittled off of your solution.  All you have to do is preface your Ruby execution command with "time" like so:

> time ruby problem3.rb

While writing my restaurant data scraper I wanted to benchmark my optimizations but "time" doesn't work in the Rails console.  My work around for that was to make a named Time.now object a the beginning of the parser and a second one at the end.  Then subtracting the two gave me the time in seconds that my parser ran.

Feb 23 2014

Sometimes you need to get a voicemail message off of your iPhone.  Maybe it's because you want to make a slide show at your wedding featuring the first message leading up to your first date, or your kids' message is too cute to not be your ring tone, or it's the last message left to you from a loved one. and maybe you don't want to download potentially sketchy third party software on your laptop.
Apple doesn't make this easy for you, it's not really their philosophy to let you just get in there like that, but it is possible to recover these messages with the power of the command-line.
First back up the phone to the computer without encrypting it.  Apple does a good job of scrambling up the names anyhow so you don't need to make the job any harder on yourself.
Then navigate to the directory where the files are backed up with the command:
cd ~/Library/Application\ Support/MobileSync/Backup
enter
ls -la
and there will be some super long numbers.
Although it doesn't look like it this is a folder and it holds all the data taken off your phone.  It has your voicemails, but it has everything else too, so opening each file at a time and looking isn't a winning strategy.  The safest thing to do right now is to make a backup file somewhere convenient.  I like to keep temporary folders on my desktop.
enter:
cp -a ~/Library/Application\ Support/MobileSync/Backup/<folder number here*> ~/Desktop/temp
The terminal will suggest paths if you type a few characters and hit the tab key.  This can help a lot with the folder name.


We need to change directory into the one with the most recent date so enter "cd" for "change directory" and the first couple of digits of this long number.  You can then hit the tab key and it will autocomplete the rest of it.  Slick!
Now that you have changed directory into the back up use ls -la again.

Now even more number without file extensions, but these are files even if they have bewildering names like "ff06279a81939221d4f6e2bf79e144b4f116a47a."  This is where we're gonna do some command-line magic!




for item in *; do file $item; done | grep "Adaptive" > tmp
for item in `awk -F : '{print $1}' tmp`; do cp $item ../mp3/$item.amr;done

Feb 17 2014

Our current project is in conjunction with the NYPL concerning the transcription of digital archives.  Due to the fact the particular resource we are working with is typeset we will be making use of OCR, Optical Character Recognition, on these digital images in order to assist the transcriptionist.
A bitmap image such as .png, .jpg, or .tiff is just information about the color of pixels in the image, and it takes some interesting programing to get an understanding what the pixels in an image means.
The two basic types of OCR processing are Matrix Matching and Feature Extraction.  Matrix Matching has a lower computational cost and works best with reproducible typefaces.  A phone bill could be scanned quickly and well with a Matrix Matching engine.  The computer metaphorically overlays a stencil of a letter on a grouping of pixels and records the letter with the closest match.  Feature Extraction works much more like the human visual system and searches for, well, features.  It looks for edges, monochrome fields, line intersections and other such topography.  Feature Extraction is more versatile than Matrix Matching for unusual typefaces, different sizes of the same type, or uneven backgrounds.

For this project we will be using Tesseract, an OCR engine developed by HP and made open source in 2005.  It is a feature detecting engine with a couple of optimization options.  Before embedding a language specific hidden Markov Model or training a convolutional neural network with an evolutionary optimization algorithm such as Particle Swarm Optimization there are some more basic steps you can take to improve your OCR results.
<ul>
  <li>First use a good resolution copy.  300dpi is about the minimum requirement to ride the ride.  Grey-scale or color is better than black and white.</li>
  <li>Tesseract does a lot of background and contrast adjustments itself so trying to anticipate what it wants is not very likely to to help much.</li>
  <li>If the background of the image is known to be unevenly aged, setting the background adjustments to "tile" local adjustments may work better than averaging across the entire image.</li>
  <li>Tesseract has different language files, so be sure to use the one appropriate to your particular document.  This will be important for Tesseract to anticipate what characters it might come across.</li>
  <li>It is possible to train Tesseract on a particular font by correcting and saving a early attempts at OCR.  A good tutorial is located <a href=http://pp19dd.com/2010/09/tesseract-ocr-boxfile-ajax-editor/>here</a></li>
</ul>


Jan 30 2014

Continuing iteration in JavaScript
Because JavaScript has a functional flavor, it is not really intended that methods be used for all the heavy lifting in a program.  It is more keeping with the style to echew prepackaged methods that are owned by a class in favor of having the program itself have control structures that take your object of interest as an argument. For example this is an old Ruby todo where we looked for anagrams:
<pre>
var arrayOfInterest = ["peas", "carrots", "beets", "Elvis"]
var knownAnagram = ["lives"]

var findAnagram = function findAnagram(knownAnagram, arrayOfInterest){
  knownAnagram.sort();
  sortedArray = arrayOfInterest.forEach(sort); #=> note the lack of parens after sort here.
  var returnArray = [];
  for (i=0; i &lt sortedArray.length; i++){
    if (sortedArray[i].downcase === knownAnagram.downcase){
      returnArray.push(arrayOfInterest[i]);
    }
  }
  return returnArray;
  };
};
</pre>

Jan 29 2014

I think of JavaScript functions like a vending machine.

<pre>function doSomething(){}</pre>

The parens look like a coin slot to me.  Put in parameters and something comes out.
I guess that makes empty parens the equivalent of shaking the machine.


Jan 28 2014

I miss the Ruby method .each so very, very much.

I've been refactoring many of my old Ruby todos (one thing I learned is that adorably younger me took a LITTERALLY arbitrary approach to spacing. Want two spaces, four spaces, THREE spaces, a tab? Sure, why not! Don't bother me with this formatting minutia, I'm crushing code here!), and recently I've been converting the same todos into JavaScript. 'cause you know, why not. Turns out that there is a "why not" and it is the fact that the lack of a direct .each replacement in JavaScript is actually making me depressed. I even pop open old code just to see its friendly little vowel-filled face sitting above its securely fenced code block, like looking at old photos after a breakup.

This is the todo that made me lose my mind:


<pre>##Write a function so that test = ['cat', 'dog', 'fish', 'fish']
##count(test) #=&gt; { 'cat' =&gt; 1, 'dog' =&gt; 1, 'fish' =&gt; 2 })

def count(array)
  unique_array = array.uniq
  return_hash = {}
  unique_array.each do |item|
    return_hash[item] = array.select{|repeat| repeat == item }.length
  end
  return_hash
end
</pre>
This, by the way is not at all the least processor intensive way to solve this, but because Ruby comes packaged with so many useful named modules it is at least short.  This solution relies on a lot of subtle flavors of iteration, which made my attempt at implementing it in JavaScript balloon distressingly before I realized that I don't know how to do nuanced iteration in JavaScript AT ALL:


<pre>function count(array){
  var returnValue = {};
  var givenArray = array;
  var allKeys = unique(givenArray);
  function unique(array){
    var uniqueArray = [];
    for(i=0; i &lt; array.length; i++){
      standard = array[i];
      if (!uniqueArray.some(element != standard){
        uniqueArray.concat(standard);
      }
    }
    return uniqueArray;}
  for(i=0; i &lt; allKeys.length; i++){
    returnValue[allKeys[i]] = givenArray.map{}.length;
  }


};</pre>

The thing is that a lot of the methods that I am used to in Ruby come along in the Module of Enumerable and so are implemented consistently across the different classes.  JavaScript does not have the same consistent application of methods across classes.  Partially, this is due to the greater degree of functional style in JavaScript.  It is more object oriented to have methods assigned to different classes and to only manipulate data through these pre-approved channels.  JavaScript does have a small vocabulary of methods that do iteration, largely methods in the Array class such as .every, .filter, .forEach,  .map, and .some.  The documentation could never be accused of being user friendly so I'm going to give one small but practical example of how to use a prepackaged Array method without using the term "this," "callback," or amusingly "thisp."
Let's say you want to step through an array and make each item an attribute in an object.
You can start by writing what would be your block in Ruby as the body of a function that you want executed on each element.
<pre>
    if(!containerObject[item]){
      containerObject[item] = 1 ;
    }
    else{
      containerObject[item]++;
    };
</pre>
Now if you break out your MDN network decoder ring that you got from the bottom of one of many cans of coffee you were drinking while reading documentation late at night you will discover that Array.forEach is the best method for executing a function on each element of an array and that the parameters piped from .forEach to the function that it executes are, in this order:
<ul>
<li>the element value</li>
<li>the element index</li>
<li>the array itself</li>
</ul>

so now your attempt at a block can be wrapped in proper JavaScript like so:
<pre>
  var containerObject = {};
  function addToContainer(item, index, array) {
    if(!containerObject[item]){
      containerObject[item] = 1 ;
    }
    else{
    containerObject[item]++;
  };
}
</pre>
If you feel that it is untidy naming parameters that your function doesn't use, you can leave out the unused "index," and "array" as JavaScript will just dump any extra parameters that it is given.
Now you can call your function by name as an argument to Array.forEach, letting it know what it should be doing for each item in this array.

<pre>
  var containerObject = {};
  function addToContainer(item) {
    if(!containerObject[item]){
      containerObject[item] = 1 ;
    }
    else{
      containerObject[item]++;
    };
  }

  ["fish", "cat", "fish"].forEach(addToContainer);
  containerObject;
</pre>

This is why Arrays are great.  But now lets say you just have to work with the attributes of an object.  In Ruby, Hashes include the module Enumerable so you can call .each on them.  This is not true of JavaScript objects.  The methods provided by the Object class are sparser as the functional style of JavaScript suggests that instead of using prewritten and preapproved class methods on objects that custom functions are written and invoked with the object as the parameter.
This is best achieved with a plain vanilla "for" loop.  JavaScript has multiple flavors of "for" loops although most of them are not recommended.  There is "for each..in" which I was originally excited about as it seemed like a possible .each replacement, but it has been deprecated right after being introduced, like Crystal Pepsi.


Jan 20 2014

JavaScript functions can be either named through a formal declaration or simply by naming them. JS is also well known for having unnamed "anonymous" functions that are constructed at the time of use, as functions are first class objects in JS. A typical call to an anonymous function starts with "function" and a set of parens that take the function's parameters. Often this will be empty, but it is important in JS to have them anyways, unlike in Ruby where they are optional.  In Ruby it is uncommon to use parens around parameters with the methods print or puts and when they do not take parameters such as with the method reverse they are completely invisible.
In JS it is possible, and not at all uncommon, for a parameter to be a function. This is very evidently the case in the Jasmine testing framework where many functions are nested and functions that are parameters take other functions as their own parameters.
After the parameter assignment come a set of braces that contain the statements that comprise the body of the function. After every statement there is a semicolon and after the curly braces there are not.


Jan 19 2014

Ruby and Javascript have many simlarities as they are both loosly-typed scripted languages with object orientation.  They have a few differences however.
Differences in values:
In Ruby the only falsy values are 'nil' and 'false.'
Javascript also has 'undefined' as well as 'null' and evaluates the boolean 'false', an empty string, and the number 0 as being falsy.
Differences in variables:
In Ruby, a variable can be declared without any kind of prefix or type definition.  Partially, this has to do with the fact that a variable's type is determined at runtime and thus does not need a strict delacration, but it is also because the scope of a variable is always block level unless it is very specifically declared otherwise.
In Javascript variables have the prefix 'var' and are constrained to the function level.
Differences in scope:
Ruby has five levels of scope, local, instance, class, global, and constants.  Anything other than a local variable has a special prefix character or (such as @ for a instance variable or $ for a global variable) and mostly need to be declared in a particular place in the program, often the class definition.
Javascript is a prototype based object oritented language and as such does not have class definitions.  Therefore it has fewer levels of scope. Variables without 'var' before the declaration is by default a global one.


Jan 16 2014

Whiteboards are fantastic.  One great thing about working in a group is how quickly and easily good ideas come out.  Okay, slightly less than good ideas do too, but it's all part of the process of clearing the pipes.  It's a reminder that ideas are really cheap and that acting on those ideas are not.  Another benefit of working in a group is that it is hard to truck along with black boxes in your schema.  Unobserved, it is easy to go "something something API", but it's harder to try and get away with that when you have to keep lines of communication open with a team.

Jan 14 2014

Working in a group is a step up in both power and complexity in the way that moving from Sinatra to Rails is an increase in both capability and confusion.

Jan 6 2014

Starting a Rails app is very similar to when I got my first apartment by myself and ended up inheriting two or three apartment's worth of stuff from the auntie network. There too it involved me going through a whole passel of stuff to find the four or five things that I would use every day.
When you are setting up your first kitchen with vague intentions of using it for making more elaborate meals than eating veggie dogs out of the package garnished with yellow mustard packets picked up at the hospital cafeteria, it is hard to know what you are going to need to be more accessible: the salad spinner or the one egg size frying pan. In the same way, it takes a while of hunting around in the freshly minted and voluminous Rails file structure to determine that routes.rb and somethingsomethingcontroller.rb are going to be your best friends and most of the other files are going to very much be a special occasion thing.

Jan 3 2014

Refactor Friday

<p> I went back to refactor one of the earliest homework assignments that we were given at the Flatiron School, which is the Number Guesser. Although it was all on one page and the tests totally didn't pass, I wasn't too displeased with how it has aged. Hardly fine wine, but at least not compost either.

I ended up with a very long commit history that I tried to make into the general outline of my blogpost.</p>
<ul>
<li><p><code>My terrible attempt at refactoring.</code>

On my first and unpublished attempt I tried to fix everything at once. I got halfway through moving the model into a class and expanding much of the functionality before totally losing track of where I was. Seriously kids don't apply functionality without a test.
My second and successful attempt used much more involved note taking and testing of each new feature.</p></li>

<li><p><code>Rspec now running.</code>

First I made sure that the original basic tests ran on the new model I had made.  Well, I mean first I made sure they didn't run, but that they later did, and anyways you know how it works.</p></li>


<li><p><code>Moved rspec helper code into .rspec file.</code>
This was also a good opportunity for me to take the time to clear up some loose ends from over a month ago such as making a .rspec file and properly applying stubbing.</p></li>

<li><p><code>Decided that all screen printing work belonged in app.</code>
My initial impulse was to move _every thing_ into the class.  Then I decided that with the extremly limited degree to which I could honor the concept of MVC in such a limited app that it was not the right move to have the class Numberguesser know anything about the screen.</p><br/>

<p> This reminded me of the seperation of conjoined twins by the brave folks over at <a href="peepresearch.com"> peepresearch.com<a>.
<img src="http://www.peepresearch.org/surgery/materials.jpg"></p></li>

<li><p><code>Moved input and standard comparison into class. Left reporting responsibilites in the app.
Added new syntax to app in order to obtain, evaluate, and report on guess.</code>
All these commits have to do with pulling apart the screen printing functionality from the knowing about number equivalency.</p></li>

<li><p><code>Radically shortened check_guess by relying on Ruby's logic processing and returns.</code>
<p> This is my favorite trick this week of shortening a method that checks for equivalency</p>

This: <br/>
    def check_for_equivalency <br/>

    if @input == @standard<br/>
      true<br/>
    else<br>
      false<br/>
     end
<br/>
Becomes: <br/>

  def equivalent?<br/>
  @input == @standard
<br/>

</li>
<li><p><code>Had to unchain method calls as .guess returns a string.</code>
Whaaaat?!  The "s" in gets means string.  ^%$#!!!</p></li>

<li><p><code>realized that gets converts input into string. Included to_i to counter that and a validity check to recognize post to_i strings.</code></p></li>

<li><p><code>Changed method name to refelct true false response.</code>
One of the few teachable moments here.  Having explicit names that both indicate the work it does as well as the return it gives makes for friendlier code.

Noticed that response was missing the interpolated value when I ran the program. Added it back in.</p></li>

<li><p><code>Added tests to methods</code>
MOAR TESTS!!!</p></li>


<li><p><code>Got up to get a cup of tea. Thought of more features. Didn't yet decide if they were app or class features.</code>
Proud of myself that I resisted dropping in three half-baked def's.  Wrote notes to myself that I wanted to get back to making these.</p></li>

<li><p><code>Added loop to allow repeated guesses.
can end the game with exit.</code></p></li>

<li><p><code>Added exit test and higher/lower functionality.</code>

New functionality in the app and in the class now that I know what goes where.</p></li>
</ul>


Jan 3 2014

HAML

I assume the developers are as much a fan of Star Wars as I am.

HTML Element/ HAML equivalent
<h1>...</h1>  %h1
<div class="row">...</div>  .row
<a href="index.html" target="_blank">...</a> %a{:href => "index.html", :target => "_blank"}
<div data-target=".nav-collapse">...</div> %div{:data=>{:target=>".nav-collapse"}}

% HTML element
.class-name div with class
#idname div with id
- evaluate Ruby
= evaluate and print


Jan 3 2014

Box-sizing is like packing items to ship.  It's possible to post that a shippable item is so many inches wide, but then when it's all packed up bam!  it won't fit in your mailbox anymore.  If it is of higher priority you can call box-sizing on it call it border-box and then it's the size of the shipping box that takes highest priority and your ordered item is going to get variably squished on its way to you.


Jan 1b 2014

Let's build a button.

You can make a whole bunch of things with a box-shadow.  Today, we learned about quite a few of them.
For practice we created a button entirely in CSS from an anchor element in HTML.  An impressive amount of this glossy button can be done all with one box-shadow element.  A multiple border effect can be achieved by using a number of box shadows and even a linear gradient can be replaced by an internal box shadow.  Using a negative value in a box shadow to replace a gradient provides an appealing curvature that makes the button look very glossy.


Jan 1 2014

If there are two things that get posted on the Internet it is opinions about programming and sarcasm.

Often posts are both.

The recent Medium article by Jenn Schiffer concerning "Two-factor In-Browser CSS Preprocessing" mimics a tips and tricks article.  In it she suggests authors include along with CSS preprocessing a second in-line styling to the body as a fall-back.  The joke here is that whatever is closer to the code gets implemented, so that the fall-back will always override everything else.  This is obfuscated by a slew of current buzzwords to dress it up like a good idea.

This is similar to the @horse_css joke about using the package manager "rm -rf home."


Dec 31 2013

In biology, plants evolve defenses against the bugs that eat them, then bugs evolve the ability to eat these harder-to-eat plants until the cycle reaches some pretty amazing extremes. This sort of feedback loop has also been in play in the development of HTML elements and those who would like to either make use or take advantage of them.

If you want to take a straight ahead interpretation of the establishing chapter of Mark Pilgrim's book "Dive into HTML 5" it would seem that many of the basic HTML elements were mostly just agreed upon by the members of a large email correspondence. Even if you think that seems a little simplified, it is certainly the case that elements become standard because they are useful and that browsers will adapt to respond to these tags to better display content.  Already, we are describing the first of a number of interlocking adaptive relationships that between browser developers and web page authors.

When the development of the Internet was still bathed in the light of amber CRT screens, the most compelling reason to apply an HTML tag on a piece of content that it did not accurately describe was formatting.  See also the rampant abuse of tables for spacing purposes. Soon though, browsers evolved the ability to glean and record data about web pages, and how web pages relate to each other to make it easier for users to navigate the web.  There were some interesting categorization efforts made my AOL and Yahoo! that are worth their own blog post, but the point of this story is that when the network got far too large for one metaphorical shelf Larry Page of Google fame developed his self-titled ranking system that treated the content on the web like a bucket of stuff.  A bucket of Internet that, as the name implies, had the particular quality of being networked. The general reasoning behind the Page Rank algorithm is that the most important web content is that which is regarded authoritative by its peers. In this case, he used the HTML element of a hypertext link as a stand in for a vote of confidence to the importance of another web page.  The more links to a web page from another well-linked page, the more likely it was of good quality.
This has led to interesting uses and abuses of the humble href.  One story recently in the news is the scandal of Rap Genius who were attempting to farm links from bloggers.  They requested that bloggers spam links to their site in return for favors in an attempt to stuff the virtual ballot box and drive their site higher in the ranking algorithm.
Another interesting adaptation taken on by authors is the "nofollow" attribute.  It has been taken up by authors that want to refer to a source without providing the web site the benefit of increased significance on the Web.  "nofollow" is a signal to web crawlers to discount a link in the Internet popularity contest.  For example, it could even be used by reporters who want to refer to the site Rap Genius without incidentally providing the same linking services the company was attempting to solicit.


Dec 30 2013

http://sewingandembroiderywarehouse.com/embtrb.htm is a truly spectacular site.  I recommend checking it out and using developer tools to take a peek.
It seems to have been written in early 1997, as it states in the Doctype that it is done in HTML 3.2, a version that had a fairly short lifespan.  It's surprising however that someone who would bother even having a complete Doctype would make such a obviously bizarre website.  Especially about something as detail oriented as trouble shooting embroidery machines.
The reason the web page starts to shout at the bottom is due to the amazing network of nested h3 tags.  I don't know if viewed with a contemporary browser that it was so off-the wall, but it would seem that modern browsers render nested header tags as progressively larger as they are ostensibly meant to be even more emphasized than their preceding elements.
The in-line duplication is also magnificent.  Instead of specifying in the body element or in a separate CSS document that all text should be in Ariel it is stated in front of each and every element.  This seems like a web page made largely by the efforts of cut-and-paste.


Dec 26 2013

Over the break I made use of a few implementations of databases. They were all very similar in that they were all based on SQLite3.  Although they had the same database under the hood, to belabor a metaphor a bit much, they all drove a little differently.
The two most similar gems were Active Record and Sequel. They both make use of migrations, which is a bit like a version control system for databases. Setting up a database in either system requires a specific folder and special instructional files that read much like SQL. Although this requires more familiarity with SQL- a language few have accused of being user friendly- it does make it much easier to determine what has gone wrong when there is a database-related error.
Data mapper on the other hand, makes a lot more use of Ruby Magic and although it has many comfortable Ruby conventions such as specifying a class's data inside the class description itself, when things are not running smoothly that much abstraction makes it harder to diagnose why.


Dec 16 2013
A sub-standard SQL database

Let's say you are an overly enthusiastic aunt or uncle to some kids and that you are perhaps concerned that you might not be distributing gifts equitably so you whip up a spreadsheet.  It might look something like this:

name   | Gifts
----------------------------------------------------------------------
Billie | Lego, Beanbag chair (blue), those clear plastic tubes for hamsters
----------------------------------------------------------------------
Suzy   | ride-atop-triceratops, cheese-making kit, Lego, Viking helmet
----------------------------------------------------------------------
Timmy  | Gender-neutral Veterinarian playset, Lego, Beanbag chair (green)
----------------------------------------------------------------------

For a quick trip to the store this might suffice, but if you are overly invested in your avuncular duties and need to maintain this list from before Halloween until the big day it's not very maintainable.
Let's say you have a sinking feeling the Lego distribution is inequitable.  This sort of list doesn't let you easily sort by gift type.  Or if your sibling's progeny are savvy about the price of things, they might notice that ride-atop-dinos are seldom just handed out for free and represent a much greater investment than a bunch of plastic tubes.  It would be hard to sum across a linear sheet the way that it is set up now. Lastly, if your partner perhaps wanted to help out with the project so you both could stay home drinking hot chocolate instead of braving the hyperventilating isles of TotLand on a Saturday night, it would be hard to integrate lists.

A more scalable solution would be to have multiple lists with relationships.
Each child could have a designation like so:

child_id | child
----------------
1         |Billie
-----------------
2         |Suzy
----------------
3         |Timmy

with the gifts in their own list related to child:

gift_id | gift              | cost  | child_id
----------------------------------------------
1       | cheese making kit | 25    |2

etc.


Dec 13 2013

So far, I've been quite taken with Sandi Metz's analysis that in some cases you end up having to do less work in the long-term if you do less work in the short-term, and since I have the personality type that does distance running and gets advanced degrees I am trying to fit that concept into my personal world view.
I've come to quite enjoy test driven development, which is very much predicated on the idea of more work at the front of a project saving work near the end, so I have taken a minute to synthesize these two directives. The reason that writing tests in the beginning of a project saves time is that a test does not change in what you want it to do. You want it to pass. It can change in how you want it to do that, which is why it is good practice to write the expected outcome description of your tests without stating all the steps used to get there. The steps taken inside your method might change, but it is unlikely that the expected outcome is going to.
However, what you want a class to do might change a lot during the life cycle of your program.  Both how you want it to go about doing its job as well as what its job is ultimately, and since you are always working with incomplete information regarding your classes it is best to not overbuild.


Dec 9 2013

This post is in response to the Medium article "Things I Wish Someone Had Told Me When I Was Learning How to Code" by Cecily Carver.  I found her post to be enjoyably humane and to possess a good perspective.  Sadly, I expect that this article will fall under the genre of "things that people with similar experience will agree with and those without will find cliche."
Cliches get worn out for a reason though.
I though one of her most actionable points was that before starting a major project like learning a programming language it is best to know why you are doing so.  This ties in with her later point that someone will always be willing to tell you that you are wrong.  With all the available paths that can take you to the end point of learning to code, it is easy to interpret opinion on how to get there as fact, and to get discouraged.  However, if your goal is highly specific, such as writing a native iOS app so a local museum can lend out an iPad for an interactive guide, or to make new business contacts by contributing to a specific PHP repository, there is less latitude for both Internet trolls and yourself to wonder if you are doing it all wrong.


Dec 4 2013
Mice are nice.

They look like little fat people in fur coats and have tiny pink hands, so mice are pretty great.
What they are not hugely efficient at is running actual mazes, no matter what the cartoons shown on Saturday Morning might tell you.
Thus, deciding to build a maze solver emulating mouse behavior maybe isn't the fastest way to win at mazes, but seems like an interesting thought experiment that I can really get into, since as I mentioned, I love mice.

One of the current projects that I am working on is coding a program that will solve a text maze that looks like this.

###########
#         #
# ##### # #
    #   # #
### # ### #
#     #   #
# # ### ###
# #   #    
# ### # # #
#     #   #
###########

First off, this is an interesting project from the point of view of test driven design, since the way the instructions are written it leaves it up to me to decide what it means to solve a maze.  I could have taken it in the direction of locating the index of the exit, which would have been fairly straight forward


November 26 2013

This is a post about the value of doing nothing.  While reading Sandi Metz's book about program design I have been intrigued by how often she recommends not making a decision and waiting until the point that the flaws in your program design bite you in the butt.  This doesn't seem like advice one would expect from a book about project design advocating that you, y'know, not design.
Here's what makes it interesting: It is most likely the best choice to sit in stinky code than clean it up even if it bites you more often than not.  Here's the math I did while smooshed in a commuter train.

Let's say that you optimize classes for one hour every time the chance comes up because it takes two hours to fix stinky code.  In the case that half the classes stink this is a wash.  For eight classes, assuming that your optimization is right every time, you invest eight hours of work to save eight hours of work.

What this doesn't take into account, though, is that optimizing too early means that some of the time your code will get stinky anyhow so optimizing for eight hours to avoid eight hours of work costs more because it is unlikely that you will avoid all future stink.  However, waiting allows you to identify what will need fixing and allow you to only spend time optimizing the code that needs it.

So ultimately you can do less by doing less.


November 24 2013

Start from Impossible

There is a phenomenon that I have heard called parking lot science.  The term comes from a shaggy dog style joke about this guy who is searching for his car keys, not where he most likely to have lost them but in the parking lot where the light is better.
Research gets called "parking lot science" when the questions asked are not particularly interesting or effective, but the techniques used are easy or well-known.  The desire to search around in the parking lot can be caused by knowing your field enough to know what is easier or possible.
The encouragement "start from impossible" seems like it is meant to avoid this particular phenomenon and is a rebranding of the much older concept of beginner's mind.  A mind that maintains the openness that it has as a beginner does not bother itself with degrees of possibility, as everything seems equally possible.
Personally, I find myself doing parking lot testing.  I know how to test for equivalency and data type, and so far almost all my tests have been of that type, whether that is the best way to test my program or not.  Usually, I find myself doing it because I feel a time crunch, and I know that if I have to learn to test for error messages or how to use stubs, this is going to take much longer than I want.  Beginner's mind just assumes the need to learn constantly and does not prejudge against what would take longer or be easier.


November 20 2013

The Single Responsibility Principle of Classes

This post is in response to the Designing Classes chapter of Practical Object-Oriented Design in Ruby by Sandi Metz

Boy Howdy, is this a quotable chapter.  Not maybe stuffed with the most helpful examples, but the concept of single responsibility in Classes is pretty interesting.   It reminds me of the "Job to be done" concept of Christiansen's.  If your class seems to be doing more than would be expected with a single job description, it's probably stepping on toes.


November 19 2013

Why documentation should be sold

I met this nice woman a bit of a while ago who had a traveling road show that went to schools and ran surprisingly non-hokey workshops that got kids excited about achieving their dreams.  She seemed semi-apologetic that her business wasn't a non-profit but instead the way she paid her rent.  We're highly conditioned to think that pro-social activities should be free and untainted by anything so crass as folks paying rent, but it really spoke well of her workshops that schools were willing to shell out so that their students could have what they thought was a valuable experience.  I'm pretty sure that, since I didn't play sports, my public high school in suburban California payed as little as possible to process me.  I remember presentations from the utilities district and the local wild-life rehabilitation center (Don't steal owls from the wild, folks.  They're no good as pets, and it's terrible for the owls.)  I feel confident they didn't charge the school money.

However, there is a pressure to someone selling a product to make it worth the price.  Which is what brings us to documentation.  It is horrible.  Although I am not obligated to pay a price to get it, it has a very real cost in time and aggravation since it is written either by a program or volunteers who can't go to sleep at night because someone on the Internet is wrong.  The 99 cent app I bought so I can keep a virtual aquarium on my iPhone, has more expectation to deliver its product of fishy relaxation than the gold standard of ruby knowledge has to actually answer my questions.

Developers, even junior developers, get paid a lot so providing documentation that actually saves time by answering questions easily would be worth a lot.


November 18 2013

Variables in a block are like fish in a fishbowl.

I made some ascii art to demonstrate this, but it got warped beyond recognition by Github.


November 17 2013

There seems to be some logical fallacies comparing iteration in ruby structured as:
<pre>
3.times do |x|
  (x+1)*5
end
</pre>
with the alleged JavaScript equivalent:

<pre>
for (var i = 1, i < 4, i++) {
  console.log( (i+1)*5);
}</pre>

even though they will print the same output.

<pre>3.times</pre> is often used as an example of how writing Ruby is like talking to your new best friend, and is a little bit of hyperbole picked up from the remarkable work "why's poignant guide to ruby."  However, there is very little heavy lifting that is going to be accomplished with this picturesque little construct.  A more direct equivalent to the previous JavaScript block is the Ruby block:

<pre>
  x = 1
  while x < 4
    (x+1)*5
    x += 1
  end
</pre>

as both blocks can be modified in the same important ways that make them common features in practical code.

One major advantage that Ruby has as a starter programming language is that the reduced amount of punctuation means that one's dumb beginner mistakes are more likely centered around flawed logic than an Easter egg hunt for a missing semi-colon.


November 14 2013

The approach that I took to solving FizzBuzz was to base it on a "while" loop and to assess the remainder of each increment when divided by 3, 5, and both 3 and 5.  This required three checks and three "puts" statements, and was pretty straight forward if not all that clever.

The solution looked like this:
<pre>
  i=0
  while i <= 100
    if i%3 == 0 && i%5 == 0
      puts "fizzbuzz"
      i +=1
  elsif i%3 == 0
      puts "fizz"
      i +=1
  elsif i%5 == 0
      puts "buzz"
      i +=1
    else
      puts "#{i}"
      i +=1
    end
  end
</pre>

The online alternative I liked the most made use of shoveling into an array to create the word FizzBuzz.  By conjoining the output of divisibility by 3 and divisibility by 5 "FizzBuzz" was not treated as a seperate word, and I found that interesting. 

<pre>
  def fizz_buzz(max)
    arr = []
    (1..max).each do |n|
      text = ""
      if (n % 3 == 0)
        text << "Fizz"
      end
      if (n % 5 == 0)
        text << "Buzz"
      end
      if !((n % 3 == 0) || (n % 5 == 0))
        text = n
      end
      arr << text
    end
    return arr
  end
</pre>


November 12 2013

There is a sinking feeling one gets when having just altered one's bash profile all that gets returned is "not a valid identifier."  That's pretty discouraging.  Also, it turns out that it is a fairly unproductive error message to cut and paste into Internet search.  Much of the results I got when searching with the exact error message were over six years old.

In order to resolve the error I had to remove all the white space from the export PATH that I had recently put in there in order to make it more readable.  More readable for me, but apparently unreadable to the machine.  I guess I had been taking for granted the human friendly characteristics of Ruby.


November 11 2013

This post is in response to the post on optimism by Mr. R. S. Braythwayt, esq. found at http://braythwayt.com/homoiconic/2009/05/01/optimism.html

I usually get fairly tense reading posts that discuss psychology research.  I sort of scootch over to the side of my chair.  Perhaps I am concerned that I might pull an extraocular muscle from the imminent eye rolling once the results from one unimportant but highly quotable study gets generalized into a global statement about why we do everything that we do.

Fortunately, my anxiety was not founded.  The writing itself refreshingly avoided becoming maudlin, and Mr. Braythwayt comes across as being a pragmatic gentleman.  Since he chose to keep his message clear in order to make it more actionable, it's good that he took a while getting to the punchline and invited the reader to attempt a prediction concerning optimistic behavior.  Going straight to the message that most folks over value negative experiences and do not put as much emphasis on the things that go right would have ended up just being a recitation from the Journal of Things We Already Know.

But just because we already know something doesn't mean it isn't important.

A week ago I attended the Bris of my friend's baby.  This came after weeks of a complicated pregnancy in which the life of both the mother and son had been imperiled.  During the ceremony, the cantor asked that the attendees make a point of continuing to work as hard to make time to observe happy occasions as sad ones.  In the case of a funeral, it seems important enough to leave work and pay for a ticket to attend in person, but attending a wedding or visiting a new baby does not.  Leaving a blog comment because someone is wrong on the Internet seems worth populating a text field, when having an equally strong, but positive opinion does not.  This is why so much of the Internet is a bad neighborhood.


November 2 2013

Had a dream last night in which I was taking a quiz on the topic of git.  I remember that the correct answer was "d.  Commit early and often."


October 31 2013

More MySQL commands

SELECT movies.title genres.name FROM movies INNER JOIN genres ON movies.genre_id = genres.id;
** why this repeatedly didn't work. Inconsistencies between pluralization.  In the future avoid sets of variables that are the same word both in singular and in plural.

SELECT movies.title, genres.name FROM movies LEFT OUTER JOIN genres ON movies.genre_id = genres.id;

SELECT COUNT(*) AS user_count FROM users;

SELECT MIN(score) AS min_score FROM reviews WHERE movie_id = 2;

SELECT SUM(score)/ COUNT(score) AS average_all_scores FROM reviews;


October 28 2013

MySQL
CREATE TABLE movies (title VARCHAR(255), year INTEGER);

SELECT * FROM table;

VARCHAR (short text)
TEXT (a whole lot more writing)

Makin' Tables
CREATE TABLE actors (name VARCHAR (50));
CREATE TABLE movies (title VARCHAR (200), year INTEGER);


Moddin' Tables
INSERT INTO movies VALUES ("AVATAR", 2009);

Numeric types
- INTEGER
- DECIMAL
- FLOAT

Date & Time
- DATE
- TIME
- DATETIME


October 23 2013

Talking about blocks pretty quickly turned into talking about the ".each" method as that has been a particular stumbling block for some of the students.  Or at least I know this one.  A well formed use of ".each" looks like:

<pre>
  object.each do |element|
    block of code that does something with "element"
  end
</pre>

The whole conversation about what "element" is and why it doesn't need to be declared like other variables ended up taking us on a tour of how all the parts work behind the scenes.


October 22 2013

Today was a plain text kind of day.  It just seemed to turn out that way.  Along with the general workshop on Git I also learned such fantastic things as how to scoot around in the terminal while keeping my mouse in my bag.  Splat+Tab is a good friend and is by far the best way to switch between Sublime and the terminal while I try check my work on the Ruby Assessment.


October 21 2013

Spent most of the day working on Ruby.  Besides being the general topic of the week, it's what I worked on the most prior to acceptance, so I feel less behind.
The whole loops thing and control flow let me feel smart.  Blocks, procs, and lambdas do not.
I would be interested in seeing some in the wild.