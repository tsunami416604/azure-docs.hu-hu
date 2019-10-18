---
title: azcopy make | Microsoft Docs
description: Ez a cikk a azcopy make paranccsal kapcsolatos tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514694"
---
# <a name="azcopy-make"></a>azcopy make

Létrehoz egy tárolót vagy fájlmegosztást.

## <a name="synopsis"></a>Áttekintés

Hozzon létre egy tárolót vagy fájlmegosztást, amelyet a megadott erőforrás URL-címe képvisel.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Példák

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Beállítások

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|Súgótartalom megjelenítése a make parancshoz. |
|--kvóta-GB UInt32|Meghatározza a megosztás maximális méretét gigabájtban (GiB), 0 azt jelenti, hogy elfogadja a Fájlszolgáltatások alapértelmezett kvótáját.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
