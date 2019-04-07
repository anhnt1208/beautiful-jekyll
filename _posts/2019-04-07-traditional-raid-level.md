---
layout: post
title: Traditional RAID Levels
tags: [AWS, Infrastructure]
--- 

RAID (Redundant Array of Independent Disks) can enable better data storage performance and higher availability,  
and there are many different types of RAID.  
Read about the different levels of RAID and where they work best.

## Traditional RAID level
<table>
  <tr>
    <th>Level</th>
    <th>Description</th>
  </tr>
  <body>
  <tr>
    <td>*RAID 0*</td>
    <td>
      Data striped across multi hard disk drives for maximum write perfomance.  
      No actual data protection
    </td>
  </tr>  
  </body>
</table>

| Level	| Description |
| ----- | ----------- | 
| *RAID 0* | Data striped across multi hard disk drives for maximum write perfomance.No actual data protection |


RAID 1 	Synchronously mirros all data from each HDD to exact duplicate HDD
	No data lost if of HDD faults or fails
	Typically highest-performance RAID level at the expense of lower usable capacity 
RAID 2	Data protected by errors correcting codes
	Parity HDD requirements proportional to the log of HDD number
	Some what inflexible and less effecient than RAID 5 or RAID 6 with lower performance and reliability 
	Not widely used 
RAID 3	Data is protected against the failure of any the HDD in a group of N+
	Similar to RAID 5, but blocks are spread across HDDs
	Parity is bitwise vs. RAID 5's block
	Parity resides on a single HDD rather than being distributed among all disks
	Random write performance is quite poor, and random performance fair at best
RAID 4	Similar to RAID 3, stripes data across many HDDs in block instead of RAID 3 bytes to improve random access performance 
	Data protection is provided by a dedicated parity instead of distributed parity 
	Dedicated parity HDD remains a bottleneck, especially for random write performance
RAID 5 	Most common RAID
	Provides RAID 0 performance with more economical redundancy 
	Stripes block data across several HDDs while distributing parity among the HDDs
	Uses HDDs more efficiently, providing overlapped read/write operations
	Provides more usable storage than RAID 0 or RAID 10
	Data protection comes from parity infomation used to reconstruct data of a failed drive
	Minimum of Three and usually Five HDDs per RAID group
	Rebuilds cause lower storage system performance 
	Potential total RAID group data loss if second drive fails during rebuild
	Read performance tends to be lower than other RAID types because parity data is distributed on each HDD
RAID 6	Similar to RAID 5, but includes the second parity scheme distributed across HDDs of the RAID group
	Dual parity protects against data loss if second HDD fails
	Tends to have lower storage system performance than RAID 5 and can plummet during dual HDD rebuilds
RAID 10 (RAID 1 + RAID 0)	RAID 1 striped 
	Improves write performance
RAID 50 (RAID 5 + RAID 0)	
	Improves performance closer to RAID 1
RAID 60 (RAID 6 + RAID 0)	RAID 6 striped
	Improves performance closer to RAID 1
