---
title: azcopy-feladatok | Microsoft Docs
description: Ez a cikk a azcopy Jobs paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514729"
---
# <a name="azcopy-jobs"></a>azcopy jobs

A feladatok kezeléséhez kapcsolódó alparancsok.

## <a name="examples"></a>Példák

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Beállítások

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|Súgótartalom megjelenítése a feladatok parancshoz.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
- [azcopy feladatok listája](storage-ref-azcopy-jobs-list.md)
- [azcopy-feladatok folytatása](storage-ref-azcopy-jobs-resume.md)
- [azcopy-feladatok megjelenítése](storage-ref-azcopy-jobs-show.md)
