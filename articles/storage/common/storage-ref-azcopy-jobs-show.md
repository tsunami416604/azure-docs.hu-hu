---
title: azcopy-feladatok megjelenítése | Microsoft Docs
description: Ez a cikk a azcopy Jobs show paranccsal kapcsolatos tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195957"
---
# <a name="azcopy-jobs-show"></a>azcopy-feladatok megjelenítése

Részletes információkat jelenít meg a megadott AZONOSÍTÓJÚ feladatról.

## <a name="synopsis"></a>Áttekintés

Ha a rendszer csak a AZONOSÍTÓJÚ feladatot adja meg jelző nélkül, akkor a rendszer visszaküldi a feladatokhoz tartozó folyamat összegzését.

Ha a `with-status` jelző be van állítva, a rendszer megjeleníti a megadott értékkel rendelkező feladatban szereplő adatátvitelek listáját.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|Megjeleníti a show parancs súgójának tartalmát.|
|--with-status sztring|Csak az adott állapotú, elérhető értékeket tartalmazó feladatok átadásának listázása: Elindítva, sikeres, sikertelen|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
