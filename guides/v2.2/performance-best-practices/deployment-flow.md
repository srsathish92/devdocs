---
group: perf-best-practices
title: Deployment flow
version: 2.2
functional_areas:
  - Configuration
  - System
  - Setup
---

The Magento production deployment flow helps a store reach maximum performance.

## Deploy static content

Deploying static content causes Magento 2 to perform the following actions:

* Analyze all static resources
* Perform merge, minimization, and bundling of content
* Read and process theme data
* Analyze theme fallback
* Store all processed and materialized content to specific folder for further usage

If your static content is not deployed, Magento performs all listed operation on the fly, leading to a significant increase in response time.

You can use a variety of options to customize deployment operations based on store size and fulfillment needs. The most common is the compact deploy strategy. See [Static files deployment strategies]({{ page.baseurl }}/config-guide/cli/config-cli-subcommands-static-deploy-strategies.html)

Run the following command to deploy static content:

``` bash
bin/magento setup:static-content:deploy
```

## Preprocess dependency injection instructions

When you preprocess and compile dependency injection (DI) instructions, Magento:

* Reads and processes all present configuration
* Analyzes dependencies between classes
* Creates autogenerated files (including proxies, factories, etc.)
* Stores compiled data and configuration in a cache that saves up to 25% of time on requests processing

Run the following command to preprocess and compile DI:

``` bash
bin/magento setup:di:compile
```

After compilation completes, we recommend running the following command:

``` bash
composer dump-autoload -o --apcu
```

This command allows Composer to rebuild the mapping to project files so that they load faster.

## Set production mode

Finally, you need to place your store in Production mode. Production mode is specifically optimized for maximum performance of your store. It also de-activates all developer-specific features. This can be done in your `.htaccess` or `nginx.conf` file:

`SetEnv MAGE_MODE production`

You can also deploy static content, compile the content, and set the mode in one CLI command:

``` bash
bin/magento deploy:mode:set production
```

The command runs in the background and does not allow you to set additional options on each specific step.

## Additional pre-launch actions

These steps are recommended, but are not mandatory. You can perform them immediately before launching  your store in production mode. The list includes:

* Re-index data to avoid the presence of any inconsistent data in your indexes.
* Flush the cache to be sure no old or incorrect data are left in cache.
* Warm up the cache, which calls out the most popular or critical store pages in advance, so the cache for them is generated and stored. This operation can be performed with any internet crawler or manually, if you have a small store.