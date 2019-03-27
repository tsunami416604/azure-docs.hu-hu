---
title: Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directory |} A Microsoft Docs
description: Azure-blobok és üzenetsorok az Azure Active Directoryval való hozzáférés hitelesítéséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 08fdc20df70e4a97dd0cb99468354ef2b5c51f2b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447040"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval

Az Azure Storage támogatja a hitelesítés és engedélyezés az Azure Active Directory (AD) a Blob és üzenetsor-szolgáltatások. Az Azure ad-vel szerepköralapú hozzáférés-vezérlés (RBAC) használhatja hozzáférést a felhasználók, csoportok és alkalmazások szolgáltatásnevének. 

Felhasználók vagy az Azure AD hitelesítő adatait használó alkalmazások hitelesítése készít felső szintű biztonság és a könnyű használat más engedélyezési módot. Miközben továbbra is megosztott kulcsos engedélyezési használata az alkalmazások, Azure AD-vel megkerüli ügyféladataik tárolásának a kód a hozzáférési kulcsára. Közös hozzáférésű jogosultságkódok (SAS) használata a minden részletre kiterjedő hozzáférést biztosítani a tárfiókban lévő erőforrásokhoz folytathatja, de az Azure AD kezelése SAS-tokeneket vagy sérült biztonságú SAS visszavonása foglalkoznia kellene hasonló funkciókat kínál. A Microsoft javasolja, hogy az Azure AD-hitelesítés használata az Azure Storage-alkalmazásokhoz, amikor csak lehetséges.

Hitelesítés és engedélyezés az Azure AD hitelesítő adatait az összes általános célú v2, általános célú v1 és Blob storage-fiókok az összes nyilvános régióban érhető el. Csak a tárfiókok az Azure Resource Manager üzembe helyezési modell támogatása az Azure AD engedélyezési hoz létre.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blobok és üzenetsorok az Azure ad-ben – áttekintés

Az Azure AD-integráció használatával az Azure Storage első lépése az tárolási adatok RBAC-szerepkörök hozzárendelése az egyszerű szolgáltatásnév (egy felhasználó, csoport vagy alkalmazás, szolgáltatásnév) vagy a felügyelt identitások az Azure-erőforrásokhoz. RBAC-szerepkörök tartalmazzák a közös jogosultságkészletek tárolók és üzenetsorok. RBAC-szerepkörök hozzárendelése az Azure Storage kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC tárolási adatok](storage-auth-aad-rbac.md).

Az Azure AD használata az alkalmazások tárolási erőforrásokhoz való hozzáférés engedélyezésére, az OAuth 2.0 hozzáférési jogkivonat kérése a kódot kell. A hozzáférési jogkivonat kérése és használhatja az Azure Storage-kérelmek engedélyezését végzi, lásd: [hitelesítés az Azure Storage-alkalmazás Azure AD-vel](storage-auth-aad-app.md). Ha egy felügyelt identitás használ, tekintse meg [blobok és üzenetsorok az Azure-ral való hitelesítés hozzáférési felügyelt identitások az Azure-erőforrások](storage-auth-aad-msi.md).

Az Azure CLI és PowerShell mostantól támogatja a adataikkal jelentkezhetnek be az Azure AD identitás. Miután jelentkezik be az Azure AD identitás, a munkamenet identitás alatt fut. További tudnivalókért lásd: [parancssori felület vagy PowerShell az Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage határozza meg, amely magában foglalja a tárolók és -várólisták eléréséhez használt gyakori jogosultságkészletek beépített RBAC-szerepkör. 

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Ismerje meg, hogyan rendelje hozzá egy beépített RBAC az Azure Storage-erőforrások, tekintse meg az alábbi témakörök:

- [Hozzáférés engedélyezése az Azure blob és üzenetsor adatok RBAC használata az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Az RBAC Azure CLI-vel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-cli.md)
- [Az RBAC a PowerShell-lel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC-szerepkörök által biztosított hozzáférési engedélyek 

További információ az Azure Storage-műveletek meghívásához szükséges engedélyekről: [REST-műveleteinek meghívására szolgáló engedélyek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>További lépések

- [Hitelesítés az Azure Active Directoryval egy alkalmazásból blobok és üzenetsorok eléréséhez](storage-auth-aad-app.md)
- [Hitelesíti a blobok és üzenetsorok, a felügyelt identitásokból hozzáférést az Azure-erőforrások](storage-auth-aad-msi.md)
- Az Azure Files SMB-n keresztül hitelesítést az Azure AD-tartományhoz csatlakoztatott virtuális gépek csak (előzetes verzió) támogatja. Azure AD SMB-n keresztül az Azure Files-vel kapcsolatos további információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../files/storage-files-active-directory-overview.md).