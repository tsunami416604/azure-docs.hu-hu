---
title: Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrások számára – Azure Storage
description: Az Azure Blob-és üzenetsor-tárolók támogatása az Azure-erőforrások Azure Active Directory és felügyelt identitásával való hozzáférés engedélyezése az erőforrásokhoz. Az Azure-erőforrások felügyelt identitásait használva engedélyezheti a blobokhoz és várólistákhoz való hozzáférést az Azure-beli virtuális gépeken, a Function apps-ben, a virtuálisgép-méretezési csoportokban és másokon futó alkalmazásokban.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 469790660e843816cc431420e7e1407c90a7de05
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249935"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz

Az Azure Blob és a üzenetsor-tároló támogatja a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik a blob-és üzenetsor-adatokhoz való hozzáférést az Azure-beli virtuális gépeken (VM-ben) futó alkalmazások Azure AD-beli hitelesítő adataival, a Function apps, a virtuálisgép-méretezési csoportokkal és más szolgáltatásokkal. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.  

Ez a cikk bemutatja, hogyan engedélyezheti a hozzáférést a blob-vagy üzenetsor-adatokhoz egy Azure-beli virtuális gép felügyelt identitásával.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen

Ahhoz, hogy az Azure-erőforrásokhoz felügyelt identitások használatával engedélyezzék a blobokhoz és a várólistákhoz való hozzáférést a virtuális gépről, először engedélyeznie kell a felügyelt identitásokat az Azure-erőforrásokhoz a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Engedélyek megadása az Azure AD által felügyelt identitásnak

Ha az Azure Storage-alkalmazásban egy felügyelt identitástól szeretne kérelmet engedélyezni a blobnak vagy Queue szolgáltatásnek, először konfigurálja a felügyelt identitáshoz tartozó szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Az Azure Storage meghatározza azokat a RBAC-szerepköröket, amelyek magukban foglalják a blob-és üzenetsor-adataikat Ha a RBAC szerepkör felügyelt identitáshoz van rendelve, a felügyelt identitás a megfelelő hatókörben megadja a blob-vagy üzenetsor-adatokhoz tartozó engedélyeket.

A RBAC szerepköreinek hozzárendelésével kapcsolatos további információkért tekintse meg a következő cikkek egyikét:

- [Hozzáférés biztosítása az Azure Blob-és üzenetsor-információhoz a Azure Portal RBAC](storage-auth-aad-rbac-portal.md)
- [Hozzáférés biztosítása az Azure Blob-és üzenetsor-adataihoz az Azure CLI-vel való RBAC használatával](storage-auth-aad-rbac-cli.md)
- [Hozzáférés biztosítása az Azure Blob-és üzenetsor-adataihoz a RBAC a PowerShell használatával](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Azure Storage-erőforrás azonosítója

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód – példa: Blokkos blob létrehozása

A Code példa bemutatja, hogyan szerezhet be egy OAuth 2,0-tokent az Azure AD-ből, és hogyan engedélyezheti a blokkos Blobok létrehozásához szükséges kérelmeket. A példa működésének megkezdéséhez kövesse az előző szakaszokban ismertetett lépéseket.

A .NET-hez készült [Microsoft Azure app Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ügyféloldali kódtára (előzetes verzió) leegyszerűsíti a jogkivonat beszerzésének és megújításának folyamatát a kódból. Az alkalmazás-hitelesítési ügyféloldali kódtár automatikusan kezeli a hitelesítést. A könyvtár a fejlesztő hitelesítő adatait használja a helyi fejlesztés során végzett hitelesítéshez. A fejlesztői hitelesítő adatok használata a helyi fejlesztés során biztonságosabb, mert nem kell létrehoznia Azure AD-beli hitelesítő adatokat, vagy meg kell osztania a hitelesítő adatokat a fejlesztők között. Ha a megoldást később üzembe helyezi az Azure-ban, a könyvtár automatikusan az alkalmazás hitelesítő adatait használja.

### <a name="install-packages"></a>Csomagok telepítése

Az alkalmazás-hitelesítési függvénytár Azure Storage-alkalmazásban való használatához telepítse a legújabb előzetes csomagot a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)-ból, valamint az [Azure Storage közös .net-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) és az [Azure Blob Storage ügyféloldali kódtára legújabb verzióját. a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Adja hozzá a következő **using** utasításokat a kódhoz:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

### <a name="add-the-callback-method"></a>Visszahívási módszer hozzáadása

A visszahívási módszer ellenőrzi a jogkivonat lejárati idejét, és szükség esetén megújítja azt:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
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

### <a name="get-a-token-and-create-a-block-blob"></a>Token beszerzése és blokk típusú blob létrehozása

Az alkalmazás-hitelesítési függvénytár biztosítja a **AzureServiceTokenProvider** osztályt. Ennek az osztálynak egy példánya átadható egy olyan visszahívás számára, amely jogkivonatot kap, majd megújítja a jogkivonatot a lejárat előtt.

A következő példa lekéri a tokent, és felhasználja egy új blob létrehozásához, majd ugyanazzal a jogkivonattal olvassa be a blobot.

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

További információ az alkalmazás hitelesítési könyvtáráról: [szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával](../../key-vault/service-to-service-authentication.md).

Ha többet szeretne megtudni a hozzáférési token beszerzéséről, tekintse [meg az Azure-beli virtuális gép felügyelt identitások használata Azure-beli virtuális gépeken hozzáférési jogkivonat](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)beszerzéséhez című témakört.

> [!NOTE]
> Az Azure AD-vel a blob-vagy üzenetsor-adatkérések engedélyezéséhez HTTPS protokollt kell használnia a kérelmekhez.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Storage RBAC szerepköreiről, tekintse meg a [hozzáférési jogosultságok kezelése a RBAC](storage-auth-aad-rbac.md)szolgáltatással című témakört.
- Ha szeretné megtudni, hogyan engedélyezheti a tárolók és a várólisták hozzáférését a Storage-alkalmazásokban, tekintse meg az [Azure ad és a Storage-alkalmazások használatát](storage-auth-aad-app.md)ismertető témakört.
- Az Azure CLI és a PowerShell-parancsok Azure AD-beli hitelesítő adatokkal való futtatásával kapcsolatos információkért lásd: [Azure CLI-vagy PowerShell-parancsok futtatása Azure ad-hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez](storage-auth-aad-script.md).
