---
title: Registry Contract for Cross-Chain NFT Mapping
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

<!--
  The Abstract is a multi-sentence (short paragraph) technical summary. This should be a very terse and human-readable version of the specification section. Someone should be able to read only the abstract to get the gist of what this specification does.

  TODO: Remove this comment before submitting
-->

## Motivation

<!--
  This section is optional.

  The motivation section should include a description of any nontrivial problems the EIP solves. It should not describe how the EIP solves those problems, unless it is not immediately obvious. It should not describe why the EIP should be made into a standard, unless it is not immediately obvious.

  With a few exceptions, external links are not allowed. If you feel that a particular resource would demonstrate a compelling case for your EIP, then save it as a printer-friendly PDF, put it in the assets folder, and link to that copy.

  TODO: Remove this comment before submitting
-->

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

```solidity
interface ITokenRegistry {

    function getSecondaryTokenAddress(
        address primaryAddr, LibChain.Chain memory secondaryChain
    ) external view returns (address secondaryAddr);

    function setSecondaryTokenAddress(
        address primaryAddr, LibChain.Chain memory secondaryChain, address newSecondaryAddr
    ) external;

}
```

## Rationale

1. Explain how the one-way mapping in Ethereum combined with that in the secondary chains can form a complete registry system.

2. Discuss about the use of `owner()` (defined in EIP-173) for access check to register.

3. Discuss about whether we should check the address registered is indeed an NFT contract.

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
