November 18 2013


Variables in a block are like fish in a fishbowl.

        ==========
        /                     \
       {                         }
     |                            |
    (         ( ''     '  ,         )
     |       '')      '<><    |
      \    (            '       /
       -------------
         ____________




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