---
title: Cross-Chain NFT Mapping Registry Contract
description: A universal registry smart contract where an NFT contract owner can register its mapping contract for each secondary blockchain.
author: Zheng Han <robin@comoco.xyz>, Ming Jiang <ming@comoco.xyz>, Fan Yang <jack@comoco.xyz>
discussions-to: <URL>
status: Draft
type: Standards Track
category: ERC
created: 2023-02-28
requires: EIP-173
---

## Abstract

This standard defines a registry where an NFT contract on Ethereum can register its mapping contract for each secondary blockchain, be it L1 or L2.

Through this way, developers are able to publish their NFTs on Ethereum while moving sophisticated application like IP licensing and derivative generation onto the secondary blockchain where it is more affordable and scalable.

Only the owner of an NFT contract on Ethereum can modify the mapping information for this contract in the registry. Anyone can query the registry to find out the deputy contract on each secondary blockchain for an Ethereum NFT contract.

## Motivation

This all started from the idea of implementing an IP licensing solution for NFTs. As we know, many NFT projects have generated derivative NFT collections on Ethereum, such as Mutant Ape Yachet Club derived from Bored Ape Yachet Club, and Space Doodles derived from Doodles. However, those collections are only called "derivative" but no actual derivative relations are ever recorded on-chain. We need to extend the NFT contract to add derivative relations as new metadata.

Furthermore, if we want to implement a full IP licensing solution, more licensing metadata along with the required enforcing logic will also be needed. For example, an NFT token holder may want to sell a derivative right to the public but doesn't want the derivative token to be tradable; or the holder may want to rent out the token and charge by the use of it counted via oracle. The computation can be even more costly when we consider the fact that new tokens can be derived from multiple tokens and derivatives can have derivatives and so on.

Fortunately, many secondary blockchains, be it L1 or L2 to Ethereum, have emerged to be more affordable and scalable making the application logic we need feasible. Therefore, what we decide is to mirror the NFTs from Ethereum to the secondary blockchain and perform all the operations of derivative extension and licensing logic over there.

That leads to the idea of this design document, by standardizing a universal registry smart contract to record the mirroring relations. Copycats could appear on secondary blockchains, but the registry is there to tell which ones are the authentic deputy contracts endorsed by the original NFT project on Ethereum.

Once we implement the aforementioned IP licensing solution, it is also straightforward to verify whether any derivative NFT token generated on a secondary blockchain is officially licensed by tokens from some Ethereum NFT collections, by tracing along the derivative relations on the secondary blockchain to the root contracts and check them against the Ethereum NFT contracts in the registry.

## Specification

The following shows the complete code of the registry contract.

```solidity
pragma solidity ^0.8.0;

struct Chain {
    string network;
    uint256 id;
}

interface IOwner {
    function owner() external view returns (address owner);
}

contract TokenRegistry {

    mapping(address => mapping(string => mapping(uint256 => address))) private _secondaryTokenAddrRegistry;

    event SecondaryTokenAddressChanged(
        address indexed primaryAddr,
        Chain indexed secondaryChain,
        address indexed newSecondaryAddr
    );

    modifier validAddress(address addr) {
        require(addr != address(0), "Invalid address");
        _;
    }

    modifier onlyContractOwner(address addr) {
        require(msg.sender == IOwner(addr).owner());
        _;
    }

    function getSecondaryTokenAddress(
        address primaryAddr,
        Chain memory secondaryChain
    ) external view validAddress(primaryAddr) returns (address) {
        return _secondaryTokenAddrRegistry[primaryAddr][secondaryChain.network][secondaryChain.id];
    }

    function setSecondaryTokenAddress(
        address primaryAddr,
        Chain memory secondaryChain,
        address newSecondaryAddr
    ) external validAddress(primaryAddr) onlyContractOwner(primaryAddr) {
        _secondaryTokenAddrRegistry[primaryAddr][secondaryChain.network][secondaryChain.id] = newSecondaryAddr;
        emit SecondaryTokenAddressChanged(primaryAddr, secondaryChain, newSecondaryAddr);
    }

}
```

## Rationale

1. Discuss about using type `string` for network in the structure of Chain instead of `uint256`. (Answer: Not all secondary blockchains have an integer ID for their network, but true for chain ID.)

2. Discuss about using `owner()` (defined in EIP-173) for access control instead of the NFT contract itself. (Answer: There is no way for existing deployed NFT contracts to register sending from the contract itself; in reality, contract owner is usually the operating team of the NFT.)

3. Discuss about whether we should check the address registered is indeed an NFT contract. (Answer: What is an NFT contract is not standardized, as we see ERC1155 and more to come out in addition to ERC721; it is also difficult to verify that for addresses in the secondary blockchain.)

## Backwards Compatibility

No backwards compatibility issues found.

## Test Cases

Test cases available in the repository: [comoco-labs/ethereum-contracts](https://github.com/comoco-labs/ethereum-contracts)

## Reference Implementation

Reference implementation available in the repository: [comoco-labs/ethereum-contracts](https://github.com/comoco-labs/ethereum-contracts)

## Security Considerations

No security considerations found.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).
