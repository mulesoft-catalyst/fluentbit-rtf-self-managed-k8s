# Fluentbit Example for RTF on Self-Managed k8s
This repository contains an example fluentbit (FB) configuration for RTF on Self-Managed kubernetes. The output used is an example for Azure Log Analytics, however this can easily be overridden to use other target systems.

## Why?
Fluentbit has become one of the [most extended](https://www.cncf.io/blog/2022/03/22/fluent-bit-reaches-1-billion-downloads/) solutions to collect and process logs given the increased scalability, flexibility, and performance when compared to other market lead solutions, like Fluentd.

Given its growing popularity, it is normal to find customers who want to adopt it as an enterprise-level solution for their k8s clusters to address typical requirements such as combining multiple logs into a single log. There is a lot of documentation on the net about how to create a multiline parser, but since the Fluentbit community is very active, changes are continuously introduced and it is easy to find documentation that refers to deprecated configurations.

## Features provided
- A parser with multiline support to unify logs that have been split due to Docker's limitation of processing 16K chunks. This is especially useful in cases where the logs are excessively large (although they shouldn't! If this is the case, you should review your login strategy)
- Total segregation of responsibilities for the parsers. There are dedicated parsers for dealing with application logs, anypoint-monitoring (sidecar container) and init (pod lifecycle events) containers logs
- Metadata added from k8s, useful to filter basic information and build complex queries in any observability platform (pod_name, namespace, container_name, labels, environment config, etc)
- Example of logs for local testing (under `/examples`)

## Features NOT provided
- Parsers for all the other k8s components that could log inside the */var/log/containers/* folder (this is easily added)

## Configuration Structure 
This section assume that you understand the following concepts from FB: 
- Key Concepts: https://docs.fluentbit.io/manual/concepts/key-concepts
- Stream Processing and Data Pipeline: https://docs.fluentbit.io/manual/stream-processing/overview

- daemonset.yaml: the daemonset defines how the FB pods will be created. Is added as part of this example as it incorporates a few environment variables that are used accross the configuration files
- secrets.yaml: defines the values that are referenced within the daemonset
- fluent-bit.conf: the FB configuration that defines the INPUT (what and how the logs files are loaded) and the OUTPUTS (where the processed logs are sent)
- parsers.conf: the FB configuration that defines how the INPUT are parsed


## How To
This section describes a few ideas to extent this example

### Add new logs to parse other services
	In case you have a new component that you want to include as part of the FB config, you should (generic steps)
- Define a new Input (where to locate the log file, usually identified by a Regex) with a proper tag 
- Define at least one parser matching the defined tag (how to parse the log structure) and one dedicated kubernetes parser (optional, inly if k8s metadata is needed)
- Optional: include filters
- Define the output (where to send the logs)

### Change the output system (target)
- Replace the existing OUTPUT section of the `fluent-bit.conf` file. Please refer to https://docs.fluentbit.io/manual/pipeline/outputs for a comprenhensive list of outputs and how to configure these

### Perform Local Testing
- Change the INPUT section to indicate where the local example files will be taken from and enable the debug for info level
- Disable the Kubernetes parser from `Parsers.conf` (This is optional in case you're not testing on a k8s environment but a local fluentbit installation)
- Change the OUTPUT to print results to stdout (or any other available output that might serve for local testing e.g. File)

# Contribution
Want to contribute? Great!

- For public contributions - Just fork the repo, make your updates and open a pull request!
- For internal contributions - Use a simplified feature workflow following these steps:
	- Clone your repo
	- Create a feature branch using the naming convention feature/name-of-the-feature (please include evidence and testing results)
	- Once it's ready, push your changes
	- Open a pull request for a review





