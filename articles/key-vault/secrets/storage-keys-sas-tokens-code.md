---
title: Közös hozzáférésű aláírási tokenek beolvasása a kódban | Azure Key Vault
description: A felügyelt tár fiók funkciója zökkenőmentes integrációt biztosít Azure Key Vault és egy Azure Storage-fiók között.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584913"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Közös hozzáférésű jogosultságkód lexikális elemeinek beolvasása kódból

A Storage-fiókját a kulcstartóban tárolt közös hozzáférésű aláírás (SAS) jogkivonatokkal kezelheti. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz sas használatával](../../storage/common/storage-sas-overview.md).

Ez a cikk olyan .NET-kóddal kapcsolatos példákat tartalmaz, amely egy SAS-tokent olvas be, és műveleteket végez vele. Az SAS-tokenek létrehozásával és tárolásával kapcsolatos információkért lásd: a [Storage-fiók kulcsainak kezelése a Key Vault és az Azure CLI](overview-storage-keys.md) -vel, illetve a [Storage-fiókok kulcsainak kezelése Key Vault és Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kódminták

Ebben a példában a kód beolvas egy SAS-jogkivonatot a kulcstartóból, ezzel új Storage-fiókot hoz létre, és létrehoz egy új Blob service ügyfelet.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Ha a megosztott hozzáférési aláírási token hamarosan lejár, a közös hozzáférésű aláírási tokent a kulcstartóból kérheti le, és frissítheti a kódot.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [biztosíthat korlátozott hozzáférést az Azure Storage-erőforrásokhoz sas használatával](../../storage/common/storage-sas-overview.md).
- Ismerje meg, hogyan [kezelheti a Storage-fiókok kulcsait Key Vault és az Azure CLI](overview-storage-keys.md) vagy [Azure PowerShell](overview-storage-keys-powershell.md)használatával.
- Lásd: [felügyelt Storage-fiók kulcsainak mintái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
