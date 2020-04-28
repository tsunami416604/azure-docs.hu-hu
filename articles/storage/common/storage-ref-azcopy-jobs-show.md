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
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034123"
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

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|Megjeleníti a show parancs súgójának tartalmát.|
|--with-status sztring|Csak az adott állapotú feladatok átvitelének listázása, elérhető értékek: elindítva, sikeres, sikertelen|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
