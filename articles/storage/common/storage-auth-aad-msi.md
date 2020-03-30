---
title: Felügyelt identitással rendelkező adatokhoz való hozzáférés engedélyezése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan használhatja a felügyelt identitások az Azure-erőforrások, hogy engedélyezze a hozzáférést a blob és a várólista-adatok az Azure virtuális gépeken futó alkalmazások, funkció alkalmazások, virtuálisgép-méretezési készletek és mások.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255339"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Blob- és várólista-adatokhoz való hozzáférés engedélyezése felügyelt identitásokkal az Azure-erőforrásokhoz

Az Azure Blob és a Queue storage támogatja az Azure Active Directory (Azure AD) [hitelesítését az Azure-erőforrások felügyelt identitásaival.](../../active-directory/managed-identities-azure-resources/overview.md) Az Azure-erőforrások felügyelt identitások engedélyezheti a blob- és várólista-adatokhoz való hozzáférést az Azure Virtuális gépeken (VM-ek), a függvényalkalmazásokban, a virtuálisgép-méretezési csoportokban és egyéb szolgáltatásokban futó alkalmazások Azure AD-hitelesítő adatainak használatával. Felügyelt identitások használatával az Azure-erőforrások és az Azure AD-hitelesítés, elkerülheti a hitelesítő adatok tárolása a felhőben futó alkalmazások használatával.  

Ez a cikk bemutatja, hogyan engedélyezheti a blob- vagy várólista-adatokhoz való hozzáférést egy Azure virtuális gépről az Azure Resources felügyelt identitások használatával. Azt is ismerteti, hogyan tesztelheti a kódot a fejlesztői környezetben.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése virtuális számítógépen

