---
title: Hitelesíti a hozzáférést a blobok és üzenetsorok az Azure Active Directory által felügyelt identitásokat az Azure-erőforrásokhoz (előzetes verzió) – Azure Storage |} A Microsoft Docs
description: Az Azure Blob- és Queue storage támogatja a felügyelt identitások Azure Active Directory-hitelesítést az Azure-erőforrások. Az Azure-erőforrások felügyelt identitások használatával hitelesíti a hozzáférést az Azure virtual machines, a függvényalkalmazások, a virtual machine scale sets és mások a futó alkalmazások a blobok és üzenetsorok. Felügyelt identitások használatával az Azure-erőforrások és alkalmazásaiban az Azure AD-hitelesítés, elkerülheti a hitelesítő adatokat az alkalmazásokkal, amelyek futtatását a felhőben tárolja.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 15c37be3f3b1b3f72c32865e095091fa10ee9750
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251690"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Hitelesíti a blobok és üzenetsorok, a felügyelt identitásokból hozzáférést az Azure-erőforrások (előzetes verzió)

Az Azure Blob- és Queue storage támogatja az Azure Active Directory (Azure AD-) hitelesítés a [felügyelt identitások az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md). Felügyelt identitások, az Azure-erőforrások hitelesíteni tudja a hozzáférés a blobokhoz és -üzenetsorok az Azure-beli virtuális gépek (VM), a függvényalkalmazások, a virtual machine scale sets és mások futó alkalmazások az Azure AD hitelesítő adataival. Felügyelt identitások használatával az Azure-erőforrások és alkalmazásaiban az Azure AD-hitelesítés, elkerülheti a hitelesítő adatokat az alkalmazásokkal, amelyek futtatását a felhőben tárolja.  

A szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör hozzárendelése a felügyelt identitás, amely magában foglalja a megfelelő hatókörben adott erőforrásra vonatkozó engedélyeket egy felügyelt identitás egy blob-tároló vagy egy üzenetsor, engedélyt kell adnia. További információ a tárolási RBAC-szerepkörök: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md). 

Ez a cikk bemutatja, hogyan hitelesítheti az Azure Blob vagy a Queue storage felügyelt identitással Azure virtuális gépből.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>A virtuális gép felügyelt identitások engedélyezése

Az Azure-erőforrások felügyelt identitások használatával hitelesíti a hozzáférést a blobok és üzenetsorok, a virtuális gépről, mielőtt először engedélyeznie kell a felügyelt identitások az Azure-erőforrások a virtuális gépen. Felügyelt identitások engedélyezése az Azure-erőforrások kezelésével kapcsolatos információkért lásd: egyet az alábbi cikkek:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK-k](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Az RBAC szerepkör hozzárendelése az Azure ad-ben felügyelt identitás

Az Azure Storage-alkalmazás egy felügyelt identitás hitelesítésére, először konfigurálnia szerepköralapú hozzáférés-vezérlés (RBAC) beállításai felügyelt identitás. Az Azure Storage határozza meg, amely magában foglalja a tárolók és a várólisták engedélyeit RBAC-szerepkörökhöz. Ha az RBAC szerepkör van rendelve egy felügyelt identitás, felügyelt identitás hozzáférést ennek az erőforrásnak. További információkért lásd: [kezelés hozzáférési jogosultságok RBAC (előzetes verzió) az adatok Azure Blob és üzenetsor](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Egy felügyelt identitás-hozzáférési jogkivonat beszerzése

Egy felügyelt identitás-hitelesítésre, az alkalmazást vagy parancsfájlt kell beszerezni egy felügyelt identitás-hozzáférési jogkivonatot. Hozzáférési jogkivonat beszerzése kapcsolatos tudnivalókért lásd: [felügyelt identitások használata az Azure-erőforrások egy Azure-beli virtuális gépen a hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód példa: Egy blokkblob létrehozása

A mintakód feltételezi, hogy egy felügyelt identitás-hozzáférési jogkivonatot. A hozzáférési jogkivonat blokkblob létrehozása felügyelt identitás hitelesítésére szolgál.

### <a name="add-references-and-using-statements"></a>Mutató hivatkozásokat tudjon felvenni, és utasításokkal  

A Visual Studióban az Azure Storage ügyféloldali kódtár előzetes verzióját telepítse. Az a **eszközök** menüjében válassza **Nuget-Csomagkezelő**, majd **Package Manager Console**. A konzolba írja be a következő parancsot:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Adja hozzá a következő using utasításokat a kódját:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>A felügyelt identitás-hozzáférési jogkivonatot a hitelesítő adatok létrehozása

A blokkblob típusú létrehozásához használja a **TokenCredentials** az előzetes verzió csomag által nyújtott osztály. Egy új példányát hozza létre **TokenCredentials**, a korábban beszerzett felügyelt identitás-hozzáférési jogkivonatot a passzok:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Az Azure AD-integráció az Azure Storage for Azure Storage-műveletek HTTPS használatát igényli.

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [storage alkalmazásait az Azure AD segítségével](storage-auth-aad-app.md).
- Ismerje meg, hogyan jelentkezhet be az Azure CLI és PowerShell az Azure AD-identitásnak, lásd: [CLI vagy a PowerShell (előzetes verzió) az Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).
- Az Azure-Blobok és üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
