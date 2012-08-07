---
layout: post
title: "The basic date validation using Ruby regexps"
description: ""
category: 
tags: [ruby]
---
{% include JB/setup %}

Hi there, Ruby lovers all over the world.

I've been playing with Ruby regexps for a while and worked out a usable regexp (well, to my opinion) which checks whether the date format is proper ("YYYY#MM#DD") where # stands for a separator: - or : or /.

It performs basic checks on a date: accepts only dates which have a valid year (which cannot start from zero), a valid month (from 01 to 12), and checks that a day is within the range \[1..31\].

The second separator must match the first.

One of the drawbacks of that regexp is that it is not smart enough to know whether a supplied day is valid for the supplied month, but it may be further improved by the use of Ruby's Date and DateTime modules.

Alrighty, let's look at the code!

    =begin rdoc
    Checks that the supplied date has the correct date format '#YYYY:MM:DD' with the separators both be either - or : or /.
    A year cannot start from the zero, while a month and a day can.
    Doesn't check that the supplied day is within the specified month's day range. It may be further improved.
    =end
    def checkDate(aDate)
      %r{
        (?<year>
          (^            #no input before the start
            [1-9]\d{3}  #a year cannot start from the zero
          ) 
        )
        (?<sep>-|:|/)   #a separator can be either - or : or / 
        (?<month>       
          (0[1-9])      #a month can start from the zero
          |
          (1[0-2])      #or it could be just 10, 11 or 12 of course
        )
        \k<sep>         #the 2nd separator must be the same as the 1st met
        (?<day>
          (0[1-9])      #a day can start from the zero
          |(1[0-9])     #or should fall into the range [1..31]
          |(2[0-9])
          |30
          |31
        )$              #marks there should be the end of the input
      }x =~ aDate
    end

    date  = "1234:12:31"
    print "date is #{checkDate(date) ? "" : "not "}correct"

A curious reader might have already noticed I used lots of spaces in the regexp. Yes, it is possible in Ruby regexps if you specify 'x' option (as I did). It makes it much easier to understand. Effective comments also worth mentioning. Without them the regexp might not be so straightforward and clear.

Okay, let's get rid of the mentioned flaws. All we need is to look at Ruby's handy Date class which performs all these checks automatically during instantiation a new Date object.

Here is a final solution:

    =begin rdoc
    Checks that the supplied date has the correct date format '#YYYY:MM:DD' with the separators both be either - or : or /.
    A year cannot start from the zero, while a month and a day can.
    Performs additional checkings that the date is valid.
    =end
    def checkDateUpdated(aDate)
      matched =
        %r{
            (?<year>
            (^            #no input before the start
              [1-9]\d{3}  #a year cannot start from the zero
            )
          )
          (?<sep>-|:|/)   #a separator can be either - or : or /
          (?<month>
            (0[1-9])      #a month can start from the zero
            |
            (1[0-2])      #or it could be just 10, 11 or 12 of course
          )
          \k<sep>         #the 2nd separator must be the same as the 1st met
          (?<day>
            (0[1-9])      #a day can start from the zero
            |(1[0-9])     #or should fall into the range [1..31]
            |(2[0-9])
            |30
            |31
          )$              #marks there should be the end of the input
        }x.match(aDate)

        if (matched)
          day, month, year = matched[:day], matched[:month], matched[:year]
          day = day[-1] if day[0] == "0"
          month = month[-1] if month[0] == "0"

          year, month, day = year.to_i, month.to_i, day.to_i

          require 'date'
          begin
            date = Date.new(year, month, day)
          rescue ArgumentError => e
            return false
          end

          true
        else
          false
        end
    end
     
    date  = "2011:11:30"
    print "date is #{checkDateUpdated(date) ? "" : "not "}correct"

It discards wrong entries such as "2011:11:31", "2011:02:29" etc.

Cheers!


