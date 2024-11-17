
# Summary
We're running into an issue when calling `safe4337Pack.createTransaction({ transactions: [transaction]})`

When we initialize Safe4337Pack with the pimlico bundler and paymaster URLs, Safe4337Pack uses `eth_estimateUserOperationGas` to estimate gas, which returns a `MethodNotFoundRpcError` from pimlico.

When we switch the paymaster and bundler URL to alchemy, Safe4337Pack uses `pimlico_getUserOperationGasPrice` to estimate gas, which returns an `Unsupported method` from alchemy.

It seems that the calls are reversed based on the provided configuration? Below are the configurations and return values for each api call.


# Using Pimlico paymaster + bundler

Our Safe4337Pack initialization values for pimlico paymaster and bundler are as follows:
```typescript
        const safe4337Pack = await Safe4337Pack.init({
            provider: process.env.RPC_URL, "https://base-sepolia.g.alchemy.com/v2/G6Kjp2b01jHSPtNexytKoX8IdW7CuG6d"
            signer: {
                rawId: hexRawId,
                coordinates,
            },
            bundlerUrl: `https://api.pimlico.io/v1/${baseSepolia.id}/rpc?apikey=${process.env.PIMLICO_API_KEY}`,
            paymasterOptions: {
                isSponsored: true,
                paymasterUrl: `https://api.pimlico.io/v2/${baseSepolia.id}/rpc?apikey=${process.env.PIMLICO_API_KEY}`,
                sponsorshipPolicyId: process.env.PIMLICO_SPONSORSHIP_POLICY_ID,
            },
            options: {
                owners: [],
                threshold: 1,
            }
        });
```

When we call `safe4337Pack.createTransaction({ transactions: [transaction]})` using the above Pimlico config we get:
```json
{
    "error": "Unable to get transfer operation hash: MethodNotFoundRpcError: The method \"eth_estimateUserOperationGas\" does not exist / is not available.",
    "details": {
      "url": "https://api.pimlico.io/v2/84532/rpc?apikey=pim_XMjZdbusjikr8PWAR5bj29",
      "request_body": {
        "method": "eth_estimateUserOperationGas",
        "params": [
          {
            "sender": "0x26d103df3DD9f25EebFe2E7fB607d8Ab212f9202",
            "nonce": "0x0",
            "initCode": "0x4e1DCf7AD4e460CfD30791CCC4F9c8a4f820ec671688f0b900000000000000000000000029fcb43b46531bca003ddc8fcb67ffe91900c7620000000000000000000000000000000000000000000000000000000000000060518ef47fb8ae23d67860abf18f38940c700bcb0cb8c1b9b5a0da66500e3793410000000000000000000000000000000000000000000000000000000000000344b63e800d0000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000000100000000000000000000000038869bf66a61cf6bdb996a6ae40d5853fd43b5260000000000000000000000000000000000000000000000000000000000000140000000000000000000000000a581c4a4db7175302464ff3c06380bc3270b4037000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000094a4f6affbd8975951142c3999aeab7ecee555c200000000000000000000000000000000000000000000000000000000000001c48d80ff0a00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000172018ecd4ec46d4d2a6b64fe960b3d64e8b94b2234eb000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000648d0dc49f00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000001000000000000000000000000a581c4a4db7175302464ff3c06380bc3270b40370194a4f6affbd8975951142c3999aeab7ecee555c2000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000640dd9692f880a7610285608997b35f74ace96554012bfb5836dc9f2a11fd540f4803c1ed6542b8ac7a76dae66b1bb420c5ae63da05cae11690153d94cb55d7b49dfa297ab000000000000000000000000a86e0054c51e4894d88762a017ecc5e5235f5dba00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
            "callData": "0x7bb37428000000000000000000000000036cbd53842c5426634e7929541ec2318f3dcf7e00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000450xa9059cbb0000000000000000000000005aaeb6053f3e94c9b9a09f33669435e7ef1beaed000000000000000000000000000000000000000000000000000000000001fbd0000000000000000000000000000000000000000000000000000000",
            "callGasLimit": "0x1",
            "verificationGasLimit": "0x1",
            "preVerificationGas": "0x1",
            "maxFeePerGas": "0x149b0e",
            "maxPriorityFeePerGas": "0x149970",
            "paymasterAndData": "0x",
            "signature": "0x00000000000000000000000000000000000000000000000094a4F6affBd8975951142c3999aEAB7ecee555c200000000000000000000000000000000000000000000000000000000000000410000000000000000000000000000000000000000000000000000000000000001e0000000000000000000000000000000000000000000000000000000000000008000000000000000000000000000000000000000000000000000000000000000e0ececececececececececececececececececececececececececececececececd5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5ad5af0000000000000000000000000000000000000000000000000000000000000025fefefefefefefefefefefefefefefefefefefefefefefefefefefefefefefefe04fefefefe00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000c2226f726967696e223a2268747470733a2f2f736166652e676c6f62616c222c2270616464696e67223a225468697320706164732074686520636c69656e74446174614a534f4e20736f20746861742077652063616e206c6561766520726f6f6d20666f72206164646974696f6e616c20696d706c656d656e746174696f6e207370656369666963206669656c647320666f722061206d6f72652061636375726174652027707265566572696669636174696f6e4761732720657374696d6174652e22000000000000000000000000000000000000000000000000000000000000"
          },
          "0x5FF137D4b0FDCD49DcA30c7CF57E578a026d2789"
        ]
      },
      "validation_error": "not valid hex data at \"params[0].callData\"",
      "version": "viem@2.21.35"
    }
  }

