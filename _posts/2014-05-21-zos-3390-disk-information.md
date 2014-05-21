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

IBM raised the bar on volume sizes with z/OS V1R10. Extended Address Volumes (EAV) can hold between 65521 and 262668 cylinders. The cylinder addressing now becomes CCCCcccH where CCCCH is the old addressing system with an extra three ccc cylinder addresses added. Datasets are defined as EAS-eligible, which means that they can be allocated into the cylinder space above the 65521 mark. This is defined as 'cylinder managed space' while the area up to the 65521 mark is 'track managed space'. By my calculations, that means an EAV volume can hold just over 223 GB. 

Cylinder managed EAV-elligible datasets need extra VTOC information, so two new DSCBs are also introduced, the F8 DSCB and the F9 DSCB. The F8 DSCB is the EAV equivalent of an F1 DSCB, while the F9DSCB can contain pointers to F3 DSCBs

While a 3390-3 disk can store 2.84 GB, you will not get that amount of data on it. Each disk has to contain a Volume Table of Contents (VTOC), a VTOC index, and a VSAM VOLUME DATASET (VVDS). Typically, these require 270 tracks, 14 tracks and 30 tracks respectively. The disk also has a single track self describing label. That lot uses up 315 tracks, or about 18 MB. Then, data is stored on each track in blocks, with inter block gaps in between. Best case for efficient space use is generally half track blocking, which is 27,998 bytes per block, or 55,996 usable bytes per track. The sum is (50,085-315)*55,996 = 2.787 GB. So that means you can store about 2.79 GB of user data on a 3390-3 disk.