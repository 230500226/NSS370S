```
well now that we've got a really good appreciation for the pieces of the router the input ports the output ports and the switching fabric we're going to dive down deep into the topics of packet scheduling and buffer management and we've seen how important these are with the Internet's best effort service model packet loss and packet delay have had a profound effect on what has to happen the transport layers and the application layers so now we're going to dive down deep into the topics of packet scheduling and buffer management and we'll take a look at how those can be used to control and also mitigate packet loss and packet delay well let's get started by first taking a look at input port queuing remember that we saw earlier that input port queuing can happen when the routers switching fabric is less than the combined rates of its incoming links in this case more packets can arrive in a unit time then the switching fabric can possibly move from the input side of the fabric to the output side and so queuing can occur on the input side a queuing phenomena that's unique to the input side of the switching fabric is what's known as head of the line blocking or hol end of the line blocking occurs when packets at different input ports want to go to the same output port if only one pack at a time can be transferred from an input to a given output port and if there are K packets that want to go to the same output port then K minus 1 of those packets are going to have to wait on the input side of the switch and if packets have to wait they can also block the packets behind them in their input queue from moving forward and this is what's known as head of the line blocking take a look at this example here where the colors of the packets correspond to the output port to which they're destined in this example say at time T we see the top and bottom input ports have a red packet destined to the upper output port in one clock deck the upper red packet is transferred from the input to the output side of the switch the blue packet in the middle it's also able to move from its input port to its output port the red packet at the bottom however is locked from moving to the read output port since the packet at the top was already transferred there and so the green packet behind this lower red packet experiences head of the line blocking let's now focus on the output port side since this is really where the action is and let's start by seeing why buffering is needed in the first place at this output port we have a link with transmission rate of our bits per second and let's say we have a non-blocking switch capable of delivering end times our bits per second to the output port now we can see why buffering occurs bits can be arriving at a rate of end times are but bits can only be drained or transmitted out at a rate of are whenever the arrival rate exceeds the departure rate the buffer will fill and since buffers are finite there may not be enough buffer space to store all of the packets that need buffering and packets will have to be dropped there simply isn't enough memory to hold all of the packets that need buffering so right here at the output port is where congestive packet loss occurs and you know it may be tempting to say well it's really the switch fabrics fault it's delivering packets too fast at a rate of end times are when the link can only drain at rate R so we should slow the fabric down but that's not really the cause of the problem is it think back to our earlier discussion of principles of congestion control the real cause of the problem congestion is that there are too many transport layer senders at the edge of the network sending too many packets too fast packets whose paths cross at this congested output port in the face of buffering and congestion there are a couple of decisions that'll need to be made at the output port first if there's not enough room for all arriving packets a packet drop policy will be needed to determine which packets are going to be dropped and if there are multiple packets in the buffer awaiting transmission a packet scheduling discipline will be needed to choose which packets among the many cute packets will be transmitted next we'll look at both drop and scheduling policies in just a second now there are a lot of really important idea that we just talked about here on this slide so make sure you've got it all it's really important well you might be thinking given buffering is such a big concern how much buffering should a router have and more to the point you might also be thinking well hey shouldn't it be a good idea to invest in a lot of buffering with a lot of buffering there'd be less packet loss since there'll be more buffers to observe the burstiness and packet flows having more buffering must be a good thing right well it might surprise you that even after 50 years the question of how much buffering is the right amount of buffering is still pretty much an unresolved issue there are some recommendations you could take a look at RFC 3439 and find a rule of thumb that says the amount of buffering should equal a typical RTT say a couple hundred milliseconds times the output link capacity there's also been more a recent theoretical research that assumes that transport layer senders are generally independent of each other and recommends dividing the RFC 34:39 amount of buffering by the square root of n where n is the number of flows crossing the link and that's a lot less buffering and while you might have the sense that large buffers will generally result in less loss and you could be right large buffers do have a downside large buffers mean larger delays and larger RT T's large delays are bad for gamers and for interactive teleconferencing users where tens of milliseconds really do matter but maybe even more importantly large RTT delays mean that TCP senders will be slower to detect and therefore react to congestion you might recall that when we studied delay based congestion control we encountered the saying that we want to keep the bottleneck link just full enough to keep the link busy but no fuller but that was from the perspective of the sending hosts when we think about the amount of buffering we really do want buffers to absorb short term statistical fluctuation in packet arrival rates and to keep the link busy but not to have so much buffering that congestion control action times become slow and sluggish well buffering sort of like salt and cooking you've got to find the right amount just the right amount makes things better but too much can really ruin things well who would have thought that buffering and queuing at an output port could actually be so complicated and so subtle and you may ask yourself why is that and it's really because it's here at an output port buffer that the global behavior of potentially thousands of active senders worldwide are converging as their flows all pass through the single output link we see the convergence of global internet scale behavior at this one location deeply inside the internet that's pretty cool well we're next going to move on to the mechanisms for buffer management and packet scheduling and will find it useful to think of an output port abstractly as a queue as shown here of course we're all familiar with queuing systems in our daily lives banks at buses at bus stops at food stores at metros and many other places think about how many cubes you've been in today packets arrive to the queue they wait for service and they're eventually selected for service receive service and then leave the queue the link layer functions as the server when a packets bits are being transmitted over the outgoing link that packets in service if an arriving packet emerges from the switching fabric to find there's no free buffer space either that packets going to be dropped and that's known as tail drop or an already queued packet could be removed from the buffer and dropped to make room for this newly arriving packet say according to some kind of priority mechanism for example there might be a policy to drop low priority packets say carrying end-user TCP traffic rather than high priority packets say carrying Network management in control packets for the ISP as we also learned when we studied Network assisted congestion control a buffered packet may also be marked with a congestion indication to signal that the link is becoming congested we learned earlier that for the internet's explicit congestion notification ecn mechanism to bits in the IP header type of service field are used for ecn marking will see these bits in the IP datagram header in the next section when we study ipv4 but it's here at the output ports and where congestion occurs that these bits are going to be
set we can now look at several packet
scheduling disciplines that are used in
practice under first come first serve
scheduling packets are transmitted
simply in the order in which they arrive
to the output port we're all familiar
with first-come first-serve queuing I'm
guessing it's what people do the most we
all join the back of the queue and
customers are served from the front of
the queue
same thing with packets
we're all familiar with priority queues
you get that with Airlines platinum
diamond gold silver or bronze class of
service they all sound pretty fancy and
great but you definitely rather be
platinum than bronze and I think we all
probably have an intuitive understanding
of how priority disciplines work having
been in the lower priority class of
service and seeing later arriving people
in higher priority classes getting
service before us under priority
scheduling packets arriving at the
output link are classified into priority
classes on arrival at the queue as shown
here where we have a high priority red
class of traffic and a lower priority
green class of traffic the priority
queueing discipline will transmit a
packet from the highest priority class
that has a non-empty queue that is has
packets waiting for transmission the
choice among packets in that same
priority class is typically done in a
first-come first-serve manner this
animation shows packets arriving from
the top entering service according to a
priority discipline and then leaving the
queue in this example packet one arrives
finds the queue empty and enters service
while packet one is in service packet
two and three arrived when packet one
finishes both packets two and three are
buffered but packet three is chosen for
transmission even though it arrived
after packet two since it has higher
priority than back at two and so on
well we've now learned about the
priority queuing mechanism but you may
still be wondering what is it that
defines a priority class in the first
place how is it determined what goes
into priority class 1 or priority class
2 for example and this is actually
determined by the network operator by
the ISP the ISP may decide that well
network management traffic's really
important it's the highest priority
class voice-over-ip gets higher priority
than email and so on and you remember
that by looking at the transport layer
port numbers one can determine what type
of traffic is being carried by that data
graph now traffic type is one way in
which priorities could be determined
another way might be on the basis of
source or even destination addresses so
here's another possible way to classify
traffic into priority classes imagine
that a company is willing to pay an
internet service provider to give its
packets better service packets are less
likely to be dropped its packets suffer
less delay as a result if it's a search
company its search results get to users
faster if it's voice over IP the voice
is more responsive if it's streaming
video there's less spinning meals well
we've got the scheduling mechanisms to
do that whether or not a company is able
to pay for that kind of better service
actually gets us into the topic of
what's known as network neutrality we're
going to cover that in just a second but
first let's wrap up by looking at a few
more scheduling disciplines so we've
seen first-come first-serve and priority
scheduling under round-robin scheduling
packets are assigned to classes as with
priority scheduling however rather than
there being a strict service priority
among classes a round-robin scheduler
alternates service among classes for
example a round-robin scheduler would
choose a packet from class 1 followed by
a packet from class 2 followed by a
packet from class 3 and so on and if
there's no packet of a given class the
scheduler moves on to the next class in
the schedule
a generalized form of round-robin
queuing that's been widely implemented
in routers and practice is known as
weighted fair queuing wfq light priority
and round-robin scheduling arriving
packets are classified and queued in the
appropriate per class waiting area we
see here red green and blue packets
waiting for their service a weighted
fair queuing scheduler schedules classes
in a round-robin like manner first
serving class 1 then serving class 2
then serving class 3 and then assuming
there are just three classes repeating
that service pattern weighted fair
queuing differs from round-robin
in that each class can receive a
different amount of service in any
interval of time and here's how it works
ideally each class of packets I has a
weight W sub I let's assume that all of
the weights the sum of W J say sum up to
1 under weighted fair queuing during any
interval of time during which there are
class I packets to send class I will
then be guaranteed to receive a fraction
of service W sub I that means if the
link has a rate R then each class of
service will get a guaranteed minimum
amount of bandwidth W sub I times R and
so we can see how weighted fair queuing
allows some type of a bandwidth
guaranteed to be made on a per class
basis network neutrality is an area
where our technical interests in
computer networking intersect with
social political and economic
considerations well we've already seen
the mechanisms for sharing resources but
what about the social political and
economic considerations about how those
resources are shared let's take a look
network neutrality is about the laws and
policies that govern how an Internet
service provider can use the traffic
control mechanisms that we've just
learned about packet scheduling in
particular to control network traffic so
what are some of the questions that
network neutrality addresses well it's
about a number of different things it's
about free speech for example cannon
Espie refused to carry some types of
traffic for example certain types of
news or political opinion it's also
about encouraging innovation and about
encouraging competition for example must
an isp give all companies big and small
equal treatment of their traffic as you
probably know different countries have
been adopting different stances with
respect to these questions we're going
to take a look at what's happening in
the united states since this is well
documented and has all been playing out
pretty publicly our textbooks got some
interesting references about net
neutrality worldwide that you might want
to take a look at in 2015 the United
States Federal Communications Commission
order on protecting and promoting an
open Internet defined three clear bright
line rules associated with network
neutrality let's take a look at what
they are the first bright line rule is
no blocking and here's what it says it
says that an isp quote unquote shall not
block lawful content applications
services or non-harmful devices subject
to reasonable network management in
quote now there's a lot packed in there
the word lawful and applications
services and devices well this rules
about blocking traffic and there
actually was a case involving an ISP
that had a practice of blocking its
customers from using vonage which is a
voice over IP service that competed with
this is p's own telephone service that
would not be allowed and notice the
phrase subject to reasonable network
management will cover network management
when we cover the network layer control
plane but note here that it is allowable
to block traffic for network management
purposes say if the networks been
degraded and then only trickles of
traffic can get through then it's best
that network management traffic be able
to get through of course the question of
what reasonable network management is is
left open to interpretation
the second bright-line rule is no
throttling that is an isp quote unquote
shall not impair or degrade lawful
Internet
effect on the basis of Internet content
application or service or use of a non
harmful device again subject to
reasonable network management note here
the words impaired or degrade there was
a case not too long ago where an ISP was
judged to be interfering with BitTorrent
peer-to-peer traffic and it was doing
this by internally creating and sending
TCP reset packets to BitTorrent client
and servers executing at the end host
this caused the host to close their
BitTorrent connections basically the ISP
was closing the TCP connection
underneath the BitTorrent application
whose traffic it really didn't want and
then finally there's the bright-line
rule of no paid prioritization
this means for example that video
streaming traffic from all video
streaming service providers must be
treated the same for example that one
streaming video service provider cannot
pay to get its packets better service on
their way to the customer and of course
we've seen how this can be done from a
technical point of view via priority
queuing well you might ask why no paid
prioritization since paying for a
priority service seems well almost like
a fact of life at least here in the
United States well one of the arguments
is that if say a video streaming service
provider can pay for better service and
if that successful incumbent the
existing service provider can pay a lot
then this introduces a high barrier to
entry for new competitors and of course
some countries want to encourage market
competition but there are counter
arguments to this claiming that creating
additional revenues for an ISP by having
paid prioritization is a good thing from
a competitive point of view since that
makes the ISP marketplace more lucrative
and so more companies will want to
invest in the ISP market or ISPs will
want to invest more in their
infrastructure and then finally there
are laws going back almost a hundred
years that have regulated the telephone
network as a telecommunications service
provider but there's much less
regulation of information service
providers so what is an ISP is it a
telecommunication service provider or an
information service provider well it
turns out from a regulatory standpoint
the answer to this question matters a
lot in the US but the answer to this
question has really not yet been finally
determined
well these bright-line rules are from a
2015 u.s. FCC order more recent FCC
orders have actually rewritten much of
that 2015 order
so until laws are written and until
there's further litigation and case law
adopted I think it's fair to simply say
that the situation continues to evolve
and I think that's true in many
countries it's interesting that we've
had the ability to do
prioritization since the internet first
became public nearly 30 years ago so
although the Internet has a strong
technical foundation as we've seen three
decades later our social political and
economic policies governing this
creation that have not yet been fully
defined or agreed upon well that wraps
up our discussion of packet scheduling
and packet buffering and also what I
hope was an interesting sidebar into the
topic of network neutrality and more
generally it wraps up our discussion of
what's inside a router remember we also
looked at input ports output ports and
switching fabrics coming up next we're
going to take a look at the Internet
Protocol ipv4 so stay tuned for that
you
```