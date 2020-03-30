---
title: azcopy jobs show | Microsoft dokumentumok
description: Ez a cikk az azcopy-feladatok megjelenítési parancsának referenciainformációit tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034123"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Az adott feladatazonosító részletes információit jeleníti meg.

## <a name="synopsis"></a>Áttekintés

Ha csak a feladatazonosítót adja meg jelző nélkül, akkor a feladat folyamatösszefoglalása kerül visszaadásra.

A parancs futtatásakor megjelenő bájtszámok és készültségi százalékok csak a feladatban befejezett fájlokat tükrözik. Nem tükrözik a részben befejezett fájlokat.

Ha `with-status` a jelző be van állítva, akkor a megadott értékkel rendelkező feladatban lévő átvitelek listája jelenik meg.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --segítség|A show parancs súgótartalmának megjelenítése.|
|--állapottal karakterlánc|Csak az ezzel az állapottal rendelkező feladat átvitelét sorolja fel, a rendelkezésre álló értékeket: Elindítva, Sikeres, Sikertelen|

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
