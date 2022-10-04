
## Create Resource

Create bucket for save image

```gsutil mb gs://demo-cf-ocr```

Create bucket for result OCR

```gsutil mb gs://demo-cf-ocr-result```

Create Pub/Sub topic for publish translation requests

```gcloud pubsub topics create cf-translate```

Create Pub/Sub topic for publish finished translation results

```gcloud pubsub topics create cf-translate-result```

## Deplot Cloud Function

### Image processing

```
gcloud functions deploy ocr-extract \
--runtime nodejs16 \
--trigger-bucket demo-cf-ocr \
--entry-point processImage \
--set-env-vars "^:^GCP_PROJECT=rahadiangg-demo-ha-temp:TRANSLATE_TOPIC=cf-translate:RESULT_TOPIC=cf-translate-result:TO_LANG=en"
```

### Text translation

```
gcloud functions deploy ocr-translate \
--runtime nodejs16 \
--trigger-topic cf-translate \
--entry-point translateText \
--set-env-vars "GCP_PROJECT=rahadiangg-demo-ha-temp,RESULT_TOPIC=cf-translate-result"
```

### Save result to GCS

```
gcloud functions deploy ocr-save \
--runtime nodejs16 \
--trigger-topic cf-translate-result \
--entry-point saveResult \
--set-env-vars "GCP_PROJECT=rahadiangg-demo-ha-temp,RESULT_BUCKET=demo-cf-ocr-result"
```