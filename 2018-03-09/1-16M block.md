### Process:

1. supervisorctl stop all
2. ./gbtrobot.sh   // to log CreateNewBlock()
3. ./rpc        // to create a large number of transactions
    
    - one to one transaction. 
    - 84589 transactions
    - bitcoin-cli getblocktemplate > debug.log    // the file size up to 52M
    
4. record btcpool log
    
    - tail -F run_gbtmaker/log*/*INFO
    - tail -F run_jobmaker/log*/*INFO
    - tail -F run_sserver/log*/*INFO
    - tail -F run_blkmaker/log*/*INFO
    
    
### Ret:

1. gbtmaker error: Message size too large
    
    ```
    I0409 11:51:28.415407 27501 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 173223413, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 6e5a4563aa7b78c4ae968d38611e09a62d8d2c6bee41113d16ea21a32123454f
    I0409 11:51:29.335260 27501 GbtMaker.cc:207] sumbit to Kafka, msg len: 64431800
    E0409 11:51:29.335496 27501 Kafka.cc:463] produce to topic [ RawGbt]: Broker: Message size too large
    ```
    
2. gtbmaker error: rpc timeout
    
    ```
    E0409 12:07:49.074784 28524 GbtMaker.cc:138] bitcoind rpc failure
    E0409 12:07:49.074797 28524 GbtMaker.cc:201] get rawgbt failure
    E0409 12:07:55.075958 28524 Utils.cc:221] unable to request data from: http://127.0.0.1:18332, error: Timeout was reached
    ```

### Resolution:

1. modify size limit of kafka:
    
    ```
    // Maximum transmit message size.
    // The RawGbt message may large than 30MB while the block size reach 8MB.
    // So allow the message to reach 60MB.
    #define RDKAFKA_MESSAGE_MAX_BYTES            "120000000"
    
    // Maximum number of bytes per topic+partition to request when
    // fetching messages from the broker
    #define RDKAFKA_FETCH_MESSAGE_MAX_BYTES      "120000000"
    
    // Maximum number of kilobytes per topic+partition in the local consumer
    // queue. This value may be overshot by fetch.message.max.bytes.
    #define RDKAFKA_QUEUED_MAX_MESSAGES_KBYTES   "120000000"
    ```
    
2. timeout:
    
    ```
      curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, CurlWriteChunkCallback);
      curl_easy_setopt(curl, CURLOPT_WRITEDATA,     (void *)&chunk);
      curl_easy_setopt(curl, CURLOPT_TIMEOUT, 50L);     // modify here
    
      status = curl_easy_perform(curl);
    ```
    
    ****
    
### New problem:

btcpool state:
    
- gbtmaker:
       
       ```
       I0409 13:58:51.270269 29313 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 176983107, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 18faf5b0cd5a44e1add4a15398d3747be8831a5daf838464f87e2c146ce60947
       I0409 13:58:52.711846 29313 GbtMaker.cc:207] sumbit to Kafka, msg len: 64419236
       I0409 13:59:10.960667 29313 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 176983107, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 7326f03a49b0d04c0c4d79270d40d461ca789dbc5c633a71686ea56adf85cfd2
       I0409 13:59:12.410404 29313 GbtMaker.cc:207] sumbit to Kafka, msg len: 64419236
       I0409 13:59:31.132867 29313 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 176983107, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 749fe43b470c8f95fc04b6b4409041bd93678d1b1157abf5b2db9e54a3af8c28
       I0409 13:59:32.574651 29313 GbtMaker.cc:207] sumbit to Kafka, msg len: 64419236
       I0409 13:59:50.645429 29313 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 176983107, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: e4c8159add765c72ce0c2d68d55e0e1c6c3e4f5d0d69efe0bfd389ca0f554e54
       I0409 13:59:52.104110 29313 GbtMaker.cc:207] sumbit to Kafka, msg len: 64419236
       I0409 14:00:09.898309 29313 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 176983107, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 8ddf9610c8b4446839930f3ed9d58c00fb54889ede5011fe0dcaf6fc04e5181a
       I0409 14:00:11.424995 29313 GbtMaker.cc:207] sumbit to Kafka, msg len: 64419236
       ```
       
