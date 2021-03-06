# Basics-of-Erlang--Lectures-from-the-Well-known-Erlang-Devolopers
This contains small descriptions of video lectures from known Erlang devolopers(Joe Amstrong, Kent University etc). The things i understood from the lectures are described in the repo.


## 1. Joe Amstrong's seminar at Stanford 
         "https://www.youtube.com/watch?v=YaUPdgtUYko"
   
      In this lecture he describes the very basic ideas of the Erlang language. The six principles in Erlang
      and how to program this principles. And also provides basics of Tandem computers, descriptions about
      fault_tolerance, messaging etc.
####a) The six principles
    1. isolation --> The Erlang process must be isolated by design, one process cannot damage the other.
    2. concurrency --> The process mut be concurrent.
    3. failure detection --> The basic idea behind this is, if you can't find a fault, then you can't fix it. 
    4. fault identification --> Identify the fault occured. If we need to improve our system, we need to know why it crashes.
    5. live code upgrade -->  allows to dynamically patch the code while it is running
    6. stable storage  -->    storage of a processor should be partitioned into stable storage(survive the processor crash).

####b) Now how do we program the six rules.
    1. isolation --> Error occuring in a process will not affect the other(strong isolation). The Erlang was designed to
    program fault tolerant system's. So they are made from isolated components.
    2. concurrency --> spawn function..  it spawns a function and send it to the mailbox of the process.Recieving process have
    some engine that look into the mailbox.
    3. failure detection -> spawn_link function..provide link between two process. Without a link , if one process die, no
    other process will not informed                                          
    4. fault identification -->  
                                 Pid = spawn_link(fun() -> .... end),
                                 process_flag(trap_exit, true)
                                 receive
                                       {'EXIT', Pid, Why} ->
                                       error_log:log_error({erlang:now(),Pid, Why})                               
                                       ............
                                 end.
        If a process died, we  need to know why. The Why parameter, provided by run time system, tells exactly why it crashed.
    5. live code upgrade --> allows as to dynamically patch code while running.
    6. stable storage --> mnesia database is used and also third party storages like Riak, CouchDB etc are used.

###Fault tolerance and scalability
         For the thing is  fault tolerant, then they are made from isolated components.If so, then they can run in
         parallel. That means they are scalable also. 

These are the main topics he covered in this lecture. And also explained the Erlang properties and future.


## 2. The Mess We Are In -- Joe Amstrong
               "https://www.youtube.com/watch?v=lKXe3HUG2l4"
   In this lecture he shows the mess we are in and the solution and ways to clean up the mess.He started with three topics,
            1.What's went wrong?
            2.What the law of physics say about computation
            3.What can we do about it
##### What went wrong
           -->The seven deadly sins(No comments, No specifications, bad codes etc),
           --> Legacy code => The devoloper is dead, and no one understand the code. But the code works perfectly.
           --> Complexity => Joe represent the complexity of programs in an interesting way. If we relate the complexity
           program to number of atoms in a planet, then you can see very very small programs passes many possible states is
           larger than number of atoms in the planet.
           --> Failures => If a problem executed in one computer and fails, you screwed. If you replicated over all computers
           then chance of one of them fails is in a thousand. ie; you can make the system reliable by making independent
           process. To make the system scalable andd fault tolerent, then the system is
                     1. Distributed
                     2. Parallel
                     3. Concurrent
            -->  Efficiency and clarity => that is a real mess. All the mess in the legacy code cause problems
                           
##### What law of physics say about computation
         --> Some causes must occur as the effect of an event.
         --> As increases the files the entropy also increases.
         --> And there is also some physical limits in computation like Bremmerman limit(1.36 * 10^50).
         
##### What can we do about it?
         --> Abolish name and places => Can use hash instead of names. It helps to prevent the spoofing and avoid man of
         middle. And able to cached forever.
         
         --> Make a condenser => 
                                 1.Find identical files
                                 2.Merge all similar files
            we need to find the most similar files to the given file. In that case he specified a least-compression difference
            mechanism. ie;
                     A~=B if
                        size(C(A)) ~= size(C(A++B)). 
                        
         --> Also we need to reverse the entropy to make things smaller.
         
