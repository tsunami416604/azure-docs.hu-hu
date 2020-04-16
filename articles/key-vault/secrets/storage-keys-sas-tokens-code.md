---
title: Azure Key Vault felügyelt tárfiók – PowerShell-verzió
description: A felügyelt tárfiók funkció zökkenőmentes integrációt biztosít az Azure Key Vault és egy Azure storage-fiók között.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431266"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Közös hozzáférésű jogosultságkód lexikális elemeinek beolvasása kódból

A tárfiókot a kulcstartóban lévő [megosztott hozzáférésű aláírás-jogkivonatokkal](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) kezelheti. Ez a cikk példákat tartalmaz a C# kód, amely lekéri a SAS-jogkivonatot, és műveleteket hajt végre vele.  A SAS-jogkivonatok létrehozásáról és tárolásáról a [Tárfiók-kulcsok kezelése a Key Vaultés az Azure CLI segítségével,](overview-storage-keys.md) illetve a [Tárfiók-kulcsok kezelése key vaulttal és Az Azure PowerShell használatával](overview-storage-keys-powershell.md)című témakörben talál további információt.

## <a name="code-samples"></a>Kódminták

Ebben a példában a kód lekéri a SAS-jogkivonatot a key vaultból, egy új tárfiókot hoz létre, és létrehoz egy új Blob szolgáltatás ügyfelet.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Ha a megosztott hozzáférésű aláírás-jogkivonat hamarosan lejár, lehívhatja a megosztott hozzáférésű aláírás jogkivonatot a key vaultból, és frissítheti a kódot.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [kezelheti a tárfiók kulcsait a Key Vault és az Azure CLI](overview-storage-keys.md) vagy az [Azure PowerShell](overview-storage-keys-powershell.md)segítségével.
- Lásd: [Felügyelt tárfiókkulcs-minták](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell-hivatkozás](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
