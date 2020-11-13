---
title: Az Azure Data Share biztonsági áttekintése
description: Az Azure Data Share biztonsági áttekintése
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578093"
---
# <a name="security-overview-for-azure-data-share"></a>Az Azure Data Share biztonsági áttekintése

Ez a cikk az Azure-beli adatmegosztási szolgáltatás biztonsági áttekintését tartalmazza.

## <a name="security-overview"></a>Biztonsági áttekintés

Az Azure-adatmegosztás kihasználja az Azure által az inaktív és az átvitel alatt álló adatok védelméhez használt mögöttes biztonsági adatokat. Az adatok titkosítva vannak, ahol a mögöttes adattár támogatja. Az adatátvitel a TLS 1,2 használatával is titkosítva van az átvitel során. Az adatmegosztások metaadatait a rendszer a REST és az átvitel során is titkosítja. Az Azure-beli adatmegosztás nem tárolja a megosztott ügyféladatok tartalmát.

Az Azure-adatmegosztás felügyelt identitást (korábbi nevén MSI) használ az adatok megosztásához használt adattárakhoz való hozzáféréshez. Az adatszolgáltató és az adatfogyasztó között nincs hitelesítő adatok cseréje. A felügyelt identitással kapcsolatos további információkért tekintse meg az [Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)ismertető témakört. Az adatok megosztásához szükséges szerepkörökről és engedélyekről a [szerepkörök és követelmények](concepts-roles-permissions.md)című témakörben olvashat bővebben.

Az Azure-adatmegosztás hozzáférés-vezérlése beállítható az adatmegosztási erőforrás szintjén, így biztosítva, hogy azok elérhetők legyenek a jogosultak számára. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Adatok megosztása a vagy a rendszerből az adattárakba az engedélyezett tűzfallal
Ha be van kapcsolva az adatok és a között a tűzfalon keresztüli fiókok megosztása, engedélyeznie kell a **megbízható Microsoft-szolgáltatások használatát** a Storage-fiókban. További részletek: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
