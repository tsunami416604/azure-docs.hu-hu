---
title: azcopy doc | Microsoft Docs
description: Ez a cikk a azcopy doc parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513868"
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

|Lehetőség|Leírás|
|--|--|
|-h,-– Súgó|A doc parancs súgójának tartalmát jeleníti meg.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
