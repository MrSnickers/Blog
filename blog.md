October 31 2013

More MySQL commands

SELECT movies.title genres.name FROM movies INNER JOIN genres ON movies.genre_id = genres.id;
** why this repeatedly didn't work. In consistancies between pluralization.  In the future avoid sets of variables that are the same word both in sigular and in plural.

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

Today was a plain text kind of day.  It just seemed to turn out that way.  Along with the general workshop on Git I also learned such fantastic things as how to scoot around in the terminal while keeping my mouse in my bag.  Splat+Tab is a good friend and is by far the best way to switch between Sublime and the terminal while I try check my work on the Ruby Assesment.

October 21 2013

Spent most of the day working on Ruby.  Besides being the general topic of the week, it's what I worked on the most prior to acceptance, so I feel less behind.
The whole loops thing and control flow let me feel smart.  Blocks, procs, and lambdas do not.
I would be interested in seeing some in the wild.