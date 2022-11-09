### MinIO Cannot Serve Some Content
Sometimes unexpected error from MinIO occur whether it's because of the filesystem was broken or MinIO failed to start the service (Disk usage full).
This will cause a `504 Gateway Timeout` issue on the client side where minio is not running properly

Problem solving:
- Inside MinIO data (ex: `/data/minio`), there is some directories from bucket's cache and reserved system `.minio.sys`
- All we need to do is remove or rename all bucket's cache directories
- This will makes MinIO run likes a fresh server or migrated from another instance

This workaround is to run MinIO server like migrated from another instance by copying `.minio.sys` directory between MinIO instance
