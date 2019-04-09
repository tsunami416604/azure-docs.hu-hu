---
title: Az Azure Cosmos DB-élettartama adatok elévülése
description: Élettartam Microsoft Azure Cosmos DB üríti ki automatikusan a rendszer bizonyos idő után dokumentumok lehetőséget biztosít.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271271"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Élettartam (TTL) az Azure Cosmos DB 

A **élettartama** vagy TTL, az Azure Cosmos DB lehetővé teszi elemek törlése automatikusan végre egy bizonyos idő elteltével. Alapértelmezés szerint be lehet állítani a tároló szintjén live és a konfigurációelem alapon érték felülírása idejét. Miután beállította az élettartam egy tároló vagy egy elem szinten, Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az adott időszakban, utolsó módosításának időpontja után. Élettartam másodpercek alatt van konfigurálva. Élettartam konfigurálásakor a rendszer automatikusan törli a lejárt elemek anélkül, hogy egy törlési művelet explicit módon az ügyfélalkalmazás által kiadott az élettartam értéke alapján.

## <a name="time-to-live-for-containers-and-items"></a>Tárolók és elemek élettartama

Az élettartam időtartamot másodpercben, és az idő, elem utolsó módosításának a különbözeti kerül értelmezésre. Egy tároló vagy egy elem a tárolóban lévő élettartam állíthatja be:

1. **Egy tároló élettartama** (használatával `DefaultTimeToLive`):

   - Ha hiányzik (vagy null értékű), elemek nem automatikusan lejárt.

   - Ha jelen van, az értéke "-1" értékre van állítva, infinity megegyezik, és alapértelmezés szerint elemek nem jár le.

   - Ha jelen van, és az érték beállítása bizonyos számú *"n"* – elemek lejárati *"n"* után az utolsó módosításának ideje (s).

2. **Egy elem élettartama idő** (használatával `ttl`):

   - Ez a tulajdonság nem alkalmazható csak akkor, ha `DefaultTimeToLive` megtalálható nincs beállítva, és a szülőtároló null értékű.

   - Ha jelen van, ez a beállítás felülbírálja a `DefaultTimeToLive` szülőtároló értékét.

## <a name="time-to-live-configurations"></a>Élő konfigurációk ideje

* Ha az élettartam értéke *"n"* a tárolót, majd az elem a tárolóban érvényessége *n* másodperc.  Ha élő,-1 (Ez azt jelzi, hogy ne járjanak le) állítsa be a saját idővel rendelkező elemek ugyanabban a tárolóban vannak, vagy ha bizonyos elemek rendelkezik felülbírálva time to live beállítás egy másik számmal, ezek az elemek lejár a saját konfigurált TTL-érték alapján. 

* Ha nincs beállítva egy tárolón TTL, majd time to live, az elem ebben a tárolóban nem befolyásolja. 

* Egy tároló TTL -1 értékre van állítva, ha egy elem ebben a tárolóban, amely rendelkezik az idő az élő set-n, n másodperc múlva lejár, és a fennmaradó elemek nem jár le. 

Élettartam alapján elemek törlése díjmentes. Nem kell további költenie (azaz nincs további Kérelemegységet felhasznált) élettartam lejárta következtében elem törlésekor.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan élettartama konfigurálja az alábbi cikkeket:

* [Élettartama konfigurálása](how-to-time-to-live.md)
