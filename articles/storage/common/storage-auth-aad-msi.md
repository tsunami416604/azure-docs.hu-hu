---
title: Az Azure-hitelesítést AD az Azure virtuális gépből felügyelt Felügyeltszolgáltatás-identitás (előzetes verzió) |} A Microsoft Docs
description: Az Azure-hitelesítést AD az Azure virtuális gépből felügyelt Felügyeltszolgáltatás-identitás (előzetes verzió).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: e20e0c412206b2a35973b192ef911bb99ed7c210
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021863"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Hitelesítés az Azure Felügyeltszolgáltatás-identitás (előzetes verzió) az Azure AD-vel

Az Azure Storage támogatja az Azure Active Directory (Azure AD) hitelesítési [Felügyeltszolgáltatás-identitás](../../active-directory/managed-identities-azure-resources/overview.md). Felügyelt Felügyeltszolgáltatás-identitás (MSI) az Azure Active Directoryban (Azure AD) egy automatikus felügyelt identitást biztosít. Az MSI használatával az Azure virtual machines, a függvényalkalmazások, a virtual machine scale sets és mások a futó alkalmazások az Azure Storage-hitelesítéshez. Az MSI használatával, és alkalmazásaiban az Azure AD-hitelesítés, elkerülheti a hitelesítő adatokat az alkalmazásokkal, amelyek futtatását a felhőben tárolja.  

A storage-tárolók vagy várólisták felügyeltszolgáltatás-identitás engedélyeket, rendelje hozzá az RBAC-szerepkör, amely magában foglalja a tárolási engedélyek az MSI-csomag. További információ a tárolási RBAC-szerepkörök: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Ebben az előzetes verzióban csak a nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető lett deklarálva. Ha az Azure AD-integrációs még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban. Az előzetes verzióra vonatkozó további információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).
>
> Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.

Ez a cikk bemutatja, hogyan hitelesítheti az MSI-vel az Azure Storage, Azure virtuális gépből.  

## <a name="enable-msi-on-the-vm"></a>A virtuális gépen az MSI engedélyezéséhez

Az MSI használatával a virtuális gépről az Azure Storage-hitelesítést, mielőtt a virtuális gépen az MSI először engedélyeznie kell. Az MSI engedélyezéséhez ezekben a cikkekben talál további információt:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Az Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK-k](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>MSI szükséges hozzáférési jogkivonat beszerzése

MSI-hitelesítésre, az alkalmazást vagy parancsfájlt kell MSI hozzáférési jogkivonat beszerzése. Hozzáférési jogkivonat beszerzése kapcsolatos tudnivalókért lásd: [egy Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata a token beszerzéséhez](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>.NET mintakód: block blob létrehozása

A mintakód feltételezi, hogy egy MSI-hozzáférési jogkivonatot. A hozzáférési jogkivonat segítségével engedélyezi a felügyeltszolgáltatás-identitás létrehozása a blokkblobok.

### <a name="add-references-and-using-statements"></a>Mutató hivatkozásokat tudjon felvenni, és utasításokkal  

A Visual Studióban az Azure Storage ügyféloldali kódtár előzetes verzióját telepítse. Az a **eszközök** menüjében válassza **Nuget-Csomagkezelő**, majd **Package Manager Console**. A konzolba írja be a következő parancsot:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Adja hozzá a következő using utasításokat a kódját:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Az MSI-jogkivonat a hitelesítő adatok létrehozása

A blokkblob típusú létrehozásához használja a **TokenCredentials** az előzetes verzió csomag által nyújtott osztály. Egy új példányát hozza létre **TokenCredentials**, az MSI-hozzáférési jogkivonat korábban beszerzett passzok:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
