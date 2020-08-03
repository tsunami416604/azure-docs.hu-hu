---
title: Adatkészlet-hozzárendelés konfigurálása az Azure-adatmegosztásban
description: Megtudhatja, hogyan konfigurálhat adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 82de05e7169c5803dd999521f61a33b9dd17b567
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511955"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Adatkészlet-hozzárendelés konfigurálása egy fogadott megosztáshoz az Azure-adatmegosztásban

Ez a cikk azt ismerteti, hogyan lehet konfigurálni egy adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával. Ezt akkor érdemes megtenni, ha elfogadta az adatmegosztási meghívást, de az "elfogadás és konfigurálás később" beállítást választotta, vagy ha az adatmegosztás helyben történik. Ha módosítani szeretné az Önnel megosztott adatokat, vagy ha az adatokat egy SQL Server szeretné fogadni, érdemes lehet konfigurálnia az adatkészlet-leképezést. 

## <a name="navigate-to-a-received-data-share"></a>Nyisson meg egy fogadott adatmegosztást

Az Azure-beli adatmegosztási szolgáltatásban navigáljon a kapott megosztáshoz, és válassza a **részletek** lapot. 

![Adatkészlet-hozzárendelés](./media/dataset-mapping.png "Adatkészlet-hozzárendelés") 

Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet. A meglévő leképezés leképezésének megszüntetése válassza a **leképezésének megszüntetése** lehetőséget. Válassza a **+ leképezés a célhelyre** lehetőséget egy új célhely kiválasztásához. 

![Leképezés célhelyre](./media/dataset-map-target.png "Leképezés célhelyre") 

## <a name="select-a-new-target-store"></a>Új tároló kiválasztása

Válassza ki a célként használni kívánt adattípust. A pillanatkép-alapú megosztáshoz a korábban leképezett tárolási fiókokban már létező adatok nem lesznek automatikusan áthelyezve az új tárolóba. A helyi megosztáshoz válasszon egy adattárat a megadott helyen. A hely az az Azure-adatközpont, ahol az adatszolgáltató forrás-adattára található.

![Cél Storage-fiók](./media/dataset-map-target-sql.png "Cél tárterülete") 

## <a name="select-a-file-format-sql-sources-only"></a>Fájlformátum kiválasztása (csak SQL-források)

Ha a forrásadatok SQL-alapú forrásból származnak, kiválaszthatja, hogy melyik formátumot fogadja a rendszer. 

![Formátum kiválasztása](./media/sql-file-formats.png "SQL-fájlformátumok")

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.



