---
title: "Azure áttelepítése assessment beállítások testreszabása |} Microsoft Docs"
description: "Ismerteti, hogyan lehet beállítása és futtatása egy értékelési áttelepítése VMware virtuális gépek esetén az Azure-bA az Azure áttelepítési Planner segítségével"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

[Az Azure áttelepítése](migrate-overview.md) értékelések létrehozza az alapértelmezett beállításokkal. Miután létrehozta a értékelését, módosíthatja ezeket az utasításokat követve ebben a cikkben alapértelmezett beállításokat.


## <a name="edit-assessment-values"></a>Értékelés értékek szerkesztése

1. Azure áttelepítése projektben **értékelések** lapon válassza ki az értékelés, és kattintson a **tulajdonságainak szerkesztése**.
2. Módosítsa a beállításokat az alábbi táblázat szerint.

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni. |  USA 2. nyugati alapértelmezett helye.
    **Tárhely-redundancia** | Az a tárolótípus, amelyet az Azure-beli virtuális gépek a migrálás után használni fognak. | Csak [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikációs jelenleg támogatott.
    **Kényelmi faktor** | Megerősítő tényező értékelés során használt puffer. Ezzel a fiókot a műveleteket, mint az határozza használati, a rövid teljesítményelőzményei, a későbbi használat valószínűleg növelése. | Alapértelmezett érték 1.3 x.
    **Teljesítmény-előzmények** | Idő teljesítményelőzményei kiértékelésekor. | Alapértelmezés szerint egy hónap.
    **PERCENTILIS kihasználtsága** | A PERCENTILIS teljesítési előzmények figyelembe kell venni. | Alapértelmezett érték 95 %.
    **Tarifacsomag** | Megadhatja a [tarifacsomag](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) egy virtuális gép számára.  | Alapértelmezés szerint a [szabványos](../virtual-machines/windows/sizes-general.md) réteg szolgál.
    **Az ajánlat** | [Az Azure-ajánlatok](https://azure.microsoft.com/support/legal/offer-details/) , amelyek érvényesek. | [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) az alapértelmezett beállítás.
    **Pénznem** | Számlázás pénzneme. | Alapértelmezés szerint USD.
    **Kedvezményes (%)** | Bármely előfizetés-specifikus kedvezményes kapni bármely platformra. | Az alapértelmezett érték 0 %.
    **Azure hibrid használata juttatás** | Azt jelzi, hogy Ön a Regisztrálás a [Azure hibrid használata juttatás](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha értéke Igen, a Windows Azure árak vannak figyelembe venni a Windows virtuális gépek. | Az alapértelmezett érték az Igen.

3. Kattintson a **mentése** az értékelés frissítése.


## <a name="next-steps"></a>Következő lépések

[További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
