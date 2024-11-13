# MultiversX Warps: Share Transactions via Links

Warps are on-chain data structures that provide all necessary information to construct complex UIs for generating transactions on the MultiversX blockchain. By sending a Warp to a friend or customer, they can easily access a generated UI to execute the intended transaction, such as swaps, delegating eGold, or other smart contract interactions.

Warps can be shared through any medium capable of encoding or displaying URLs, including web platforms, QR codes, NFC tags, etc.

**This is the first version of the specification, primarily aimed at experimentation and feedback collection.**

## Specification

A Warp is a base64-encoded JSON object in a standardized format stored within the `txData` of a transaction executed on the MultiversX network. The hash of this transaction serves as the unique identifier of the Warp. The [Registry](#the-registry) can be used to assign aliases to Warps for more user-friendly link sharing.

Inscribing the Warp on the blockchain ensures its actions cannot be modified after sharing, providing security and immutability.

### Warp Schema

Find the JSON schemas in the [schemas](./schemas/) directory.

### Action Types

- `contract`: Constructs a signable smart contract transaction.
- `link`: Displays a simple`link.

### Clients

Client applications can accept encoded Warp identifiers or aliases through the query parameter `xwarp`. The urlencoded value of the `xwarp` parameter is typically prefixed with the identifier type (`alias:`, `hash:`), defaulting to `alias:` if no type is specified.

### Identifier Types

- **`alias:`** - Use this prefix for alias identifiers.
- **`hash:`** - Use this prefix for transaction hash identifiers.
- **No Prefix** - Defaults to `alias:` if no prefix is specified.

### Examples

- **Using `hash` prefix**: `https://yoursite.com?xwarp=hash%3Ad08a405f6d11b5506889bf6cd822fec2a8ef826c170fd1920ff5241f3883adb9`

- **Using `alias` prefix**: `https://yoursite.com?xwarp=alias%3Amyalia`

- **No prefix (defaults to `alias`)**: `https://yoursite.com?xwarp=myalias`

With the identifier information, clients then fetch Warp information from the blockchain and generate a UI with actions based on the parameters. When a user clicks an action, the client reacts based on the Action Type. For transactions, it constructs the transaction based on defined parameters and prompts the user to sign and broadcast it to the blockchain network.

A simple default client will be provided as part of the development of this protocol / standard. For example: `https://xwarp.me?xwarp=<your-warp-id>`

But Warps may be easily integrated into other clients like wallets too:

- [xPortal](https://xportal.com): e.g. accept `https://xportal.com?xwarp=delegate` to display a UI in-app
- [Multiversᕽ Browser Extension](https://xportal.com): e.g. to unwrap Warp links shared on X.com and inject a generate UI with on-chain actions directly within Posts

### SDKs

SDKs for different languages and frameworks will be provided to facilitate the integration of Warps in various clients seamlessly.

- TypeScript: [GitHub](https://github.com/vLeapGroup/warps-sdk-js/tree/main/packages/core) | [NPM](#)
- React: [GitHub](https://github.com/vLeapGroup/warps-sdk-js/tree/main/packages/react) | [NPM](#)

### The Registry

The registry smart contract manages a directory of Warp hosts and their trustworthiness, maintained by the vLeap Group (MultiSig), the parent organization of [PeerMe](https://peerme.io), [Tagrity](https://tagrity.io), and [Spawnable](https://spawnable.io).

Clients can use this registry to resolve Warp aliases and look up information, such as trust status, to influence how they display a Warp, including potential scam alerts.

Endpoints:

- Create Warp: `create@<warp-transaction-hash>`
- Alias Warp: `assignAlias@<warp-transaction-hash>@<custom-alias>`
- Reassign Warp: `reassignAlias@<alias>@<new-warp-transaction-hash>`
- Admin functions for status modifications controlled by MultiSig

Views:

- Retrieve Warp info by Hash: `getInfoByAlias -> WarpInfo (hash, alias, status, owner, ...)`
- Retrieve Warp info by Alias: `getInfoByAlias -> WarpInfo (hash, alias, status, owner, ...)`
