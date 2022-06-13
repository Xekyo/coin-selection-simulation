# Bitcoin Core Coin Selection Simulation

This repository contains data and results from running coin selection simulations

## Running Simulations

The simulation script is `scripts/simulation.py`. It is based on a slimmed down version of the Bitcoin Core test framework.

To run, first compile the Bitcoin Core branch you wish to simulate. Then find the `config.ini` file that is usually located in `test/config.ini`.

GitPython will also need to be installed and available to the Python executed by the root user:

    sudo pip install gitpython

A simulation can be run with:

    sudo scripts/simulation.py --scenario <path/to/scenario/file> <path/to/config.ini> results/

Note that the script uses USDT Tracepoints which requires either running the script as root, or setting the requisite privileges for using tracepoints as per [these instructions](https://github.com/bitcoin/bitcoin/pull/24358#issuecomment-1083149220).

The script uses GitPython which executes git itself in order to get some information about the repo.
However sometimes git does not like it when the repo is owned by a different user than the one that is executing commands on it, so you may need to run the following:

    sudo sudo git config --global --add safe.directory <path/to/repo>

Usage:

```
usage: simulation.py [options]

positional arguments:
  configfile            Path to the config.ini file generated by compiling Bitcoin Core usually in test/config.ini
  resultsdir            Path to a directory where the simulation results will be stored

options:
  -h, --help            show this help message and exit
  --nocleanup           Leave bitcoinds and test.* datadir on exit or error
  --noshutdown          Don't stop bitcoinds after the test execution
  --cachedir CACHEDIR   Directory for caching pregenerated datadirs (default: /home/andy/bitcoin/cache)
  --tmpdir TMPDIR       Root directory for datadirs
  -l LOGLEVEL, --loglevel LOGLEVEL
                        log events at this level and higher to the console. Can be set to DEBUG, INFO, WARNING, ERROR or CRITICAL. Passing --loglevel DEBUG will output all logs to console. Note that logs at all levels are always written to the
                        test_framework.log file in the temporary test directory.
  --tracerpc            Print out all RPC calls as they are made
  --portseed PORT_SEED  The seed to use for assigning port numbers (default: current process id)
  --randomseed RANDOMSEED
                        set a random seed for deterministically reproducing a previous test run
  --timeout-factor TIMEOUT_FACTOR
                        adjust test timeouts by a factor. Setting it to 0 disables all timeouts
  --scenario SCENARIO   Path to scenario file consisting of lines of comma separated pairs of payments and feerates
  --label LABEL         A label to give to this simulation to help identify its results
  --payments PAYMENTS   Path to payments file consisting of one payment per line in BTC. Positive is a deposit into the test wallet, negative is a withdrawal from the test wallet.
  --feerates FEERATES   Path to a feerates file consisting of one feerate per line in BTC/kvb
  --ops OPS             Maximum number of deposits and withdrawal actions to do, default is all payments in the scenario or payments file
  --weights WEIGHTS [WEIGHTS ...]
                        Causes recipient output types to be chosen randomly with provided weights per address type. Weights must add to 100 and be provided in the following order: bech32m bech32 p2sh-segwit legacy
```

## Results

Inside the results directory is a directory for the commit hash the simulation was run on.
Inside of the commit hash directory are directories for each simulation run.
These are named with random generated unique ids.
Inside each simulation run's directory are the files:

* `results.txt`: Summarized data about the simulation taken every 500 operations and at the end.
* `full_results.csv`: Data about each payment sent from the test wallet, including amount, fees, feerates, algorithm used, etc.
* `inputs.csv`: The values of every input for each payment
* `utxos.csv`: The values of each UTXO in the UTXO pool prior to each payment.
* `sim_debug.log`: Log file of every operation in the simulation, primarily for debugging purposes.

## Scenarios

CSV files containing the simulation scenarios are stored in this directory.
Each line contains a pair of the amount being sent/withdrawn from the wallet, and the feerate it is done at.
If the amount is positive, the wallet is receiving this amount.
If it is negative, the wallet is sending the that amount.
