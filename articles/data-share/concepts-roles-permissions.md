---
title: Szerepkörök és a követelmények az Azure Data megosztás előzetes verzió
description: Szerepkörök és a követelmények az Azure Data megosztás előzetes verzió
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807522"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Szerepkörök és a követelmények az Azure Data megosztás előzetes verzió

Ez a cikk ismerteti a szerepkörök megosztani az adatokat használja adatok megosztása előzetes verziójába, fogadja el, és az Azure Data megosztása előzetes verzió használata az adatok fogadásához szükséges. 

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Megosztani, és használata az Azure adatokat oszt meg az adatok fogadásához, jelentkezzen be az Azure-ban használt felhasználói fiók számára adatokat megosztani, hogy az adatok megosztása vagy az adatok fogadása a Storage-fiókba kell lennie. Általában egy engedéllyel, hogy megtalálható-e a **tulajdonosa** , vagy egy egyéni szerepkör hozzárendelt Microsoft.Authorization/role hozzárendelések/írási engedéllyel. 

Megosztásához, vagy az adatok fogadására, vagy az Azure Storage-fiókot, a storage-fiók tulajdonosának kell lennie. Akkor is, ha a tárfiókot hozott létre, ez nem automatikusan biztosít, a Storage-fiók tulajdonjogát. A tulajdonosi szerepkörhöz az Azure Storage-fiók hozzáadása a saját maga, kövesse az alábbi lépéseket.

1. Lépjen a tárfiókhoz az Azure Portalon
1. Válassza ki **hozzáférés-vezérlés (IAM)**
1. Kattintson a **hozzáadása**
1. Adja hozzá a saját maga tulajdonosként

Az előfizetésben található engedélyek megtekintéséhez a Microsoft Azure Portalon kattintson a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. 

## <a name="resource-provider-registration"></a>Erőforrás-szolgáltatói regisztrációt 

Ha egy Azure Data megosztási meghívás elfogadása, manuálisan kell regisztrálni a Microsoft.DataShare erőforrás-szolgáltatót az előfizetésében kell. Kövesse az alábbi lépéseket az Azure-előfizetésben helyezi a Microsoft.DataShare erőforrás-szolgáltató regisztrálásához. 

1. Az Azure Portalon lépjen a **előfizetések**
1. Válassza ki az előfizetést, amely a használni kívánt Azure-adatok megosztása
1. Kattintson a **erőforrás-szolgáltatók**
1. Microsoft.DataShare keresése
1. Kattintson a **regisztrálása**

## <a name="next-steps"></a>További lépések

- További információ az Azure - szerepkörök [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

