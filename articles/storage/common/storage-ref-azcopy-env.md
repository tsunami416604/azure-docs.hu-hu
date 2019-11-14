---
title: azcopy env | Microsoft Docs
description: Ez a cikk a azcopy env parancsra vonatkozó tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033746"
---
# <a name="azcopy-env"></a>azcopy env

Megjeleníti a AzCopy viselkedését beállító környezeti változókat.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|Megjeleníti a env parancshoz tartozó súgó tartalmát. |
|--bizalmas megjelenítés|Érzékeny/titkos környezeti változókat jelenít meg.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
