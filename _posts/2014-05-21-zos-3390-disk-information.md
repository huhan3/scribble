---
layout: post
title: Z/OS 3390 disk information
date: 2014-05-21 16:27:31
disqus: y
---

Every 3390 disk volume contains 56,664 bytes per track, 15 tracks per cylinder and 849,960 Bytes per Cylinder. The terms 'track' and 'cylinder' come from old pre-raid disks, which were like 8 old fashioned vinyl records stacked in a pile, with a set of fixed read/write heads which moved in and out of them. The disks had recording surfaces on both sides. Of the 16 surfaces, one surface was used for control information which left 15 for data. A 'track' was the amount of data which could be read from a single surface in one revolution, without moving the heads. Think of it as the needle on the vinyl record doing one revolution. A 'cylinder' could be read from all 15 surfaces without moving the heads. This was quite important, as a cylinder could be read quite quickly, without any mechanical movement. The diagram might help explain.

![Z/OS 3390 disk information](/images/2014/05/3390.gif "Z/OS 3390 disk information")



Modern disks use FBA storage, but they have to emulate 3390 CKD format, so the terms 'track' and 'cylinder' are still used.

**3390 disk numbers**
| Model | Model 1 | Model 2 | Model 1 | Model 9 | Model 27 | Model 54 |
| ----- | ------- | ------- | ------- | ------- | -------- | -------- |
| Tracks per Volume | 16,695 | 33,390 | 50,085 | 150,255 | 491,400 | 982,800 |
| Cylinders per Volume | 1,113 | 2,226 | 3,339 | 10,017 | 32,760 | 65,520 |
| Bytes per Volume | 946 MB | 1.89 GB | 2.84 GB | 8.51 GB | 27.84 GB | 55.68 GB |

