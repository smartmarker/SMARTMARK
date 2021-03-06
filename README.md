# **SMARTMARK - Software Watermarking Scheme for Smart Contracts**

*SMARTMARK* is a novel software watermarking scheme, aiming to address the ownership verification problem for smart contracts. *SMARTMARK* builds the control flow graph of a target contract runtime bytecode and carefully selects some bytes from each block in the control flow graph to represent a watermark. Any extra bytes for the watermark are not added to the contract's runtime bytecode, making it safe from an adversary's watermark targeted attack and gas cost increment.  

This includes *SMARTMARK*'s source code and smart conract dataset, as well as watermark resiliency evaluation code and data.
We submitted the *SMARTMARK* paper to ASE 2022.
</br>

## Usage


The common input of embedder and verifier is control flow graph (CFG) generated from the contract runtime bytecode. In *SMARTMARK*, we basically use CFG file in json format provided by [EtherSolve](https://github.com/SeUniVr/EtherSolve.git).  

This tool has been tested on Ubuntu 18.04 with Python 3.7.5.

### Prerequisites
If Python3 and pip3 are installed on your system, all you have to do before using this tool is just installing a crypto library for Keccak-256 hash function as below:
```
$ pip3 install pycryptodome
```

### Watermark Embedder

Watermark embedder/embed_watermark.py

```
usage: embed_watermark.py [-h] -i INPUT -W WRO -H HASHMARK -L LENGTH -N NUMBER
                          [-C GASCOST] [-R RATIO] [-o MAXOPNUM]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        input CFG json file
  -W WRO, --WRO WRO     output WRO file
  -H HASHMARK, --hashmark HASHMARK
                        output hashmark file
  -L LENGTH, --length LENGTH
                        length of watermark
  -N NUMBER, --number NUMBER
                        number of watermark
  -C GASCOST, --gasCost GASCOST
                        minumum gas cost for opcode group (default: 9)
  -R RATIO, --ratio RATIO
                        opcode group ratio (default: 20)
  -o MAXOPNUM, --maxOpNum MAXOPNUM
                        max opcode number for opcode grouping (default: 5)
```

- The path of input file path (CFG file of contract) and output files (watermark reference object, hashmark) must be specified.
- The length (*L*) and number (*N*) of watermark are also user-specified options, but embedding may fail depending on the watermark capacity of the contract.

### Watermark Verifier

Watermark Verifier/verify_watermark.py

```
usage: verify_watermark.py [-h] -i INPUT -W WRO -H HASHMARK -o WATERMARK

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        input CFG json file
  -W WRO, --WRO WRO     input WRO file
  -H HASHMARK, --hashmark HASHMARK
                        input hashmark file
  -o WATERMARK, --watermark WATERMARK
                        output watermark file
```

- The path of input files (CFG file of contract, watermark reference object, hashmark) and output file (watermark) must be specified.

  
</br>

## Watermark capacity

*SMARTMARK* embeds only one watermark byte per CFG block to spread the watermark over the entire contract area. Threfore, the contract must have as many CFG blocks (*L x N*) as can accommodate the given watermark length (*L*) and number (*N*).

If embedding failes due to insufficient number of CFG blocks, it returns the error. (`[ERROR] this contract has insufficient CFG block to be watermarked`)  
</br>

## Smart contract and watermark deployment

In order to assert the copyright of the contract afterwards, the author should store the hashmark in the EVM storage of the contract address. hashmark is a commitment to whether the watermark reference object held by the author is the same as that generated during watermark embedding operation.


How to initialize the storage variable with hashmark value inside the constructor function is as below:

```solidity
//Solidity code snippet
contract Watermark {

    bytes hashmark;

    constructor() {
        // Hashmark value using Keccak-256
        hashmark = "cc860417...fc6b";
    }
}
```


## Dataset
we collected nine million blocks from the Ethereum Mainnet, extracted 11,754 unique bytecodes through CFG generation ([EtherSolve](https://github.com/SeUniVr/EtherSolve.git)) and DBSCAN clustering. (Section 6)

The CFG files in json format for 11,754 contracts are located in the [contract_CFG_dataset](contract_CFG_dataset) directory.
