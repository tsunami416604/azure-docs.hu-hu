---
title: azcopy munkahelyek eltávolítása | Microsoft dokumentumok
description: Ez a cikk az azcopy-feladatok eltávolítási parancsának referenciainformációit tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034159"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Távolítsa el az adott feladatazonosítóhoz társított összes fájlt.

> [!NOTE] 
> Testreszabhatja a napló- és tervfájlok mentési helyét. További információért tekintse meg az [azcopy env](storage-ref-azcopy-env.md) parancsot.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Beállítások

**-h, --segítség**                Segítség az eltávolításhoz.

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

**--sapka-mbps uint32**      Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.

**--output-type** string A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg". (alapértelmezett "szöveg")

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
