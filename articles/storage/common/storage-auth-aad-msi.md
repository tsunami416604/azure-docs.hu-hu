---
title: Az Azure-erőforrásokhoz – Azure Storage-blobok és üzenetsorok a felügyelt identitásokból való hozzáférés hitelesítéséhez |} A Microsoft Docs
description: Az Azure Blob- és Queue storage támogatja a felügyelt identitások Azure Active Directory-hitelesítést az Azure-erőforrások. Az Azure-erőforrások felügyelt identitások használatával hitelesíti a hozzáférést az Azure virtual machines, a függvényalkalmazások, a virtual machine scale sets és mások a futó alkalmazások a blobok és üzenetsorok.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4245c44ceaf907512187d7db4a9d6f087a855f70
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507894"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Blobok és üzenetsorok az Azure Active Directory és a felügyelt identitásokból való hozzáférés hitelesítése az Azure-erőforrások

Az Azure Blob- és Queue storage támogatja az Azure Active Directory (Azure AD-) hitelesítés a [felügyelt identitások az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md). Felügyelt identitások, az Azure-erőforrások engedélyezheti a hozzáférést a blob és az üzenetsor adatok az Azure AD hitelesítő adatait az Azure-beli virtuális gépek (VM), a függvényalkalmazások, a virtual machine scale sets és egyéb szolgáltatások futó alkalmazások. Az Azure AD-hitelesítés és Azure-erőforrások felügyelt identitások használatával elkerülheti a hitelesítő adatokat az alkalmazásokkal, amelyek futtatását a felhőben tárolja.  

Ez a cikk bemutatja, hogyan felügyelt identitással blob és üzenetsor adatokhoz való hozzáférés engedélyezése Azure virtuális gépből. 

## <a name="enable-managed-identities-on-a-vm"></a>A virtuális gép felügyelt identitások engedélyezése

Az Azure-erőforrások felügyelt identitások használatával engedélyezze a hozzáférést a virtuális gép a blobok és üzenetsorok, mielőtt először engedélyeznie kell a felügyelt identitások az Azure-erőforrások a virtuális gépen. Felügyelt identitások engedélyezése az Azure-erőforrások kezelésével kapcsolatos információkért lásd: egyet az alábbi cikkek:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK-k](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Az Azure ad-ben felügyelt identitás engedélyeket

Számára engedélyezni egy kérelmet a Blob és üzenetsor-szolgáltatás, az Azure Storage-alkalmazás egy felügyelt identitás, először felügyelt identitás szerepköralapú hozzáférés-vezérlés (RBAC) beállításainak konfigurálása. Az Azure Storage blob és üzenetsor adatok engedélyek építőelemekkel RBAC-szerepkörök határozza meg. Ha az RBAC szerepkör van rendelve egy felügyelt identitás, a felügyelt identitást kapnak a ilyen engedéllyel, hogy a megfelelő hatókörben blob és üzenetsor adatok. 

RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Hozzáférés engedélyezése az Azure blob és üzenetsor adatok RBAC használata az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Az RBAC Azure CLI-vel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-cli.md)
- [Az RBAC a PowerShell-lel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Egy felügyelt identitás-hozzáférési jogkivonattal engedélyezése

A Blob és a Queue storage felügyelt identitással irányuló kérések engedélyezésére, az alkalmazást vagy parancsfájlt kell beszerezniük az OAuth-jogkivonat. A [a Microsoft Azure App hitelesítési](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ügyféloldali kódtára a .NET-hez (előzetes verzió) egyszerűbbé beszerzéséhez és a egy jogkivonatot a kódból megújítása.

Az alkalmazás hitelesítési ügyféloldali kódtár automatikusan kezeli a hitelesítést. A könyvtár a fejlesztői hitelesítő adatok használatával helyi fejlesztés során hitelesítsék magukat. Helyi fejlesztés során fejlesztői hitelesítő adatok használatával használata biztonságosabb, mert nem kell létrehozni az Azure AD hitelesítő adatait, vagy fájlmegosztási hitelesítő adatokat a fejlesztők közötti. Amikor a megoldás ezt követően telepíti az Azure-ba, a tár automatikusan vált, amennyiben az alkalmazás hitelesítő adatok használatával.

Az alkalmazás-hitelesítési tár az Azure Storage-alkalmazás használatához telepítse a legújabb előzetes csomagot [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), valamint a legújabb verzióját a [Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/). Adja hozzá a következő **használatával** utasítások a kód használatával:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

Az alkalmazás hitelesítési kódtár biztosítja a **AzureServiceTokenProvider** osztály. Ez az osztály egy példányát egy visszahívást, amelyet kér le egy tokent, majd megújítja a jogkivonatot, ameddig adható át.

Az alábbi példa kér le egy tokent és annak használatával hozzon létre egy új blob, majd használja ugyanezt a tokent a blob olvasása.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function 
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token, 
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider, 
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), 
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Visszahívási metódus ellenőrzi a jogkivonat lejárati időt, és igény szerint megújítja azt:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

Az alkalmazás-hitelesítési tár kapcsolatos további információkért lásd: [szolgáltatások közötti hitelesítés a .NET használatával az Azure Key Vault](../../key-vault/service-to-service-authentication.md). 

Hozzáférési jogkivonat beszerzése kapcsolatos további tudnivalókért lásd: [felügyelt identitások használata az Azure-erőforrások egy Azure-beli virtuális gépen a hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Az Azure AD-adatok a blob vagy várólista irányuló kérések engedélyezésére, ezeket a kérelmeket HTTPS kell használnia.

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC tárolási adatok](storage-auth-aad-rbac.md).
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [storage alkalmazásait az Azure AD segítségével](storage-auth-aad-app.md).
- Azure CLI és PowerShell-parancsok futtatásához az Azure AD hitelesítő adatait, lásd: [futtatása az Azure parancssori felület vagy PowerShell-parancsokat az Azure AD-beli hitelesítő adatok blob vagy a várólista eléréséhez](storage-auth-aad-script.md).