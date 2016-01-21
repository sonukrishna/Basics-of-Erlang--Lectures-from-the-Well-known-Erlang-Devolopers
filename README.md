# Basics-of-Erlang--Lectures-from-the-Well-known-Erlang-Devolopers
This contains small descriptions of video lectures from known Erlang devolopers(Joe Amstrong, Kent University etc). The things i understood from the lectures are described in the repo.


###Joe Amstrong's seminar at Stanford 
   
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
