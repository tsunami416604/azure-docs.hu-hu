---
title: azcopy feladatok eltávolítása | Microsoft Docs
description: Ez a cikk a azcopy feladatok eltávolítására szolgáló paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518290"
---
# <a name="azcopy-jobs-remove"></a>azcopy feladatok eltávolítása

Távolítsa el a megadott AZONOSÍTÓJÚ feladathoz társított összes fájlt.

> [!NOTE] 
> Testreszabhatja azt a helyet, ahol a rendszer menti a napló-és a megtervezési fájlokat. További információért tekintse meg a [azcopy env](storage-ref-azcopy-env.md) parancsot.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>Példák

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Beállítások

**-h,-– Súgó**                Súgó az eltávolításhoz.

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

**--Cap-Mbps UInt32**      Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**--** a parancs kimenetének kimeneti típusú karakterlánc-formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text")

## <a name="see-also"></a>Lásd még:

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
