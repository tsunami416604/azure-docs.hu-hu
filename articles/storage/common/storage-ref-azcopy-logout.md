---
title: azcopy kijelentkezése | Microsoft Docs
description: Ez a cikk a azcopy kijelentkezési parancsával kapcsolatos információkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195918"
---
# <a name="azcopy-logout"></a>azcopy kijelentkezése

Naplózza a felhasználót, és leállítja az Azure Storage-erőforrásokhoz való hozzáférést.

## <a name="synopsis"></a>Áttekintés

Ezzel a paranccsal az aktuális felhasználó összes gyorsítótárazott bejelentkezési adata törlődik.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|A kijelentkezési parancshoz tartozó súgótartalom megjelenítése.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