- jobmaker:
   
        ```
        I0409 14:00:56.984681 29319 JobMaker.cc:265] received rawgbt message, len: 64419324
        W0409 14:00:57.040799 29319 JobMaker.cc:461] rawgbt diff time is too large: 6 seconds
        I0409 14:00:57.781939 29319 JobMaker.cc:513] add rawgbt, height: 1225046, gbthash: 7db3b9330d2b7895..., gbtTime(UTC): 2018-04-09 06:00:51, isEmpty:0
        I0409 14:00:57.832473 29319 JobMaker.cc:537] remove timeout rawgbt: 2018-04-09 05:59:11|1523253551, height:1225046, isEmptyBlock:0
        I0409 14:01:17.757051 29319 JobMaker.cc:265] received rawgbt message, len: 64419324
        W0409 14:01:17.800514 29319 JobMaker.cc:461] rawgbt diff time is too large: 5 seconds
        I0409 14:01:18.605478 29319 JobMaker.cc:513] add rawgbt, height: 1225046, gbthash: bf2c88873d41b4e7..., gbtTime(UTC): 2018-04-09 06:01:12, isEmpty:0
        I0409 14:01:18.654563 29319 JobMaker.cc:537] remove timeout rawgbt: 2018-04-09 05:59:31|1523253571, height:1225046, isEmptyBlock:0
        I0409 14:01:20.135072 29319 JobMaker.cc:582] --------producer stratum job, jobId: 6542324738024048775, height: 1225046--------
        I0409 14:01:20.135118 29319 JobMaker.cc:584] sjob: {"jobId":6542324738024048775,"gbtHash":"bf2c88873d41b4e718bdbd445488ef1fa80179bd21d8b073f18f447ed1266448","prevHash":"00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25","prevHashBeStr":"b1e22b25e17567dbdd0d8b88979ae519206f25c9b6dc4816000003c700000000","height":1225046,"coinbase1":"02000000010000000000000000000000000000000000000000000000000000000000000000ffffffff230356b11204b001cb5a636f70657220425443506f6f6c2f","coinbase2":"ffffffff01039f8c0a000000001976a91448b20e254c0677e760bab964aec16818d6b7134a88ac00000000","merkleBranch":"e2f2bc81d261ad107da1372bf2f3949c533bc9f2b121018f612cb8ecc3a9dd84a07e689354393ab1b33a10692967e71ca2ba78ce0740b7286e1a063e00c22ed895cd41e6bb1224413bd20f59e8da5df7f952bbb41e552e9dfcf3158f0330d85b69bc153c107f6058ee4543ee5760a24ca70e7c1e7dcc5d9f176e54392aa02983a791923df39d7ac2eb1508d6b26160be8b5b09fa7ba4448f216f6cf533926ab632d13cc83ee3e3ca1c031b7badf387c2ca9d97529ca49f904f06507c70c19bec1a0d54d62a21164f95af18dc97a1b6181be36ce412e11f65e2fcd90a153628d9eb099e13bfea40e31e7ad0c0f6c78400d87b61f446cc81163609e6692dc72db6a2e701cee6d771ee11a7dbf39c604f371a03944dd1ed54b659f2d0379d9e9b01017449734520bdee08bad5bc9bd2e2a819476286c3968a059130e214526568690802b0c4f9fe25bda58f642a67c4f5c4e70034f68f35eae1be521ed00caed34d2dc1fc1e5c59c359df7b4c240d1a9c2c9c87231c8c9d11e18dc5d4fb584e2241725183df50bf1c19a12ade8d579a2202b3214a4601291c4a0e6029f515dce78daffc66749d1c9835008dbfe0afad7e44dfddfe71547b632e5b3a5ebdf713cf5eff6f46fe9fa328338ee6a3d74b06dd16f809e7a06bcfefeaa844bb41149a157c5c5de95a95ea20e4e98681980a856970bcf1d29c2b66a93cf07072c243e3ff262bfa8d3b72aacceecad26de84d949bb8bbeef291d55aa07da3dd8e6f86314b8c","nVersion":536870914,"nBits":486604799,"nTime":1523253664,"minTime":1523241804,"coinbaseValue":176987907,"witnessCommitment":"","nmcBlockHash":"0000000000000000000000000000000000000000000000000000000000000000","nmcBits":0,"nmcHeight":32766,"nmcRpcAddr":"","nmcRpcUserpass":"","rskBlockHashForMergedMining":"","rskNetworkTarget":"0x0000000000000000000000000000000000000000000000000000000000000000","rskFeesForMiner":"","rskdRpcAddress":"","rskdRpcUserPwd":"","isRskCleanJob":false}
        I0409 14:01:37.824765 29319 JobMaker.cc:265] received rawgbt message, len: 64419324
        W0409 14:01:37.881777 29319 JobMaker.cc:461] rawgbt diff time is too large: 5 seconds
        I0409 14:01:38.577965 29319 JobMaker.cc:513] add rawgbt, height: 1225046, gbthash: 1be7e1beeacd64b8..., gbtTime(UTC): 2018-04-09 06:01:32, isEmpty:0
        I0409 14:01:38.627668 29319 JobMaker.cc:537] remove timeout rawgbt: 2018-04-09 05:59:51|1523253591, height:1225046, isEmptyBlock:0
        ```
        
