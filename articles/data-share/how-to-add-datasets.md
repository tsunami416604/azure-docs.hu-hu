---
title: Adatkészletek hozzáadása meglévő Azure-adatmegosztáshoz
description: Ismerje meg, hogyan adhat hozzá adatkészleteket egy meglévő adatmegosztáshoz az Azure Data Share-ben, és hogyan oszthat meg ugyanazokat a címzetteket.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490541"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Adatkészletek hozzáadása meglévő megosztáshoz az Azure Data Share-ben

Ez a cikk bemutatja, hogyan adhat hozzá adatkészleteket egy már meglévő adatmegosztáshoz az Azure Data Share használatával. Ez lehetővé teszi, hogy több adatot osszon meg ugyanazzal a címzettel anélkül, hogy új megosztást kellene létrehoznia.

Az adatkészletek megosztás létrehozásakor való hozzáadásáról az [Adatmegosztás](share-your-data.md) oktatóanyagban talál további információt.

## <a name="navigate-to-a-sent-data-share"></a>Navigálás elküldött adatmegosztásra

Az Azure Data Share-ben keresse meg az elküldött **+ Add Datasets** megosztást, és válassza az **Adatkészletek** lapot.

![Adatkészletek hozzáadása](./media/how-to/how-to-add-datasets/add-datasets.png)

A jobb oldali panelen jelölje ki a hozzáadni kívánt adatkészlettípust, majd kattintson a **Tovább**gombra. Válassza ki a hozzáadni kívánt adatok előfizetését és erőforráscsoportját. A legördülő nyilak segítségével keresse meg, majd jelölje be a hozzáadni a hozzáadni a jelölőnégyzet.

![Adatkészletek hozzáadása](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Ha az **Adatkészletek hozzáadása gombra**kattint, az adatkészletek hozzáadódnak a megosztáshoz. Megjegyzés: A fogyasztóknak el kell indítaniuk egy pillanatképet, hogy az új adatkészleteket láthassák. Ha vannak pillanatkép-beállítások konfigurálva, a fogyasztók az új adatkészletek, ha a következő ütemezett pillanatkép befejeződik. A pillanatkép-beállítások konfigurálása nélkül az fogyasztónak manuálisan kell elindítania az adatok teljes vagy növekményes másolatát a frissítések fogadásához. A pillanatképekről további információt a Pillanatképek című [témakörben talál.](terminology.md)

## <a name="next-steps"></a>További lépések
További információ arról, hogyan [vehet fel címzetteket egy meglévő adatmegosztásba.](how-to-add-recipients.md)