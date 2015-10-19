**File Management for EuPathDB**

October 19, 2015

---

<span style='font-size:10pt'>mash the 's' key for presenter notes</span>



## Topics

  - Existing file management scenarios at EuPathDB
  - Problems faced with current implementations
  - Propose technology to address these problems



## Existing file management scenarios at EuPathDB



**Application reads/writes to single filesystem**
![singlefs](content/images/Slide16.png)
- provider data
- GBrowse custom tracks
- BLAST



**Application reads/writes to multiple filesystems.**  
*Could be same or different data to each fs.*
![multiplefs](content/images/Slide17.png)

- Jenkins
- Workflow
- OrthoMCL Proteome Mapping Service

Note:
Filesystems could be separated by a WAN
Could be same data to each fs (replicated for failover, data protection)
or different data (different processes acting from each fs or needing
different storage characteristics (e.g. archival))



**Application writes to single filesystem with**  
**filesystem replicated.**
![multiplefs](content/images/Slide18.png)
- apiSiteFiles

Note:
Data dev writes to apiSiteFiles on the workflow server.
rsync process copies to mirrors on webservers



**Multiple Applications write to multiple filesystems.**  
Filesystem replicated bidirectionally.
![multiplefs](content/images/Slide19.png)
- Community File Uploads



**Replication at the storage level**
![multiplefs](content/images/Slide20.png)
- disaster recovery (_planned_)

Note:
- For data too large for traditional backup.
- replica is offline so not suited for real-time application failover



**Multiple Applications write to multiple storage classes.**  
![multiplefs](content/images/Slide21.png)
- *step analysis??*
- *workspaces??*

Note:
workflow sort of does this by writing to slow filesystem vs database on fast filesystem



## Problems faced with current implementations



## File Management Use Cases
  - apiSiteFiles
    - bulk data downloads
    - webservice files
    - auxiliary files (whitepapers, large images)
  - custom GBrowse tracks
  - Provider data (manualDelivery)
  - Community Uploads
  - Workflow
  - *Workspaces*



#### Existing Use Cases
## apiSiteFiles
  - manual rsync from data dev to websites
    - attempts to automate have been unsuccessful due to the scale (6TB, 4 million files, 0.5 million directories, depth=8)
  - excessive duplication
    - each release gets a (mostly) copy of previous
    - BRC-3 pays for costly de-duplicating storage
    - requires manual pruning of old releases (alleviated by de-dup)
  - file ownership/permission conflicts
  - immediate publication of bulk data downloads



#### Existing Use Cases
## custom Gbrowse tracks
  - originally, track data stored with website installation and lost with each rebuild
  - now track data stored in a common directory where it accumulates, unmanaged
    - storage wasted on obsolete data
  - not replicated to other webservers so no guarantee availability



#### Existing Use Cases
## Provider Data (manualDelivery)
- no redundancy
- no failover



#### Existing Use Cases
## Community Uploads
  - metadata: Oracle bidirectional replication (seconds)
  - files: Unison bidirectional replication (minutes)
  - checksum recorded but no followup validation checks
  - no monitoring fs are in sync (assume eventual consistency on next rsync run)
  - ok for current light usage, not scalable



#### Existing Use Cases
### OrthoMCL Proteome Mapping Service
  - user input needs to be validated at upload time
  - difficult to know status of files in pipeline
  - insufficient integrity checks when copying files between systems
  - no failover



#### Summary
## File management complications
  - matching application server with data server
    - time-consuming, error-prone to locate files for syncing, modification, troubleshooting
  - each file management scenario is treated differently
    - unidirectional replication
    - bidirectional replication



#### Summary
## File management complications
  - rsync + cron
    - does not scale
    - cron is terribly unmanageable for production applications
  - rsync + inotify
    - does not scale
    - rsync + inotify is cumbersome to setup, re-queue failed tasks
  - rsync-ssh authentication and security, esp. when running as root



#### Summary
## File management complications
  - volume partitioning
    - single 100TB volume or ten 10TB volumes?
      - how to re-allocate data if we exceed a 10TB volume and need to split?
      - how does the application handle that?
  - file ownership
    - tomcat, apache, staff, etc
  - co-administration
    - need root permissions and knowledge of file locations
  - file integrity checks rarely done, if ever



# A Solution



**Take this**  
![multiplefs](content/images/Slide21.png)



**Insert Filesystem Middleware**  
![multiplefs](content/images/Slide22.png)



**Filesystem Middleware**
  - Nirvana
  - DSpace
  - **iRODS**



## iRODS
  - "**I**ntegrated **R**ule-**O**riented **D**ata **S**ystem"



iRODS is a
### uniform interface to distributed, heterogeneous storage systems

![multiplefs](content/images/Slide25.png)



iRODS allows
### File access via unified namespace
![multiplefs](content/images/Slide23.png)



iRODS provides
### Automated Data Operations
Defined with Rules
- validate checksums when adding, removing files
- validate file format when adding a file
- archive data that has not been accessed for over 6 months
- check for viruses when someone uploads a Community file



iRODS has
### Robust Metadata Catalog
Assign arbitrary attributes to files, collections, etc.
![multiplefs](content/images/Slide24.png)



iRODS aids
### Security Compliance
  - Access control
    - e.g. secure personally identifiable information
  - Audit trails
  - Enforce retention policies



iRODS is
### Extensible
  - rules
  - microservices
  - plugins
  - APIs
    - REST
    - Java, Python, PHP, others



## Final Take-home

  - Workspaces and step analysis are expected to add significant data influx and efflux to our system.
  - Our existing file data management is inadequate to accomodate workspaces.
  - iRODS is needed to manage application data across distributed, heterogeneous storage systems.
  - Other use cases for iRODS may apply.