These are the main topics he discussed here.


## 3.Erlang master class(Kent University) -- Lnaguage processing in erlang
            "https://www.youtube.com/watch?v=gKRyAb7d8lo"
###     1. Representing structured data -- Simon Thompson
            We can represent the expressions(2 + (3 * 4)) simply as a string. But it really a bad idea, Why????....
            When look into an expression we see a structure.ie;(2 + (3 * 4)), is a addition of 2 to the multiplication of 3
            and 4. But a string is a sequence of one character to the another.
                 So the problem is how represent the expressions in a program. We can represent it like below, as tuple.ie;
                 (2+(3*4)) --> {add,{num,2},{mul,{num,3},{num,4}}}.
            In general we use a type expression as below
               -type expr() :: {'num', integer()}
                               |{'var', atom()}
                               |{'add', expr(), expr()}
                               |{'mul', expr(), expr()}.
            What it means that, writing a description ofexpression that refers to itself.ie; It gives a very clear description
            of what all expression look like, what it points to etc.
            
            
            
            String to expr() --> parsing
                    "(2+(3*4))" -> {add,{num,2},{mul,{num,3},{num,4}}}.
            expr() to string --> (pretty)printing
                    {add,{num,2},{mul,{num,3},{num,4}}}.
                    
###      2. Using recursion; Pretty printing - Simon Thompson
             Starts from the pretty printing, we can convert it to string by two ways.
                  1. Bottom Up
                     Here first we convert the numbers to string(2 --> "2") and compine those numbers and operators to
                     string("(3 * 4)"), and then at the top level we can compine to get the whole expression.
                     
                  2. Top down
                     Here the idea of the recurssion is explained.We start from the top.ie; "(smthn left + smthn right)".
                     Then we need to get the dtring from left and the string from the right.If some one gives a present 
                     at value for the left side and the right, it will easy to write the whole expression. This is the simple
                     idea of recurssion.
                        
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

###      3. Evaluating expression - Simon Thompson
              In this section, its all about evaluating an expression. We take an expression and will  convert it to its
              value.
#####       Direct evaluation of expression
               Basic example : (2 + (3 * 4)).
               starts with taking each expression to a number.
                     -spec eval(expr()) -> integer()
               So then evaluating the expression, using the basic recursion.
                     eval({add, E1, E2}) ->
                        eval(E1) + eval(E2).
               And do same for the multiplication,then the result for (2 + (3 * 4)) returns as 14.
               But what we do, when n expression like (2 + (3 * a)) cames. So we have to add an environment, which capable of
               compiling atoms and integers.
                   -type env() :: [{atom(), integer()}].
               So now, when comes a variable, we need a lookup, just like below
                   eval(Env,{var, A}) ->
                        lookup(Env,A).
               The lookup()
               --------------------
               The lookup function, it runs through the list. When find a pair,if the first element in the pair matches with
               the atom we looking for, returns the second element then. We do recurssion and look for the value in the tail
               of the list. If the list is empty we fails.
                  lookup(A,[{A,V}|_]) ->
                     V;
                  lookup(A,[_|Tail]) ->
                     lookup(A, Tail).
         
               Now do exactly the same for the add and multiplication. But we need to pass Env on left and right
               subexpression,so that we can calculate the value of the sub expression. And compined them.
               
