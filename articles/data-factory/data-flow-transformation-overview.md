---
title: Adatfolyam-transzformáció leképezése – áttekintés
description: Az adatfolyam leképezése során elérhető különböző átalakítások áttekintése
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 0442c701f39fd3e84361ad0201aaf8d1840d8851
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606285"
---
# <a name="mapping-data-flow-transformation-overview"></a>Adatfolyam-transzformáció leképezése – áttekintés

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Az alábbi lista az adatfolyam leképezése jelenleg támogatott átalakításokat tartalmazza. Kattintson az egyes átalakítások, hogy megtanulják a konfigurációrészleteit.

| Name (Név) | Kategória | Leírás |
| ---- | -------- | ----------- |
| [Összesítés](data-flow-aggregate.md) | Sémamódosító | Definiáljon különböző típusú összesítéseket, például SZUM, MIN, MAX és DARAB meglévő vagy számított oszlopok szerint csoportosítva. | 
| [Sor módosítása](data-flow-alter-row.md) | Sormódosító | Állítson be beszúrási, törlési, frissítési és upsert házirendeket a sorokra. |
| [Feltételes felosztás](data-flow-conditional-split.md) | Több bemenet/kimenet | Adatsorok at irányítson különböző adatfolyamokba az egyeztetési feltételek alapján. |
| [Származtatott oszlop](data-flow-derived-column.md) | Sémamódosító | új oszlopokat hozhat létre, vagy módosíthatja a meglévő mezőket az adatfolyam-kifejezés nyelvével. | 
| [Létezik](data-flow-exists.md) | Több bemenet/kimenet | Ellenőrizze, hogy az adatok léteznek-e más forrásban vagy adatfolyamban. | 
| [Szűrő](data-flow-filter.md) | Sormódosító | Sor szűrése feltétel alapján. |
| [Egybesimítás](data-flow-flatten.md) | Sémamódosító |  A hierarchikus struktúrákon , például a JSON-on belül vegye be a tömbértékeket, és állítsa le őket az egyes sorokba. |
| [Csatlakozás](data-flow-join.md) | Több bemenet/kimenet |  Két forrásból vagy adatfolyamból származó adatok kombinálása. |
| [Keresés](data-flow-lookup.md) | Több bemenet/kimenet | Hivatkozási adatok egy másik forrásból. |
| [Új ág](data-flow-new-branch.md) | Több bemenet/kimenet | Több művelet- és átalakítást alkalmazhat ugyanarra az adatfolyamra. |
| [Kimutatás](data-flow-pivot.md) | Sémamódosító | Olyan összesítés, amelyben egy vagy több csoportosítási oszlop különböző sorértékeit egyedi oszlopokká alakítják át. |
| [Kiválasztás](data-flow-select.md) | Sémamódosító | Aliasoszlopok és adatfolyamnevek, valamint oszlopok eldobása vagy átrendezése |
| [Sink (Fogadó)](data-flow-sink.md) | - | Az adatok végső úti célja |
| [Rendezés](data-flow-sort.md) | Sormódosító | Bejövő sorok rendezése az aktuális adatfolyamban |
| [Forrás](data-flow-source.md) | - | Adatforrás az adatfolyamhoz |
| [Helyettes kulcs](data-flow-surrogate-key.md) | Sémamódosító | Növekménynövelő, nem üzleti jellegű tetszőleges kulcsérték hozzáadása |
| [Union](data-flow-union.md) | Több bemenet/kimenet | Több adatfolyam egyesítése függőlegesen |
| [Elemi értékekre bontás](data-flow-unpivot.md) | Sémamódosító | Oszlopok elforgatása sorértékekké |
| [Ablak](data-flow-window.md) | Sémamódosító |  Ablakalapú összesítések definiálása az adatfolyamokban. |
