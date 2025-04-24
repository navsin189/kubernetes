helm install minio minio/minio   --set existingSecret=minio-keys

These are deprecated but still supported. Internally, they set:

MINIO_ROOT_USER = accessKey

MINIO_ROOT_PASSWORD = secretKey

helm install minio minio/minio \
  --set accessKey=minioadmin \
  --set secretKey=minioadmin \
  --set resources.requests.memory=512Mi


existingSecret: minio-creds