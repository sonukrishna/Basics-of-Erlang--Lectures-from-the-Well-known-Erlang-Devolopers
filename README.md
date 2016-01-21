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


                        
                           
