---
layout: post
title: Z/OS channel speeds
date: 2014-05-21 16:40:31
disqus: y
---

**FICON**

4 Gb FICON (FIbre CONnection) supports up to 16,384 device addresses under a single channel. Bandwidth is 512 MB/s. Ficon will also handle mixed block size IO much better than ESCON, which tends to favour large blocks. FICON supports multiple IOs per channel and will support 10km or 20km native. Various channel extender options exist, including the CNT Ultranet Storage Director eXtended (now part of McData) that can extend both FICON and ESCON channels to an indefinite distance.

**ESCON**

ESCON is a fibre based communications method, with several advantages over parallel. It can run at up to 17 MB/s nominal speed, and can support up to 1024 devices per channel. ESCON will only support 1 IO per channel. Native ESCON distance is limited to 3km, but it can be extended to 60km using repeaters.

**Copper Parallel**

Called Bus and Tag, or parallel channels, these were massive blue sheathed copper cables as thick as your arm. They were limited to 400 feet long. There are still a few of them out there. Their speed is a maximum of 4.5 MB/s