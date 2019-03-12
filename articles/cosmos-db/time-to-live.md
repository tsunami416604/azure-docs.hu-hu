---
title: Az Azure Cosmos DB-élettartama adatok elévülése
description: Élettartam Microsoft Azure Cosmos DB üríti ki automatikusan a rendszer bizonyos idő után dokumentumok lehetőséget biztosít.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: f9dec5b3aeb951316985c965de70a372f55b8225
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549187"
---
# <a name="time-to-live-in-azure-cosmos-db"></a>Az Azure Cosmos DB élettartama 

A "Time to Live" vagy a TTL a Azure Cosmos DB lehetővé teszi elemek törlése automatikusan végre egy bizonyos idő elteltével. Alapértelmezés szerint be lehet állítani a tároló szintjén live és a konfigurációelem alapon érték felülírása idejét. Miután beállította az élettartam egy tároló vagy egy elem szinten, Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az adott időszakban, utolsó módosításának időpontja után. Élettartam másodpercek alatt van konfigurálva. Élettartam konfigurálásakor a rendszer automatikusan törli a lejárt elemek az élettartam értéke ellentétben kifejezetten az ügyfélalkalmazás által kiadott egy törlési művelet alapján.

## <a name="time-to-live-for-containers-and-items"></a>Tárolók és elemek élettartama

Az élettartam időtartamot másodpercben, és az idő, elem utolsó módosításának a különbözeti kerül értelmezésre. Egy tároló vagy egy elem a tárolóban lévő élettartam állíthatja be:

1. **Egy tároló élettartama** (használatával `DefaultTimeToLive`):

   - Ha hiányzik (vagy null értékű), elemek nem automatikusan lejárt.

   - Ha jelen van, az értéke "-1" értékre van állítva, végtelen egyenlő – alapértelmezés szerint elemek nem jár le.

   - Ha jelen van, és az érték beállítása néhány száma ("n") – elemek "n" másodperc után az utolsó módosításának ideje hamarosan lejár.

2. **Egy elem élettartama idő** (használatával `ttl`):

   - Ez a tulajdonság nem alkalmazható csak akkor, ha `DefaultTimeToLive` megtalálható nincs beállítva, és a szülőtároló null értékű.

   - Ha jelen van, ez a beállítás felülbírálja a `DefaultTimeToLive` szülőtároló értékét.

## <a name="time-to-live-configurations"></a>Élő konfigurációk ideje

* Élettartam egy tárolón "n" értékre van állítva, majd az elem a tárolóban lejár n másodperc múlva.  Ha élő,-1 (Ez azt jelzi, hogy ne járjanak le) állítsa be a saját idővel rendelkező elemek ugyanabban a tárolóban vannak, vagy ha bizonyos elemek rendelkezik felülbírálva time to live beállítás egy másik számmal, ezek az elemek lejár a konfigurált TTL-érték alapján. 

* Ha nincs beállítva egy tárolón TTL, majd time to live, az elem ebben a tárolóban nem befolyásolja. 

* Egy tároló TTL -1 értékre van állítva, ha egy elem ebben a tárolóban, amely rendelkezik az idő az élő set-n, n másodperc múlva lejár, és a fennmaradó elemek nem jár le. 

Élettartam alapján elemek törlése díjmentes. Nem kell további költenie (azaz nincs további Kérelemegységet felhasznált) élettartam lejárta következtében elem törlésekor.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan élettartama konfigurálja az alábbi cikkeket:

* [Élettartama konfigurálása](how-to-time-to-live.md)
