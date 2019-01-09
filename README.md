# utl-monty-hall-problem-r-sas-python
Monty Hall Problem
    Monty Hall Problem

    Suppose you're on a game show, and you're given the choice of three doors:
    Behind one door is a car; behind the others, goats. You pick a door, say No.
    1 [but the door is not opened], and the host, who knows what's behind the doors,
    opens another door, say No. 3, which has a goat.
    He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?

      Three Solutions ( 10,000,000 simulation - timings may not be fair - I just ran the provided code)

             Seconds
             -------
          1.   1.54  SAS datstep  (SAS C compiler is amazing - SAS used to sell it?)
                     http://www.sascommunity.org/wiki/Monty_Hall,_Paul_Erd%C3%B6s,_and_Monte_Carlo

          2. 456.52  SAS drop down to R
                     http://www.rpubs.com/Patrick_li1994/404292


          3.  46.27  SAS  drop down to Python
                     https://rosettacode.org/wiki/Monty_Hall_problem#Python

    80 differenent language solutions
    https://rosettacode.org/wiki/Monty_Hall_problem

    SAS Forum
    https://communities.sas.com/t5/SAS-Programming/Monty-Hall-Problem/m-p/525102

    https://communities.sas.com/t5/user/viewprofilepage/user-id/13879
    Reeza

    IML solution
    https://blogs.sas.com/content/iml/2015/04/01/monty-hall.html

    Data step approach - Single iteration
    http://www.sascommunity.org/wiki/Monty_Hall,_Paul_Erd%C3%B6s,_and_Monte_Carlo

    SAS & Python
    https://blogs.sas.com/content/sgf/2016/10/12/the-monty-hall-paradox-sas-vs-python/

    Read more here:
    http://www.dreamincode.net/forums/topic/292496-prove-the-monty-hall-problem-via-simulation/
    http://en.wikipedia.org/wiki/Monty_Hall_problem


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;


    1. SAS datstep
    ===============

    data _null_;

        switch_the_pick=0;
        stick_with_pick=0;

    do game=1 to 10000000;

        seed=7;

        player = ceil(ranuni(seed)*3);
        prize = ceil(ranuni(seed)*3);

        do until(monty ~= prize and monty ~= player);
            monty=ceil(ranuni(seed)*3);
        end;

        if monty~=prize and monty~=player then
        do;
            if player=1 and monty=3 then pick2=2;
            else if player=1 and monty=2 then pick2=3;
            else if player=2 and monty=1 then pick2=3;
            else if player=2 and monty=3 then pick2=1;
            else if player=3 and monty=1 then pick2=2;
            else if player=3 and monty=2 then pick2=1;

            if prize = pick2 then switch_the_pick + 1;
            if prize = player then stick_with_pick + 1;

            if player=1 and monty=3 then
                do;
                if prize=1 then prize1+1;
                if prize=2 then prize2+1;
            end;
        end;
    end;

    switch_winning_pct = switch_the_pick/(stick_with_pick + switch_the_pick);
    stick_winning_pct = stick_with_pick/(stick_with_pick + switch_the_pick);
    numb_obs = stick_with_pick + switch_the_pick;

    put 'Number of Observation = ' numb_obs;
    put;
    put 'Wins with initial pick = ' stick_with_pick 'times.';
    put;
    put 'Wins Switching = ' switch_the_pick 'times.';
    put;
    put 'Stick winning percent = ' stick_winning_pct;
    put;
    put 'Switch winning percent = ' switch_winning_pct;
    put;
    run;


    Number of Observation = 10000000
    Wins with initial pick = 3335401 times.
    Wins Switching = 6664599 times.
    Stick winning percent = 0.3335401
    Switch winning percent = 0.6664599


    NOTE: DATA statement used (Total process time):
          real time           1.54 seconds
          user cpu time       1.54 seconds



    2. SAS drop down to R
    ---------------------

    %utl_submit_r64('
    n <- 0;
    door <- c(1,2,3);
    for ( i in 1:10000000) {
        cardoor <- sample(door,1);
        select <- sample(door,1);
        remove <- ifelse(cardoor==select,
                        sample(setdiff(door,cardoor),1),
                        setdiff(door,c(cardoor,select)));
        reselect <- setdiff(door,c(select,remove));
        if (cardoor == reselect) {
            n <- n + 1;
        }
    };
    print(n/100000);
    ');

    [1] 0.66535

    NOTE: DATA statement used (Total process time):
          real time           7:36.52
          user cpu time       0.03 seconds
          system cpu time     0.07 seconds


    3. SAS  drop down to Python
    ---------------------------


    %utl_submit_py64('
    from random import randrange;
    doors, iterations = 3,10000000;
    def monty_hall(choice, switch=False, doorCount=doors):;
    .  door = [False]*doorCount;
    .  door[randrange(doorCount)] = True;
    .  chosen = door[choice];
    .  unpicked = door;
    .  del unpicked[choice];
    .  alternative = True in unpicked;
    .  if switch:;
    .    return alternative;
    .  else:;
    .    return chosen;
    print "\nMonty Hall problem simulation:" ;
    print doors, "doors,", iterations, "iterations.\n";
    print "Not switching allows you to win",;
    print sum(monty_hall(randrange(3), switch=False);
    .          for x in range(iterations)),;
    print "out of", iterations, "times.";
    print "Switching allows you to win",;
    print sum(monty_hall(randrange(3), switch=True);
    .          for x in range(iterations)),;
    print "out of", iterations, "times.\n";
    ');


    Monty Hall problem simulation:
    3 doors, 10000000 iterations.

    Not switching allows you to win 3331230 out of 10000000 times.
    Switching allows you to win 6664047 out of 10000000 times.

    NOTE: DATA statement used (Total process time):
          real time           46.67 seconds
          user cpu time       0.12 seconds
          system cpu time     0.06 seconds



