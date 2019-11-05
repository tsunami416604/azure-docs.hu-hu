---
title: Adatkészlet-hozzárendelés konfigurálása az Azure-adatmegosztásban
description: Megtudhatja, hogyan konfigurálhat adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490623"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Adatkészlet-hozzárendelés konfigurálása egy fogadott megosztáshoz az Azure-adatmegosztásban

Ez a cikk azt ismerteti, hogyan lehet konfigurálni egy adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával. Ezt akkor érdemes megtenni, ha elfogadta az adatmegosztási meghívást, de az "elfogadás és konfigurálás később" lehetőséget választotta. Ha módosítani szeretné az Önnel megosztott adatokat, vagy ha az adatokat egy SQL Server szeretné fogadni, érdemes lehet konfigurálnia az adatkészlet-leképezést. 

## <a name="navigate-to-a-received-data-share"></a>Nyisson meg egy fogadott adatmegosztást

Az Azure-beli adatmegosztási szolgáltatásban navigáljon a kapott megosztáshoz, és válassza a **részletek** lapot. 

![Adatkészlet-hozzárendelés](./media/dataset-mapping.png "Adatkészlet-hozzárendelés") 

Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet. A meglévő leképezés leképezésének megszüntetése válassza a **leképezésének megszüntetése** lehetőséget. Válassza a **+ leképezés a célhelyre** lehetőséget egy új célhely kiválasztásához. 

![Leképezés célhelyre](./media/dataset-map-target.png "Leképezés célhelyre") 

## <a name="select-a-new-destination-store"></a>Új célhely kiválasztása

Válassza ki a célként használni kívánt adattípust. Vegye figyelembe, hogy a korábban leképezett Storage-fiókokban már létező összes adattal a rendszer nem helyezi át automatikusan az új célhelyre.

![Cél Storage-fiók](./media/dataset-map-target-sql.png "Cél tárterülete") 

## <a name="select-a-file-format-sql-sources-only"></a>Fájlformátum kiválasztása (csak SQL-források)

Ha a forrásadatok SQL-alapú forrásból származnak, kiválaszthatja, hogy melyik formátumot fogadja a rendszer. 

![Formátum kiválasztása](./media/sql-file-formats.png "SQL-fájlformátumok")

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.



