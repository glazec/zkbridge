TL;DR

- ZK technology offers better security for cross-chain communication
- Cross-chain communication protocols are still in their early stages but promise to allow dapps to access data on different chains
- The DeFi, DID, and governance sectors stand to benefit from the development of cross-chain dapps
- The impact of cross-chain dapps is expected to be significant in the coming years, akin to the impact of globalization
- Developers are working hard to explore the best schema for building cross-chain dapps.

Intro

In the past, there were only Ethereum and Bitcoin. They have the most liquidity, the most players, the most applications and the most txs. In 2020, many new blockchains were launched, such as Avalanche, Polygon and BSC.

After the launch of these mainnets, we saw a paradigm shift from Ethereum and Bitcoin to ALT. Users are moving from Ethereum to ALT to find new opportunities. Builders move from Ethereum to ALT to fork existing projects. These builders create new opportunities for users looking for high returns.

Ethereum used to have all the liquidity in the crypto world apart from bitcoin. By the end of 2020, it dropped dramatically to around 60%. Below is the TVL for 165 chains from DefiLlama.

DefiLlama

Today, the TVL pie chart looks like this. Ethereum takes the majority of the liquidity. Tron and BSC take the second and their position.

After spreading assets and liquidity across different chains, users start to think about how to manage and move assets across different chains. Asset issuers also think about how to increase their acceptance by expanding to different chains.

Cross-chain asset bridges become popular in 2022. Instead of using CEX as a cross-chain bridge, users are trying to move to a decentralized cross-chain bridge. Again, it is sometimes stuck and vulnerable, but it is easier to use and move large a mounts of funds.

However assets bridges are still in the early stages and cannot meet the demands for Dapp builders. Dapp builders would like to pass messages between different chains and get necessary blockchain information. With such feature, the all chain Dapp building paradigm shifts from distributed and isolated to monolithic and integrated.

The fragmented liquidity can be unified again without the help of CEX.

Where are we

A cross-chain asset bridge is a mechanism that allows for the transfer of assets or tokens between different blockchain networks. This is important because most blockchain networks are isolated from each other and cannot directly exchange assets or tokens.

The concept of cross-chain asset bridges started to gain attention in the last few years, with the launch of projects such as Wormwhole, cbridge and Stargate. These projects aimed to create interoperable blockchain bridges that could exchange assets and tokens seamlessly.

There are several cross-chain bridges available in the market, including Binance Bridge, Celer cBridge, Wormhole, Anyswap, and Stargate

Most of these cross-chain bridges has their own cross-chain message relaying, but they are in their early stages and has function limitation, like latency, two-way communication and broadcast.

Why We Need ZK

While these bridges bring several benefits, such as increased asset productivity and improved user experience, they also pose security risks. Recurring attacks against cross-chain bridges have caused significant financial loss to users, making security a top priority in the development of these systems. The attack cost users more than $1.5 billion.

To address these security concerns, the use of ZK has become increasingly popular in the development of cross-chain bridges. The ZK bridge could be an efficient cross-chain bridge that guarantees strong security without relying on additional trust assumptions. The use of succinct proofs in the ZK bridge not only guarantees the cross-chain transfer completed successfully, but also significantly reduces the cost of on-chain verification. This ensures that users can safely and efficiently transfer assets between different blockchain networks without worrying about the security of their transactions.

Design Architecture

The idea of relaying messages with ZK is relatively simple, but the detailed design can be complex. The overall workflow can be broken down into the following steps:

- Decide what data to pass to the target chain
- Obtain the storage proof(proof some data is in the EVM storage)
- Generate the ZK proof based on the storage proof
- Pass the ZK proof from the source chain to the target chain
- Unroll the ZK Proof on the target chain
- Read the result from the target chain

Storage Proof Generation

Most EVM-compatible chains offer this functionality out of the box. Once users have identified the storage slot, they can use RPC calls to generate the appropriate storage proof.

```jsx
eth_getProof(DATA,ARRAY,QUANTITY|TAG)

DATA, 20 Bytes - address of the account.
ARRAY, 32 Bytes - an array of storage-keys that should be proofed and included. See eth_getStorageAt
QUANTITY|TAG - integer block number, or the string "latest" or "earliest", see the default block parameter
```

Due to the use of Merkle Tree to store accounts and storage, every value can be verified by creating the Merkle Proof.

A Merkle Tree is a type of data structure used in computer science, specifically in cryptography and blockchain technology. It is named after its inventor, Ralph Merkle, and is also known as a binary hash tree. The basic idea behind a Merkle Tree is to divide a large amount of data into smaller parts, hash each part, and then combine the hashes to form a single root hash. This root hash acts as a fingerprint for the entire data set, allowing for efficient and secure verification of the data's integrity.

