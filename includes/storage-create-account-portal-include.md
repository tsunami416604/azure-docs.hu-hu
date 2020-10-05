---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bed8475e5d6c7bf26003672b6cf9ce51a82384ad
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377174"
---
Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Storage-fiókok** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Storage-fiókok**lehetőséget.
1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások** lapon válassza ki azt az előfizetést, amelyben létre szeretné hozni a Storage-fiókot.
1. Az **erőforráscsoport** mezőben válassza ki a kívánt erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot.  További információ az Azure-erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../articles/azure-resource-manager/resource-group-overview.md)).
1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. Válasszon teljesítményszint-szintet. Az alapértelmezett szint a *standard*.
1. Állítsa a **Fiók típusa** mezőt a *Storage v2 (általános célú v2)* értékre.
1. Annak meghatározása, hogy a rendszer hogyan replikálja a Storage-fiókot. Az alapértelmezett replikációs beállítás a következő: *olvasási hozzáférés geo-redundáns tárolás (ra-GRS)*. Az elérhető replikációs lehetőségekkel kapcsolatos további információkért lásd: [Azure Storage redundancia](../articles/storage/common/storage-redundancy.md).
1. Határozza meg a Blobok hozzáférési szintjét a Storage-fiókban. Az alapértelmezett szint a *gyors*. A blob-hozzáférési rétegekkel kapcsolatos további információkért lásd: gyakori [, ritka elérésű és archív hozzáférési szintek a blobokhoz](../articles/storage/blobs/storage-blob-storage-tiers.md).
1. A Azure Data Lake Storage használatához válassza a **speciális** fület, majd állítsa be a **hierarchikus névteret** **engedélyezve**értékre. További információ: [Azure Data Lake Storage Gen2 bevezetés](../articles/storage/blobs/data-lake-storage-introduction.md)
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Kattintson a **Létrehozás** gombra.

Az alábbi képen az új Storage-fiók **alapjai** lapon található beállítások láthatók:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Tárfiók Azure Portalon történő létrehozását bemutató képernyőkép":::
