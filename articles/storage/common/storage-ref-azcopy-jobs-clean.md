---
title: azcopy munkahelyek tiszta | Microsoft dokumentumok
description: Ez a cikk az azcopy feladatok tiszta parancs referenciaadatait tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033722"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Az összes napló- és tervfájl eltávolítása az összes feladathoz

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Beállítások

**-h, --segítség**                Segítség a tiszta.

**--állapottal karakterláncmal** csak az ezzel az állapottal rendelkező feladatok eltávolítása, elérhető értékek: Visszavont, Befejezett, Sikertelen, Sikertelen, InProgress, Mind (alapértelmezett "Összes")

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

**--sapka-mbps uint32**      Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.

**--output-type** string A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg". (alapértelmezett "szöveg")

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
