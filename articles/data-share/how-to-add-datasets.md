---
title: Adatkészletek hozzáadása meglévő adatmegosztáshoz az Azure-beli adatmegosztás előzetes verziójában
description: Adatkészletek hozzáadása meglévő adatmegosztáshoz
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: 75ac837491927881fd8b7547b0f49a069c3f2d47
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257217"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Adatkészletek hozzáadása meglévő megosztáshoz az Azure-beli adatmegosztás előzetes verziójában

Ez a cikk azt ismerteti, hogyan adhat hozzá adatkészleteket egy meglévő adatmegosztáshoz az Azure adatmegosztás előzetes verziójával. Így több adatmegosztást is megoszthat ugyanazzal a címzettekkel anélkül, hogy új megosztást kellene létrehoznia.

Az adatkészletek megosztás létrehozásakor történő hozzáadásával kapcsolatos információkért tekintse meg az [adatok megosztása](share-your-data.md) oktatóanyagot.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-beli adatmegosztás előzetes verziójában navigáljon az elküldett megosztáshoz, és válassza az **adatkészletek** lapot. További adathalmazok hozzáadásához kattintson az **+ adatkészletek hozzáadása** gombra.

![Adatkészletek hozzáadása](./media/how-to/how-to-add-datasets/add-datasets.png)

A jobb oldali panelen válassza ki a hozzáadni kívánt adatkészlet típusát, majd kattintson a **tovább**gombra. Válassza ki azt az előfizetést és erőforráscsoportot, amelyet hozzá szeretne adni. A legördülő nyilak használatával keresse meg, majd jelölje be a hozzáadandó adatmező melletti jelölőnégyzetet.

![Adatkészletek hozzáadása](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Ha az **adatkészletek hozzáadása**gombra kattint, a rendszer hozzáadja az adatkészleteket a megosztáshoz. Megjegyzés: A felhasználóknak el kell indítaniuk a pillanatképet ahhoz, hogy megjelenjenek az új adatkészletek. Ha vannak beállított pillanatkép-beállítások, a felhasználók a következő ütemezett pillanatkép befejeződése után láthatják az új adatkészleteket. A pillanatkép-beállítások konfigurálása nélkül a felhasználónak manuálisan kell elindítania az adatok teljes vagy növekményes másolatát a frissítések fogadásához. A pillanatképekkel kapcsolatos további információkért lásd: [Pillanatképek](terminology.md).

## <a name="next-steps"></a>További lépések
További információ a [címzettek meglévő adatmegosztáshoz való hozzáadásáról](how-to-add-recipients.md).