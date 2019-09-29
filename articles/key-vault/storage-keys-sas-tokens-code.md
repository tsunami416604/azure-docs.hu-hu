---
title: Azure Key Vault felügyelt Storage-fiók – PowerShell-verzió
description: A felügyelt tár fiók funkciója zökkenőmentes integrációt biztosít Azure Key Vault és egy Azure Storage-fiók között.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673457"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Közös hozzáférésű jogosultságkód lexikális elemeinek beolvasása kódból

A Storage-fiókját a kulcstartó [közös hozzáférés-aláírási jogkivonatával](../storage/common/storage-dotnet-shared-access-signature-part-1.md) kezelheti. Ez a cikk példákat C# tartalmaz arra a kódra, amely egy sas-jogkivonatot olvas be, és műveleteket végez vele.  Az SAS-tokenek létrehozásával és tárolásával kapcsolatos információkért lásd: a [Storage-fiók kulcsainak kezelése a Key Vault és az Azure CLI](key-vault-ovw-storage-keys.md) -vel, illetve a [Storage-fiókok kulcsainak kezelése Key Vault és Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kódminták

Ebben a példában a kód beolvas egy SAS-jogkivonatot a kulcstartóból, ezzel új Storage-fiókot hoz létre, és létrehoz egy új Blob service ügyfelet.  

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

Ha a megosztott hozzáférési aláírási token hamarosan lejár, a közös hozzáférésű aláírási tokent a kulcstartóból kérheti le, és frissítheti a kódot.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [kezelheti a Storage-fiókok kulcsait Key Vault és az Azure CLI](key-vault-ovw-storage-keys.md) vagy [Azure PowerShell](key-vault-overview-storage-keys-powershell.md)használatával.
- Lásd: [felügyelt Storage-fiók kulcsainak mintái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [PowerShell-útmutató Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
