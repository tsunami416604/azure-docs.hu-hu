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
ms.openlocfilehash: ff543b7275ab05a83b1be1d156cbc6059a3b5430
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369890"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval

Az Azure Storage támogatja a hitelesítés és engedélyezés az Azure Active Directory (AD) a Blob és üzenetsor-szolgáltatások. Az Azure ad-vel szerepköralapú hozzáférés-vezérlés (RBAC) használhatja hozzáférést a felhasználók, csoportok és alkalmazások szolgáltatásnevének. 

Felhasználók vagy az Azure AD hitelesítő adatait használó alkalmazások hitelesítése készít felső szintű biztonság és a könnyű használat más engedélyezési módot. Miközben továbbra is megosztott kulcsos engedélyezési használata az alkalmazások, Azure AD-vel megkerüli ügyféladataik tárolásának a kód a hozzáférési kulcsára. Közös hozzáférésű jogosultságkódok (SAS) használata a minden részletre kiterjedő hozzáférést biztosítani a tárfiókban lévő erőforrásokhoz folytathatja, de az Azure AD kezelése SAS-tokeneket vagy sérült biztonságú SAS visszavonása foglalkoznia kellene hasonló funkciókat kínál. A Microsoft javasolja, hogy az Azure AD-hitelesítés használata az Azure Storage-alkalmazásokhoz, amikor csak lehetséges.

Hitelesítés és engedélyezés az Azure AD hitelesítő adatait az összes általános célú v2, általános célú v1 és Blob storage-fiókok az összes nyilvános régióban érhető el. Csak a tárfiókok az Azure Resource Manager üzembe helyezési modell támogatása az Azure AD engedélyezési hoz létre.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blobok és üzenetsorok az Azure ad-ben – áttekintés

Az Azure AD-integráció használatával az Azure Storage első lépése az tárolási adatok RBAC-szerepkörök hozzárendelése az egyszerű szolgáltatásnév (egy felhasználó, csoport vagy alkalmazás, szolgáltatásnév) vagy a felügyelt identitások az Azure-erőforrásokhoz. RBAC-szerepkörök tartalmazzák a közös jogosultságkészletek tárolók és üzenetsorok. RBAC-szerepkörök hozzárendelése az Azure Storage kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC tárolási adatok](storage-auth-aad-rbac.md).

Az Azure AD használata az alkalmazások tárolási erőforrásokhoz való hozzáférés engedélyezésére, az OAuth 2.0 hozzáférési jogkivonat kérése a kódot kell. A hozzáférési jogkivonat kérése és használhatja az Azure Storage-kérelmek engedélyezését végzi, lásd: [hitelesítés az Azure Storage-alkalmazás Azure AD-vel](storage-auth-aad-app.md). Ha egy felügyelt identitás használ, tekintse meg [blobok és üzenetsorok az Azure-ral való hitelesítés hozzáférési felügyelt identitások az Azure-erőforrások](storage-auth-aad-msi.md).

Az Azure CLI és PowerShell mostantól támogatja a adataikkal jelentkezhetnek be az Azure AD identitás. Miután jelentkezik be az Azure AD identitás, a munkamenet identitás alatt fut. További tudnivalókért lásd: [parancssori felület vagy PowerShell az Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage határozza meg, amely magában foglalja a tárolók és -várólisták eléréséhez használt gyakori jogosultságkészletek beépített RBAC-szerepkör. 

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Az Azure Portal beépített szerepkör hozzárendelése kapcsolatban lásd: [hozzáférést biztosítani Azure-tárolók és az Azure Portalon az RBAC üzenetsorok](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC-szerepkörök által biztosított hozzáférési engedélyek 

További információ az Azure Storage-műveletek meghívásához szükséges engedélyekről: [REST-műveleteinek meghívására szolgáló engedélyek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>További lépések

- [Hozzáférés engedélyezése az Azure-tárolók és a várólisták RBAC használata az Azure Portalon](storage-auth-aad-rbac.md)
- [Hitelesítés az Azure Active Directoryval egy alkalmazásból blobok és üzenetsorok eléréséhez](storage-auth-aad-app.md)
- [Hitelesíti a blobok és üzenetsorok, a felügyelt identitásokból hozzáférést az Azure-erőforrások](storage-auth-aad-msi.md)
- [Az Azure Storage-be a parancssori felület vagy PowerShell eléréséhez használja az Azure AD identity](storage-auth-aad-script.md)