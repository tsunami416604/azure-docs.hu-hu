---
title: azcopy-feladatok listája | Microsoft Docs
description: Ez a cikk a azcopy Jobs List parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a37735fcfec7909e37177140116ebcf7de8a298e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513672"
---
# <a name="azcopy-jobs-list"></a>azcopy jobs list

Az összes feladatra vonatkozó információkat jeleníti meg.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy jobs list [flags]
```

## <a name="options"></a>Beállítások

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|Súgótartalom megjelenítése a List parancshoz.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
