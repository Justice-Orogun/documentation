---
layout: ../../../../layouts/MainLayout.astro
section: nodeOperator
date: Last Modified
title: "CBOR Parse Task"
permalink: "docs/jobs/task-types/cborparse/"
---

CBOR Parse tasks parse a CBOR payload, typically as part of a Direct Request workflow. In Direct Request, a user makes an on-chain request using a `ChainlinkClient` contract, which encodes the request parameters as CBOR. See below for an example.

**Parameters**

- `data`: A byte array containing the CBOR payload.
- `mode`: An optional parameter that specifies how to parse the incoming CBOR. The default mode is `diet`, which expects the input to be a map. Set the mode to `standard` to pass literal values through "as-is". Empty inputs return nil.

**Outputs**

A map containing the request parameters. Parameters can be individually accessed using `$(dot.accessors)`.

**Example**

```toml
// First, we parse the request log and the CBOR payload inside of it
decode_log  [type="ethabidecodelog"
             data="$(jobRun.logData)"
             topics="$(jobRun.logTopics)"
             abi="SomeContractEvent(bytes32 requestID, bytes cborPayload)"]

decode_cbor [type="cborparse"
             data="$(decode_log.cborPayload)"]

// Then, we use the decoded request parameters to make an HTTP fetch
fetch [type="http" url="$(decode_cbor.fetchURL)" method=GET]
parse [type="jsonparse" path="$(decode_cbor.jsonPath)" data="$(fetch)"]

// ... etc ...
```

See the [Direct Request page](/chainlink-nodes/oracle-jobs/job-types/direct_request) for a more comprehensive example.
