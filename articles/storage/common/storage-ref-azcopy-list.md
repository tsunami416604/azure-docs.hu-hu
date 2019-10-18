---
title: azcopy listája | Microsoft Docs
description: Ez a cikk a azcopy List parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513519"
---
# <a name="azcopy-list"></a>azcopy list

Egy adott erőforrás entitásait sorolja fel.

## <a name="synopsis"></a>Áttekintés

A jelenlegi kiadásban csak a blob-tárolók támogatottak.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Példák

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Beállítások

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|Súgótartalom megjelenítése a List parancshoz.|
|– géppel olvasható|A fájlok mérete bájtban.|
|--Mega-egységek|Az 1000-as, nem 1024-as megrendelések egységeit jeleníti meg.|
|--Futtatás – Tally|Megszámolja a fájlok teljes számát és méreteit.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
