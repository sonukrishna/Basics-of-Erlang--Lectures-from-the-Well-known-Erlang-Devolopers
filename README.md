# Basics-of-Erlang--Lectures-from-the-Well-known-Erlang-Devolopers
This contains small descriptions of video lectures from known Erlang devolopers(Joe Amstrong, Kent University etc). The things i understood from the lectures are described in the repo.


## 1. Joe Amstrong's seminar at Stanford 
   
                     In this lecture he describes the very basic ideas of the Erlang language. The six principles in Erlang and how to program this principles. And also provides basics of Tandem computers, descriptions about fault_tolerance, messaging etc.
####a) The six principles
    1. isolation --> The Erlang process must be isolated by design, one process cannot damage the other.
    2. concurrency --> The process mut be concurrent.
    3. failure detection --> The basic idea behind this is, if you can't find a fault, then you can't fix it. 
    4. fault identification --> Identify the fault occured. If we need to improve our system, we need to know why it crashes.
    5. live code upgrade -->  allows to dynamically patch the code while it is running
    6. stable storage  -->    storage of a processor should be partitioned into stable storage(survive the processor crash).

####b) Now how do we program the six rules.
    1. isolation --> Error occuring in a process will not affect the other(strong isolation). The Erlang was designed to program fault tolerant system's. So they are made from isolated components.
    2. concurrency --> spawn function..  it spawns a function and send it to the mailbox of the process.Recieving process have some engine that look into the mailbox.
    3. failure detection -> spawn_link function..provide link between two process. Without a link , if one process die, no other process will not informed                                          
    4. fault identification -->  
                                                 Pid = spawn_link(fun() -> .... end),
                                                     process_flag(trap_exit, true)
                                                      receive
                                                             {'EXIT', Pid, Why} ->
                                                                     error_log:log_error({erlang:now(),                                                                          Pid, Why})
                                                                       ............
                                                          end.
        If a process died, we  need to know why. The Why parameter, provided by run time system, tells exactly why it crashed.
    5. live code upgrade --> allows as to dynamically patch code while running.
    6. stable storage --> mnesia database is used and also third party storages like Riak, CouchDB etc are used.

###Fault tolerance and scalability
                     For the thing is  fault tolerant, then they are made from isolated components.If so, then they can run in parallel. That means they are scalable also. 

These are the main topics he covered in this lecture. And also explained the Erlang properties and future.


## 2. The Mess We Are In -- Joe Amstrong

   In this lecture he shows the mess we are in and the solution and ways to clean up the mess.He started with three topics,
                  1.What's went wrong?
                  2.What the law of physics say about computation
                  3.What can we do about it
##### What went wrong
           -->The seven deadly sins(No comments, No specifications, bad codes etc),
           --> Legacy code => The devoloper is dead, and no one understand the code. But the code works perfectly.
           --> Complexity => Joe represent the complexity of programs in an interesting way. If we relate the complexity of a
           program to number of atoms in a planet, then you can see very very small programs passes many possible states is               larger than number of atoms in the planet.
           --> Failures => If a problem executed in one computer and fails, you screwed. If you replicated over all computers             then chance of one of them fails is in a thousand. ie; you can make the system reliable by making independent                  process. To make the system scalable andd fault tolerent, then the system is
                           1. Distributed
                           2. Parallel
                           3. Concurrent
            -->  Efficiency and clarity => that is a real mess. All the mess in the legacy code cause problems
                           
##### What law of physics say about computation
         --> Some causes must occur as the effect of an event.
         --> As increases the files the entropy also increases.
         --> And there is also some physical limits in computation like Bremmerman limit(1.36 * 10^50).
         
##### What can we do about it?
         --> Abolish name and places => Can use hash instead of names. It helps to prevent the spoofing and avoid man of                middle. And able to cached forever.
         
         --> Make a condenser => 
                                 1.Find identical files
                                 2.Merge all similar files
            we need to find the most similar files to the given file. In that case he specified a least-compression differenc e             mechanism. ie;
                     A~=B if
                        size(C(A)) ~= size(C(A++B)). 
                        
         --> Also we need to reverse the entropy to make things smaller.
         
These are the main topics he discussed here.


## 3.Erlang master class(Kent University) -- Lnaguage processing in erlang
###     1. Representing structured data -- Simon Thompson
             We can represent the expressions(2 + (3 * 4)) simply as a string. But it really a bad idea, Why????....
             When look into an expression we see a structure.ie;(2 + (3 * 4)), is a addition of 2 to the multiplication of 3                and 4. But a string is a sequence of one character to the another.
                 So the problem is how represent the expressions in a program. We can represent it like below, as tuple.ie;
                 (2+(3*4)) --> {add,{num,2},{mul,{num,3},{num,4}}}.
            In general we use a type expression as below
               -type expr() :: {'num', integer()}
                               |{'var', atom()}
                               |{'add', expr(), expr()}
                               |{'mul', expr(), expr()}.
            What it means that, writing a description ofexpression that refers to itself.ie; It gives a very clear description             of what all expression look like, what it points to etc.
            
            String to expr() --> parsing
                    "(2+(3*4))" -> {add,{num,2},{mul,{num,3},{num,4}}}.
            expr() to string --> (pretty)printing
                    {add,{num,2},{mul,{num,3},{num,4}}}.
                    
###      2. Using recursion; Pretty printing - Simon Thompson
             Starts from the pretty printing, we can convert it to string by two ways.
                     1. Bottom Up
                        Here first we convert the numbers to string(2 --> "2") and compine those numbers and operators to                              string("(3 * 4)"), and then at the top level we can compine to get the whole expression.
                     2. Top down
                        Here the idea of the recurssion is explained.We start from the top.ie; "(smthn left + smthn right)".
                        Then we need to get the dtring from left and the string from the right.If some one gives a present 
                        at value for the left side and the right, it will easy to write the whole expression. This is the                             simple idea of recurssion.
                        lets start with a -spec function, it says what type we are writing. lets see
                             -spec print(expn()) -> string.
                              the print takes an expression and change top string. lets look at the code,
                                  print({num, N})->
                                       integer_to_list(N); %% convert integer to list
                                  print({var, A}) ->
                                       atom_to_list(A);  %% convert atoms to list
                                  print({add, E1, E2}) ->
                                       "(" ++ print(E1) ++ "+" ++ print(E2) ++ ")";
                                  print ({mul, E1, E2}) ->
                                       "(" ++ print(E1) ++ "*" ++ print(E2) ++ ")".
                                    
                           this code returns the expression recursively as shown below.
                           print({add,{num,2},{mul,{num,3},{num,4}}}).
                               -> "(" ++ print({num,2}) ++ "+" print({mul,{num,3},{num,4}}) ++ ")"
                               -> "(" ++ "2" ++ "+" ++ "(" ++ print({num,3}) ++ "*" print({num, 4}) ++ ")" ++ ")"
                               -> "(" ++ "2" ++ "+" ++ "(" ++ "3" ++ "*" ++ "4" ++ ")" ++ ")"
                               -> "(2 + (3 * 4))"
                               
                        This is how the top down method works and the recurssion.
                        And these are the main topics discussed here in this chapter. 
