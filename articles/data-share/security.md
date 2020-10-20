---
title: Az Azure Data Share biztonsági áttekintése
description: Az Azure Data Share biztonsági áttekintése
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 5d815c27ecc7825f0bc1e6772654b094a799b63d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216555"
---
# <a name="security-overview-for-azure-data-share"></a>Az Azure Data Share biztonsági áttekintése

Ez a cikk az Azure-beli adatmegosztási szolgáltatás biztonsági áttekintését tartalmazza.

## <a name="security-overview"></a>Biztonsági áttekintés

Az Azure-adatmegosztás kihasználja az Azure által az inaktív és az átvitel alatt álló adatok védelméhez használt mögöttes biztonsági adatokat. Az adatok titkosítva vannak, ahol a mögöttes adattár támogatja. Az adatátvitelt is titkosítja a rendszer. Az adatmegosztások metaadatait a rendszer a REST és az átvitel során is titkosítja. 

A hozzáférés-vezérlés beállítható az Azure-beli adatmegosztási erőforrás szintjén, így biztosítva, hogy azok elérhetők legyenek a jogosultak számára. 

Az Azure-adatmegosztás felügyelt identitást (korábbi nevén MSI) használ az adatok megosztásához használt adattárakhoz való hozzáféréshez. Az adatszolgáltató és az adatfogyasztó között nincs hitelesítő adatok cseréje. A felügyelt identitással kapcsolatos további információkért tekintse meg az [Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)ismertető témakört. Az adatok megosztásához szükséges szerepkörökről és engedélyekről a [szerepkörök és követelmények](concepts-roles-permissions.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.