- sserver:
    
        ```
        W0409 14:02:31.935228 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:31.978664 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:31.981338 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.078135 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.216951 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.247613 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.317757 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.364653 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.369948 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.441532 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.452116 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.595973 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.629194 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.633242 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.841600 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.852288 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.890527 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        W0409 14:02:32.937805 29320 StratumSession.cc:832] invalid share spamming, diff: 64, uid: 1, uname: "user1", agent: "bmminer/2.0.0", ip: 58.18.54.83
        ```
        
- blkmaker:
    
        ```
        I0409 14:00:39.480551 29668 BlockMaker.cc:707] received StratumJob message, len: 2116
        I0409 14:00:39.480864 29668 BlockMaker.cc:730] StratumJob, jobId: 6542324561168267494, gbtHash: 91bf78e65afea089c3ebb5be63db5bc65f468e2793a3d79d3855116570d8103d
        I0409 14:00:40.315827 29316 BlockMaker.cc:509] received SolvedShare message, len: 264
        E0409 14:00:40.315940 29316 BlockMaker.cc:543] find gbthash in jobId2GbtHash_ =========== hash : : 91bf78e65afea089c3ebb5be63db5bc65f468e2793a3d79d3855116570d8103d
        E0409 14:00:40.315986 29316 BlockMaker.cc:549] can't find this gbthash in rawGbtMap_: 91bf78e65afea089c3ebb5be63db5bc65f468e2793a3d79d3855116570d8103d
        I0409 14:01:20.182648 29668 BlockMaker.cc:707] received StratumJob message, len: 2116
        I0409 14:01:20.182790 29668 BlockMaker.cc:730] StratumJob, jobId: 6542324738024048775, gbtHash: bf2c88873d41b4e718bdbd445488ef1fa80179bd21d8b073f18f447ed1266448
        I0409 14:01:21.033293 29316 BlockMaker.cc:509] received SolvedShare message, len: 264
        E0409 14:01:21.033453 29316 BlockMaker.cc:543] find gbthash in jobId2GbtHash_ =========== hash : : bf2c88873d41b4e718bdbd445488ef1fa80179bd21d8b073f18f447ed1266448
        E0409 14:01:21.033520 29316 BlockMaker.cc:549] can't find this gbthash in rawGbtMap_: bf2c88873d41b4e718bdbd445488ef1fa80179bd21d8b073f18f447ed1266448
        I0409 14:01:59.698402 29668 BlockMaker.cc:707] received StratumJob message, len: 2116
        I0409 14:01:59.698561 29668 BlockMaker.cc:730] StratumJob, jobId: 6542324903385293456, gbtHash: 3f78e29005cc6f33d2294e4d2bdec1938da69c472dbf0e5e09494886a18b9b1e
        I0409 14:02:00.145572 29316 BlockMaker.cc:509] received SolvedShare message, len: 264
        E0409 14:02:00.145649 29316 BlockMaker.cc:543] find gbthash in jobId2GbtHash_ =========== hash : : 3f78e29005cc6f33d2294e4d2bdec1938da69c472dbf0e5e09494886a18b9b1e
        E0409 14:02:00.145699 29316 BlockMaker.cc:549] can't find this gbthash in rawGbtMap_: 3f78e29005cc6f33d2294e4d2bdec1938da69c472dbf0e5e09494886a18b9b1e
        I0409 14:02:38.407227 29668 BlockMaker.cc:707] received StratumJob message, len: 2116
        I0409 14:02:38.407373 29668 BlockMaker.cc:730] StratumJob, jobId: 6542325073704400010, gbtHash: e748348a32147363de27361c876ee0e2e6921836d8d4448f0269dcd10b934e4c
        I0409 14:02:38.957365 29316 BlockMaker.cc:509] received SolvedShare message, len: 264
        E0409 14:02:38.957514 29316 BlockMaker.cc:543] find gbthash in jobId2GbtHash_ =========== hash : : e748348a32147363de27361c876ee0e2e6921836d8d4448f0269dcd10b934e4c
        E0409 14:02:38.957595 29316 BlockMaker.cc:549] can't find this gbthash in rawGbtMap_: e748348a32147363de27361c876ee0e2e6921836d8d4448f0269dcd10b934e4c
        ```
        
