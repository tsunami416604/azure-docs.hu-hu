---
title: azcopy-feladatok megjelenítése | Microsoft Docs
description: Ez a cikk a azcopy Jobs show paranccsal kapcsolatos tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513448"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Részletes információkat jelenít meg a megadott AZONOSÍTÓJÚ feladatról.

## <a name="synopsis"></a>Áttekintés

Ha a rendszer csak a AZONOSÍTÓJÚ feladatot adja meg jelző nélkül, akkor a rendszer visszaküldi a feladatokhoz tartozó folyamat összegzését.

A parancs futtatásakor megjelenő bájtok száma és százalékos készültsége csak a feladatokban befejezett fájlokat tükrözi. Nem tükrözik a részben befejezett fájlokat.

Ha a `with-status` jelző be van állítva, a rendszer megjeleníti a megadott értékkel rendelkező feladatban szereplő adatátvitelek listáját.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Beállítások

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|Megjeleníti a show parancs súgójának tartalmát.|
|--with-status sztring|Csak az adott állapotú feladatok átvitelének listázása, elérhető értékek: elindítva, sikeres, sikertelen|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
