---
title: azcopy lista | Microsoft dokumentumok
description: Ez a cikk az azcopy list parancs hivatkozási információit tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034098"
---
# <a name="azcopy-list"></a>azcopy list

Egy adott erőforrás entitásait sorolja fel.

## <a name="synopsis"></a>Áttekintés

Csak blob tárolók támogatottak az aktuális kiadásban.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --segítség|Súgótartalom megjelenítése a listaparancshoz.|
|--gép által olvasható|A fájlméretek listázása bájtban.|
|--mega-egységek|Az egységeket 1000-es rendelésben jeleníti meg, nem 1024-ben.|
|--futás-tally|Megszámolja a fájlok teljes számát és méretét.|

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