Mielőtt az Azure Resources felügyelt identitások használatával engedélyezheti a blobokhoz és várólistákhoz való hozzáférést a virtuális gépről, először engedélyeznie kell a felügyelt identitásokat az Azure Resources számára a virtuális gépen. Ha tudni szeretné, hogyan engedélyezheti az Azure Resources felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure-portál](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféltárak](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

A felügyelt identitásokról az [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md)című témakörben talál további információt.

## <a name="authenticate-with-the-azure-identity-library"></a>Hitelesítés az Azure Identity-kódtárral

Az Azure Identity ügyfélkódtár azure Azure AD tokenhitelesítési támogatást nyújt az [Azure SDK-hoz.](https://github.com/Azure/azure-sdk) Az Azure Storage-ügyfélkódtárak legújabb verziói .

Az Azure Identity-ügyfélkódtár előnye, hogy lehetővé teszi, hogy ugyanazt a kódot használja a hitelesítéshez, függetlenül attól, hogy az alkalmazás a fejlesztői környezetben vagy az Azure-ban fut. Az Azure Identity ügyfélkódtár . Amikor a kód fut az Azure-ban, a rendszerbiztonsági tag egy felügyelt identitás az Azure-erőforrások. A fejlesztői környezetben a felügyelt identitás nem létezik, így az ügyfélkódtár tesztelési célokra hitelesíti a felhasználót vagy a szolgáltatásnév.

A hitelesítés után az Azure Identity ügyfélkódtár egy jogkivonat-hitelesítő adatokat kap. Ez a jogkivonat-hitelesítő adatok ezután beágyazva lesznek az Azure Storage-on végrehajtott műveletek hez létrehozott szolgáltatás-ügyfélobjektumba. A könyvtár kezeli ezt zökkenőmentesen a megfelelő jogkivonat-hitelesítő adatok beszerzése.

A .NET-hez való Azure Identity ügyfélkódtárról további információt az [Azure Identity ügyfélkódtár .NET-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)című témakörben talál. Az Azure Identity-ügyfélkódtár dokumentációját az [Azure.Identity Namespace című témakörben találja.](/dotnet/api/azure.identity)

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök hozzárendelése az adatokhoz való hozzáféréshez

Ha egy Azure AD rendszerbiztonsági tag próbál hozzáférni blob vagy várólista adatait, hogy a rendszerbiztonsági tag nak rendelkeznie kell az erőforrás hoz. Függetlenül attól, hogy a rendszerbiztonsági tag felügyelt identitás az Azure-ban, vagy egy Azure AD felhasználói fiók futó kód a fejlesztési környezetben, a rendszerbiztonsági tag kell rendelni egy RBAC szerepkör, amely hozzáférést biztosít a blob vagy a várólista adatait az Azure Storage-ban. Az Engedélyek RBAC-on keresztüli hozzárendeléséről az **RBAC-szerepkörök hozzárendelése** a hozzáférési jogokhoz című szakaszban az [Azure-blobokhoz és várólistákhoz való hozzáférés engedélyezése az Azure Active Directory használatával](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)című szakaszban talál.

### <a name="authenticate-the-user-in-the-development-environment"></a>A felhasználó hitelesítése a fejlesztői környezetben

Ha a kód a fejlesztői környezetben fut, előfordulhat, hogy a hitelesítés automatikusan kezelhető, vagy böngészőbejelentkezésre lehet szükség, attól függően, hogy milyen eszközöket használ. A Microsoft Visual Studio például támogatja az egyszeri bejelentkezést (SSO), így az aktív Azure AD felhasználói fiók automatikusan hitelesítésre szolgál. Az egyszeri bejelentkezésről további információt az [alkalmazásokegyszeri bejelentkezés](../../active-directory/manage-apps/what-is-single-sign-on.md)című témakörben talál.

Más fejlesztői eszközök kérhetik, hogy webböngészőn keresztül jelentkezzen be.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Egyszerű szolgáltatás hitelesítése a fejlesztői környezetben

Ha a fejlesztői környezet nem támogatja az egyszeri bejelentkezést vagy a webböngészőn keresztüli bejelentkezést, akkor egy egyszerű szolgáltatás használatával hitelesítheti magát a fejlesztői környezetből.

#### <a name="create-the-service-principal"></a>A szolgáltatásnév létrehozása

Ha egyszerű szolgáltatást szeretne létrehozni az Azure CLI-vel, és rbac szerepkört szeretne hozzárendelni, hívja meg az [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancsot. Adjon meg egy Azure Storage-adatelérési szerepkört az új egyszerű szolgáltatáshoz rendelve. Emellett adja meg a szerepkör-hozzárendelés hatókörét. Az Azure Storage beépített szerepköreiről további információt az [Azure-erőforrások beépített szerepkörei című témakörben talál.](../../role-based-access-control/built-in-roles.md)

Ha nem rendelkezik megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen az egyszerű szolgáltatáshoz, előfordulhat, hogy meg kell kérnie a fiók tulajdonosát vagy a rendszergazdát a szerepkör-hozzárendelés végrehajtására.

A következő példa az Azure CLI használatával hoz létre egy új egyszerű szolgáltatásszolgáltatást, és hozzárendeli a **Storage Blob Data Reader** szerepkört a fiókhatókörrel

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A `az ad sp create-for-rbac` parancs json formátumban adja vissza a szolgáltatásegyszerű tulajdonságok listáját. Másolja ezeket az értékeket, hogy a következő lépésben létrehozhassa a szükséges környezeti változókat.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Az RBAC szerepkör-hozzárendelések propagálása néhány percet is igénybe vehet.

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Az Azure Identity ügyfélkódtár beolvassa az értékeket három környezeti változók futásidőben a szolgáltatás névszerint hitelesített. Az alábbi táblázat az egyes környezeti változókhoz beállítandó értéket ismerteti.

|Környezeti változó|Érték
|-|-
|`AZURE_CLIENT_ID`|A szolgáltatásnév alkalmazásazonosítója
|`AZURE_TENANT_ID`|Az egyszerű szolgáltatás Azure AD-bérlőazonosítója
|`AZURE_CLIENT_SECRET`|Az egyszerű szolgáltatáshoz létrehozott jelszó

> [!IMPORTANT]
> A környezeti változók beállítása után zárja be és nyissa meg újra a konzolablakot. Ha visual studio vagy más fejlesztői környezetet használ, előfordulhat, hogy újra kell indítania a fejlesztői környezetet ahhoz, hogy regisztrálja az új környezeti változókat.

További információ: [Identity for Azure app in portal.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Példa : Blokkblob létrehozása

Adja hozzá `using` a következő direktívákat a kódhoz az Azure Identity és az Azure Storage ügyfélkódtárak használatához.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Egy token hitelesítő adatok bekérése, amely a kód segítségével engedélyezheti az Azure Storage-kérelmeket, hozzon létre egy példányt a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály. A következő kód példa bemutatja, hogyan lehet beszerezni a hitelesített jogkivonathitelesítő adatokat, és használja egy szolgáltatás ügyfélobjektum létrehozásához, majd a szolgáltatásügyfél segítségével töltsön fel egy új blobot:

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
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Blob- vagy várólista-adatok ra vonatkozó kérelmek engedélyezése az Azure AD-vel, https-t kell használnia ezekhez a kérelmekhez.

## <a name="next-steps"></a>További lépések

- [A tárolási adatokhoz való hozzáférési jogok kezelése az RBAC segítségével.](storage-auth-aad-rbac.md)
- [Az Azure AD használata tárolási alkalmazásokkal.](storage-auth-aad-app.md)
- [Az Azure CLI- vagy PowerShell-parancsok futtatása Azure AD-hitelesítő adatokkal a blob- vagy várólista-adatok eléréséhez.](authorize-active-directory-powershell.md)
