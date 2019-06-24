---
layout: post
title:  Buggy calendar systems
date:   2019-06-24 17:09:29 -0400
categories: programming history
---

Today I want to write about a confusing yet intended behavior of Ruby dates:

```ruby
# Let's create a date object, sometime in the 16th century...
d = Date.new 1582, 10, 2
d.strftime("%A, %B %-d, %Y") # => "Tuesday, October 2, 1582"

# In Ruby, you can easily increment a date by one day like this:
(d + 1).strftime("%A, %B %-d, %Y") # => "Wednesday, October 3, 1582"

# Or two days:
(d + 2).strftime("%A, %B %-d, %Y") # => "Thursday, October 4, 1582"

# Or three days:
(d + 3).strftime("%A, %B %-d, %Y") # => "Friday, October 15, 1582"
                                   # Wait, what!?
```

My computer says the day after October 4th is October 15th in 1582. I first
thought it'd be a bug.

It turns out it was a bug, just not a bug in Ruby. It was
a bug much older than Ruby, much older than computers!

So let me tell you a story of how a bug is much more difficult to fix once it
has made it into production. And how this applies to all systems even before
modern computers existed.

### The bug

Up until 1582, Europe was using the [Julian calendar][julian-calendar].
This calendar
acknowledged that a leap day had to be introduced every now and then
to adjust for the fact that a year isn't 365 exact days.

To account for that, every 4 years would be a leap year. This causes the
average length of a Julian year to be 365.25 days.

However, the duration of a solar year is ~365.24217 days, very close to the
calendar's implementation but slightly different.

This mismatch of ~0.00783 days per year was causing the Julian calendar to
drift apart from the solar calendar at a rate of 1 day every ~128 years.

Many other systems relied on this calendar system, and I would assume that
it was critical for some of them that it was reliable. For example if we think
about harvesting seasons.


### The fix

Over the course of the years, the difference between the solar
year and the calendar year had already drifted by 10 days. Even worse, this
difference was getting bigger every year. Had it been carried over until today,
this would have grown up to 14 days. A fix was needed.

The pope decreed a reform to the calendar with two big breaking changes:

 1. The 10 day mismatch would be corrected by erasing them from the calendar
    after October 4, 1582.
 2. The average calendar year duration would change from 365.25 solar days to
    365.24237, this was achieved by skipping 3 leap years every 400 years.

The [Gregorian calendar][gregorian-calendar] was introduced.

### Deprecation warnings all over Europe

And this was good, but it also introduced other sorts of problems.

Not all countries adopted the system immediately. For centuries, each country
would use a slightly different version of a *Roman based calendar*. Notably
England wouldn't introduce similar changes until 1752, the gap by then was
already 11 days.

```ruby
d = Date.new(1752, 9, 2, Date::ENGLAND)
d.strftime("%A, %B %-d, %Y") # => "Wednesday, September 2, 1752"

(d + 1).strftime("%A, %B %-d, %Y") # => "Thursday, September 14, 1752"
```

Also it introduced confusion as to how to refer to the dates before the fix.

Nowadays it's common to obviate the reform and count the days prior to the
reform just as Gregorian dates. This is what is called a [Proleptic Gregorian
calendar][proleptic-gregorian-calendar] and has caused a lot of confusion to
identify dates, such as
[George Washington's birth date][washingtons-birth] &
[Shakespeare's death date][shakespeares-death], which coincides with
Cervante's death date despite they died on different days.


[julian-calendar]: https://en.wikipedia.org/wiki/Julian_calendar
[gregorian-calendar]: https://en.wikipedia.org/wiki/Gregorian_calendar
[proleptic-gregorian-calendar]: https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar
[washingtons-birth]: https://www.archives.gov/legislative/features/washington
[shakespeares-death]: https://www.uh.edu/engines/epi2368.htm
