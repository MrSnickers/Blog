Jan 1 2014

The Internet is "funny"

If there are two things that get posted on the Internet it is opinions about programming and sarcasm.

Often posts are both.

The recent Medium article by Jenn Schiffer about "Two-factor In-Browser CSS Preprocessing" mimics a tips and tricks article that suggests along with CSS preprocessing adding a second in-line styling to the body as a "fall back" the joke here is that whatever is closer to the code gets implemented so that the fall-back over rides everything else.  This is obsfucated with a slew of current buzzwords to dress it up like a good idea.

This is similar to the @horse_css joke about the package manager "rm -rf home."


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

name    |Gifts
----------------------------------------------------------------------
Billie | Lego, Beanbag chair (blue), those clear plastic tubes for hamsters
----------------------------------------------------------------------
Suzy  | ride-atop-triceratops, cheese-making kit, Lego, Viking helmet
----------------------------------------------------------------------
Timmy | Gender-neutral Veterinarian playset, Lego, Beanbag chair (green)
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

gift_id | gift    | cost  | child_id
------------------------------------
1        | cheese making kit | 25  |2

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

    3.times do |x|
    (x+1)*5
    end

with the alleged JavaScript equivalent:

    for (var i = 1, i < 4, i++) {
    console.log( (i+1)*5);
    }

even though they will print the same output.

3.times is often used as an example of how writing ruby is like talking to your new best friend, and is a little bit of hyperbole picked up from the remarkable work "why's poignant guide to ruby."  However, there is very little heavy lifting that is going to be accomplished with this picturesque little construct.  A more direct equivalent to the previous JavaScript block is the Ruby block:

    x =1
    while x<4
    (x+1)*5
    x += 1
    end

as both blocks can be modified in the same important ways that make them common features in practical code.

One major advantage that Ruby has as a starter programming language is that the reduced amount of punctuation means that one's dumb beginner mistakes are more likely centered around flawed logic than an Easter egg hunt for a missing semi-colon.


November 14 2013

The approach that I took to solving FizzBuzz was to base it on a "while" loop and to assess the remainder of each increment when divided by 3, 5, and both 3 and 5.  This required three checks and three "puts" statements, and was pretty straight forward if not all that clever.

The solution looked like this:

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

The online alternative I liked the most made use of shoveling into an array to create the word FizzBuzz.  By conjoining the output of divisibility by 3 and divisibility by 5 "FizzBuzz" was not treated as a seperate word, and I found that interesting. 

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

object.each do |element|
_block of code that does something with "element"_
end

The whole conversation about what "element" is and why it doesn't need to be declared like other variables ended up taking us on a tour of how all the parts work behind the scenes.


October 22 2013

Today was a plain text kind of day.  It just seemed to turn out that way.  Along with the general workshop on Git I also learned such fantastic things as how to scoot around in the terminal while keeping my mouse in my bag.  Splat+Tab is a good friend and is by far the best way to switch between Sublime and the terminal while I try check my work on the Ruby Assessment.


October 21 2013

Spent most of the day working on Ruby.  Besides being the general topic of the week, it's what I worked on the most prior to acceptance, so I feel less behind.
The whole loops thing and control flow let me feel smart.  Blocks, procs, and lambdas do not.
I would be interested in seeing some in the wild.