###      4. Compiling and run on virtual machine - Simon Thompson
               Basic example : (2 + ( 3 * 4 )).
               The virtual machine has a stack which is manipulated by machine instructions.Two steps mainly occured,
               compiling and run it on the virtual machine.Now we got the basic idea(compile and run), the problem is,
               how we code them in erlang???
                     1) how to code machine instructions
                     2) how to model the running of the machine
                     3) how to compile the expression into a sequence of expression.
               Represent these things as types(atoms, integers etc)
               
               Now time to compile our expression into a program. ie;
                     -spec compile(expr()) -> program().%% list of instruction.
               Run the program to get an integer value
                     -spec run(program(), env(), stack()) %% the program we havn't executed yet , environment and the stack
                     the program points to return the integer.
               Now running the program.
                     run([{push, N}|Cont], Env, Stack) ->
                           run(Cont, Env, [N|Stack]). 
                  We run using some pattern matching ideas and tail recursion.Here if the element is a integer PUSH it to
                  stack. Do the same for atoms, FETCH it to stack.
                  
                  When we comescomes to the add, you can see pattern match act its best.
                        run([{add2}|Cont], Env, [{N1,N2|Stack}]) ->
                              run(Cont, Env,[(N1+N2)|Stack]).
                     Here take those two values from the stack and replace them by their some, Do the same for multiplication.
                  In the final case, we have no instruction and only one value on the stack, then we returned it.
                  
                  In the compiling part, compile by PUSH/FETCH to the stack,then adding compile E1 and E2 and also add the
                  operation, and then runs.
               Next section is about parsing.
               
###     5. Parsing - Simon Thompson
            Parsing is converting a string to an expression, we learned the basics in previous sections.
            Basic example : ("(2+(3*4))")
            Lets check some small examples...
               parse("(2 + ( 3 * 4 ))") => {add,{num,2},{mul,{num,3},{num,4}}}
               parse("2") => {num,2}
      
               parse("2 + (3 * 4)") => {num, 2}--> 
            what we do in this. We keep hold the remaining part of the string which is
            still to be processed.ie;
               -spec parse(string()) -> {expr(), string()}. 
            It returns the expression find in the beginning of the string and the second part is the remaining of the string.
               
            Now going to the parse function. Here pattern match the argument with an opening paranthesis("("), then rest is
            parse recursevely. If we spot an expression, whatever remains after that, check if it starts with an operator.We
            pass again,
            if the remaining spot an expression, we pattern match for the closing paranthesis. We also do the case switch to
            do the operations.
            In this section he also provide some ideas about the functional programming, function inside another function,
            passea as an argument. To get the longest initial segment of a list with a given property.ie;
                  -spec get_while(fun((T) -> boolean()),[T]) -> {[T], [T]}.
               The boolean function represent the properties, what we do here is finding the longest initial segment with the
               property.
         
            You get the tutorials here: https://www.youtube.com/watch?v=gKRyAb7d8lo.(in this link, First tutorial link, find
            the other sections using this this).
         
###     6. Simplification - Simon Thompson
            In this section he simplifying the code by adding more simple functions. He uses the idea of functional
            programming,tail recursion etc.And is better class to understand these basic erlang concepts in a simple
            and more easiest way.
            You can get the video using the above link.
         
  These are the first part of Erlang master class, and I advice everyone to go through these videos. Its really helpful for 
  a fresher(like me), to understand basic concepts of erlang. I don't have the remaining video section myself. I will update
  the remaining part when i get it.
  
  Thank you..
  
  
##  4. Thinking Like An Erlanger --Torben Hoffmann
                  "https://www.youtube.com/watch?v=_fgaPGLGZI0"
            It is a small section from lambda days 2015 by Torben Hoffmann. Here he discussed some ideas about how you can 
            approach to Erlang program, and some other topics. For the kinds, who said langauge like Erlang is hard to
            learn. And he says, it is because the langauge is different and special itself. The syntax of the langauge is
            somewhat irrelevant, simply it's not about the syntax, but thinking is everything.The Erlang is a domain
            specific langauge and starts with the telecome.
            
            The GOLDEN TRINITY of Erlang
            --------------------------------
               1. share nothing
               2. fail fast  -- let it fail
               3. failure handling
               
            Erlang uses tons of process.Program java to get objets, Erlang give process instead.
            He described how processes interacts using a simple example -- "Game Of Life". It is the evolution of a cell
            in descrete time. the evolution of cell is depends on the neighbours. In Erlang, each cell is meant by each 
            process, that is One process per cell. to know about the neighbours, need a little bit of protocol. After that
            update the state to T+1 and update it's own content. Spawn a process to collect from all neighbours. We do
            sending messages between entity, that's all we need instead of spending time by creating objects.
            What if a process asked for older value or for future value. How we get???
                  For older --> create a history
                  For future --> just Queue the responds
                  
            Failure handling
            --------------------
            We need to supervise the  cells in the system if the cell dies. the supervisor then restarts it. Every time
            it restarted, the new one that restarted with same argument and with same look, but lost all states and all
            history.How we fix this???
            Fix this by monitoring the cells, if a new cell come,it says please add the rest and step forwad them to the
            current time. If a cell is down, then supervisor sends a DOWN message to monitor the cell. Then restarts it
                     handle_info({'DOWN', Ref, process, _Pid, _Info},_) ->
                     handle_cast({reg,XY,Pid},_)->
                     New = erlang:monitor(process, Pid)
            And it register with the Cell manager, it monitor and then asked the time module to what the max time for the
            rest to reach the cell. And then returns.
            
            Now how about a small deadlock. A cell req to a process for its future value, but it queues the responds until
            it get updated to the next state. Then it send back, unfortunately the cell is died or killed by something.
            Then the cell restarted and has no previuos stateor history then comes the deadlock. He said about the quick
            check, operation by operation etc here.
            
            These are basic things discussed by Torben Hoffmann.
            You get the video: https://www.youtube.com/watch?v=_fgaPGLGZI0
            Thank you...
            
