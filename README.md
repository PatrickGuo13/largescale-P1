Author: Baichuan Guo 30841143
##1.
```
#k=2:
==================================
found. count:1
(1099978331this_is_a_bitcoin_block_of_30841143,00fec1f84b5564825f9fbfbeea1a4dab532b39de2beb7b5aa439a4c00b22f09f)
Time elapsed:16s
number of trials:100
==================================
#k=3:
==================================
found. count:1
(1587538783this_is_a_bitcoin_block_of_30841143,0008c3ee2f014ff10980a1d1ec6d2d7ed54edd0e94516fa42879afbc7b78b1dc)
Time elapsed:11s
number of trials:1000
==================================
#k=4:
==================================
found. count:2
(319242148this_is_a_bitcoin_block_of_30841143,00002e4f2ceeedc230dda372d357fdf886b9946b0443cf4082ee085207d70fbb)
Time elapsed:11s
number of trials:100000
==================================
==================================
found. count:13
(381421201this_is_a_bitcoin_block_of_30841143,0000b08bb308ec8eef12637a3ded3203ebee90f816075891cf9a409f35d3396c)
Time elapsed:14s
number of trials:1000000
==================================
==================================
found. count:1540
(1059729294this_is_a_bitcoin_block_of_30841143,0000c12df4412a520c59ae39202f1c0268100b70e7d5e2f4408daa1f34ac8069)
Time elapsed:368s
number of trials:100000000
==================================
#k=5:
==================================
found. count:2
(622219071this_is_a_bitcoin_block_of_30841143,000003882fa424d026b47aba8740569ecc59603d710b6270b074bfb31db987c7)
Time elapsed:14s
number of trials:1000000
==================================
==================================
found. count:11
(102852044this_is_a_bitcoin_block_of_30841143,00000515d77677590187a02308e557638443b6ae2ecace7ef6b840e3ddb3f2c9)
Time elapsed:47s
number of trials:10000000
==================================
#k=6:
==================================
found. count:1
(408183909this_is_a_bitcoin_block_of_30841143,000000089b8a80cadd82fc5070e79c1ce9300f1b0c1f229d20618c06019500d9)
Time elapsed:46s
number of trials:10000000
==================================
```
##2.
```
#k=7:
==================================
found. count:2
(2049009482this_is_a_bitcoin_block_of_30841143,0000000a1a37623c07718151025a99c11e7e4b396acd3ca30d663069b703382c)
Time elapsed:3529s
number of trials:1000000000
==================================
```
2. As the difficulty level increases, the probability of finding nonce decreases. As we can see from
the number of trials from k=2 to k=6, for every increment in k, number of trials would tend to increase by 10* in an exponential way.

Configuration for case k=7:
```
Region
  us-central1
Zone
  us-central1-a
Autoscaling
  Off
Dataproc Metastore
  None
Scheduled deletion
  The cluster will be deleted when idle exceeds 2 hours
Enhanced flexibility mode
  Off
Master node
  Standard (1 master, N workers)
Machine type
  n1-standard-4
Number of GPUs
  0
Primary disk type
  pd-standard
Primary disk size
  500GB
Local SSDs
  0
Worker nodes
  2
Machine type
  n1-standard-4
Number of GPUs
  0
Primary disk type
  pd-standard
Primary disk size
  500GB
Local SSDs
  0
Secondary worker nodes
  0
Secure Boot
  Disabled
VTPM
  Disabled
Integrity Monitoring
  Disabled
Cloud Storage staging bucket
  dataproc-staging-us-central1-872576000587-dkejac1d
Subnetwork
  default
Network tags
  None
Internal IP only
  No
Image version
  2.0.7-debian10
 ```

 ##3. 
 modify the original line 56 - 59
 ```
  val nonce = sc.range(0, trials).mapPartitionsWithIndex((indx, iter) => {
  val rand = new scala.util.Random(indx + seed)
  iter.map(x => rand.nextInt(Int.MaxValue - 1) + 1)
})
```
to the following:
```
val nonce = sc.range(0, trials).mapPartitionsWithIndex((indx, iter) => {
    val rand = 0
    def incr(): Long=value.incrementAndGet
    rand=rand.incr()
    iter.map(x => rand.nextInt(Int.MaxValue - 1) + 1)
  })
```
  This method would be less efficiency because the hash function of hexadecimal is not evenly distributed from 1 to 2^(32)
