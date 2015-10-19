**Application File Management for EuPathDB**

October 19, 2015

---

<span style='font-size:10pt'>mash the 's' key for presenter notes</span>



## Topics

  - File management
  - Relevance to others
    - what build artifacts may be useful to others



Application reads/writes to single filesystem
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



**Application writes to single filesystem.**  
Filesystem replicated.
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
- offsite archive redundancy (_planned_)

Note:
- For data too large for traditional backup.
- replica is offline so not suited for real-time application failover



**Multiple Applications write to multiple storage classes.**  
![multiplefs](content/images/Slide21.png)

Note:



## File management
  - replication
    - UGA <-> Penn <-> Globus <-> other
  - volume partitioning
    - single 100TB volume or several 10TB volumes?
      - how to re-allocate data if we exceed a 10TB volume and need to split
  - co-administration
    - (see staff demographics)
    - w/o iRODS, need root permissions and knowledge of file locations
  - file ownership
    - tomcat, apache, staff, etc



## File Management Use Cases
  - apiSiteFiles
    - Bulk data downloads
    - webservice files
    - auxiliary files (whitepapers, large images)
  - custom GBrowse tracks
  - Provider
  - Community Uploads
  - Workflow
  - Workspaces



#### Existing Use Cases
## apiSiteFiles
  - immediate publication of bulk data downloads
  - manual rsync from data dev to websites
    - attempts to automate have been unsuccessful due to the scale (6TB, 4 million files, 0.5 million directories, depth=8)
  - excessive duplication
    - each release gets a (mostly) copy of previous
    - manual pruning of old releases
    - BRC-3 budget for de-duplicating storage



#### Existing Use Cases
## custom Gbrowse tracks
  - originally, track data stored with website installation and lost with each rebuild
  - now track data stored in a common directory where it accumulates, unmanaged
    - wasted storage of obsolete data
  - not replicated to other webservers so no guarantee availability


#### Existing Use Cases
## Provider Data



#### Existing Use Cases
## Community Uploads
  - metadata: Oracle bidirectional replication (seconds)
  - files: Unison bidirectional replication (minutes)
  - checksum recorded but no followup validation checks
  - no monitoring fs are in sync (assume eventual consistency on next rsync run)
  - ok for current light usage



#### Existing Use Cases
## OrthoMCL Proteome Mapping Service
- difficult to know status of files in pipeline
- insufficient integrity checks when copying files between systems



## Biggest Issues
  - maintaining file ownership
  - replication
  - integrity checks
  - locating files
  - cron is terribly unmanageable for production applications
  - inotify is cumbersome to setup
    - re-queue failed tasks



## Filesystem Sync Software
  - Unison
  - Aspera Sync
  - WAN Distributed Filesystem
    - XtreemFS
    - GlusterFS
    - MooseFS



**Filesystem Middleware**
![multiplefs](content/images/Slide22.png)



## User Demographics
  - bench scientist
  - HTS Center
  - EupathDB Staff
    - Outreach
    - programmers
    - admins



## iRODS direct access vs behind our UI



## Technology Assessment

Given existing tech that addresses a problem,  
do we adopt it?

- Feature set
  - state of documentation
  - documentation
  - community/vendor support
  - extensible
    - custom modules
    - API
- Ambiguous, open-ended requirements


## User Demographics

- bench scientist
- informatics center (MaHPIC)
- EuPathDB staff
  - outreach
  - admins
  - application programmers



## Ongoing issues

- managing GBrowse data
  - periodically deleting stale
  - not available after failover
- community file uploads
- download files
  - sync == publish
- apiSiteFiles
  - replication to mirrors
- manual delivery
  - ownership/permissions
  - no failover
  - no distinction between derived and gold copy
- OrthoMCL mapping service
  - failover for results

- maintaining file ownership and syncing
  - apache, tomcat, others
  - requires root rsync
    - dangerous. difficult to secure.

- when to sync
  - inotify
    - complex
  - cron
    - how often?
      - too frequent: excessive notifications when remote node offline
      - less frequent: race conditions when failing over

- User quotas
  - can iRODS report usage by user? (e.g. for 'user' by attribute, total bytes stored)

## Microservices

- curl
  - myCurl.get("ftp.ncbi.nih.gov/gene/DATA/gene2pubmed.gz", "/ebrcZone/manualDelivery/common/dbxref/gene2pubmed.gz");

## Strengths

## Weaknesses


## Metrics

- log data access (rule)
  - how often is X downloaded?

## Costs

- expect to send staff to training



## Similar Solutions

- Nirvana
  - similar feature set (SRB common ancestor)
  - commercial
- Fedora
  - targeted for long-term archiving
  - no native replication? iRODS backend integration for that.
- DSpace
- misc. academic and gov't projects
- http://www.stevemoranscustomrods.com



## iRODS Use Cases
- document archive: grants, RFP, reports
- datasets for community downloads, shared with workflow
- federate data hosted by collaborators, e.g. MaHPIC, Liverpool

