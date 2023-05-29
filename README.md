# Project 4 Networks & Distributed Systems, March 2023
## Description 
This project was to design a transport protocol akin to TCP. We designed two programs: a sender (3700send), and a receiver (3700recv) that work in tangent to reliably transport data messages, split into packets. Like project 3, this was also run in a simulator, since the
real Internet is too reliable to be a challenge. Starter code used UDP sockets, with a very slow stop-and-wait protocol, and we used
elements of various TCP versions in order to improve performance and reliability.

## Approach
Most of the code in 3700send is contained in the run() function, where for loop continuously loops through the sockets, 
differentiating between system input, or new packets to be sent, and acks that have been received. Before this happens 
however, run() checks to make sure that the oldest un-ACKed packet has not been dropped. If it has, then the packet is 
dropped and the function executes multiplicative decrease. If the socket is an ack message, the message is checked to 
be uncorrupted. If the ACK is a duplicate, all in-flight packets are retransmitted, if the ACK belongs to a packet that
is not in flight, it is ignored, and if an ACK corresponds to a packet that is currently in flight the packet is removed
from in-flight and either slow start or multiplicative decrease is called upon. If the socket is a system input, run() 
checks to see how big the current window is and takes in that many inputs, sending them as packets to 3700recv and 
increasing and adding the sequence number to in-flight.

In 3700send there are also helper functions. Slow start() increases the window size based on its relation to the slow 
start threshold. Multiplicative_decrease() decreases the slow start threshold to half and sets the window size to 1. 
Fast_retransmit() retransmits all in-flight packets if there are three duplicate ACKs in a row. Check_hashes() checks
to make sure the hashes of the packet and the recieved ack are the same to make sure the file wasnt corrupted. And 
better_send() sends a given message to 3700recv.

Like 3700send, most of 3700recv's code is contained within run(). Within run(),  the message is broken down, checked 
for corruption and data loss, and prints out packets once they have been received in the correct order, as found by 
the given sequence numbers.

In 3700recv there are some helper functions as well. sort_and_merge_buffer() sorts the buffer in sequence number 
numerical order and prints out the messages that have been received in order. Get_last_num() gets the last sequence 
number that was recieved by the program.

The biggest challenge with this project was making it more efficient. Being able to send messages without corruptions 
and resend when dropped etc. was fairly simple, but once that was complete we had to make sure the program ran fast 
and efficient. This took a lot of trial and error. We obviously tested our code by running the configs, but also by 
using self.log to see in real time how different variables changed (rtt, buffer size, etc). We had log statements to 
show the window and buffer almost the entire project, because these were very helpful for seeing where packets were 
being transmitted out of order or not at all. We implemented slow start and AIMD, however we did not do fast retransmit
or recovery or SACK, because trying to implement these actually made the code more buggy or slower.

Some features we thought we designed well were:
	The implementation of slow start and multiplicative increase/decrease
	The ability to detect dropped packets
	and bring able to detect corruption and duplication

                # if len(self.in_flight) > 1:
                #     self.rtt = (ALPHA * self.rtt) + ((1 - ALPHA) * (time.time() - self.in_flight[0][1]))
                #     self.log("RTT: " + str(self.rtt))
