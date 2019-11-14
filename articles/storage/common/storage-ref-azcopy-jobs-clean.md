---
title: azcopy-feladatok tisztítása | Microsoft Docs
description: Ez a cikk a azcopy-feladatok tiszta parancsával kapcsolatos információkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033722"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Az összes feladat összes naplózási és megtervezési fájljának eltávolítása

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Beállítások

**-h,-– Súgó**                Segítség a tisztításhoz.

**--az-status** sztring csak az ilyen állapotú feladatokat távolítja el, a rendelkezésre álló értékeket: megszakított, befejezett, sikertelen, Inprogress, all (alapértelmezett "all")

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

**--Cap-Mbps UInt32**      Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**--** a parancs kimenetének kimeneti típusú karakterlánc-formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text")

## <a name="see-also"></a>Lásd még

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
