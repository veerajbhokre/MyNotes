# Back of envelope calculation or capacity planning

Example: Estimate Twitter QPS and storage requirements:

Please note the following numbers are for this exercise only as they are not real numbers from Twitter:

Assumptions:

- 300 million monthly active users.
- 50% of users use Twitter daily.
- Users post 2 tweets per day on average. • 10% of tweets contain media.
- Data is stored for 5 years.

Estimations:
Query per second (QPS) estimate:

- Daily active users (DAU) = 300 million * 50% = 150 million
- Tweets QPS = 150 million * 2 tweets / 24 hour / 3600 seconds = ~3500 • PeekQPS=2*QPS=~7000

We will only estimate media storage here. • Average tweet size:

- tweet_id 64 bytes • text 140 bytes • media 1MB
- Mediastorage:150million*2*10%*1MB=30TBperday
- 5-yearmediastorage:30TB*365*5=~55PB

**Tips:**

- Rounding and Approximation.
    - It is difficult to perform complicated math operations during the interview. For example, what is the result of
      “99987 / 9.1”? There is no need to spend valuable time to solve complicated math problems. Precision is not
      expected. Use round numbers and approximation to your advantage. The division question can be simplified as
      follows: “100,000 / 10”.
- Write down your assumptions. It is a good idea to write down your assumptions to be referenced later.
- Label your units. When you write down “5”, does it mean 5 KB or 5 MB? You might confuse yourself with this. Write down
  the units because “5 MB” helps to remove ambiguity.
- Commonly asked back-of-the-envelope estimations:
    - QPS (Queries per sec)
    - Peak QPS
    - Storage
    - Cache
    - Number of servers

## Rate limiter

// key can be IP address or userId

```shell
throttled(key) {

interval = 60 throttledLimit = 10 currentTime = $(data+%s)

redis-cli ZREMRANGEBYSCORE key 0 $((currentTime - interval))

redis-cli ZADD key currentTime currentTime

count = redis-cli ZCARD key

if(count > throttledLimit)
    return false 
  else 
    return true
}

```

## Unique ID generator

UUID - 128 bits and alpha numeric and not sortable

DB ID generator -> Not ideal

Timestramp is 64 bits , we can reduce it to 41 bits Datacenter 5 bits Node 5 bits Sequence number 12 bits -> reset every
millisec

Timestramp + DatacenterID + Node + Seq number

Talk on clock sync issues and NTP server

SCalability and HA Monitoring and auto scaling

## URL shortner

Make storage capacity. Which will help to idenfy the size of hash value for tiny url

hash fuction will have 0-9, a-z, A-Z that is 64 possible caracter. So total combination possible is 64^N

To calculate length (N) of hash. if we take 7 then it will be close to 3.5 trillion combination.

For hashing use cyclic redundancy check (CRC 32 hash function), it is short hasing and will generate 8 caracter long.

We want 7 , so reduce one and , if it collide then add a random caracter and again perform CRC32.