---
title: azcopy kijelentkezés | Microsoft dokumentumok
description: Ez a cikk az azcopy kijelentkezési parancsra vonatkozó információkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 538c54c338540059baa17b9818d444cc863b7309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034058"
---
# <a name="azcopy-logout"></a>azcopy logout

Kijelentkezteti a felhasználót, és megszakítja az Azure Storage-erőforrásokhoz való hozzáférést.

## <a name="synopsis"></a>Áttekintés

Ez a parancs eltávolítja az aktuális felhasználó összes gyorsítótárazott bejelentkezési adatát.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --segítség|A kijelentkezési parancs súgótartalmának megjelenítése.|

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