##   5. Erlang Pattern Matching - Buissness Needs  by Torben Hoffmann.(One of the best)
                     "https://www.youtube.com/watch?v=LbR0LtEw_OA"
            In this section, he describes hoe Erlang meets the buissness value. Let's relate the Erlang to it's bussness
            values. Do you know what are the real buissness imperatives, it include three things 1) Faster, 2) Bigger,
            3) Cheeper. So comes to the software needs, it demandssomething
                     1. speed to market
                     2. reliable
                     3. scalable
                     4. maintainable
            So time to create a software that meets the needs, but the requirements are high. Then comes the Erlang matter.
            
            Erlang Original Requirements
            -------------------------------
                     1.Large scale concurrency
                     2.Soft real time
                     3.Distributed Systems
                     4.hardware interactions
                     5.Very large software systems
                     6.Complex functionality
                     7.Continue operation for many years
                     8.Software maintanence
                     9.Quality and Reliability
                     10.Fault tolerence
            
            Now time to think how they relate with the buissness. For that, first grab an idea about what the market wants
                     --> Productivity
                     --> No down time
                     --> Something always work
               Compine all these together, then it simply points to a single word, "MONEY".
               
            Erlang goods
            -----------------
                     1.Low latency
                     2.Stateful
                     3.Concurrent
                     4.Distributed
                     5.Fault tolerant etc..
            
            =>To Share OR Not To Share
              ------------------------
              When two process acting on a memmory space, if one process dies,the memmory is correpted and the process is
              shutdown.But in the case of Erlang, each process has it's own memmory spaces. So the program never correpts.
            =>Failures
              --------
                  --> Programming errors
                  --> Disk failures
                  --> Network failures etc..
              Erlang, the langauge is fault tolerent by design. There is failure and get managed.And also it got some 
              buissnessbenefits too.
              Torben described a graph, which shows how each langauge solves the customers needs. He said that Erlang is
              three times faster than the C++ (customer needs -- C++ => 20%, Erlang => 60%).
              
            =>The Supervisor Pattern
              ----------------------
               The supervisor is not that much simple as we think, for a fresher like me,or others, its little bit a
               complicated one.
               It start with the start_link(Discussed supervisors in above sections), to some internal functionality, some
               kind of start mechanism.Then it looks to the restart stratergy,child specification, number of children in
               the process comes with the spawn link and update the restart hystory. If the child fails, then it goes to 
               some supervising functionality,and looks the restart stratergy. The take decission's  like "should it goes
               to restart or terminating".
            
            =>Buissness Benefits of Supervisor
              -------------------------------
              One process dies, won't matter the system continues the service.
              Everything is logged --- You know what is wrong and you can fix it really really fast.
              Here, the product owner is the incharge, not the softwre. Thats really anice think
            
            These are the basic factors or ideas discussed in this small section.
            Thank you
         