```
# Using the Alchemy paymaster + bundler

Our Safe4337Pack initialization values for pimlico paymaster and bundler are as follows:
```typescript
        const safe4337Pack = await Safe4337Pack.init({
            provider: process.env.RPC_URL, //"https://base-sepolia.g.alchemy.com/v2/G6Kjp2b01jHSPtNexytKoX8IdW7CuG6d"
            signer: {
                rawId: hexRawId,
                coordinates,
            },
            bundlerUrl: process.env.RPC_URL, //"https://base-sepolia.g.alchemy.com/v2/G6Kjp2b01jHSPtNexytKoX8IdW7CuG6d"
            paymasterOptions: {
                isSponsored: true,
                paymasterUrl: process.env.RPC_URL, // "https://base-sepolia.g.alchemy.com/v2/G6Kjp2b01jHSPtNexytKoX8IdW7CuG6d"
                sponsorshipPolicyId: process.env.ALCHEMY_GAS_SPONSORSHIP_POLICY_ID,
            },
            options: {
                owners: [],
                threshold: 1,
            }
        });
```

When we call `safe4337Pack.createTransaction({ transactions: [transaction]})` using the above Alchemy config we get:

```json

{
    "error": "Unable to get transfer operation hash: HttpRequestError: HTTP request failed.",
    "details": {
        "status": 400,
        "url": "https://base-sepolia.g.alchemy.com/v2/G6Kjp2b01jHSPtNexytKoX8IdW7CuG6d",
        "request_body": {
        "method": "pimlico_getUserOperationGasPrice"
        },
        "error_details": {
        "code": -32600,
        "message": "Unsupported method: pimlico_getUserOperationGasPrice. See available methods at https://docs.alchemy.com/alchemy/documentation/apis"
        }
    },
    "version": "viem@2.21.35"
}

```

# Relevant package versions
This issue is coming up with the latest versions of the safe-global packages as of today (11/15/2024)
```json
    "@safe-global/protocol-kit": "^5.0.4",
    "@safe-global/relay-kit": "^3.2.4",
    "@safe-global/safe-modules-deployments": "^2.2.4",
```

We've also tried reverting to this set of versions which worked under the same configuration in September, but found the same exact issue:
```json
    "@safe-global/protocol-kit": "^4.1.0",
    "@safe-global/relay-kit": "^3.1.0",
    "@safe-global/safe-modules-deployments": "^2.2.1",
```