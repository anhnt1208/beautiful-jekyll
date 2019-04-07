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
    <td>RAID 0</td>
    <td>
      <div>
      &#9642;Data striped across multi hard disk drives for maximum write perfomance.<br/>
      &#9642;No actual data protection<br/>
      </div>
    </td>
  </tr>
  <tr>
    <td>RAID 1</td>
    <td>
      <div>
      &#9642;Synchronously mirros all data from each HDD to exact duplicate HDD<br/>
      &#9642;No data lost if of HDD faults or fails<br/><br/>
      &#9642;Typically highest-performance RAID level at the expense of lower usable capacity <br/>
      </div>
    </td>
  </tr>  
  <tr>
    <td>RAID 2</td>
    <td>
      <div>
      &#9642;Data protected by errors correcting codes  <br/>
      &#9642;Parity HDD requirements proportional to the log of HDD number  <br/>
      &#9642;Some what inflexible and less effecient than RAID 5 or RAID 6 with lower performance and reliability  <br/>
      &#9642;Not widely used    <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 3</td>
    <td>
      <div>
      &#9642;Data is protected against the failure of any the HDD in a group of N+  <br/>
      &#9642;Similar to RAID 5, but blocks are spread across HDDs  <br/>
      &#9642;Parity is bitwise vs. RAID 5's block  <br/>
      &#9642;Parity resides on a single HDD rather than being distributed among all disks  <br/>
      &#9642;Random write performance is quite poor, and random performance fair at best  <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 4</td>
    <td>
      <div>
      &#9642;Similar to RAID 3, stripes data across many HDDs in block instead of RAID 3 bytes to improve random access performance <br/>
      &#9642;Data protection is provided by a dedicated parity instead of distributed parity <br/>
      &#9642;Dedicated parity HDD remains a bottleneck, especially for random write performance  <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 5</td>
    <td>
      <div>
      &#9642;Most common RAID<br/>
      &#9642;Provides RAID 0 performance with more economical redundancy <br/>
      &#9642;Stripes block data across several HDDs while distributing parity among the HDDs<br/>
      &#9642;Uses HDDs more efficiently, providing overlapped read/write operations<br/>
      &#9642;Provides more usable storage than RAID 0 or RAID 10<br/>
      &#9642;Data protection comes from parity infomation used to reconstruct data of a failed drive<br/>
      &#9642;Minimum of Three and usually Five HDDs per RAID group<br/>
      &#9642;Rebuilds cause lower storage system performance <br/>
      &#9642;Potential total RAID group data loss if second drive fails during rebuild<br/>
      &#9642;Read performance tends to be lower than other RAID types because parity data is distributed on each HDD <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 6</td>
    <td>
      <div>
      &#9642;Similar to RAID 5, but includes the second parity scheme distributed across HDDs of the RAID group<br/>
      &#9642;Dual parity protects against data loss if second HDD fails<br/>
      &#9642;Tends to have lower storage system performance than RAID 5 and can plummet during dual HDD rebuilds <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 10 (RAID 1 + RAID 0)</td>
    <td>
      <div>
      &#9642;RAID 1 striped <br/>
      &#9642;Improves write performance <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 50 (RAID 5 + RAID 0)</td>
    <td>
      <div>
      &#9642;Improves performance closer to RAID 1 <br/>
      </div>
    </td>
  </tr> 
  <tr>
    <td>RAID 60 (RAID 6 + RAID 0)</td>
    <td>
      <div>
      &#9642;RAID 6 striped<br/>
      &#9642;Improves performance closer to RAID 1<br/>
      </div>
    </td>
  </tr> 
  </body>
</table>

---
Reference Links
[RAID types and benefits explained](https://searchstorage.techtarget.com/answer/RAID-types-and-benefits-explained)

        
