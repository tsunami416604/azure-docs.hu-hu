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
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518511"
---
# <a name="azcopy-jobs-clean"></a>azcopy-feladatok tisztítása

Az összes feladat összes naplózási és megtervezési fájljának eltávolítása

```
azcopy jobs clean [flags]
```

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

## <a name="see-also"></a>Lásd még:

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