- tcpdump -A -l -i eth1 port 3333 | grep '{"'
    
        ```
        	.....k.{"params": ["user1.test", "9", "5889000000000000", "5acb0216", "69bf0c27"], "id": 188959, "method": "mining.submit"}
        ..k.	...{"id":188958,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....k.{"params": ["user1.test", "9", "7b89000000000000", "5acb0216", "687f50d0"], "id": 188960, "method": "mining.submit"}
        ......k.{"params": ["user1.test", "9", "f37a000000000000", "5acb0216", "2e441ec2"], "id": 180743, "method": "mining.submit"}
        ..l.....{"id":180743,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....k.{"params": ["user1.test", "9", "5f8a000000000000", "5acb0216", "574b085b"], "id": 188961, "method": "mining.submit"}
        ..l		...{"id":188959,"result":null,"error":[21,"Job not found (=stale)",null]}
        {"id":188960,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l.{"params": ["user1.test", "9", "917b000000000000", "5acb0216", "4e5aec11"], "id": 180744, "method": "mining.submit"}
        ..l	....{"id":180744,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....k.{"params": ["user1.test", "9", "6e8a000000000000", "5acb0216", "f7a69a09"], "id": 188962, "method": "mining.submit"}
        ..l.	...{"id":188961,"result":null,"error":[21,"Job not found (=stale)",null]}
        {"id":188962,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....l.{"params": ["user1.test", "9", "ba8b000000000000", "5acb0216", "52bd1c55"], "id": 188963, "method": "mining.submit"}
        	.....l.{"params": ["user1.test", "9", "df8b000000000000", "5acb0216", "5e247633"], "id": 188964, "method": "mining.submit"}
        ..l 	...{"id":188963,"result":null,"error":[21,"Job not found (=stale)",null]}
        {"id":188964,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l	{"params": ["user1.test", "9", "957d000000000000", "5acb0216", "a7aa0b22"], "id": 180745, "method": "mining.submit"}
        ..l%....{"id":180745,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....l.{"params": ["user1.test", "9", "668c000000000000", "5acb0216", "b14d0540"], "id": 188965, "method": "mining.submit"}
        ......l	{"params": ["user1.test", "9", "a07d000000000000", "5acb0216", "efa06eba"], "id": 180746, "method": "mining.submit"}
        ......l	{"params": ["user1.test", "9", "f07d000000000000", "5acb0216", "a24d74de"], "id": 180747, "method": "mining.submit"}
        ......l	{"params": ["user1.test", "9", "0b7e000000000000", "5acb0216", "34961349"], "id": 180748, "method": "mining.submit"}
        	.....l {"params": ["user1.test", "9", "e28c000000000000", "5acb0216", "efb70cdf"], "id": 188966, "method": "mining.submit"}
        ..l-	...{"id":188965,"result":null,"error":[21,"Job not found (=stale)",null]}
        ..l-....{"id":180746,"result":null,"error":[21,"Job not found (=stale)",null]}
        {"id":180747,"result":null,"error":[21,"Job not found (=stale)",null]}
        {"id":180748,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l%{"params": ["user1.test", "9", "4b7e000000000000", "5acb0216", "d3a80511"], "id": 180749, "method": "mining.submit"}
        ..l>....{"id":180749,"result":null,"error":[21,"Job not found (=stale)",null]}
        ..l@	...{"id":188966,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....l-{"params": ["user1.test", "9", "5a8e000000000000", "5acb0216", "43c469d3"], "id": 188967, "method": "mining.submit"}
        ..lH	...{"id":188967,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l>{"params": ["user1.test", "9", "9181000000000000", "5acb0216", "43a595d0"], "id": 180750, "method": "mining.submit"}
        ..l]....{"id":180750,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l>{"params": ["user1.test", "9", "ca81000000000000", "5acb0216", "1bb34968"], "id": 180751, "method": "mining.submit"}
        	.....lH{"params": ["user1.test", "9", "a890000000000000", "5acb0216", "0dab4063"], "id": 188968, "method": "mining.submit"}
        ..ld	...{"id":188968,"result":null,"error":[21,"Job not found (=stale)",null]}
        ..le....{"id":180751,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....lH{"params": ["user1.test", "9", "d690000000000000", "5acb0216", "78ddff6a"], "id": 188969, "method": "mining.submit"}
        	.....lH{"params": ["user1.test", "9", "e690000000000000", "5acb0216", "5b5c2336"], "id": 188970, "method": "mining.submit"}
        ..lk	...{"id":188969,"result":null,"error":[21,"Job not found (=stale)",null]}
        {"id":188970,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l]{"params": ["user1.test", "9", "9e82000000000000", "5acb0216", "d7727d82"], "id": 180752, "method": "mining.submit"}
        ..lu....{"id":180752,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......le{"params": ["user1.test", "9", "5783000000000000", "5acb0216", "6bcb3889"], "id": 180753, "method": "mining.submit"}
        	.....lk{"params": ["user1.test", "9", "5c92000000000000", "5acb0216", "34447421"], "id": 188971, "method": "mining.submit"}
        ..l|	...{"id":188971,"result":null,"error":[21,"Job not found (=stale)",null]}
        ..l}....{"id":180753,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l}{"params": ["user1.test", "9", "c284000000000000", "5acb0216", "ca38779a"], "id": 180754, "method": "mining.submit"}
        ..l.....{"id":180754,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....l|{"params": ["user1.test", "9", "b693000000000000", "5acb0216", "cc406352"], "id": 188972, "method": "mining.submit"}
        ..l.	...{"id":188972,"result":null,"error":[21,"Job not found (=stale)",null]}
        ......l.{"params": ["user1.test", "9", "9b85000000000000", "5acb0216", "a044be2c"], "id": 180755, "method": "mining.submit"}
        ..l.....{"id":180755,"result":null,"error":[21,"Job not found (=stale)",null]}
        	.....l.{"params": ["user1.test", "9", "6994000000000000", "5acb0216", "864e5f86"], "id": 188973, "method": "mining.submit"}
        ..l.	...{"id":188973,"result":null,"error":[21,"Job not found (=stale)",null]}
        ```
        
