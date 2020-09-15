---
title: Közös hozzáférésű aláírási tokenek beolvasása a kódban | Azure Key Vault
description: A felügyelt tár fiók funkciója zökkenőmentes integrációt biztosít Azure Key Vault és egy Azure Storage-fiók között. Ez a példa a .NET-hez készült Azure SDK-t használja az SAS-tokenek kezeléséhez.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0f81ffb5279e10c71f7d7cccfb6b738bc12e5cf4
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086776"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>SAS-definíció létrehozása és közös hozzáférésű aláírási jogkivonatok beolvasása a kódban

A Storage-fiókját a kulcstartóban tárolt közös hozzáférésű aláírás (SAS) jogkivonatokkal kezelheti. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz sas használatával](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Azt javasoljuk, hogy [szerepköralapú Access Control (RBAC)](../../storage/common/storage-auth-aad.md) használatával biztonságossá tegye a Storage-fiókját a kiváló biztonság és a könnyű használat érdekében a megosztott kulcs engedélyezésével.

Ez a cikk olyan .NET-kód mintáit tartalmazza, amely létrehoz egy SAS-definíciót, és SAS-jogkivonatokat olvas be. Tekintse meg a [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) -mintát a teljes részletekért, beleértve a generált ügyfelet Key Vault által felügyelt Storage-fiókokhoz. Az SAS-tokenek létrehozásával és tárolásával kapcsolatos információkért lásd: a [Storage-fiók kulcsainak kezelése a Key Vault és az Azure CLI](overview-storage-keys.md) -vel, illetve a [Storage-fiókok kulcsainak kezelése Key Vault és Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kódminták

A következő példában létrehozunk egy SAS-sablont:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

A sablon használatával létrehozhatunk egy SAS-definíciót a következő használatával 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Miután létrejött az SAS-definíció, lekérheti az a-t használó SAS-tokeneket, például a titkokat `SecretClient` . A titkos nevet a Storage-fiók nevével kell megadnia, majd egy kötőjelet:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Ha a közös hozzáférésű aláírási jogkivonat hamarosan lejár, akkor újra lekérheti ugyanezt a titkot egy új létrehozásához.

A Key Vault SAS-tokenről az Azure Storage-szolgáltatásokhoz való hozzáférésre vonatkozó beolvasással kapcsolatos útmutatóért lásd: [fiók sas használata a blob Service eléréséhez](https://docs.microsoft.com/azure/storage/common/storage-account-sas-create-dotnet#use-an-account-sas-from-a-client)

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan [biztosíthat korlátozott hozzáférést az Azure Storage-erőforrásokhoz sas használatával](../../storage/common/storage-sas-overview.md).
- Ismerje meg, hogyan [kezelheti a Storage-fiókok kulcsait Key Vault és az Azure CLI](overview-storage-keys.md) vagy [Azure PowerShell](overview-storage-keys-powershell.md)használatával.
- Lásd: [felügyelt Storage-fiók kulcsainak mintái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
