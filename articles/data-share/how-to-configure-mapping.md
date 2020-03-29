---
title: Adatkészlet-leképezés konfigurálása az Azure Data Share szolgáltatásban
description: Ismerje meg, hogyan konfigurálhatja az adatkészlet-leképezést egy fogadott megosztáshoz az Azure Data Share használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964243"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Adatkészlet-leképezés konfigurálása fogadott megosztáshoz az Azure Data Share-ben

Ez a cikk bemutatja, hogyan konfigurálhatja az adatkészlet-leképezést egy fogadott megosztáshoz az Azure Data Share használatával. Ezt akkor kell megtennie, ha elfogadta az adatmegosztási meghívást, de úgy döntött, hogy "Később elfogadja és konfigurálja", vagy ha az adatokat helyben osztják meg. Előfordulhat, hogy be szeretne állítani egy adatkészlet-leképezést, ha módosítania kell az Önnel megosztott adatok célját, vagy ha adatokat szeretne kapni egy SQL Server kiszolgálón. 

## <a name="navigate-to-a-received-data-share"></a>Navigálás fogadott adatmegosztásra

Az Azure Data Share szolgáltatásban keresse meg a fogadott megosztást, és válassza a **Részletek** lapot. 

![Adatkészlet-hozzárendelés](./media/dataset-mapping.png "Adatkészlet-hozzárendelés") 

Jelölje be azt az adatkészletet, amelyhez úti célt szeretne rendelni. A meglévő leképezés leképezésének megszüntetéséhez válassza a **Leképezés megszüntetése** lehetőséget. Válassza a **+ Térkép lehetőséget, ha** új célboltot szeretne választani. 

![Leképezés a célhoz](./media/dataset-map-target.png "Leképezés a célhoz") 

## <a name="select-a-new-target-store"></a>Új céltár kiválasztása

Válassza ki azt a céladattípust, amelyben az adatokat meg szeretné adni. Pillanatkép-alapú megosztás esetén a korábban leképezett tárfiókokban már meglévő adatok nem kerülnek automatikusan az új céltárba. Helybeni megosztásesetén jelöljön ki egy adattavát a megadott helyen. A hely az az Azure-adatközpont, ahol az adatszolgáltató forrás-adattár található.

![Céltárfiók](./media/dataset-map-target-sql.png "Céltárolás") 

## <a name="select-a-file-format-sql-sources-only"></a>Fájlformátum kiválasztása (csak SQL-források esetén)

Ha a forrásadatok SQL-alapú forrásból származnak, kiválaszthatja, hogy melyik formátumban érkezik. 

![Formátum kiválasztása](./media/sql-file-formats.png "SQL fájlformátumok")

## <a name="next-steps"></a>További lépések

Az adatok megosztásának megkezdéséhez folytassa az [adatkezelési oktatóanyag megosztásával.](share-your-data.md)



