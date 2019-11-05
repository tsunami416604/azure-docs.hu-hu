---
title: Adatkészletek hozzáadása egy meglévő Azure-adatmegosztáshoz
description: Megtudhatja, hogyan adhat hozzá adatkészleteket egy meglévő adatmegosztáshoz az Azure-adatmegosztásban, és hogyan oszthat meg ugyanazzal a címzettekkel.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490541"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Adatkészletek hozzáadása meglévő megosztáshoz az Azure-beli adatmegosztásban

Ez a cikk azt ismerteti, hogyan adhat hozzá adatkészleteket egy már meglévő adatmegosztáshoz az Azure-adatmegosztás használatával. Így több adatmegosztást is megoszthat ugyanazzal a címzettekkel anélkül, hogy új megosztást kellene létrehoznia.

Az adatkészletek megosztás létrehozásakor történő hozzáadásával kapcsolatos információkért tekintse meg az [adatok megosztása](share-your-data.md) oktatóanyagot.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-adatmegosztás területen navigáljon az elküldett megosztáshoz, és válassza az **adatkészletek** lapot. további adatkészletek hozzáadásához kattintson az **+ adatkészletek hozzáadása** gombra.

![Adatkészletek hozzáadása](./media/how-to/how-to-add-datasets/add-datasets.png)

A jobb oldali panelen válassza ki a hozzáadni kívánt adatkészlet típusát, majd kattintson a **tovább**gombra. Válassza ki azt az előfizetést és erőforráscsoportot, amelyet hozzá szeretne adni. A legördülő nyilak használatával keresse meg, majd jelölje be a hozzáadandó adatmező melletti jelölőnégyzetet.

![Adatkészletek hozzáadása](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Ha az **adatkészletek hozzáadása**gombra kattint, a rendszer hozzáadja az adatkészleteket a megosztáshoz. Megjegyzés: a felhasználóknak el kell indítaniuk a pillanatképet ahhoz, hogy megjelenjenek az új adatkészletek. Ha vannak beállított pillanatkép-beállítások, a felhasználók a következő ütemezett pillanatkép befejeződése után láthatják az új adatkészleteket. A pillanatkép-beállítások konfigurálása nélkül a felhasználónak manuálisan kell elindítania az adatok teljes vagy növekményes másolatát a frissítések fogadásához. A pillanatképekkel kapcsolatos további információkért lásd: [Pillanatképek](terminology.md).

## <a name="next-steps"></a>További lépések
További információ a [címzettek meglévő adatmegosztáshoz való hozzáadásáról](how-to-add-recipients.md).