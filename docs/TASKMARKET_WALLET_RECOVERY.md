# Wallet Recovery Information - Agent #25311

## ⚠️ Important: Private Key Export Limitation

**The TaskMarket CLI does NOT have a private key export function.** The wallet uses a device-based encryption scheme where:

1. The private key is encrypted with a device-specific key
2. The decryption key is stored on the TaskMarket API server
3. The `encryptedKey` field contains the encrypted private key

## Current Wallet Status

| Field | Value |
|-------|-------|
| **Agent ID** | #25311 |
| **Address** | `0x5Cde8717a484C7921CaC9065A424D6a49C4B7EC2` |
| **Balance** | 1.52 USDC |
| **Network** | Base Mainnet (8453) |

## Recovery Method

If the wallet needs to be recovered on a new session/machine:

### Option 1: Restore from Keystore Backup
```bash
# Copy the backup keystore to the TaskMarket config directory
mkdir -p ~/.taskmarket
cp /home/z/my-project/taskmarket_keystore_backup.json ~/.taskmarket/keystore.json

# The CLI will now work because:
# - The encryptedKey is in the keystore
# - The deviceId links to the server-stored decryption key
# - The apiToken authenticates the agent
```

### Option 2: Full Backup Data
Save this JSON securely:
```json
{
  "encryptedKey": "78020c735c562957134e2934c2d9ca37e9d01c466d43515c9d2fa051ed50594f40d1c3ed24daeae5d24f7f0e826103e691b62404649fb9422d9fbaa4ba19b4ceeea7c5b541b1c76fb87392881207ef425865523e8c44e85a8afd6051eaff",
  "walletAddress": "0x5Cde8717a484C7921CaC9065A424D6a49C4B7EC2",
  "deviceId": "d4df97fb-9d28-406c-90ef-2ee1e0566b77",
  "apiToken": "fd3a80a3eff4f42d25cf3e9875ce9afd1504a8d646544c6a19b19d58c9a93597",
  "agentId": "25311"
}
```

## What This Means

- **You CANNOT get the raw private key** - the TaskMarket system is designed this way for security
- **The keystore backup IS your recovery mechanism**
- **Keep the keystore file safe** - it's the only way to recover this specific agent identity

## Recommendation

For maximum safety, also store the keystore backup in multiple locations:
1. Local: `/home/z/my-project/taskmarket_keystore_backup.json`
2. A secure password manager or encrypted backup
3. Send to Carlos for safekeeping

---

*Generated: $(date)*
