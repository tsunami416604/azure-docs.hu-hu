---
title: "Azure áttelepítése assessment beállítások testreszabása |} Microsoft Docs"
description: "Ismerteti, hogyan lehet beállítása és futtatása egy értékelési áttelepítése VMware virtuális gépek esetén az Azure-bA az Azure áttelepítési Planner segítségével"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a068b9c7-5f87-4fe1-90b9-3be48d91aa3f
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 20aeb2073ad307952f92c8377bc9d78169f1756c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="customize-an-assessment"></a>Egy értékelési testreszabása

[Az Azure áttelepítése](migrate-overview.md) értékelések létrehozza az alapértelmezett beállításokkal. Miután létrehozta a értékelését, módosíthatja ezeket az utasításokat követve ebben a cikkben alapértelmezett beállításokat.


## <a name="edit-assessment-values"></a>Értékelés értékek szerkesztése

1. Azure áttelepítése projektben **értékelések** lapon válassza ki az értékelés, és kattintson a **tulajdonságainak szerkesztése**.
2. Módosítsa a beállításokat az alábbi táblázat szerint.

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, amelyre át szeretné. |  USA 2. nyugati alapértelmezett helye.
    **Adattároló redundanciája, amely** | Az Azure virtuális gépek által használt, az áttelepítés után tárolási típusát. | Csak [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikációs jelenleg támogatott.
    **Megerősítő tényező** | Megerősítő tényező értékelés során használt puffer. Ezzel a fiókot a műveleteket, mint az határozza használati, a rövid teljesítményelőzményei, a későbbi használat valószínűleg növelése. | Alapértelmezett érték 1.3 x.
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
