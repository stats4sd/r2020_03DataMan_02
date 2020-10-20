---
title: "Manipulating Data using dplyr: Part 2"
output: 
  learnr::tutorial:
    progressive: true
    allow_skip: true
    df_print: paged
runtime: shiny_prerendered
description: >
  Learn to manipulate data with the package dplyr.
---





## Sequence of manipulations

[Embed video #2]

As explained in the video, if we don't store the results of our commands, there is no way to re-use them. This is actually true for the great majority of R commands, not just those involving the core functions of dplyr. So to perform a sequence of manipulations, we need to either:

- store the result of each manipulation as a dataframe, to then make it the first argument of the next function.
- combine all the manipulations we want to perform into one single command using the pipe operator.

You already know everything there is to know to perform to perform a sequence of manipulations via the first option.
For example, if we wanted to calculate summary statistics based only on the movies in the data only, we could do the following:

<div class="tutorial-exercise" data-label="sequence_1" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_movies <- filter(imdb, type=="movie")
summarise(imdb_movies, entries=n(), meanVotes=mean(numVotes), meanRating=mean(averageRating), sdRating=sd(averageRating))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
First we use `filter()` to keep only the entries of type "movie". We store the result as a new object, called "imdb_movies". Then we use the `summarise()` command, but with the newly created object as the first argument instead of the full imdb dataset.



**Question: change the command to get summaries for the tv series instead of movies**

<div class="tutorial-exercise" data-label="sequence_2" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_movies <- filter(imdb, type=="movie")
summarise(imdb_movies, entries=n(), meanVotes=mean(numVotes), meanRating=mean(averageRating), sdRating=sd(averageRating))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

<div class="tutorial-exercise-support" data-label="sequence_2-solution" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_tvSeries <- filter(imdb, type=="tvSeries")
summarise(imdb_tvSeries, entries=n(), meanVotes=mean(numVotes), meanRating=mean(averageRating), sdRating=sd(averageRating))
```

</div>

We could do the same for each type of entry, but there is a much better approach: using `group_by()`, the last core function of the package dplyr.


## group_by()

`group_by()` tells R to separate a dataset into groups, based on the values of a column. All the subsequent operations performed on the resulting "grouped" dataset are applied to each group rather than to the whole dataset. For the syntax, we indicate the dataset first, as usual, and then we indicate the column whose values will define our groups. Let's group our dataset by type of entry

<div class="tutorial-exercise" data-label="group_1" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
group_by(imdb, type)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
It looks like nothing happened to our dataset, but it's just because the grouping is invisible. We need to apply another function to see the effect of group_by(). Let's store our grouped dataframe in an object called say, "imdb_type", and use this object as the first argument of the function summarise()

<div class="tutorial-exercise" data-label="group_2" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_type <- group_by(imdb, type)
summarise(imdb_type, entries=n(), meanVotes=mean(numVotes), meanRating=mean(averageRating), sdRating=sd(averageRating))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

**Question: Investigate the result of the previous command. What are the 3 video games in the imdb dataset? (Note: it's not a question where you need to use group_by)**
<div class="tutorial-exercise" data-label="group_3" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0"><script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
**Are you surprised by such a low number of entries? Do you trust the values for the average number of votes? Double-check the `Appendix: "imdb" dataset` to remind yourself how the dataset was constructed.**

<div class="tutorial-exercise-support" data-label="group_3-solution" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
# A command like
filter(imdb, type=="videoGame")
# shows that the three video games are Dante's Inferno, GoldenEye 007, and a short film related to the Divergent Series, that has probably not been very well categorized.

# One might be surprised at this low number of video games, but the appendix says that the imdb dataset contains the entries having more than 500 votes. It's probable that most video games don't get many votes, as imdb is not really known to also host video games in the database.
# For the same reason, I would not trust the values for the average number of votes here, since by construction, the dataset is missing all the entries with less than 500 votes. This is less of an issue for the average rating summaries though, where focusing on the entries with a reasonable number of votes prevent the results to be biased by the entries that would only be rated by the entourage of the director or so.
```

</div>


The effect of `group_by()` on the result of `summarise()` is very intuitive. We obtain the calculated summaries for each of the groups defined by group_by. At first, it might be slightly less obvious that `group_by()` is also very useful in combination with `filter()` or `mutate()`. But consider the case where we would like to obtain for each type of entry, the one that received the highest number of votes. With filter, we can easily get the entry that has received the highest number of votes in the full dataset:
<div class="tutorial-exercise" data-label="group_4" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
filter(imdb, numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
But the highest number of votes in the the full dataset corresponds to the highest number of votes of the movies. For the other types of entry, the maximum number of votes is a lot smaller. And so the associated most popular entries are not captured by our function filter. 

Using `group_by()` first, and *then* `filter()` would restrict the scope of `numVotes==max(numVotes)` to the group of the entry, and so if the entry has the highest number of votes in the group, filter will retrieve it.

<div class="tutorial-exercise" data-label="group_4a" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_type<-group_by(imdb, type)
filter(imdb_type, numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

That's an interesting list that we have here. I'd definitely recommend you check the most popular entry of type "short" if you have not watched it yet. 

**Question: For each year of release (instead of type of entry), retrieve the entry with the highest average rating (instead of highest number of votes)**
<div class="tutorial-exercise" data-label="group_4b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0"><script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

<div class="tutorial-exercise-support" data-label="group_4b-solution" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_year<-group_by(imdb, year)
filter(imdb_year, averageRating==max(averageRating))
```

</div>


### ungroup()
What if we wanted to further filter the previous result to only keep the entry that had the least number of votes, out of the ones that received the most votes for each entry. Said differently, we got the most popular entry of each type, and I now want to know which of these is actually the least popular. It seems like storing the previous result and then applying a second function filter would do the trick. Let's try.

<div class="tutorial-exercise" data-label="group_5" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_type <- group_by(imdb, type)
mostPopular <- filter(imdb_type, numVotes==max(numVotes))
filter(mostPopular, numVotes==min(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

The result is the same as before. That's because the grouping still applies, so we are actually asking, for each type of entry, to keep only the entry that received the least number of votes. But there is already only one entry by type, so this entry is inevitably also the one that is the least popular. What we need to do, is add an intermediary step where we break the grouping, by using the function `ungroup()`, which also takes the dataframe as its first argument.
<div class="tutorial-exercise" data-label="group_6" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_type <- group_by(imdb, type)
mostPopular <- filter(imdb_type, numVotes==max(numVotes))
mostPupularUngrouped <- ungroup(mostPopular)
filter(mostPupularUngrouped, numVotes==min(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
You may have noted that my spelling of *popular* was wrong in the name I gave for the third created object (*mostP**u**pularUngrouped*). It is not a problem for R, because names are defined by users. It will be an issue for you though, as it will quickly become difficult to remember the right spelling of all your objects.


The code above starts to be quite messy, with lots of intermediary dataframes that we are not really interested in. One thing you may suggest to simplify our set of commands is to have only one intermediary dataframe, that we overwrite. Something like
<div class="tutorial-exercise" data-label="group_7" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
temp_data <- group_by(imdb, type)
temp_data <- filter(temp_data, numVotes==max(numVotes))
temp_data <- ungroup(temp_data)
filter(temp_data, numVotes==min(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

It looks slightly simpler maybe, and show you that when creating objects with `<-`, it makes no difference whether the name of the object is new or not. If it is not new, R will just overwrite the old object.

But we can make our command much more clean and readable if we use the pipe operator.





## pipe %>%

The symbol used for the pipe operator in R is `%>%`, that is a symbol greater than `>` surrounded by two percentages `%`. This operator is extremely useful because it makes it possible to perform a sequence of data manipulations using dplyr functions, without having to create any intermediate dataframe. This is due to the consistent syntax of these dplyr functions, and in particular, the fact that their first argument is always the datafame that we want to manipulate.

Because what the pipe operator does is to tell R

> take what's on my left, and make it the first argument of the next function on my right or below me

So if in the command `thing1 %>% thing2`, `thing1` is a dataframe and `thing2` is a dplyr function, the pipe operator will ask R to make the dataframe the first argument of the dplyr function. And R will happily perform the corresponding manipulation on the dataframe since it results in a valid command.

<div class="tutorial-exercise" data-label="pipe_1" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>% filter(numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

In the above commands, the pipe operator asks R to take what's on its left - the dataframe `imdb` - and to make it the first argument of what's on its right - the function `filter()`.
The command is therefore equivalent to

<div class="tutorial-exercise" data-label="pipe_1b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
filter(imdb, numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Instead of placing the function filter to the right of the pipe, we can, and usually should place it below the pipe, with a little indentation, similar to what you do with the `+` in ggplot2. It's good practice for readability, and it doesn't change anything. R will see the pipe and look for the next command. This command happens to be below the pipe rather than on its right.

<div class="tutorial-exercise" data-label="pipe_2" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
 

What is great with pipes is that the *what's on my left* can well be a command itself, if the result of the command is a dataframe. So we can redo the last commands of the previous section, using pipes.


Our commands were
<div class="tutorial-exercise" data-label="pipe_2b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb_type <- group_by(imdb, type)
mostPopular <- filter(imdb_type, numVotes==max(numVotes))
mostPupularUngrouped <- ungroup(mostPopular)
filter(mostPupularUngrouped, numVotes==min(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>


Using pipes it becomes:

<div class="tutorial-exercise" data-label="pipe_3" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  group_by(type) %>%
    filter(numVotes==max(numVotes)) %>%
      ungroup() %>%
        filter(numVotes==min(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

We start with the dataset `imdb`. The pipe next to it will make it the first argument of the function `group_by()` that follows. The next pipe makes the resulting command the first argument of the first function `filter()`. The next pipe takes the result of all of this and make it the first argument of the next function, which is `ungoup()`. And the last pipe makes the resulting dataframe, the first argument of the second function filter. Here we go. We have a neat and concise command! Note that when using pipes, the output from the previous line always takes the place of the ‘data’ argument we have seen so far. So when using the commands with the pipes, we skip straight to the second argument. Sometimes we might not have a second argument, like with ungroup(). So here, we don’t need to write anything within the brackets. Also note that the order of our commands matter. Try and see if you can understand what happens when you change the order of these commands.


And that's where things start to be very interesting. Because with pipes, it is not a pain anymore to perform a long sequence of manipulations. So we can really start to have fun!


**Question: Determine for each type of entry, which is the best rated title using pipes?**

<div class="tutorial-exercise" data-label="pipe_4" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0"><script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
<div class="tutorial-exercise-support" data-label="pipe_4-solution" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  group_by(type) %>%
    filter(averageRating==max(averageRating))
```

</div>



## Bringing it all together


Let's now continue exploring this imdb dataset using all the knowledge acquired so far. We will start using the columns indicating the genre of the entries (`animation`, `action`, `adventure`, etc.). Let's use the function select to have a better look at these columns:

<div class="tutorial-exercise" data-label="all_0" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
select(imdb, animation:thriller)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

If you look below each of the column names, there is a grey set of weird characters. It indicates the type of the variable. Here the set of characters is `<lgl>` and "lgl" is a shorthand for 'logical'. So these Genre variables are of type 'logical'. The only values that a logical variable can take are TRUE and FALSE, which are special values used by R to determine if a condition is true or false.

For that reason, if we want to filter our rows based on these columns, say we want to keep only the rows where `sci-fi`equals "TRUE"; we can omit the usual quotes around the TRUE:

<div class="tutorial-exercise" data-label="all_0b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(sci_fi==TRUE)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>


Now if you are a bit into science-fiction like me, you may like to know what are the most popular entries of this genre, for each type of entry.


Let's group the result of our command filter by type:
<div class="tutorial-exercise" data-label="all_0c" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(sci_fi=="TRUE") %>%
    group_by(type)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
Nothing changes. Normal, since the grouping done by `group_by` is invisible.

Let's then use filter again to retrieve the most voted entries for each of our groups.
<div class="tutorial-exercise" data-label="all_1" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(sci_fi=="TRUE") %>%
    group_by(type) %>%
      filter(numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>


And let's not hesitate to add a `select()` at the end of our command, to get rid of the columns that we don't want and rearrange the columns we want for better readability.

<div class="tutorial-exercise" data-label="all_2" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(sci_fi=="TRUE") %>%
    group_by(type) %>%
      filter(numVotes==max(numVotes)) %>%
        select(title, averageRating, numVotes, type)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
The tv short entry in the list is a definite little gem according to Sam. I will probably watch it on the weekend.


**Question: Try to change the order of the commands. What happens if you start with select()? And what happens if you combine the two `filter()` together, by bringing `sci_fi=="TRUE"` inside the second command filter? Take some time to analyse the commands and outputs before looking at the solution**

<div class="tutorial-exercise" data-label="all_2b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(sci_fi=="TRUE") %>%
    group_by(type) %>%
      filter(numVotes==max(numVotes)) %>%
        select(title, averageRating, numVotes, type)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

<div class="tutorial-exercise-support" data-label="all_2b-solution" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
# select at the start
imdb %>%
  select(title, averageRating, numVotes, type)
    filter(sci_fi=="TRUE") %>%
      group_by(type) %>%
        filter(numVotes==max(numVotes)) %>%
# we get an error, because after the command select, the column sci-fi is not in our dataset anymore.

# filter combined
imdb %>%
  group_by(type) %>%
    filter(numVotes==max(numVotes) & sci_fi=="TRUE") %>%
      select(title, averageRating, numVotes, type)
# we only get one entry instead of one entry per type. That's because apart for the type "tvMovie", the most voted entry was never of Genre sci-fi. So the command filter retrieves nothing.
```

</div>



Of course, if we want to store the result of our full command into a dataframe object for later use, we can do that.

<div class="tutorial-exercise" data-label="all_3" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
best_scifi <- imdb %>%
  filter(sci_fi=="TRUE") %>%
    group_by(type) %>%
      filter(numVotes==max(numVotes)) %>%
        select(title, averageRating, numVotes, type)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>



Something we can also do is continue our commands with functions that are not part of dplyr, because pipe is so popular that lots of the most recent packages provide functions that are compatible with pipes. And as said in the video, `ggplot()` is compatible with pipes, since its first argument is the dataframe used to make the plot.



Let's try and make a little scatter plot showing the average rating (on the y axis) of the most voted on movie released per year, where year is on the x axis. How are we going to do that?

Well we know that we're only interested in movies, so a safe first step would be to filter out the other entries using filter:
<div class="tutorial-exercise" data-label="all_4a" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Then we probably need to think a little. We want to plot the average rating for each year. Fine, we have both columns in the dataset, so no need to create new ones. But we don't want to plot all the entries on our graph. We only want the entries that received the highest number of votes for that year. Ok, so we will need a second filter. If we use the second filter now, we only get one point to plot:

<div class="tutorial-exercise" data-label="all_4b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    filter(numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Instead, we want one data point per yer... ok I think we all got it right? We need to group our dataset per year first
<div class="tutorial-exercise" data-label="all_4c" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Nothing happens, as usual, which is fine.
Then we use our second filter:

<div class="tutorial-exercise" data-label="all_4d" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year) %>%
      filter(numVotes==max(numVotes))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

I'm pretty sure our resulting data is ready for the scatter plot, but you may not be, so I suggest we add an extra arrange to order our rows by year and check that indeed, we have one unique entry for each year. `arrange()` doesn't do anything other than order rows, so we can safely add it to our stack of commands.

<div class="tutorial-exercise" data-label="all_4e" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year) %>%
      filter(numVotes==max(numVotes))%>%
        arrange(year)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>
Note that we didn't need to use `ungroup()` before `arrange()`, because the function arrange is not affected by groupings.

Ok I think it looks good. Let's now add our ggplot command with `year` as x and `arverageRating` as y
<div class="tutorial-exercise" data-label="all_4f" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year) %>%
      filter(numVotes==max(numVotes)) %>%
        arrange(year)%>%
          ggplot(aes(x=year, y=averageRating))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Note that we don't need to indicate the usual "data=..." as the first argument, since the pipe already does it for us.

... and finally, let's add a geom. We want a scatter plot, so we use `geom_point()`

<div class="tutorial-exercise" data-label="all_4g" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year) %>%
      filter(numVotes==max(numVotes)) %>%
        arrange(year)%>%
          ggplot(aes(x=year, y=averageRating))+
            geom_point()
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Yes, we have it! So let's recap. We filtered our dataset to keep only the entries of type movie. We then grouped those movies by year. We used filter again to only keep the most popular movie released in that year We reordered the rows to check that everything was ok. And we finally made our scatter plot. If performance was a concern, I would definitely remove the command arrange now, which is unnecessary. But I believe our server should be fine.


Looking at the graph, there seems to be an upward trend, that is the average rating of the most popular movie for each year seems to increase as we get to more recent years. Well, on average at least. That's interesting. Is it suggesting that the movies increase in quality over the years? Maybe we should explore how the rating evolves in average over all the entries rather than restrict ourselves to the most popular entries. How would we do that?



Well it all looks the same to me till the second filter. Because we don't want to restrict ourselves to the most popular movies. So let's remove all the commands after group_by() and start from there:

<div class="tutorial-exercise" data-label="all_5a" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year)
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>


Now for each year, we want to plot the *average* of the average rating of the movies. So this a summary statistics that we need. We need to use `summarize()`.

<div class="tutorial-exercise" data-label="all_5b" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year) %>%
      summarise(meanRating=mean(averageRating))
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

Ok, we know have two columns. One for the year and one for the this *average* average rating. We're good to do our scatter plot


<div class="tutorial-exercise" data-label="all_5c" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  filter(type=="movie")%>%
    group_by(year) %>%
      summarise(meanRating=mean(averageRating)) %>%
        ggplot(aes(x=year, y=meanRating))+
          geom_point()
```

<script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>


Wow, the story is quite different here. In average, the rating of the movies seems to decline over the years. We definitely need to be careful with our interpretations though, as the way our dataset and the imdb database are built, makes comparisons between years a bit tricky.


As you may start to see, once you get familiar with the pipe operator and all the functions we've seen so far, it quickly becomes pretty easy to make long sequences of manipulations.



Here is a hard question to finish with this workbook. Take your time to analyse and understand what we want. Try to find the different steps required and see if you can arrange all of it in a command that would respond to the question. You'll see two boxes below the question. One where you can get a series of hints (you'll have a button "next hint" in the hint window), and one where you can get a full solution. Start with the hints before checking the solution.



**Question: rank the directors who have more than 10 entries in the imdb dataset and an average number of votes over all their entries greater than 10000, by decreasing average of the "average rating" of all their entries**


<div class="tutorial-exercise" data-label="all_7" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0"><script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>


<div class="tutorial-exercise-support" data-label="all_7-hint-1" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
# we want to rank the directors, so at the end, we need one row per director.
# For now we often have many entries for each director though,
# so we probably need to group our data by director
# and then use a filter or summarize... or both?
```

</div>

<div class="tutorial-exercise-support" data-label="all_7-hint-2" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
# We only want to keep the directors who have more than 10 entries in the imdb dataset
# and an average number of votes over all their entries greater than 10000
# ... but the number of entries and average number of votes are not columns of our dataset,
# so we probably need to calculate these using summarise().
```

</div>

<div class="tutorial-exercise-support" data-label="all_7-hint-3" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
# Once we've grouped our dataset by director
# and then summarised the rows to get the number of entries (function n())
# and the average number of votes (function mean()),
# we need can get rid of the directors who don't meet our condition using filter()
```

</div>

<div class="tutorial-exercise-support" data-label="all_7-hint-4" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  group_by(director) %>% 
    summarise(n=n(),  meanVotes=mean(numVotes)) %>%
      filter(n>10 & meanVotes>10000)

# what's left is to rank the directors by decreasing average of the "average rating" of all their entries.
# So we need to also calculate the average of the "average rating" in the summarise function.
# And then we can arrange the rows by this new variable.
```

</div>



<div class="tutorial-exercise" data-label="all_8" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0"><script type="application/json" data-opts-chunk="1">{"fig.width":6.5,"fig.height":4,"fig.retina":2,"fig.align":"default","fig.keep":"high","fig.show":"asis","out.width":624,"warning":true,"error":false,"message":true,"exercise.df_print":"paged","exercise.timelimit":10,"exercise.checker":"NULL"}</script></div>

<div class="tutorial-exercise-support" data-label="all_8-solution" data-caption="Code" data-completion="1" data-diagnostics="1" data-startover="1" data-lines="0">

```text
imdb %>%
  group_by(director) %>% 
    summarise(n=n(), meanVotes=mean(numVotes), meanRating=mean(averageRating)) %>%
      filter(n>10 & meanVotes>10000) %>%
        arrange(desc(meanRating))
```

</div>




## Appendix: 'imdb' dataset

For this session, we are using a dataset called "imdb", which we constructed from the subsets of the Internet Movie Database made available for non-commercial purposes by the IMDb team:
<a href="https://www.imdb.com/interfaces/" target="_blank">https://www.imdb.com/interfaces/</a>


It contains the following informations for all the entries having more than 500 votes, that are not of type "tvEpisodes" and for which information about year of release, running time and director(s) was available at the time of extraction (28/09/2020):  



|Column        |Description                                                                                          |
|:-------------|:----------------------------------------------------------------------------------------------------|
|title         |popular title of the entry                                                                           |
|type          |type of entry: movie, short, tvMiniSeries, tvMovie, tvSeries, tvShort, tvSpecial, video or videoGame |
|year          |year of release (for series, year of release of the first episode)                                   |
|length        |duration in minutes                                                                                  |
|numVotes      |number of votes for the entry                                                                        |
|averageRating |IMDb's weighted average rating for the entry                                                         |
|director      |director of the entry (if multiple directors, the first one was picked)                              |
|birthYear     |year of birth of the director                                                                        |
|animation     |the entry is of genre animation (TRUE/FALSE)                                                         |
|action        |the entry is of genre action (TRUE/FALSE)                                                            |
|adventure     |the entry is of genre adventure (TRUE/FALSE)                                                         |
|comedy        |the entry is of genre comedy (TRUE/FALSE)                                                            |
|documentary   |the entry is of genre documentary (TRUE/FALSE)                                                       |
|fantasy       |the entry is of genre fantasy (TRUE/FALSE)                                                           |
|romance       |the entry is of genre romance (TRUE/FALSE)                                                           |
|sci_fi        |the entry is of genre science fiction (TRUE/FALSE)                                                   |
|thriller      |the entry is of genre thriller (TRUE/FALSE)                                                          |


The dataset has 61,776 rows. It's too much to be displayed here. Below are 50 arbitrary rows though. Spend a bit of time familiarising yourself with the columns of this dataset. You can use the arrow keys to scroll right and left.

<!--html_preserve--><div id="htmlwidget-983a4c79e0496f9ad4d4" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-983a4c79e0496f9ad4d4">{"x":{"filter":"none","data":[["18783","34507","49790","56743","37269","46078","8793","28880","51323","44060","23344","22561","54806","55927","57491","17066","47902","41588","18403","23892","9931","13161","27681","32701","13199","51808","24609","56924","19338","10637","48808","50107","60887","40550","53553","20571","10897","37264","13850","4640","57618","59870","9524","49710","55512","29316","19669","51457","18377","55459"],["Sex and Zen II","Destination Truth","Dirty Beautiful","The Killing Season","TiMER","Lewis Black: In God We Rust","The Asphyx","El Crimen Perfecto (The Perfect Crime)","Blood Money","The Citizen","Rejected","All for Love","Dawson City: Frozen Time","Mother","Barbara","American Yakuza","Hot Tub Time Machine 2","Interny","The Chamber","Mom's Got a Date with a Vampire","Treasure of Matecumbe","84 Charing Cross Road","Once More","I Have Never Forgotten You: The Life &amp; Legacy of Simon Wiesenthal","Blue City","Chalk It Up","Eight Crazy Nights","Distorted","Mr. Jealousy","Boulevard Nights","Batman v Superman: Dawn of Justice","X-Men: Apocalypse","Daybreak","Tab Hunter Confidential","Frontier","Batman &amp; Mr. Freeze: SubZero","Magnum, P.I.","10 Promises to My Dog","Nuts","Man from Del Rio","Serenity","Ága","Lisztomania","Spice Girls: Wannabe","This Is Us","Cry in the Woods","Jakob the Liar","My Beautiful Broken Brain","Breaking the Waves","DuckTales"],["movie","tvSeries","movie","tvSeries","movie","tvSpecial","movie","movie","movie","movie","short","movie","movie","movie","movie","movie","movie","tvSeries","movie","tvMovie","movie","movie","movie","movie","movie","movie","movie","movie","movie","movie","movie","movie","tvSeries","movie","tvSeries","video","tvSeries","movie","movie","movie","movie","movie","movie","video","tvSeries","movie","movie","movie","movie","tvSeries"],[1996,2007,2015,2016,2009,2012,1972,2004,2017,2012,2000,1986,2016,2016,2017,1993,2015,2010,1996,2000,1976,1987,1997,2007,1986,2016,2002,2018,1997,1979,2016,2016,2019,2015,2016,1998,1980,2008,1987,1956,2019,2018,1975,1996,2016,2004,1999,2014,1996,2017],[87,60,95,43,99,59,99,105,100,99,9,92,120,95,98,96,93,25,113,85,117,100,160,105,83,90,76,86,103,102,152,144,60,90,60,70,48,117,116,82,106,96,103,4,45,106,120,86,159,21],[521,2771,685,907,12267,571,2127,11276,1961,3158,7808,575,1518,1400,950,2289,36379,2848,12458,3442,548,8398,554,634,990,1143,21210,4518,1920,526,613508,385469,13931,2921,16005,13385,21707,552,5784,519,33652,1054,2196,770,96710,982,14872,2018,61251,5627],[5.6,7.6,5.5,7.6,6.5,7.1,6.3,7.1,4.4,7,7.9,8.6,7.5,5.6,6.1,5.7,5.1,7.2,6,5.9,6.2,7.4,6.1,7.8,4.4,4.6,5.4,4.8,6.4,6.7,6.4,6.9,6.7,7.7,7.2,7.2,7.5,7.1,6.6,6.3,5.4,7.4,6.3,6.6,8.7,6,6.6,7.1,7.8,8.2],["Man Kei Chin","Daniel Ramirez-Suarez","Tim Bartell","Joshua Zeman","Jac Schaeffer","Adam Dubin","Peter Newbrook","Álex de la Iglesia","Lucky McKee","Sam Kadi","Don Hertzfeldt","Nikolay Volev","Bill Morrison","Aaron Burns","Mathieu Amalric","Frank A. Cappello","Steve Pink","Dina Shturmanova","James Foley","Steve Boyum","Vincent McEveety","David Hugh Jones","S.A. Chandrashekhar","Richard Trank","Michelle Manning","Hisonni Mustafa","Seth Kearsley","Rob W. King","Noah Baumbach","Michael Pressman","Zack Snyder","Bryan Singer","Michael Patrick Jann","Jeffrey Schwarz","Brad Peyton","Boyd Kirkland","Jerry Jameson","Katsuhide Motoki","Martin Ritt","Harry Horner","Steven Knight","Milko Lazarov","Ken Russell","Johan Camitz","Zetna Fuentes","Erich Hörtnagl","Peter Kassovitz","Sophie Robinson","Lars von Trier","Matthew Humphreys"],[null,null,null,null,null,1964,1920,1965,1975,null,1976,1946,1965,1985,1965,null,null,null,1953,1952,1929,1934,null,null,null,1982,1971,1959,1969,1950,1966,1965,1970,1969,1978,1950,1934,1963,1914,1910,1959,1967,1927,1962,null,1950,1938,null,1956,null],[false,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,true],[false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,true,false,false,false,false,false,true,true,true,false,false,true,true,false,false,false,false,false,false,false,false,false,false,false,false,true],[false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,true,true,true,false,true,false,true,false,false,false,false,false,false,false,false,false,false,false,false,true],[true,false,true,false,true,true,false,true,false,false,true,false,false,false,false,false,true,true,false,true,false,false,true,false,false,true,true,false,true,false,false,false,true,false,false,false,false,true,false,false,false,false,true,true,true,false,false,false,false,false],[false,false,false,true,false,true,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,true,false,false],[true,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],[false,false,true,false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,true,true,false,false,false,false,false,true,false,false,false,false,false,false,false,false,true,false,true,false,false,false,false,true,false,false,false,false,false],[false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,true,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],[false,false,false,false,false,false,false,true,true,false,false,false,false,true,false,false,false,false,true,false,false,false,false,false,false,false,false,true,false,false,false,false,false,false,false,false,false,false,true,false,true,false,false,false,false,true,false,false,false,false]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>title<\/th>\n      <th>type<\/th>\n      <th>year<\/th>\n      <th>length<\/th>\n      <th>numVotes<\/th>\n      <th>averageRating<\/th>\n      <th>director<\/th>\n      <th>birthYear<\/th>\n      <th>animation<\/th>\n      <th>action<\/th>\n      <th>adventure<\/th>\n      <th>comedy<\/th>\n      <th>documentary<\/th>\n      <th>fantasy<\/th>\n      <th>romance<\/th>\n      <th>sci_fi<\/th>\n      <th>thriller<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[3,4,5,6,8]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

## Appendix: Useful reference links  

The official list of functions and documentation of the dplyr package (you'll see that dplyr comes with lots of other useful functions in addition to the core ones that we've learnt to use): <a href="https://r4ds.had.co.nz/transform.html" target="_blank">https://dplyr.tidyverse.org/reference/index.html</a> 

A more advanced tutorial: <a href="https://r4ds.had.co.nz/transform.html" target="_blank">https://r4ds.had.co.nz/transform.html</a> 

dplyr CheatSheet:<a href="https://github.com/rstudio/cheatsheets/blob/master/data-transformation.pdf" target="_blank">https://github.com/rstudio/cheatsheets/blob/master/data-transformation.pdf   </a>  

Data Manipulation Tools - Rstudio video:
<a href="https://www.youtube.com/watch?v=Zc_ufg4uW4U" target="_blank">dplyr -- Pt 3 Intro to the Grammar of Data Manipulation with R   </a> 

Some documentation on subsetting r-objects using base-R: <a href="https://bookdown.org/rdpeng/rprogdatascience/subsetting-r-objects.html" target="_blank">https://bookdown.org/rdpeng/rprogdatascience/subsetting-r-objects.html </a> 
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server-start">
library(learnr)
library(tidyverse)
tutorial_options(exercise.timelimit = 10)
options(max.print=200, dplyr.summarise.inform = FALSE)
imdb <- readRDS("imdb2020.RDS")
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
learnr:::register_http_handlers(session, metadata = NULL)
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
session$onSessionEnded(function() {
        learnr:::session_stop_event(session)
      })
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-sequence_1-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-sequence_1-code-editor`)), session)
output$`tutorial-exercise-sequence_1-output` <- renderUI({
  `tutorial-exercise-sequence_1-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-sequence_2-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-sequence_2-code-editor`)), session)
output$`tutorial-exercise-sequence_2-output` <- renderUI({
  `tutorial-exercise-sequence_2-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_1-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_1-code-editor`)), session)
output$`tutorial-exercise-group_1-output` <- renderUI({
  `tutorial-exercise-group_1-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_2-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_2-code-editor`)), session)
output$`tutorial-exercise-group_2-output` <- renderUI({
  `tutorial-exercise-group_2-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_3-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_3-code-editor`)), session)
output$`tutorial-exercise-group_3-output` <- renderUI({
  `tutorial-exercise-group_3-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_4-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_4-code-editor`)), session)
output$`tutorial-exercise-group_4-output` <- renderUI({
  `tutorial-exercise-group_4-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_4a-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_4a-code-editor`)), session)
output$`tutorial-exercise-group_4a-output` <- renderUI({
  `tutorial-exercise-group_4a-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_4b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_4b-code-editor`)), session)
output$`tutorial-exercise-group_4b-output` <- renderUI({
  `tutorial-exercise-group_4b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_5-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_5-code-editor`)), session)
output$`tutorial-exercise-group_5-output` <- renderUI({
  `tutorial-exercise-group_5-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_6-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_6-code-editor`)), session)
output$`tutorial-exercise-group_6-output` <- renderUI({
  `tutorial-exercise-group_6-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-group_7-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-group_7-code-editor`)), session)
output$`tutorial-exercise-group_7-output` <- renderUI({
  `tutorial-exercise-group_7-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-pipe_1-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-pipe_1-code-editor`)), session)
output$`tutorial-exercise-pipe_1-output` <- renderUI({
  `tutorial-exercise-pipe_1-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-pipe_1b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-pipe_1b-code-editor`)), session)
output$`tutorial-exercise-pipe_1b-output` <- renderUI({
  `tutorial-exercise-pipe_1b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-pipe_2-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-pipe_2-code-editor`)), session)
output$`tutorial-exercise-pipe_2-output` <- renderUI({
  `tutorial-exercise-pipe_2-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-pipe_2b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-pipe_2b-code-editor`)), session)
output$`tutorial-exercise-pipe_2b-output` <- renderUI({
  `tutorial-exercise-pipe_2b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-pipe_3-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-pipe_3-code-editor`)), session)
output$`tutorial-exercise-pipe_3-output` <- renderUI({
  `tutorial-exercise-pipe_3-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-pipe_4-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-pipe_4-code-editor`)), session)
output$`tutorial-exercise-pipe_4-output` <- renderUI({
  `tutorial-exercise-pipe_4-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_0-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_0-code-editor`)), session)
output$`tutorial-exercise-all_0-output` <- renderUI({
  `tutorial-exercise-all_0-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_0b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_0b-code-editor`)), session)
output$`tutorial-exercise-all_0b-output` <- renderUI({
  `tutorial-exercise-all_0b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_0c-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_0c-code-editor`)), session)
output$`tutorial-exercise-all_0c-output` <- renderUI({
  `tutorial-exercise-all_0c-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_1-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_1-code-editor`)), session)
output$`tutorial-exercise-all_1-output` <- renderUI({
  `tutorial-exercise-all_1-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_2-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_2-code-editor`)), session)
output$`tutorial-exercise-all_2-output` <- renderUI({
  `tutorial-exercise-all_2-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_2b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_2b-code-editor`)), session)
output$`tutorial-exercise-all_2b-output` <- renderUI({
  `tutorial-exercise-all_2b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_3-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_3-code-editor`)), session)
output$`tutorial-exercise-all_3-output` <- renderUI({
  `tutorial-exercise-all_3-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4a-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4a-code-editor`)), session)
output$`tutorial-exercise-all_4a-output` <- renderUI({
  `tutorial-exercise-all_4a-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4b-code-editor`)), session)
output$`tutorial-exercise-all_4b-output` <- renderUI({
  `tutorial-exercise-all_4b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4c-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4c-code-editor`)), session)
output$`tutorial-exercise-all_4c-output` <- renderUI({
  `tutorial-exercise-all_4c-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4d-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4d-code-editor`)), session)
output$`tutorial-exercise-all_4d-output` <- renderUI({
  `tutorial-exercise-all_4d-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4e-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4e-code-editor`)), session)
output$`tutorial-exercise-all_4e-output` <- renderUI({
  `tutorial-exercise-all_4e-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4f-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4f-code-editor`)), session)
output$`tutorial-exercise-all_4f-output` <- renderUI({
  `tutorial-exercise-all_4f-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_4g-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_4g-code-editor`)), session)
output$`tutorial-exercise-all_4g-output` <- renderUI({
  `tutorial-exercise-all_4g-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_5a-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_5a-code-editor`)), session)
output$`tutorial-exercise-all_5a-output` <- renderUI({
  `tutorial-exercise-all_5a-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_5b-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_5b-code-editor`)), session)
output$`tutorial-exercise-all_5b-output` <- renderUI({
  `tutorial-exercise-all_5b-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_5c-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_5c-code-editor`)), session)
output$`tutorial-exercise-all_5c-output` <- renderUI({
  `tutorial-exercise-all_5c-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_7-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_7-code-editor`)), session)
output$`tutorial-exercise-all_7-output` <- renderUI({
  `tutorial-exercise-all_7-result`()
})
</script>
<!--/html_preserve-->
<!--html_preserve-->
<script type="application/shiny-prerendered" data-context="server">
`tutorial-exercise-all_8-result` <- learnr:::setup_exercise_handler(reactive(req(input$`tutorial-exercise-all_8-code-editor`)), session)
output$`tutorial-exercise-all_8-output` <- renderUI({
  `tutorial-exercise-all_8-result`()
})
</script>
<!--/html_preserve-->
