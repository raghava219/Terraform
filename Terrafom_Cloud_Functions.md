https://towardsdatascience.com/deploy-cloud-functions-on-gcp-with-terraform-111a1c4a9a88

[**Cloud Functions**](https://cloud.google.com/functions/docs/writing) are scalable “pay-as-you-go” Functions as a Service (FaaS) from [Google Cloud Platform](https://cloud.google.com/) (GCP) to run your code with zero server management.

Cloud Functions can be written in [Node.js](https://nodejs.org/), [Python](https://python.org/), [Go](https://golang.org/), [Java](https://www.java.com/), [.NET](https://dotnet.microsoft.com/languages), [Ruby](https://www.ruby-lang.org/en/), and [PHP](https://www.php.net/) programming languages. For the sake of readability, this tutorial will contain a Cloud Function written in **Python**. But please feel free to choose your preferred programming language.

[**Terraform**](https://www.terraform.io/) is an Infrastructure as Code (IaC) development tool that allows you to build, change, and version infrastructure safely, reproducibly, and efficiently. It codifies cloud APIs (GCP, AWS, Azure, …) into declarative configuration files.

At the end of this tutorial you will have a Cloud Function triggered as soon as a file is uploaded to a Google Cloud Storage bucket.

## Prerequisites

To follow this tutorial you will need:

-   [Terraform installed](https://learn.hashicorp.com/tutorials/terraform/install-cli) on your local machine.
-   [Google Cloud SDK installed](https://cloud.google.com/sdk/docs/install) on your local machine.
-   a [Google Cloud Platform project](https://cloud.google.com/resource-manager/docs/creating-managing-projects) set up and attached to a [billing account](https://cloud.google.com/billing/docs/how-to/modify-project). Make sure the Cloud Functions [API is Enabled](https://cloud.google.com/service-usage/docs/enable-disable).

Then you can authenticate with GCP on your local machine running `gcloud auth application-default login` in your terminal.

## Objectives

The objective of this tutorial is to use Terraform to deploy in a GCP project:

-   a bucket to upload files to.
-   a bucket to store the source code of the Cloud Function.
-   a Cloud Function that is triggered each time a file is uploaded to the first bucket and whose source code is in the second bucket.

## Project Structure

```
.cloud_function_project/  
│  
├── terraform/  
│ │  
│ ├── backend.tf  
│ ├── function.tf  
│ ├── main.tf  
│ ├── storage.tf  
│ └── variables.tf  
│  
└── src/  
│  
├── main.py  
└── requirements.txt
```

The `src` folder contains the source code of the cloud function. It is a structure specific to Python.

-   `main.py`: source code of the Cloud Functions.
-   `requirements.txt`: the list of python libraries required by `main.py` to run. (you won’t need it in this tutorial)

The `terraform` folder contains the configuration files of the environment to deploy.

-   `backend.tf`: declaration of the [Terraform backend](https://www.terraform.io/language/settings/backends).
-   `main.tf`: main declarations of the environment.
-   `variables.tf`: definition of the variables.
-   `storage.tf`: declaration of the Google Cloud Storage bucket.
-   `function.tf`: declaration of the Cloud Function.


## Create the Terraform Infrastructure

### Backend

Start declaring your environment by specifying the Terraform backend. You can choose `[local](https://www.terraform.io/language/settings/backends/local)` which means that all the state files will be stored in the local directory. You could also choose a `[gcs](https://www.terraform.io/language/settings/backends/gcs)` backend but let’s keep it simple.

```	
terraform {
		backend "local" {}	
	}
```


### Variables

Declare the variables used in the Terraform files. You need to change the `project_id` variable according to the ID of the project in which you want to deploy the resources. Of course, you also can change the `region`.

```
variable "project_id" {

default = "<YOUR-PROJECT-ID>"

}

variable "region" {

default = "europe-west1"

}
```

### Main

Declare the connection to the google [provider](https://www.terraform.io/language/providers).

```
provider "google" {

project = var.project_id

region = var.region

}
```

### Google Cloud Storage

Declare the two Google Cloud Storage buckets respectively to store the code of the Cloud Function and to upload files.
```
resource "google_storage_bucket" "function_bucket" {

name = "${var.project_id}-function"

location = var.region

}

resource "google_storage_bucket" "input_bucket" {

name = "${var.project_id}-input"

location = var.region

}
```

**Note:** The bucket names are prefixed with `project_id` because the buckets must have unique names.

### Google Cloud Function

Last step: declare the Cloud Function. This requires compressing the source code into a zip file and uploading it to a bucket for storage. The source code can then be accessed when creating the Cloud Function with Terraform.

```
# Generates an archive of the source code compressed as a .zip file.

data "archive_file" "source" {

type = "zip"

source_dir = "../src"

output_path = "/tmp/function.zip"

}

# Add source code zip to the Cloud Function's bucket

resource "google_storage_bucket_object" "zip" {

source = data.archive_file.source.output_path

content_type = "application/zip"

# Append to the MD5 checksum of the files's content

# to force the zip to be updated as soon as a change occurs

name = "src-${data.archive_file.source.output_md5}.zip"

bucket = google_storage_bucket.function_bucket.name

# Dependencies are automatically inferred so these lines can be deleted

depends_on = [

google_storage_bucket.function_bucket, # declared in `storage.tf`

data.archive_file.source

]

}

# Create the Cloud function triggered by a `Finalize` event on the bucket

resource "google_cloudfunctions_function" "function" {

name = "function-trigger-on-gcs"

runtime = "python37" # of course changeable

# Get the source code of the cloud function as a Zip compression

source_archive_bucket = google_storage_bucket.function_bucket.name

source_archive_object = google_storage_bucket_object.zip.name

# Must match the function name in the cloud function `main.py` source code

entry_point = "hello_gcs"

#

event_trigger {

event_type = "google.storage.object.finalize"

resource = "${var.project_id}-input"

}

# Dependencies are automatically inferred so these lines can be deleted

depends_on = [

google_storage_bucket.function_bucket, # declared in `storage.tf`

google_storage_bucket_object.zip

]

}
```

### Deploy the environment

Everything is ready to be deployed. Go to the root of the project in your terminal then to the root of the `terraform` folder.
$ cd ./terraform
Initialize your code to download the requirements mentioned in your code.
$ terraform init
Review changes.
$ terraform plan
Accept changes and apply them against real infrastructure.
$ terraform apply

### Test your Cloud Function

To test everything works fine:

-   Open the [Google Cloud Console](https://console.cloud.google.com/) and connect to your project.
-   Go to the [Google Cloud Storage browser](https://console.cloud.google.com/storage/browser).
-   You can see the `<YOUR-PROJECT-ID>-function` and `<YOUR-PROJECT-ID>-input` buckets. Click on the bucket named `<YOUR-PROJECT-ID>-input` and upload any file into it to trigger the Cloud Function.
-   To verify it worked, go to your [Cloud Functions list](https://console.cloud.google.com/functions/list). A Cloud Function named `function-trigger-on-gcs` should be here. Click on its name and go to the `LOGS` tab to see it was triggered by the file you uploaded.

### Going further

I hope you enjoyed this short tutorial to deploy a Cloud Function. Many improvements can be applied. You can:

-   set an [IAM policy](https://cloud.google.com/iam/docs/policies).
-   add variables and convert this code into a [Terraform module](https://learn.hashicorp.com/tutorials/terraform/module) to be reusable.
-   integrate the deployment into a [Cloud Build](https://cloud.google.com/build) process to have a continuous deployment.


