---
title: azcopy doc | Microsoft Docs
description: Ez a cikk a azcopy doc parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d1f57ae40b47c1d910ba20ae8a8f19cdc6908d6b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196035"
---
# <a name="azcopy-doc"></a>azcopy doc

Markdown formátumban hozza létre az eszköz dokumentációját.

## <a name="synopsis"></a>Áttekintés

Markdown formátumban hozza létre az eszköz dokumentációját, és a megadott helyen tárolja azokat.

Alapértelmezés szerint a fájlokat a rendszer egy "doc" nevű mappában tárolja az aktuális könyvtáron belül.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|A doc parancs súgójának tartalmát jeleníti meg.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
