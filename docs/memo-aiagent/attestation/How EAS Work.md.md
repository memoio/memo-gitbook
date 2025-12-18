# How EAS Works

EAS primarily implements on-chain and off-chain attestations through the following two main contracts, which are responsible for schema registration and attestation creation, respectively:

| Contract       | Function                                                     | Main On-Chain Methods           |
| :------------- | :----------------------------------------------------------- | :------------------------------ |
| SchemaRegistry | Registers an attestation schema, which describes the structured data included in the attestation | register()                      |
| EAS            | Creates an attestation, specifying the schema and including corresponding data during creation | attest(); attestByDelegation(); |

## SchemaRegistry

A schema is a template for your attestation, defining the format and structure of the attestation you will generate. Before creating an attestation, you can first check if there is a similar schema that meets your needs. If not, you can register a schema based on your requirements. For example, if you wanted to a vote, you might have a "eventName" and a "location" and "startTime" and "endTime".

The general process for registering a schema is as follows:

1. Check if a similar schema is available for use.
2. Define the structure of your schema.
3. Use the `register`method to register your schema.
4. After successful registration, the contract returns a unique ID for your schema.
5. You can now create attestations using your schema.

## EAS

Once a schema is determined, you can proceed to create an attestation:

1. Construct the attestation according to the data structure required by the schema.
2. Digitally sign the structured data in the attestation to create on-chain and off-chain attestations.
3. Pass the attestation and the unique ID of the schema to the EAS contract.
4. EAS validates the structured data against the schema and stores the attestation.
5. The attestation is now recorded on the blockchain and can be verified by anyone!