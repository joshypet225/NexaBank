# Data Sovereignty and Governance

## Objective

Treat Nigerian data residency and controlled data movement as
architectural requirements.

The authoritative banking data plane remains within the Nigerian
sovereign boundary.

## Classification

A practical implementation should classify information before it crosses
trust or geographic boundaries.

  -----------------------------------------------------------------------
  Classification          Example                 Default external
                                                  handling
  ----------------------- ----------------------- -----------------------
  Restricted              Customer identity,      Deny unless explicitly
                          transactions,           approved
                          credentials             

  Confidential            Internal                Controlled
                          operational/business    
                          data                    

  Internal                Non-public operational  Controlled
                          information             

  Public                  Approved public         Allowed subject to
                          information             policy
  -----------------------------------------------------------------------

The institution must define the authoritative classification scheme.

## Egress decision

``` text
Data
 |
 v
Classification
 |
 v
Policy
 |
 +---- Deny ----> Block + Alert
 |
 +---- Allow ---> Transform if required
                    |
                    v
              Approved destination
```

## External processing

Before data is sent externally, evaluate data category, purpose,
destination, legal basis, contractual controls, provider commitments,
residency, retention, logging, sub-processors, and applicable transfer
mechanisms.

Encryption alone is not treated as automatic authorization for
cross-border processing.

## Data minimization

Where external processing is approved, send only what is necessary.
Possible transformations include tokenization, pseudonymization,
redaction, aggregation, or metadata-only telemetry.

## Sovereignty boundary

Sovereignty includes control over data, identity, keys, network paths,
administration, backup, audit evidence, and recovery---not only physical
geography.
