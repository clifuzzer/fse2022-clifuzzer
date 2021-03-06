# CLIFuzzer: A tool to grammar fuzz command line utilities

CLIFuzzer automatically extracts the configuration options and arguments of CLI utilities (that use `getopt`, `getopt_long` or `getopt_long_only` to parse their invocations) to construct the utility's invocation grammar. This constructed grammar is then used to grammar fuzz the same utilities.

## Language Support

CLIFuzzer supports Python3 currently.

## Installation

You can install CLIFuzzer by navigating to the root directory and executing
```
pip3 install .
```

## Execution

To execute CLIFuzzer, you need to have a directory named `outputdir` (to store outputs and logs) relative to the point of invocation
```
mkdir outputdir
```

Once you have that, you can invoke CLIFuzzer on utilities for multiple purposes -
```
$ ./clifuzzer --help
usage: run-cf-cmd.py [-h] [--get-grammar | --get-options | -f FUZZ | --get-coverage | --fuzz-coverage FUZZ_COVERAGE | --get-manual-coverage] [-o O] [-g GRAM_FILE] [--log-pass] [--invalid-options] [--invalid-values] [--seed SEED]
                     binary

Run Configuration fuzzing on the given binary

positional arguments:
  binary                Binary or path to Binary to fuzz on

optional arguments:
  -h, --help            show this help message and exit
  --get-grammar         Print the grammar extracted from options
  --get-options         Print a list of cmdline options available in the binary
  -f FUZZ, --fuzz FUZZ  No. of times the fuzzer should fuzz the binary and note unexpected behaviour including crashes and interesting return codes
  --get-coverage        Extract the coverage achieved in the tool
  --fuzz-coverage FUZZ_COVERAGE
                        Run fuzz FUZZ_COVERAGE times and then report the extracted coverage. Equivalent to running -f FUZZ_COVERAGE first and then running --get-coverage on a binary
  --get-manual-coverage
                        Extract the manual testing coverage of the binary
  -o O                  File to dump the output to. Defaults to binary-name.out
  -g GRAM_FILE, --gram-file GRAM_FILE
                        File containing the grammar to fuzz from.
  --log-pass            logs passing invocations as well.
  --invalid-options     Inserts invalid options into the grammar
  --invalid-values      Inserts invalid values for some options in the grammar
  --seed SEED           Seed for randomising
```

In particular, say you have a CLI utility named `myutility` in your current directory, then the invocation to extract its options be -
```
$ ./clifuzzer --get-options -o outputdir/myutility.options ./myutility
```

If you wish to get `myutility`'s grammar, then invoke `clifuzzer` as 
```
$ ./clifuzzer --get-options -o outputdir/myutility-gram.json ./myutility
```
This extracts myutility's grammar in JSON format.

Finally, if you wish to fuzz myutility 100 times using this extracted grammar, invoke `clifuzzer` as
```
$ ./clifuzzer --fuzz 100 --gram-file outputdir/myutility-gram.json -o outputdir/myutility.out ./myutility
```

To collect the achieved statement and branch coverage, invoke  `clifuzzer` as 

```
$ ./clifuzzer --get-coverage -o outputdir/myutility.cov ./myutility
```