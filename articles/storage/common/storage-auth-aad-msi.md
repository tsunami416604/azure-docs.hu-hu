---
title: Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrások számára – Azure Storage
description: Az Azure Blob-és üzenetsor-tárolók támogatása az Azure-erőforrások Azure Active Directory és felügyelt identitásával való hozzáférés engedélyezése az erőforrásokhoz. Az Azure-erőforrások felügyelt identitásait használva engedélyezheti a blobokhoz és várólistákhoz való hozzáférést az Azure-beli virtuális gépeken, a Function apps-ben, a virtuálisgép-méretezési csoportokban és másokon futó alkalmazásokban.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d77ab142e227cfaa6533395cc256d992e698dd17
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495928"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz

Az Azure Blob és a üzenetsor-tároló támogatja a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik a blob-és üzenetsor-adatokhoz való hozzáférést az Azure-beli virtuális gépeken (VM-ben) futó alkalmazások Azure AD-beli hitelesítő adataival, a Function apps, a virtuálisgép-méretezési csoportokkal és más szolgáltatásokkal. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.  

Ez a cikk bemutatja, hogyan engedélyezhető a blob-vagy üzenetsor-adatokhoz való hozzáférés az Azure-beli virtuális gépekről az Azure-erőforrások felügyelt identitásai használatával. Azt is leírja, hogyan tesztelheti a kódot a fejlesztői környezetben.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen

Ahhoz, hogy az Azure-erőforrásokhoz felügyelt identitások használatával engedélyezzék a blobokhoz és a várólistákhoz való hozzáférést a virtuális gépről, először engedélyeznie kell a felügyelt identitásokat az Azure-erőforrásokhoz a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Hitelesítés az Azure Identity Library (előzetes verzió) használatával

Az Azure Identity .NET-hez készült ügyféloldali kódtára (előzetes verzió) egy rendszerbiztonsági tag hitelesítésére szolgál. Ha a kód az Azure-ban fut, a rendszerbiztonsági tag felügyelt identitás az Azure-erőforrásokhoz.

Ha a kód a fejlesztési környezetben fut, a hitelesítés automatikusan kezelhető, vagy szükség lehet egy böngészőbeli bejelentkezésre, attól függően, hogy melyik eszközt használja. A Microsoft Visual Studio támogatja az egyszeri bejelentkezést (SSO), így az aktív Azure AD-felhasználói fiók automatikusan használatos a hitelesítéshez. További információ az egyszeri bejelentkezésről: [egyszeri bejelentkezés az alkalmazásokba](../../active-directory/manage-apps/what-is-single-sign-on.md).

Más fejlesztői eszközök megkérhetik a bejelentkezést egy webböngészőn keresztül. Az egyszerű szolgáltatásnév használatával is végezheti el a hitelesítést a fejlesztési környezetből. További információkért lásd: [identitás létrehozása az Azure-alkalmazáshoz a portálon](../../active-directory/develop/howto-create-service-principal-portal.md).

A hitelesítés után az Azure Identity Client Library megkapja a jogkivonat hitelesítő adatait. Ezt a jogkivonat hitelesítő adatait a rendszer az Azure Storage szolgáltatással kapcsolatos műveletek elvégzéséhez létrehozott szolgáltatási ügyfél objektumba ágyazza be. A könyvtár a megfelelő jogkivonat-hitelesítő adatok lekérésével zökkenőmentesen kezeli ezt.

Az Azure Identity ügyféloldali függvénytárával kapcsolatos további információkért lásd: az [Azure Identity ügyféloldali kódtára a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-szerepkörök kiosztása az adathoz való hozzáféréshez

Ha egy Azure AD rendszerbiztonsági tag megpróbál hozzáférni a blob-vagy üzenetsor-adatszolgáltatáshoz, akkor a rendszerbiztonsági tag engedélyekkel kell rendelkeznie az erőforráshoz. Azt jelzi, hogy a rendszerbiztonsági tag felügyelt identitás-e az Azure-ban vagy egy olyan Azure AD-felhasználói fiókban, amely kódot futtat a fejlesztési környezetben, a rendszerbiztonsági tag számára olyan RBAC-szerepkört kell hozzárendelni, amely hozzáférést biztosít a blob-vagy üzenetsor-adatokhoz Az engedélyek **RBAC-n** keresztüli hozzárendelésével kapcsolatos információkért tekintse meg az [Azure-blobok és-várólisták hozzáférésének engedélyezése a Azure Active Directory használatával](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)című témakör című szakaszát.

## <a name="install-the-preview-packages"></a>Az előzetes verziójú csomagok telepítése

A jelen cikkben szereplő példák az Azure Storage ügyféloldali kódtár legújabb előzetes verzióját használják a blob Storage-hoz. Az előnézeti csomag telepítéséhez futtassa a következő parancsot a NuGet Package Manager konzolról:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

A cikkben szereplő példák a [.net-hez készült Azure Identity Client Library](https://www.nuget.org/packages/Azure.Identity/) legújabb előzetes verzióját is használják az Azure ad-beli hitelesítő adatokkal való hitelesítéshez. Az előnézeti csomag telepítéséhez futtassa a következő parancsot a NuGet Package Manager konzolról:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód – példa: Blokkos blob létrehozása

Adja hozzá a következő `using`-irányelveket a kódjához az Azure Identity és az Azure Storage ügyféloldali kódtárainak előzetes verziójának használatához.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Ha meg szeretné kapni a jogkivonat hitelesítő adatait, amelyet a kód használhat az Azure Storage-ba irányuló kérések engedélyezéséhez, hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát. A következő mintakód bemutatja, hogyan kérheti le a hitelesített jogkivonat hitelesítő adatait, és hogyan használhatja szolgáltatás-ügyfél objektum létrehozására, majd a szolgáltatás-ügyféllel feltöltheti az új blobokat:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Az Azure AD-vel a blob-vagy üzenetsor-adatkérések engedélyezéséhez HTTPS protokollt kell használnia a kérelmekhez.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Storage RBAC szerepköreiről, tekintse meg a [hozzáférési jogosultságok kezelése a RBAC](storage-auth-aad-rbac.md)szolgáltatással című témakört.
- Ha szeretné megtudni, hogyan engedélyezheti a tárolók és a várólisták hozzáférését a Storage-alkalmazásokban, tekintse meg az [Azure ad és a Storage-alkalmazások használatát](storage-auth-aad-app.md)ismertető témakört.
- Az Azure CLI és a PowerShell-parancsok Azure AD-beli hitelesítő adatokkal való futtatásával kapcsolatos információkért lásd: [Azure CLI-vagy PowerShell-parancsok futtatása Azure ad-hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez](storage-auth-aad-script.md).
