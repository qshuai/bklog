### gbtmaker: rpc failure

when bitcoind client output following log:
	
	```
	2018-04-08 06:24:55 CreateNewBlock(): total size: 8065180 txs: 42106 fees: 8430067 sigops 42219
	```

gbtmaker error:

	```
	E0408 14:24:55.628964  1708 Utils.cc:227] server responded with code: 500
	E0408 14:24:55.629166  1708 GbtMaker.cc:138] bitcoind rpc failure
	```

gbtmaker recovered after client pack a new block

> An exception of client:gbtmaker has failed, but client was still call CreateNewBlock() function! And the log is not correct under normal condition.

```
if (pindexPrev != chainActive.Tip() || (mempool.GetTransactionsUpdated() != nTransactionsUpdatedLast && GetTime() - nStart > 5)) {
...
pblocktemplate = BlockAssembler(config).CreateNewBlock(scriptDummy);
...
}
```

### bitcoind client log

```
2018-04-08 06:24:55 CreateNewBlock(): total size: 8065180 txs: 42106 fees: 8430067 sigops 42219
2018-04-08 06:24:57 CreateNewBlock(): total size: 8065947 txs: 42110 fees: 8430867 sigops 42223
2018-04-08 06:24:58 CreateNewBlock(): total size: 8066331 txs: 42112 fees: 8431267 sigops 42225
2018-04-08 06:25:00 CreateNewBlock(): total size: 8073801 txs: 42151 fees: 8439068 sigops 42264
2018-04-08 06:25:02 CreateNewBlock(): total size: 8074183 txs: 42153 fees: 8439468 sigops 42266
2018-04-08 06:25:03 CreateNewBlock(): total size: 8074756 txs: 42156 fees: 8440068 sigops 42269
2018-04-08 06:25:05 CreateNewBlock(): total size: 8088738 txs: 42229 fees: 8454670 sigops 42342
2018-04-08 06:25:07 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:08 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:10 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:12 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:13 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:15 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:16 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:18 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:20 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:21 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:23 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:24 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:26 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:28 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:29 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:31 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:33 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:34 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:36 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:37 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:39 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:41 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:43 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:44 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:46 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:48 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:49 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:51 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:52 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:54 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:56 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:57 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:25:59 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:01 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:02 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:04 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:06 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:07 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:09 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:11 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:12 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:14 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:16 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:17 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:19 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:21 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:23 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:24 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:26 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:27 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:29 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:31 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:32 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:34 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:35 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:37 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:39 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:40 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:42 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:44 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:45 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:47 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:48 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:50 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:52 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:54 CreateNewBlock(): total size: 8100225 txs: 42289 fees: 8466671 sigops 42402
2018-04-08 06:26:56 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:26:57 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:26:59 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:00 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:02 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:04 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:05 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:07 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:09 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:11 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:12 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:14 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:16 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:17 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:19 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:20 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:22 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:24 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:25 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:27 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:28 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:30 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:32 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:33 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:35 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:36 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:38 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:40 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:42 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:43 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:45 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:46 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:48 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:50 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:51 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:53 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:55 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:56 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:27:58 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:00 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:02 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:03 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:05 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:06 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:08 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:10 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:11 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:13 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:15 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:16 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:18 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:20 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:21 CreateNewBlock(): total size: 8100450 txs: 42290 fees: 8566671 sigops 42404
2018-04-08 06:28:23 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:25 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:26 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:28 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:29 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:31 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:33 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:35 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:36 CreateNewBlock(): total size: 8100675 txs: 42291 fees: 8666671 sigops 42406
2018-04-08 06:28:38 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:40 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:41 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:43 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:45 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:46 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:48 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:50 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:51 CreateNewBlock(): total size: 8100900 txs: 42292 fees: 8766672 sigops 42408
2018-04-08 06:28:53 CreateNewBlock(): total size: 8101270 txs: 42293 fees: 8772282 sigops 42411
2018-04-08 06:28:55 CreateNewBlock(): total size: 8101637 txs: 42294 fees: 8777892 sigops 42414
2018-04-08 06:28:57 CreateNewBlock(): total size: 8101637 txs: 42294 fees: 8777892 sigops 42414
2018-04-08 06:28:58 CreateNewBlock(): total size: 8101637 txs: 42294 fees: 8777892 sigops 42414
2018-04-08 06:29:00 CreateNewBlock(): total size: 8101637 txs: 42294 fees: 8777892 sigops 42414
2018-04-08 06:29:02 CreateNewBlock(): total size: 8101637 txs: 42294 fees: 8777892 sigops 42414
2018-04-08 06:29:03 CreateNewBlock(): total size: 8101637 txs: 42294 fees: 8777892 sigops 42414
2018-04-08 06:29:05 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:07 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:08 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:10 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:11 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:13 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:15 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:17 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:18 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:20 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:22 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:23 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:25 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:26 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:28 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:30 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:31 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:33 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:35 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:36 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:38 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:40 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
2018-04-08 06:29:41 CreateNewBlock(): total size: 8102007 txs: 42295 fees: 8783502 sigops 42417
    ```


