Synapse S3 Storage Provider
===========================

This module can be used by synapse as a storage provider, allowing it to fetch
and store media in Amazon S3.


Usage
-----

The `s3_storage_provider.py` should be on the PYTHONPATH when starting
synapse.

Example of entry in synapse config:

```yaml
media_storage_providers:
- module: s3_storage_provider.S3StorageProviderBackend
  store_local: True
  store_remote: True
  store_synchronous: True
  config:
    bucket: <S3_BUCKET_NAME>
```

This module uses `boto3`, and so the credentials should be specified as
described [here](https://boto3.readthedocs.io/en/latest/guide/configuration.html#guide-configuration).

Regular cleanup job
-------------------

There is additionally a `s3_media_upload.py` which can be used in a regular job to
upload content to s3, then delete that from local disk. This script can be used in
combination with configuration for the storage provider to pull media from s3, but
upload it asynchronously.

Once the package is installed, the script should be run somewhat like the 
following. We suggest using tmux or screen as these can take a long time on larger
servers.

More options are available in the command help.

```
> cd s3_media_upload
# cache.db will be created if absent. database.yaml is required to contain PG credentials
> ls
cache.db database.yaml
# Update cache from /path/to/media/store looking for files not used within 2 months
> s3_media_upload update /path/to/media/store 2m
Syncing files that haven't been accessed since: 2018-10-18 11:06:21.520602
Synced 0 new rows
100%|█████████████████████████████████████████████████████████████| 1074/1074 [00:33<00:00, 25.97files/s]
Updated 0 as deleted

> s3_media_upload upload /path/to/media/store matrix_s3_bucket_name --storage-class STANDARD_IA --delete
# prepare to wait a long time
```
