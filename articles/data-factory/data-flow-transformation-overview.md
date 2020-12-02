---
title: Az adatfolyam-átalakítás megfeleltetésének áttekintése
description: A leképezési adatfolyamban elérhető különböző átalakítások áttekintése
author: dcstwh
ms.author: weetok
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9d44890e84e97a413543a4291d1331fee0f04841
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490890"
---
# <a name="mapping-data-flow-transformation-overview"></a>Az adatfolyam-átalakítás megfeleltetésének áttekintése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Alább látható a leképezési folyamat által jelenleg támogatott átalakítások listája. A konfiguráció részleteinek megismeréséhez kattintson az egyes átalakításokra.

| Név | Kategória | Leírás |
| ---- | -------- | ----------- |
| [Összesítés](data-flow-aggregate.md) | Séma-módosító | Definiáljon különböző típusú összesítéseket, például a SUM, a MIN, a MAX és a COUNT értéket meglévő vagy számított oszlopok szerint csoportosítva. | 
| [Sor módosítása](data-flow-alter-row.md) | Sor módosítója | Sorok beszúrási, törlési, frissítési és upsert beállítása. |
| [Feltételes felosztás](data-flow-conditional-split.md) | Több bemenet/kimenet | Adatsorok továbbítása különböző streamekhez a megfelelő feltételek alapján. |
| [Származtatott oszlop](data-flow-derived-column.md) | Séma-módosító | új oszlopok előállítása vagy meglévő mezők módosítása az adatfolyam kifejezésének nyelvével. | 
| [Létezik](data-flow-exists.md) | Több bemenet/kimenet | Győződjön meg arról, hogy az adatai más forrásokban vagy streamekben léteznek. | 
| [Szűrő](data-flow-filter.md) | Sor módosítója | Sorok szűrése feltétel alapján. |
| [Egybesimítás](data-flow-flatten.md) | Séma-módosító |  A tömb értékeit hierarchikus szerkezetekben, például JSON-ban, illetve az egyes sorokba való kivonással végezheti el. |
| [Csatlakozás](data-flow-join.md) | Több bemenet/kimenet |  Két forrásból vagy streamből származó adatok egyesítése. |
| [Keresés](data-flow-lookup.md) | Több bemenet/kimenet | Egy másik forrásból származó adatokra mutató hivatkozás. |
| [Új ág](data-flow-new-branch.md) | Több bemenet/kimenet | Több művelet és átalakítás alkalmazása ugyanazon az adatfolyamon. |
| [Kimutatás](data-flow-pivot.md) | Séma-módosító | Összesítés, amelyben egy vagy több csoportosítási oszlop különálló oszlopokra alakítja át a különböző sorok értékeit. |
| [Sorszám](data-flow-rank.md) | Séma-módosító | Rendezett rangsor létrehozása rendezési feltételek alapján |
| [Kiválasztás](data-flow-select.md) | Séma-módosító | Alias oszlopok és adatfolyam-nevek, valamint oszlopok eldobása vagy átrendezése |
| [Sink (Fogadó)](data-flow-sink.md) | - | Az adataihoz tartozó végső cél |
| [Rendezés](data-flow-sort.md) | Sor módosítója | A bejövő sorok rendezése az aktuális adatfolyamon |
| [Forrás](data-flow-source.md) | - | Az adatfolyam adatforrása |
| [Helyettes kulcs](data-flow-surrogate-key.md) | Séma-módosító | Növekményes nem üzleti tetszőleges kulcs értékének hozzáadása |
| [Union](data-flow-union.md) | Több bemenet/kimenet | Több adatfolyam függőleges egyesítése |
| [Elemi értékekre bontás](data-flow-unpivot.md) | Séma-módosító | Oszlopok tagolása sorokba |
| [Ablak](data-flow-window.md) | Séma-módosító |  Az oszlopok ablakos összesítésének meghatározása az adatfolyamokban. |
