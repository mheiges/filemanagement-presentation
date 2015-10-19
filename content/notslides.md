

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




## Filesystem Sync Software
  - Unison
  - Aspera Sync
  - WAN Distributed Filesystem
    - XtreemFS
    - GlusterFS
    - MooseFS

