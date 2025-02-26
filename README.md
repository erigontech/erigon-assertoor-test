# Setting up a Kurtosis Local Chain with Erigon Node

## Prerequisites

Before starting, ensure you have Kurtosis installed by following the instructions here: [Kurtosis Installation Guide](https://docs.kurtosis.com/install).

## Setup Steps

### Step 1: Install Kurtosis

Download and install Kurtosis by following the guide linked above.

### Step 2: Choose the Kurtosis Execution Script

The most comprehensive execution script is provided by `ethpandops`:  
[GitHub - Kurtosis Ethereum Package](https://github.com/kurtosis-tech/ethereum-package)

### Step 3: Clone and Build Erigon Docker Image

Clone the Erigon repository and build its Docker image:

```sh
# Clone the Erigon repository
git clone https://github.com/erigontech/erigon.git

# Navigate into the repository directory
cd erigon

# Build the Docker image
docker build -t test/erigon:current .
```

(Optional) If you want to use a standalone assertoor image, you can build it using the following commands:

```sh
# Clone the Assertoor repository
git clone https://github.com/noku-team/assertoor

# Navigate into the repository directory
cd assertoor

# Build the Docker image
docker build -t test/assertoor:current .
```

### Step 4: Create a Configuration File

Below is an example of a Kurtosis YAML configuration file that sets up a simple testnet with two client pairs and configures Assertoor to run specific test playbooks.

Create a file named `args.yaml` and add the following content:

```yaml
participants:
- el_type: erigon
  el_image: test/erigon:current
  cl_type: lighthouse
  count: 1
- el_type: erigon
  el_image: test/erigon:current
  cl_type: lodestar
  count: 1
- el_type: erigon
  el_image: test/erigon:current
  cl_type: prysm
  count: 1
additional_services:
- assertoor
- dora
assertoor_params:
  run_stability_check: false
  run_block_proposal_check: false
  # image: test/assertoor:current
  tests:
  - https://raw.githubusercontent.com/ethpandaops/assertoor-test/master/assertoor-tests/block-proposal-check.yaml
  - https://raw.githubusercontent.com/ethpandaops/assertoor-test/master/assertoor-tests/all-opcodes-test.yaml
```

### Step 5: Start the Testnet

Run the following command to start the testnet:

```sh
kurtosis run --enclave my-testnet github.com/ethpandaops/ethereum-package --args-file ./args.yaml
```

Once the setup is complete, Kurtosis will provide a summary of running services, including a link to the Assertoor Web UI & API:

```
================================= User Services =================================
UUID           Name        Ports                                         Status
f3a69b405cdb   assertoor   http: 8080/tcp -> http://127.0.0.1:32795      RUNNING
```

### Step 6: Monitor Test Progress via Assertoor Web UI

Visit the Assertoor Web UI and API at the provided link before (service named "assertoor").
Here, you can monitor live test progress, start new tests, or cancel existing ones.

### Step 7: Stop the Testnet

To stop the testnet, run:

```sh
kurtosis enclave stop my-testnet
```

### Step 8 (Optional): Remove the Testnet

To completely remove the testnet, use:

```sh
kurtosis enclave rm my-testnet
```

## Conclusion

Following these steps, you can successfully set up and run a local Ethereum testnet with Kurtosis and Erigon. Assertoor provides a valuable interface for monitoring and managing test execution efficiently.
