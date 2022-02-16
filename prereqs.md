# Prerequisites

The following software needs to be available in the environment where you will be installing your Tanzu Application Platform on Azure

- Azure Account with proper permissions to provision AKS cluster
- Disable agentpool Autoscaling
- Set Node pool capacity to the following:
	- 10 Node count
	- VM Size: Standard DS2 v2 (2 vcpus, 7 GiB memory)
	- Cores : 20 vCPUs
	- Memory: 70 GiB
	