### After reboot kits: supervisorctl restart all

- gbtmaker: OK
    
   ```
   I0409 14:24:23.240355  1159 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 178578927, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: a3077b2985c1d460b9d6c1ccb9dd3aaf96885b7a59ed1dc09a45e61630a890e7
   I0409 14:24:24.677170  1159 GbtMaker.cc:207] sumbit to Kafka, msg len: 64414044
   I0409 14:24:43.089013  1159 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 178578927, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 3e9ca469014cec25c3045355e20488115a7fdf68fc572cafe02316bac25ef84a
   I0409 14:24:44.471902  1159 GbtMaker.cc:207] sumbit to Kafka, msg len: 64414044
   I0409 14:25:02.139137  1159 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 178578927, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 59d39f6219901f7c4095a1b8b16858172aea525168cd95be55149d27069c0c5a
   I0409 14:25:03.268582  1159 GbtMaker.cc:207] sumbit to Kafka, msg len: 64414044
   I0409 14:25:21.340996  1159 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 178578927, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: 88e844e015d0f044f02f438e6cf79c987ebef28f46882583ac8d75955b14c625
   I0409 14:25:22.723995  1159 GbtMaker.cc:207] sumbit to Kafka, msg len: 64414044
   I0409 14:25:34.525188  1159 GbtMaker.cc:126] stop gbtmaker
   I0409 14:25:34.526620  1524 GbtMaker.cc:257] stop thread listen to bitcoind
   I0409 14:25:39.666764  1159 GbtMaker.cc:171] gbt height: 1225046, prev_hash: 00000000000003c7b6dc4816206f25c9979ae519dd0d8b88e17567dbb1e22b25, coinbase_value: 178578927, bits: 1d00ffff, mintime: 1523241804, version: 536870912|0x20000000, gbthash: faccc24760781fb3ad8696981738b061f8095c9dcd85a26f0c51249caa23c9ea
   I0409 14:25:40.541230  1159 GbtMaker.cc:207] sumbit to Kafka, msg len: 64414044
   ```
        
