---
title: Hitelesítés az Azure AD egy Azure virtuális gép felügyelt Szolgáltatásidentitás (előzetes verzió) |} Microsoft Docs
description: Hitelesítés az Azure AD egy Azure virtuális gép felügyelt Szolgáltatásidentitás (előzetes verzió).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 83d3a2d973604e3b8a709b24cabcb3abba1e304c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660867"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Hitelesítés az Azure AD az Azure felügyelt szolgáltatás identitásának (előzetes verzió)

Az Azure Storage támogatja az Azure Active Directory (Azure AD) hitelesítési [Szolgáltatásidentitás felügyelt](../../active-directory/managed-service-identity/overview.md). Felügyelt szolgáltatás identitásának (MSI) biztosít az Azure Active Directory (Azure AD) automatikusan felügyelt identitást. MSI-fájl segítségével futó Azure virtuális gépeken, függvény alkalmazások, virtuálisgép-méretezési csoportok és más alkalmazások az Azure Storage hitelesítéséhez. MSI-fájl használatával, és alkalmazásaiban az Azure AD-alapú hitelesítés, elkerülheti a tárolása a felhőben futó alkalmazásoknak hitelesítő adatokat.  

Engedélyeket lehet biztosítani a tároló vagy olyan várólisták esetében felügyelt szolgáltatásidentitás, rendeljen hozzá egy tárolási engedélyekkel az MSI-csomag felölelő RBAC szerepkört. A tárolási RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Ez az előzetes kiadás csak nem üzemi használatra készült. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető van deklarálva. Ha az Azure AD-integrációs jelenleg nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokenje az alkalmazásokban. Az előzetes kapcsolatos további információkért lásd: [hitelesíti a hozzáférést az Azure Active Directoryval (előzetes verzió) Azure Storage](storage-auth-aad.md).
>
> Az előzetes RBAC szerepkör-hozzárendelések terjesztése akár öt percet is eltarthat.

Ez a cikk bemutatja, hogyan hitelesítheti az MSI Azure Storage egy Azure virtuális gépről.  

## <a name="enable-msi-on-the-vm"></a>A virtuális Gépen az MSI engedélyezése

MSI-fájl a virtuális Gépet az Azure Storage hitelesítéséhez használhatja, először engedélyeznie kell a virtuális Gépen az MSI-fájl. MSI engedélyezésével kapcsolatban lásd: egyet a következő cikkeket:

- [Azure Portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablon](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK-k](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Az MSI-access-token beszerzése

MSI végzett hitelesítéshez, az alkalmazás vagy a parancsfájl kell beszerezniük egy MSI-jogkivonatot. Olyan hozzáférési jogkivonatot beszerzésére vonatkozó további tudnivalókért lásd: [egy Azure virtuális gép felügyelt szolgáltatás Identity (MSI) használata a token beszerzési](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>.NET-példakód: hozzon létre egy blokkblob

A Kódpélda feltételezi, hogy rendelkezik-e egy MSI-jogkivonatot. A hozzáférési jogkivonat segítségével engedélyezésére a felügyelt szolgáltatásidentitás a blokkblob létrehozásához.

### <a name="add-references-and-using-statements"></a>Vegye fel a hivatkozásokat, és utasítások segítségével  

A Visual Studióban az Azure Storage ügyféloldali kódtár előzetes verzióját telepítse. Az a **eszközök** menü **Nuget-Csomagkezelő**, majd **Csomagkezelő konzol**. A konzolba írja be a következő parancsot:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Adja hozzá a következő using utasításokat a a kódot:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Az MSI-jogkivonat a hitelesítő adatok létrehozása

A blokkblob létrehozásához használja a **TokenCredentials** az előzetes csomag által biztosított osztály. Egy új példányt létrehozni **TokenCredentials**, az MSI-jogkivonat korábban beszerzett a tompított:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Az Azure AD integrálása az Azure Storage Azure Storage műveletekhez HTTPS használatát igényli.

## <a name="next-steps"></a>További lépések

- Az Azure-tárolás RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md).
- A tárolók és a tárolási alkalmazásokon belül a várólista elérésének hitelesítéséhez, lásd: [tárolási alkalmazások az Azure AD használatára](storage-auth-aad-app.md).
- Az Azure parancssori felület és a PowerShell használatával az Azure AD identitással bejelentkezni, lásd: [CLI vagy a PowerShell használatával (előzetes verzió) Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).
- Az Azure AD integrálása az Azure-blobokat és üzenetsorokat kapcsolatos további információkért lásd a következő Azure Storage csapat blogbejegyzésben, [az Azure betekintő AD hitelesítés az Azure Storage bejelentése](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
