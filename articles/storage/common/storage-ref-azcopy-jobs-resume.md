---
title: azcopy állások folytatódnak | Microsoft dokumentumok
description: Ez a cikk az azcopy-feladatok folytatási parancsának referenciaadatait tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034144"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Folytatja a meglévő feladatot az adott feladatazonosítóval.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|--cél-sas karakterlánc|Az adott JobId úti cél célSAS-a.|
|--karakterlánc kizárása|Szűrő: A feladat folytatásakor zárja ki ezeket a sikertelen átvitel(eke)t. A fájlokat a ";" (a)|
|-h, --segítség|A folytatási parancs súgótartalmának megjelenítése.|
|--karakterláncot is tartalmaz|Szűrő: csak ezeket a sikertelen átvitel(eke)t tartalmazza a feladat folytatásakor. A fájlokat a ";" (a)|
|--forrás-sas karakterlánc |forrás SAS forrás az adott JobId.|

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