- jobmaker: seem OK
    
   ```
   tail: 'run_jobmaker/log_jobmaker/jobmaker.INFO' has been replaced;  following new file
   Log file created at: 2018/04/09 14:23:14
   Running on machine: iZm5eakzlq9sm4ycm9lw0wZ
   Log line format: [IWEF]mmdd hh:mm:ss.uuuuuu threadid file:line] msg
   I0409 14:23:14.925222  1165 Zookeeper.cc:63] Zookeeper: connecting to zookeeper brokers: 0s
   I0409 14:23:19.931421  1165 Zookeeper.cc:133] Zookeeper: fight for lock with 1 clients
   I0409 14:23:19.931474  1165 Zookeeper.cc:139]   * 0. node0000000013
   I0409 14:23:19.931480  1165 Zookeeper.cc:147] Zookeeper: got the lock!
   W0409 14:23:19.931506  1165 JobMakerMain.cc:140] using bitcoin testnet3
   I0409 14:23:19.931803  1165 Kafka.cc:347] producer librdkafka version: 0.9.1
   I0409 14:23:19.931823  1165 Kafka.cc:82] consumer librdkafka version: 0.9.1
   I0409 14:23:19.931835  1165 Kafka.cc:82] consumer librdkafka version: 0.9.1
   I0409 14:23:19.931849  1165 Kafka.cc:82] consumer librdkafka version: 0.9.1
   I0409 14:23:19.931857  1165 JobMaker.cc:64] Block Version: 20000002
   I0409 14:23:19.931864  1165 JobMaker.cc:65] Coinbase Info: coper BTCPool/
   I0409 14:23:19.931869  1165 JobMaker.cc:66] Payout Address: qpytyr39fsr80emqh2ukftkpdqvdddcnfg9s6wjtfa
   I0409 14:23:19.932097  1165 Kafka.cc:417] add brokers: 127.0.0.1:9092
   I0409 14:23:19.932160  1165 Kafka.cc:424] create topic handle: StratumJob
   I0409 14:23:20.009683  1165 Kafka.cc:160] add brokers: 127.0.0.1:9092
   I0409 14:23:20.010273  1165 Kafka.cc:167] create topic handle: RawGbt
   I0409 14:23:23.041097  1165 Kafka.cc:160] add brokers: 127.0.0.1:9092
   I0409 14:23:23.041234  1165 Kafka.cc:167] create topic handle: NMCAuxBlock
   I0409 14:23:24.077891  1165 Kafka.cc:160] add brokers: 127.0.0.1:9092
   I0409 14:23:24.077994  1165 Kafka.cc:167] create topic handle: RawGw
   I0409 14:23:25.148891  1165 JobMaker.cc:190] consume latest rawgbt message from kafka...
   I0409 14:23:30.149081  1165 JobMaker.cc:200] consume latest rawgbt messages done
   ```
        
