---
title: Adatkészlet-hozzárendelés konfigurálása az Azure-adatmegosztásban
description: Megtudhatja, hogyan konfigurálhat adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964243"
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

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.



