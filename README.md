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
    moneybook new TITLE
    
and follow the onscreen instructions (give title, list of people in the group and optionally a currency exchange)
next edit the file as you wish and, when you want to know how things stand (how much
each one has to give/receive) simply do this:
    moneybook parse moneybook_file.txt
    
If you want to see intermediate results (they will give you a better
understanding of the whys..) simply add a -i option, like this:
    moneybook parse -i moneybook_file.txt
    
To see other available options do this:
    moneybook parse --help

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
    
    # Knowing he was in debt, Kevin decides to give 50$ to Jack to start paying him back..
    50Kevin -> Jack

the output to the example file above is:

    ### trip to paris ###
    #### 2011-01-10 #####
    #####################
        
    
    # Jack payed 120$ for the tickets for all three..
    ==== flight tickets (120Jack) ====
    total: 120.0
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
    
    # Jack payed 50$ and Mary 30$ for the dinner at hotel, and they didn't all spend the same:
    # Kev got a dessert! He spent 10$ more than the others..
    ==== dinner at hotel (50Jack 30Mary) +10Kevin ====
    total: 80.0
    +-------+-------+-------+
    | jack  | kevin | mary  |
    +-------+-------+-------+
    | 50.0  |       | 30.0  |
    | 23.3  | 33.3  | 23.3  |
    +-------+-------+-------+
    | 26.7  | -33.3 | 6.7   |
    +-------+-------+-------+
    | 106.7 | -73.3 | -33.3 |
    +-------+-------+-------+
    
    # The museum was payed by Mary, and Kevin will pay only 5$ since he's a student.. Mary and 
    # Jack will then pay the rest (10$ each). Names can be abbrev: Kevin -> Kev -> K
    ==== museum (25Mary) 5Kev ====
    total: 25.0
    +-------+-------+-------+
    | jack  | kevin | mary  |
    +-------+-------+-------+
    |       |       | 25.0  |
    | 10.0  | 5.0   | 10.0  |
    +-------+-------+-------+
    | -10.0 | -5.0  | 15.0  |
    +-------+-------+-------+
    | 96.7  | -78.3 | -18.3 |
    +-------+-------+-------+
    
    # Knowing he was in debt, Kevin decides to give 50$ to Jack to start paying him back..
    ==== 50Kevin -> Jack ====
    total: 50.0
    +-------+-------+-------+
    | jack  | kevin | mary  |
    +-------+-------+-------+
    |       | 50.0  |       |
    | 50.0  |       |       |
    +-------+-------+-------+
    | -50.0 | 50.0  |       |
    +-------+-------+-------+
    | 46.7  | -28.3 | -18.3 |
    +-------+-------+-------+
    jack  	 receives      46.67 spent    73.33 given   170.00
    mary  	 gives        -18.33 spent    73.33 given    55.00
    kevin 	 gives        -28.33 spent    78.33 given    50.00



each table is the result of each line in the source file, in each table there are 4 lines:
the first line is what's been payed [given], the second line is who should have payed it [spent], 
then third line is the difference (so if the number is negative, the person owes money to
those whose number is positive), and the fourth line is the total balance that far.

At the end the total balance is also printed in words, kevin and mary both give something
to jack! And for each one is said how much he spent and how much he actually gave. The balance
is the difference: given - (spent + paybacks). Paybacks are not considered in the spent field. 