- sserver: not normal
    
   ```
   I0409 14:23:17.388427  1166 Kafka.cc:417] add brokers: 127.0.0.1:9092
   I0409 14:23:17.388509  1166 Kafka.cc:424] create topic handle: SolvedShare
   I0409 14:23:17.429057  1166 Kafka.cc:417] add brokers: 127.0.0.1:9092
   I0409 14:23:17.429219  1166 Kafka.cc:424] create topic handle: NMCSolvedShare
   I0409 14:23:17.464152  1166 Kafka.cc:417] add brokers: 127.0.0.1:9092
   I0409 14:23:17.464257  1166 Kafka.cc:424] create topic handle: RskSolvedShare
   I0409 14:23:17.500613  1166 Kafka.cc:417] add brokers: 127.0.0.1:9092
   I0409 14:23:17.500710  1166 Kafka.cc:424] create topic handle: CommonEvents
   E0409 14:23:18.568569  1486 StratumServer.cc:221] too large delay from kafka to receive topic 'StratumJob'
   I0409 14:23:18.927901  1166 StratumSession.cc:246] client connect, ip: 58.18.54.83
   I0409 14:23:18.936062  1166 StratumSession.cc:246] client connect, ip: 58.18.54.83
   W0409 14:23:18.956881  1166 StratumServer.cc:128] getLatestStratumJobEx fail
   W0409 14:23:18.967542  1166 StratumServer.cc:128] getLatestStratumJobEx fail
   I0409 14:24:48.993518  1166 StratumServer.cc:1136] socket closed
   I0409 14:24:49.025072  1166 StratumSession.cc:246] client connect, ip: 58.18.54.83
   W0409 14:24:49.056607  1166 StratumServer.cc:128] getLatestStratumJobEx fail
   I0409 14:24:49.079727  1166 StratumServer.cc:1136] socket closed
   I0409 14:24:49.106376  1166 StratumSession.cc:246] client connect, ip: 58.18.54.83
   W0409 14:24:49.134138  1166 StratumServer.cc:128] getLatestStratumJobEx fail
   ```
        
- blkmaker: 
    
   ```
   I0409 14:23:19.003361  1518 BlockMaker.cc:730] StratumJob, jobId: 6542324561168267494, gbtHash: 91bf78e65afea089c3ebb5be63db5bc65f468e2793a3d79d3855116570d8103d
   I0409 14:23:19.003371  1518 BlockMaker.cc:707] received StratumJob message, len: 2116
   I0409 14:23:19.003402  1518 BlockMaker.cc:730] StratumJob, jobId: 6542324738024048775, gbtHash: bf2c88873d41b4e718bdbd445488ef1fa80179bd21d8b073f18f447ed1266448
   I0409 14:23:19.003412  1518 BlockMaker.cc:707] received StratumJob message, len: 2116
   I0409 14:23:19.003443  1518 BlockMaker.cc:730] StratumJob, jobId: 6542324903385293456, gbtHash: 3f78e29005cc6f33d2294e4d2bdec1938da69c472dbf0e5e09494886a18b9b1e
   I0409 14:23:19.003451  1518 BlockMaker.cc:707] received StratumJob message, len: 2116
   I0409 14:23:19.003482  1518 BlockMaker.cc:730] StratumJob, jobId: 6542325073704400010, gbtHash: e748348a32147363de27361c876ee0e2e6921836d8d4448f0269dcd10b934e4c
   I0409 14:23:19.003491  1518 BlockMaker.cc:707] received StratumJob message, len: 2116
   I0409 14:23:19.003620  1518 BlockMaker.cc:730] StratumJob, jobId: 6542325243437030454, gbtHash: 6c229c36c5fcf82d3f5a3c929eb9fb67e3a272b0bed81486843e07659a742730
   I0409 14:23:19.003648  1518 BlockMaker.cc:707] received StratumJob message, len: 2116
   I0409 14:23:19.003684  1518 BlockMaker.cc:730] StratumJob, jobId: 6542325406216356714, gbtHash: 528a036af54252658baf2bdac7acfb6b383f93a38c96f21e319188a3e1ff33b7
   I0409 14:23:19.003692  1518 BlockMaker.cc:707] received StratumJob message, len: 2116
   I0409 14:23:19.003728  1518 BlockMaker.cc:730] StratumJob, jobId: 6542325584365254060, gbtHash: cd0a75ac4d6ce78da082aa3e4ba4d125b73fe870bc933c924cfc58bd37f5d780
   I0409 14:23:20.512473  1162 BlockMaker.cc:509] received SolvedShare message, len: 264
   E0409 14:23:20.512619  1162 BlockMaker.cc:543] find gbthash in jobId2GbtHash_ =========== hash : : 6c229c36c5fcf82d3f5a3c929eb9fb67e3a272b0bed81486843e07659a742730
   E0409 14:23:20.512960  1162 BlockMaker.cc:549] can't find this gbthash in rawGbtMap_: 6c229c36c5fcf82d3f5a3c929eb9fb67e3a272b0bed81486843e07659a742730
   I0409 14:23:20.512979  1162 BlockMaker.cc:509] received SolvedShare message, len: 264
   E0409 14:23:20.512991  1162 BlockMaker.cc:543] find gbthash in jobId2GbtHash_ =========== hash : : 528a036af54252658baf2bdac7acfb6b383f93a38c96f21e319188a3e1ff33b7
   E0409 14:23:20.513005  1162 BlockMaker.cc:549] can't find this gbthash in rawGbtMap_: 528a036af54252658baf2bdac7acfb6b383f93a38c96f21e319188a3e1ff33b7
   ```
        