In blockchain , a Merkle Tree is used to summarize and verify transactions in a block. Each transaction is hashed and added to the tree, and the hashes are combined in a specific way to form a single root hash, which is then added to the block header. This allows for an efficient and secure way to verify the validity of a large number of transactions in a block, without having to verify each transaction individually. In the event that any data in a transaction is changed, the root hash will also change, indicating that the data has been tampered with.

A Merkle proof, also known as a Merkle path, is a cryptographic proof that a specific piece of data is included in a Merkle Tree. A Merkle proof provides a way to verify the authenticity of a transaction or other piece of data without having to download and verify the entire Merkle Tree.

In a Merkle proof, a series of hashes from the bottom of the Merkle Tree to the root hash are provided, along with the specific data being verified. By starting with the specific data and following the hashes up the tree, a recipient can calculate the root hash and compare it to the root hash stored in the block header. If the calculated root hash matches the stored root hash, the recipient can be confident that the specific data is included in the block and has not been altered.

Merkle proofs are an important component in ensuring the efficiency and scalability of blockchain networks. By allowing for the verification of specific data without having to download and verify the entire Merkle Tree, Merkle proofs reduce the amount of data that needs to be transmitted and processed, improving the overall performance of the network.

ZKP for Storage Proof

It is not practical to post the entire storage proof on the target chain because it is too large, around 4kb. Verification of the proof is also expensive. It takes 600k gas to do the verification.

ZKP has some great features for this scenario. ZKP can provide compression and composability. Developers can create a ZKP based on the Merkle Tree Proof. This can greatly reduce the size of the proof and make it easier to verify. Verifying a Plonk takes about 290k gas. A Groth16 verification used about 210k gas.

With composability, developers can even combine different memory proofs into a single ZKP to save resources.

Relay ZKP

To securely relay related commitment, like state root or related ZKP to the destination chain, we need to design a consensus mechanism to do this.

There are three common ways to relay the ZKP:

- Use some messaging protocol to pass the ZKP and get related commitment by OP code
- The related commitment is verified by running the consensus algorithm
- Optimistic MPC relayer: A party of participants will achieve consensus based on some rules. The result can be challenged by anyone.

The main benchmarks for relaying ZKP are:

- Latency
- Gas cost
- Trust
- Off-chain computation overhead

|                        | Latency | Gas cost | Trust | Off-chain computation overhead |
| ---------------------- | ------- | -------- | ----- | ------------------------------ |
| Messaging              | Bad     | Bad      | Good  | Good                           |
| Consensus Validation   | Bad     | Bad      | Good  | Bad                            |
| Optimistic MPC relayer | Good    | Good     | Fine  | Good                           |

Unrolling

After getting the commitment, users on the destination chain can unroll the commitment, like headers or blockhash to access the past states.

Three common ways to do the unrolling are:

- Onchain accumulation
- Onchain compression
- Offchain compression

On-chain accumulation is a method for unrolling commitments in a blockchain network. In this approach, the entire procedure of recreating block headers from a commitment is performed directly on the blockchain. The properly encoded block headers are provided as part of the call data in a transaction, and the blockchain can be trusted to perform the computation. The benefit of this approach is that there is no overhead in terms of proving time, and the latency is low because the proof does not need to be verified outside the blockchain. However, the downside is that the gas cost (the cost to perform the computation on the blockchain) can be high, as the computation can be resource-intensive.

On-chain compression is a method for reducing the amount of data that needs to be stored on a blockchain network. It is used to minimize the storage cost associated with storing large amounts of data on a blockchain. The idea behind on-chain compression is to use a compression algorithm to reduce the size of the data, so that it takes up less space on the blockchain. This can be done by removing redundant or unnecessary information from the data, or by using data structures that are optimized for space efficiency. The compressed data is then stored on the blockchain, and can be decompressed when it is needed for use.

On-chain compression has the advantage of reducing storage costs and improving the scalability of the blockchain network. However, it also has some disadvantages. For example, the process of compressing and decompressing the data can be computationally expensive, which can increase the latency of the blockchain. Additionally, the compression algorithm used may have a negative impact on the security of the data, as it may be vulnerable to tampering or attack.

Similarly, off-chain compression compresses the data and stores it off-chain.

Here is a comparison table for these three methods:

|                      | Prover overhead | Gas cost | Storage cost |
| -------------------- | --------------- | -------- | ------------ |
| Onchain accumulation | Good            | Bad      | Bad          |
| Onchain compression  | Fine            | Good     | Good         |
| Offchain compression | Fine            | Nice     | Nice         |

Related Projects

Many players join this field with the hope to improve the interoperability of different chains and decrease the potential hacking risks.

The most popular and promising projects in the fields are the following:

- Succinct Labs
- Lagrange
- zkBridge
- Herodotous

Succinct Labs uses light client approaches. It uses a light client to verify the consensus from the source chain to the consensus layer of the target chain. ZKP is used to generate the proof of consensus.