##    6. Learning Erlang -- Advantage of keeping things simple  by Torben Hoffmann
                     "https://www.youtube.com/watch?v=W3szsF6X5r8&list=TLKfjyU_WT-GY"
            Here he discussed the basics of Erlang and the idea of OTP. We discussed the basics already, now time learn
            basics of OTP.
            OTP -- Open Telecom Platform
            -------------------------------
            Here is a question, and hope you will get the answer at the end.
                  "Why do most production system use OTP????".
            Building a system
            ------------------
               When you build a system, you need the following
                  -- libraries
                  -- Design principle pattern
                  -- Methodology
                  -- Tools
            Now the answer is, OTP is designed to tackle all the above things in a way that make good use of Erlangs
            features.
            Erlang is based on a set of principles.
                  -- Releases
                  -- Application
                  -- Behaviour
            
            Release
            ----------
            It is the complete Erlang system, contains
                  -- Erlang run time --ie; actual VM of the system.
                  -- Large number of OTP applications
                  -- Rules for starting/stoping , manging the system while it is running.
                  
            Application
            -------------
            It is a logical unit, it contains
                  -- The modules that building the Erlang part of the system(Erlang code).
                  -- Static data -- the application means to run
                  -- Code in other langauge etc
                  
            Behaviour
            -----------
            It is the formalisation of the design pattern. You have a design pattern, the behaviour in the code on OTP
            implements the design pattern.
            The basic principle is---
                  Split the code into two parts
                     1. generic behaviour:- probided by the OTP library
                     2. callback module:- implementedby the programmer.
            There are five behaviour type
                  1. gen_server -- client/server pattern
                  2. gen_fsm -- finite stete machine pattern
                  3. gen_event -- event manager
                  4. supervisor -- supervision tree manager
                  5. application -- encapsulate the application pattern
                  
            Supervisor
            ------------
            Why need a supervisor??? If you want your system long exist, there is number of services that always must
            exists. The supervisor tree contain number of child process(supervisor, worker).Each supervisor monitor the
            worker, if it dies, then restart it. Supervisor is discussed correctly in the above section.
            
            The system is open ended means that, it is perfectly possible to define new behaviours. Some of the reason 
            that most of the production system uses the OTP is, it allows lot of extra functions, lot of reliability,
            highly concurrent,  allow ypu to add things to OTP etc..
            
            These are the main topics discussed by Torben Hoffmann and Robert Virding.
            
##   7. Creating A Real Time Game Engine in Erlang  -- Mark Allen
                      " "https://www.youtube.com/watch?v=sla-t0ZNlMU""
            Here he describes the basics of Erlang,what it offers as a development platform etc.
            Basics of Erlang
            ----------------------
             --> The great things about Erlang---> concurrency model, transperancy, fault tolerance etc. All the points are
                discussed in each of the above sections.
                
             --> What sucks in Erlang
             ---------------------------
               1. String handling is stupid
               2. Syntax is off-putting
               3. Library isn't that great --Erlang user community is really smaller one as compared to the other
               functional programming langauges.
               4. A bit of learning curve -- Sometimes the easy things become hard in Erlang. It is easy to spawn one
               million process, but not to insert something on the middle of a list.
            
             --> Key Erlang syntax idioms
                ------------------------
               1. processes -- It is the unit of computation in Erlang. Simply said it is a super super lightweight thread.
                  No memmory sharing between the processes. They pass information through message passing.
               2. Immutable Variables
               3. Recurssion
               4. Pattern Matching -- A very powerful and cool idea in Erlang
               
            A simple "fizzbuzz" example
            --------------------------
            You get the description and code from:
                     "https://github.com/sonukrishna/The-fizzbuzz-example-in-Erlang"
            
            Then he explained about designing a game -- a simple one of players and ports.
            The ports contains list traders, quantity and current price etc. And the players includes the current location,
            current amount of cash etc. He described the ports and players as Erlang servers(gen_server). He described,
            we have some supervisor at the top which spawns two childs, the game_player_sup and game_port_sup.So each
            time a player or port show up, it starts the gen_server under it's supervision.
            
            These are the things he discussed. To watch and see the code use the link below
                     "https://www.youtube.com/watch?v=sla-t0ZNlMU"
         Thank you
