# Airports CI/CD V1 Proxy

This contains an example proxy for the CI/CD pipeline reference using Cloud Build
and Maven.

## Development

- Install dependencies: `npm install`
- Unit testing: `npm run unit-test`
- Integration testing against host `$APIGEE_ORG-$APIGEE_ENV.apigee.net` and
  default basepath `airports-cicd/v1`:
  `TEST_HOST=$APIGEE_ORG-$APIGEE_ENV.apigee.net npm run integration-test`
- Integration testing against feature branch deployment on
  `airports-cicd-feature-ABC/v1`:

``` sh
TEST_HOST=$APIGEE_ORG-$APIGEE_ENV.apigee.net TEST_BASE_PATH='/airports-cicd-feature-ABC/v1' npm run integration-test
```

## Run Cloud Build Deployment

### Apigee hybrid

Requires the Cloud Build API to be enabled and a Service Account with the
following roles:
  * Apigee API Creator
  * Apigee Deployer

```sh
gcloud services enable cloudbuild.googleapis.com
PROJECT_ID=$(gcloud config get-value project)
PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format="value(projectNumber)")
CLOUD_BUILD_SA="$PROJECT_NUMBER@cloudbuild.gserviceaccount.com"

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="serviceAccount:$CLOUD_BUILD_SA" \
  --role="roles/apigee.deployer"

gcloud projects add-iam-policy-binding "$PROJECT_ID" \
  --member="serviceAccount:$CLOUD_BUILD_SA" \
  --role="roles/apigee.apiCreator"
```

Run the following command to trigger a cloud build manually:

```sh
gcloud builds submit --config=cloudbuild.yaml --substitutions=_INT_TEST_HOST=api.my-host.example.com,_INT_TEST_BASE_PATH=/airports-cicd/v1
```