Lagrange Labs builds non-interactive cross-chain state proofs. The Lagrange Attestation Network is responsible for creating state roots. Each Lagrange Node contains a portion of a sharded private key that it uses to attest to the state of a particular chain. Each State Root is a threshold signed Verkle Root that can be used to prove the state of a particular chain at a particular time. State Roots are fully generalised and can be used within State Proofs to prove the current state of any contract or wallet in a chain.

Herodotus uses storage proofs with ZKP to provide smart contracts with synchronous access to on-chain data coming from other Ethereum layers. It has an MPC Optimistic Relayer to relay the commitment. The off-chain compression unrolls the relayed blockchain headers off-chain and creates the proof.

zkBridge uses an MPC relay network to generate and relay the ZKP of block headers to the target chains. It uses deVrigo and recursive proof to achieve very fast proof times, scarfing the complexity in MPC computation.

Latency and cost would be the major benchmarks for different projects.

Here is a detailed comparison for these projects.

|               | Lagrange                                                                | Herodotus                                                                             | Succint Labs                  | zkBridge                              |
| ------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | ----------------------------- | ------------------------------------- |
| Core Tech     | MPC, TSS, zkSNARKs                                                      | MPC, zkSNARKs                                                                         | zkSNARKs, Light Clients, TSS  | zkSNARKs, MPC, Light Client           |
| Security      | MPC, TSS                                                                | Optimistic Relay                                                                      | zkSNARKs, Light Client, TSS   | Relay Netowrk, zkSNARKs, Light Client |
| Communication | N to 1                                                                  | 1 to 1                                                                                | 1 to 1                        | 1 to 1                                |
| Advantage     | Easier to implement in the short term. N to 1 communication is possible | Easier to implement in the short term. Optimistic Security is very pragmatic approach | Using light clients is secure | Virgo + Plonk                         |
| Investor      | 1kx                                                                     | Fabric, Geometry                                                                      | Paradigm                      | Polychain, Binance Labs               |

Application Scenario

With the ZK-based cross-chain message relay protocol, developers can easily extend their applications to different blockchains.

In the past, the development scheme was focused on one chain. When extending to another chain, the application has to be deployed again.

With the ZK-based cross-chain message relay protocol, there would be a paradigm shift from a single-chain application to a cross-chain application. Large projects can easily be extended to different chains. This would have a similar effect to globalisation. We would like to see more international companies or giant cross-chain dapps.

With low latency/real-time and low cost cross-chain message relay protocol, it opens up the market with many possibilities. The main application would be DeFi, DID and governance.

DeFi

DeFi can benefit a lot from this. The cross-chain message relay protocol can help DeFi products unit the liquidity from different chains.

DEXs, cross-chain swaps and aggregators can provide better user experience, lower slippage and more liquidity on trading pairs. There would be one united liquidity pool for one trading pairs on different chains. There would be smaller price difference between different chains.

On-chain exchanges can obviously gather more liquidity and provide CEX comparable user experience.

Yield farmings can have more flexible strategy. They can now reach different yield opportunities on different chains.

Lending protocols can coordinate with more DeFi protocols on different chains and accepts deposits on more chains.

On-chain derivatives suffers a lot on liquidity. With safe cross-chain communication, it can reach more potential customers on different chains and gathering more liquidity. This could provide better trading experience.

Money management can access more assets on different chains. They can also access derivatives from different chains. This makes more investment strategies available to portfolio manager.

DID

With the ability to cross-chain message delivery, the on-chain credit score provider can access user activity on different chains and provide more accurate and detailed results. The credit score can also be used on different chains through different protocols. As more and more credit score providers compete directly with each other, it would be easier for them to form a unified credit score standard for better composability.

Similarly, KYC can also have better composability. The KYC result can be passed between different chains. Users do not have to repeat the redundant KYC process over and over again.

Governance

As more cross-chain applications emerge, their governance tokens will be scattered across different chains. With cross-chain communication, token holders on different chains can vote on the same proposals at the same time. This would make governance tokens easier to use.

Conclusion

ZK offers better security for cross-chain communication. Cross-chain communication protocols are promising. They allow the dapp to access data on different chains. The DeFi, DID and governance sectors can all benefit from the development of cross-chain dapps.

Cross-chain communication protocols are still in their infancy. Developers are working hard on these protocols. Developers are exploring the best schema to build cross-chain dapps. We will see the impact of cross-chain dapps in the next few years. It would be as impactful as globalisation as a cross-chain communication protocol connecting different blockchains.

Source

[https://medium.com/@ingonyama/bridging-the-multichain-universe-with-zero-knowledge-proofs-6157464fbc86](https://medium.com/@ingonyama/bridging-the-multichain-universe-with-zero-knowledge-proofs-6157464fbc86)

[https://www.youtube.com/watch?v=8mE_0qZNVjo](https://www.youtube.com/watch?v=8mE_0qZNVjo)