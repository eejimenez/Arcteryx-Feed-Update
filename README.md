# Arcteryx Documentacion

## Content
**[1. Update Arcteryx Feed in Production](#update-arcteryx-feed-in-production)**

**[2. Update Arcteryx Feed in UAT env](#update-arcteryx-feed-in-uat-env)**

**[3. Troubleshooting](#update-arcteryx-feed-in-uat-env)**

# Update Arcteryx Feed in Production

## Requirements
* AWS user with read and execute permissions on the [workflow pipeline](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions/finder-workflow-pipeline-prod?tab=testing) lambda function.
* Access to Jenkins pipelines

## Steps

### Update the categories file:
You can update the categories file by running a transformation in the workflow pipeline using this payload in the Test tab.

```
{
  "pk": "workflow::pipeline",
  "sk": "arcteryx::categories"
}
```

Sample

![run-categories-update](/images/run-categories-update.png)

### Update **filters** data in the metadata script:

You must wait for confirmation from the operations team to run this transformation because they must perform a product synchronization and add an access token to this [coda table](https://coda.io/d/Arcteryx-Tokens_dAGZTy3PFp_/Arcteryx-Jacket-Footwear_suIg0MWh#_lu8TZy4Z).

You can update the metadata script file by running a transformation in the workflow pipeline with this payload

```
{
  "pk": "workflow::pipeline",
  "sk": "arcteryx-footwear::transformation"
}
```

![run-footwear-transformation](/images/run-footwear-transformation.png)


### Clean the CDN cache for the metadata file.
You can clean the CDN cache with this [jenkins pipeline](http://jenkins.cartfulsolutions.com:8080/job/environments/job/prod/job/cache/job/purge/).

Urls for the pipeline payload:
`https://embed.cartfulsolutions.com/arcteryx/metadata.js`
`https://embed.cartfulsolutions.com/arcteryx/footwear/metadata.js`

Sample

![purge-metadata-cache](/images/purge-metadata-cache.png)


# Update Arcteryx Feed in UAT env

## Requirements
* AWS user with read and execute permissions on the [workflow pipeline](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions/finder-workflow-pipeline-prod?tab=testing) lambda function.
* Access to Jenkins pipelines
* Access to Cartful Admin of uat environment

## Steps

### Update UAT environment with latest changes from production
You can update the uat env with the deploy_update [jenkins pipeline](http://jenkins.cartfulsolutions.com:8080/job/environments/job/dev/job/deploy_update/build?delay=0sec) with the following parameters.

- DEPLOY_API: yes
- API_VERSION: latest
- REACTGEN_VERSION: latest
- DEPLOY_ADMIN: yes
- ADMIN_VERSION: latest
- REFRESH_DATA: yes
- SIMPLE_AUTOUNDATE: no

Sample
![update-uat-environment](/images/update-uat-environment.png)

### Update **filters** data in the metadata script for the UAT environment:

You have to get the access token from Cartful Admin of the uat environment by following these steps:
- Navigate to product page of the finder, for example: https://admin-env.cartfulsolutions.com/arcteryxuat/#experiences/791/products

- Open the development tools of the browser and select the network tab.

![network-tab-in-products-page](/images/network-tab-in-products-page.png)

- Click on the **Export Product** button then in the network tool you will see a request with name **product-excel**. 

![product-excel-request](/images/product-excel-request.png)

- Copy the entire url of the product-excel request.

![copy-product-excel-url](/images/copy-product-excel-request.png)

Once you get the product-excel url you have to add the url in the cell of token column and staging row of this [coda table](https://coda.io/d/Arcteryx-Tokens_dAGZTy3PFp_/Arcteryx-Jacket-Footwear_suIg0MWh#_lu8TZy4Z).

Sample

![paste-token-in-coda-table](/images/past-token-in-coda-table.png)

Now you can update the metadata script file of staging by running a transformation in the workflow pipeline with this payload

```
{
  "pk": "workflow::pipeline",
  "sk": "arcteryx-footwear-staging::transformation"
}
```

![run-footwear-transformation](/images/run-footwear-transformation-staging.png)


### Clean the CDN cache for the metadata file of staging.
You can clean the CDN cache with this [jenkins pipeline](http://jenkins.cartfulsolutions.com:8080/job/environments/job/prod/job/cache/job/purge/).

Urls for the pipeline payload:
`https://embed.cartfulsolutions.com/arcteryx/metadata-staging.js`
`https://embed.cartfulsolutions.com/arcteryx/footwear/metadata-staging.js`

Sample

![purge-metadata-cache](/images/purge-cache-metadata-staging.png)


# Troubleshooting

