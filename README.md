INTRO
=====
MoneyBook is a simple ruby command line application to track group expenses
and know exactly how much each one of your friends owes you from your last trip!

It's not super easy to use, nor is it meant to be (since I wrote it for myself), but
it's meant to be powerful and useful.

QUICK START
===========

to install simply do:
    gem install moneybook
    
now to create your first moneybook file do:
    moneybook new
    
and follow the onscreen instructions (give title and list of people in the group)
next edit the file as you wish and, when you want to know how things stand (how much
each one has to give/receive) simply do this:
    moneybook parse moneybook_file.txt
    
and answer yes when asked to see intermediate results (they will give you a better
understanding of the whys..)

EXAMPLE FILE
============

    ### trip to paris ###
    #### 2011-01-10 #####
    #####################
    
    PEOPLE: Jack Mary Kevin
    
    # Jack payed 120$ for the tickets for all three..
    flight tickets (120Jack)
    
    # Jack payed 50$ and Mary 30$ for the dinner at hotel, and they didn't all spend the same:
    # Kev got a dessert! He spent 10$ more than the others..
    dinner at hotel (50Jack 30Mary) +10Kevin
    
    # The museum was payed by Mary, and Kevin will pay only 5$ since he's a student.. Mary and 
    # Jack will then pay the rest (10$ each). Names can be abbrev: Kevin -> Kev -> K
    museum (25Mary) 5Kev

the output to the example file above is:

    ==== flight tickets (120Jack) ====
    true total: 120.0
    +-------+-------+-------+
    | jack  | kevin | mary  |
    +-------+-------+-------+
    | 120.0 |       |       |
    | 40.0  | 40.0  | 40.0  |
    +-------+-------+-------+
    | 80.0  | -40.0 | -40.0 |
    +-------+-------+-------+
    | 80.0  | -40.0 | -40.0 |
    +-------+-------+-------+
    ==== dinner at hotel (50Jack 30Mary) +10Kevin ====
    true total: 80.0
    +-------+-------+-------+
    | jack  | kevin | mary  |
    +-------+-------+-------+
    | 50.0  |       | 30.0  |
    | 30.0  | 40.0  | 30.0  |
    +-------+-------+-------+
    | 20.0  | -40.0 |       |
    +-------+-------+-------+
    | 100.0 | -80.0 | -40.0 |
    +-------+-------+-------+
    ==== museum (25Mary) 5Kev ====
    true total: 25.0
    +-------+-------+-------+
    | jack  | kevin | mary  |
    +-------+-------+-------+
    |       |       | 25.0  |
    | 10.0  | 5.0   | 10.0  |
    +-------+-------+-------+
    | -10.0 | -5.0  | 15.0  |
    +-------+-------+-------+
    | 90.0  | -85.0 | -25.0 |
    +-------+-------+-------+
    jack   receives   90.0
    kevin  gives      -85.0
    mary   gives      -25.0

each table is the result of each line in the source file, in each table there are 4 lines:
the first line is what's been payed, the second line is who should have payed it, 
then third line is the difference (so if the number is negative, the person owes money to
those whose number is positive), and the fourth line is the total balance that far.

At the end the total balance is also printed in words, kevin and mary both give something
to jack!