# Thinking about sbot system design.

My goal is to try and reveal all the parts of the sbot domain to help inform the design of the rust sbot.
This isn't supposed to map one to one to the js implementation.

## Things that happen 

- Peer discovery
  - udp
  - rtc
  - dht
  - checking feeds for information about pubs
    - gossip.json (want to deprecate) 
- Peer network connections (Dialing out and dialing in)
  - rtc
  - tcp
  - socket file
- Connection scheduling + prioritising
- Authentication (provided by secret hand shake at the moment)
- Authorisation (should we allow this peer to connect to us, should that peer be allowed to call this rpc method?, is this client allowed to publish that message?)
- Replication (initiated by the client in js.) 
  - decides who to replicate 
  - asks for data from a feed it wants to replicate.
    - should this happen for _any_ connected peer, or just ones we've dialed out to?
- Clients use the mux-rpc api (eg Patchwork)
  - clients query the db
- Blobs

## Domain stories

### Discover a lan peer, connect, and replicate

1) We discover a peer on the local network. 
  - At this point their identity is claimed but not verified by shs
2) We decide it's time to connect to the peer on the lan. 
3) We check with the authorisation thing to see if we're allowed to connect to the peer. We are.
4) A multiserver "dialer" that can handle tcp connections using shs makes the connection.
5) We work out what feeds we want to replicate from our social graph.
6) We replicate feeds we're interested in. 
7) They replicate feeds they're interested in from us. 

## Noticings

The client and server distinction starts to get blurry when I delve into it.
The easy case is a pub on the internet. That seems to clearly be a server. My sbot hits that and replicates feeds I'm interested in.
  - But, the pub also replicates information off my sbot too.
Yes we do have entities that are clients and servers on the network. But once we're connected both peers can make rpc calls on each other. In that sense they're equals.
  - This extends to blobs too I think, a request for a blob floods out over the network in search of the blob. It doesn't matter who initiated the connection (assumption).  
- Replication (and maybe other things) rely on the initiator of the connection (client) to establish the replication stream. It's part of the protocol I guess.


