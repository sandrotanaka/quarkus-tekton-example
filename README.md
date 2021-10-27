# Simple Tekton Quarkus build pipeline

# Pre-requisite

1. OpenShift 4.9
2. Tekton installed (via Operator)
3. `oc` and `tkn` CLI installed

# Customizable values
This pipeline has some parameters that relates to for example which namespace and which deployment name should be used so for this demo we are going to use environment variables so that things like namespace matches, but you can replace these as long as you make sure that they match your namespace etc.

Deployment name and also name of image in the the build stream

    APP_NAME=quarkus-hello 
    
Project name (namespace). For this example we are going to use `quarkus-pipeline-demo`, but you can of course use something else.

    NAMESPACE=quarkus-pipeline-demo

Git repo url of the app to build, in this example it's the same as this repo

    GIT_REPO=https://github.com/tqvarnst/quarkus-tekton-example

# To install the pipeline
For this example we are going to use `quarkus-pipeline-demo`, but you can of course use something else.

    oc new-project $NAMESPACE
    oc apply -f src/main/tkn/pipeline.yaml

# To run the pipeline

    tkn pipeline start build-and-deploy-quarkus \
        -w name=shared-workspace,volumeClaimTemplateFile=https://raw.githubusercontent.com/openshift/pipelines-tutorial/pipelines-1.5/01_pipeline/03_persistent_volume_claim.yaml \
        -p deployment-name=$APP_NAME \
        -p git-url=$GIT_REPO \
         -p IMAGE=image-registry.openshift-image-registry.svc:5000/$NAMESPACE/$APP_NAME \
        --use-param-defaults

# To deploy the built application

    oc new-app $APP_NAME -n $NAMESPACE
    oc expose svc $APP_NAME -n $NAMESPACE