- tcpdump -A -l -i eth1 port 3333 | grep '{"'
    
   ```
   .9Bl....{"id": 183504, "method": "mining.subscribe", "params": ["bmminer/2.0.0", "0100006c"]}
.....9Bl{"id":183504,"result":[[["mining.set_difficulty","0100006e"],["mining.notify","0100006e"]],"0100006e",8],"error":null}
.9Bo....{"id": 183505, "method": "mining.authorize", "params": ["user1.test", "123"]}
.....9Bo{"id":183505,"result":true,"error":null}
.9Br....{"id": 183506, "method": "mining.multi_version", "params": [1]}
	......2{"id": 191772, "method": "mining.subscribe", "params": ["bmminer/2.0.0", "0100006d"]}
...9	...{"id":191772,"result":[[["mining.set_difficulty","0100006f"],["mining.notify","0100006f"]],"0100006f",8],"error":null}
	......9{"id": 191773, "method": "mining.authorize", "params": ["user1.test", "123"]}
...B	...{"id":191773,"result":true,"error":null}
	......B{"id": 191774, "method": "mining.multi_version", "params": [1]}
.9e...r.{"id": 183527, "method": "mining.subscribe", "params": ["bmminer/2.0.0", "0100006e"]}
..r..9e.{"id":183527,"result":[[["mining.set_difficulty","01000070"],["mining.notify","01000070"]],"01000070",8],"error":null}
.9e...r.{"id": 183528, "method": "mining.authorize", "params": ["user1.test", "123"]}
..r..9e.{"id":183528,"result":true,"error":null}
.9e...r.{"id": 183529, "method": "mining.multi_version", "params": [1]}
	..<..v<{"id": 191789, "method": "mining.subscribe", "params": ["bmminer/2.0.0", "0100006f"]}
..vD	..<{"id":191789,"result":[[["mining.set_difficulty","01000071"],["mining.notify","01000071"]],"01000071",8],"error":null}
	..?..vD{"id": 191790, "method": "mining.authorize", "params": ["user1.test", "123"]}
..vL	..?{"id":191790,"result":true,"error":null}
	..B..vL{"id": 191791, "method": "mining.multi_version", "params": [1]}
.9......{"id": 183544, "method": "mining.subscribe", "params": ["bmminer/2.0.0", "01000070"]}
.....9..{"id":183544,"result":[[["mining.set_difficulty","01000072"],["mining.notify","01000072"]],"01000072",8],"error":null}
.9......{"id": 183545, "method": "mining.authorize", "params": ["user1.test", "123"]}
.....9..{"id":183545,"result":true,"error":null}
.9......{"id": 183546, "method": "mining.multi_version", "params": [1]}
	..v...M{"id": 191816, "method": "mining.subscribe", "params": ["bmminer/2.0.0", "01000071"]}
...U	..v{"id":191816,"result":[[["mining.set_difficulty","01000073"],["mining.notify","01000073"]],"01000073",8],"error":null}
	..y...U{"id": 191817, "method": "mining.authorize", "params": ["user1.test", "123"]}
...]	..y{"id":191817,"result":true,"error":null}
	..|...]{"id": 191818, "method": "mining.multi_version", "params": [1]}
   ```

