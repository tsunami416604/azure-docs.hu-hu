---
title: azcopy-feladatok | Microsoft Docs
description: Ez a cikk a azcopy Jobs paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd5469691f8d859bfcbca7479cdcb2e468a94e26
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195840"
---
# <a name="azcopy-jobs"></a>azcopy-feladatok

A feladatok kezeléséhez kapcsolódó alparancsok.

## <a name="examples"></a>Példák

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|Súgótartalom megjelenítése a feladatok parancshoz.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
- [azcopy feladatok listája](storage-ref-azcopy-jobs-list.md)
- [azcopy-feladatok folytatása](storage-ref-azcopy-jobs-resume.md)
- [azcopy-feladatok megjelenítése](storage-ref-azcopy-jobs-show.md)
