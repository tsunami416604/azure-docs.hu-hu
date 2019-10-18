---
title: azcopy kijelentkezése | Microsoft Docs
description: Ez a cikk a azcopy kijelentkezési parancsával kapcsolatos információkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 84b65470b12212eb1038e18bd442ff07511a5c1a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513496"
---
# <a name="azcopy-logout"></a>azcopy logout

Naplózza a felhasználót, és leállítja az Azure Storage-erőforrásokhoz való hozzáférést.

## <a name="synopsis"></a>Áttekintés

Ezzel a paranccsal az aktuális felhasználó összes gyorsítótárazott bejelentkezési adata törlődik.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Beállítások

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|A kijelentkezési parancshoz tartozó súgótartalom megjelenítése.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
