---
title: Adatkészlet-hozzárendelés konfigurálása az Azure-beli adatmegosztás előzetes verziójában
description: Megtudhatja, hogyan konfigurálhatja az adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás előzetes verziójának használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169131"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Adatkészlet-hozzárendelés konfigurálása egy fogadott megosztáshoz az Azure-beli adatmegosztás előzetes verziójában

Ez a cikk azt ismerteti, hogyan lehet konfigurálni az adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-beli adatmegosztás előzetes verziójának használatával. Ezt akkor érdemes megtenni, ha elfogadta az adatmegosztási meghívást, de az "elfogadás és konfigurálás később" lehetőséget választotta. Ellenkező esetben előfordulhat, hogy egyszerűen módosítani szeretné a célhely Storage-fiókját a kapott adataihoz. 

## <a name="navigate-to-a-received-data-share"></a>Nyisson meg egy fogadott adatmegosztást

Az Azure-beli adatmegosztási szolgáltatásban navigáljon a kapott megosztáshoz, és válassza a **részletek** lapot. 

![Adatkészlet-hozzárendelési](./media/dataset-mapping.png "adatkészlet megfeleltetése") 

Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet, majd kattintson a **+ Térkép**gombra a cél elemre. Előfordulhat, hogy először leképezésének megszüntetése kell, ha már konfigurált egy célként megadott Storage-fiókot, és módosítani szeretné a leképezést egy másik Storage-fiókra. 

![Leképezés célként]megadott(./media/dataset-map-target.png "leképezésre") 

## <a name="select-a-new-storage-account"></a>Új Storage-fiók kiválasztása 

Válassza ki azt a Storage-fiókot, amelybe az adatterületet szeretné kijelölni. Vegye figyelembe, hogy a korábban leképezett Storage-fiókokban már létező összes adattal a rendszer nem helyezi át automatikusan az új Storage-fiókba.

![Cél Storage-fiók](./media/map-target.png "célhelyének tárolója") 

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.



