---
title: Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval (előzetes verzió) |} A Microsoft Docs
description: Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval (előzetes verzió).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57e793278af7eb03fe49fd657e45b0846db8b1c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817915"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval (előzetes verzió)

Az Azure Storage támogatja a hitelesítés és engedélyezés az Azure Active Directory (AD) a Blob és üzenetsor-szolgáltatások. Az Azure ad-vel szerepköralapú hozzáférés-vezérlés (RBAC) használhatja hozzáférést a felhasználók, csoportok és alkalmazások szolgáltatásnevének. 

Felhasználók vagy az Azure AD hitelesítő adatait használó alkalmazások hitelesítése készít felső szintű biztonság és a könnyű használat más engedélyezési módot. Miközben továbbra is megosztott kulcsos engedélyezési használata az alkalmazások, Azure AD-vel megkerüli ügyféladataik tárolásának a kód a hozzáférési kulcsára. Közös hozzáférésű jogosultságkódok (SAS) használata a minden részletre kiterjedő hozzáférést biztosítani a tárfiókban lévő erőforrásokhoz folytathatja, de az Azure AD kezelése SAS-tokeneket vagy sérült biztonságú SAS visszavonása foglalkoznia kellene hasonló funkciókat kínál. A Microsoft javasolja, hogy az Azure AD-hitelesítés használata az Azure Storage-alkalmazásokhoz, amikor csak lehetséges.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>Tudnivalók az előzetes verzió

Tudnivalók az előzetes verzió a következő szempontokat tartsa szem előtt:

- Az Azure AD-integráció a Blob és üzenetsor-szolgáltatásainak csak az előzetes verzióban érhető el.
- Az összes nyilvános régióban GPv1-, GPv2 és Blob storage-fiókok Azure AD-integráció érhető el. 
- Csak a Resource Manager üzemi modellel létrehozott tárfiókok támogatottak. 
- Hívó azonosító adatokat az Azure Storage Analytics naplózási támogatása hamarosan elérhető lesz.
- A standard szintű storage-fiókok erőforrásokhoz való hozzáférés az Azure AD engedélyezési jelenleg támogatott. Lapblobokat a prémium szintű storage-fiókban való hozzáférés engedélyezése hamarosan támogatott lesz.
- Az Azure Storage támogatja a beépített és az egyéni RBAC-szerepkörökhöz. Az előfizetés, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor hatóköre szerepköröket rendelhet hozzá.
- Az Azure Storage-ügyfélkönyvtárak, amelyek jelenleg támogatják az Azure AD-integráció a következők:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB, Queue és fájlok](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Az Azure AD-tároló áttekintése

Az Azure AD-integráció használatával az Azure Storage első lépése az tárolási adatok RBAC-szerepkörök hozzárendelése az egyszerű szolgáltatásnév (egy felhasználó, csoport vagy alkalmazás, szolgáltatásnév) vagy a felügyelt identitások az Azure-erőforrásokhoz. RBAC-szerepkörök tartalmazzák a közös jogosultságkészletek tárolók és üzenetsorok. RBAC-szerepkörök hozzárendelése az Azure Storage kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).

Az Azure AD használata az alkalmazások tárolási erőforrásokhoz való hozzáférés engedélyezésére, az OAuth 2.0 hozzáférési jogkivonat kérése a kódot kell. A hozzáférési jogkivonat kérése és használhatja az Azure Storage-kérelmek engedélyezését végzi, lásd: [hitelesítés az Azure Storage-alkalmazás (előzetes verzió) az Azure AD](storage-auth-aad-app.md). Ha egy felügyelt identitás használ, tekintse meg [hitelesítés blobok és üzenetsorok az Azure-ral a hozzáférést az Azure-erőforrások (előzetes verzió) által felügyelt identitások](storage-auth-aad-msi.md).

Az Azure CLI és PowerShell mostantól támogatja a adataikkal jelentkezhetnek be az Azure AD identitás. Az Azure AD identity bejelentkezés után a munkamenet identitás alatt fut. További tudnivalókért lásd: [CLI vagy a PowerShell (előzetes verzió) az Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage határozza meg, amely magában foglalja a tárolók és -várólisták eléréséhez használt gyakori jogosultságkészletek beépített RBAC-szerepkör. 

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC-szerepkörök által biztosított hozzáférési engedélyek 

A következő táblázat összefoglalja a hatókör függően a beépített szerepkörök, hozzáférési jogot:

|Hatókör|BLOB adatok tulajdonosa|Blobadatok Közreműködője|Blob Data Reader|Üzenetsorbeli adatok Közreműködője|Queue Data Reader|
|---|---|---|---|---|---|
|Előfizetési szinten|Olvasási/írási hozzáférést és a POSIX hozzáférés-vezérlési felügyeleti a tárolók és blobok az előfizetésben|Tárolók és blobok az előfizetésben az olvasási/írási hozzáférést| Olvasási hozzáférés tárolók és blobok az előfizetésben|Olvasási/írási hozzáférést az előfizetés összes várólista|Olvasási hozzáférés az előfizetésben található összes várólista|
|Erőforráscsoport szintjén|Olvasási/írási hozzáféréssel és a POSIX hozzáférés-vezérlési felügyeleti a tárolók és blobok az erőforráscsoportban|Olvasási/írási hozzáférést a tárolók és blobok az erőforráscsoportban|Olvasási hozzáférés tárolók és blobok az erőforráscsoportban|Olvasási/írási hozzáférés az erőforráscsoportban lévő összes várólista|Olvasási hozzáférés az erőforráscsoportban lévő összes üzenetsor|
|Storage-fiók szintjén|Olvasási/írási hozzáféréssel és a POSIX hozzáférés-vezérlés kezelése a tárolókhoz és a BLOB storage-fiókban|Olvasási/írási hozzáférést a tárolókhoz és a BLOB storage-fiókban|Olvasási hozzáférés tárolók és a BLOB storage-fiókban|Olvasási/írási hozzáférést a storage-fiókban lévő összes várólista|Olvasási hozzáférés a tárfiókban lévő összes üzenetsor|
|Tároló/várólista szint|Olvasási/írási hozzáféréssel és a POSIX hozzáférés-vezérlési kezelési a megadott tárolóhoz és annak blobjaihoz.|Olvasási/írási hozzáférést a megadott tárolóhoz és annak blobjaihoz|A megadott tárolóhoz és annak blobjaihoz olvasási hozzáférés|Olvasási/írási hozzáférést a megadott várólista|Olvasási hozzáférés a megadott várólista|

További információ az Azure Storage-műveletek meghívásához szükséges engedélyekről: [REST-műveleteinek meghívására szolgáló engedélyek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>További lépések

Az Azure-Blobok és üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
