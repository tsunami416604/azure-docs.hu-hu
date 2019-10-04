---
title: Adathalmaz-leképezés konfigurálása
description: Adathalmaz-leképezés konfigurálása
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789240"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Egy adatkészlet leképezés kapott megosztás konfigurálása az Azure-adatok megosztása Előzetesében

Ez a cikk azt ismerteti, hogy egy adatkészlet-leképezés konfigurálása Azure Adatelőnézet megosztás használatával fogadott megosztás. Ehhez, ha egy adatok megosztási Meghívás elfogadva, de rögzítette szeretné "Accept és a beállítás később". Ellenkező esetben előfordulhat, hogy egyszerűen módosítani szeretné a cél tárfiók a fogadott adatokhoz. 

## <a name="navigate-to-a-received-data-share"></a>Keresse meg a kapott adatok megosztása

A szolgáltatásban az Azure adatokat oszt meg, keresse meg a kapott megosztást, és válassza ki a **részletek** fülre. 

![Adatkészlet leképezés](./media/dataset-mapping.png "adatkészlet leképezés") 

Jelölje be a jelölőnégyzetet, hova szeretné hozzárendelni, és kattintson a kívánt adatkészlet mellett **+ térkép cél**. Szükség lehet első leképezésének megszüntetése, ha már konfigurálta az a cél tárfiók, és meg szeretné változtatni a leképezés egy másik tárfiókba. 

![Térkép cél](./media/dataset-map-target.png "cél-térkép") 

## <a name="select-a-new-storage-account"></a>Új tárfiók kiválasztása 

Válassza ki, hogy szeretné-e az adatok kerülnek a storage-fiók. Vegye figyelembe, hogy bármely már meglévő minden adatot korábban hozzárendelt tárfiókok nem automatikusan átkerül az új tárfiókot.

![Cél tárfiók](./media/map-target.png "adatméretét") 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan indítsa el az adatok megosztása, folytassa a [az adatok megosztása](share-your-data.md) oktatóanyag.



