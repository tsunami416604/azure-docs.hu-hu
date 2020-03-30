---
title: azcopy env | Microsoft dokumentumok
description: Ez a cikk az azcopy env parancsra vonatkozó referenciainformációkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033746"
---
# <a name="azcopy-env"></a>azcopy env

Az AzCopy viselkedését konfiguráló környezeti változók megjelenítése.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Ha egy környezeti változót a parancssorból állít be, az a változó olvasható lesz a parancssori előzményekben. Fontolja meg a parancssori előzmények hitelesítő adatait tartalmazó változók törlését. Ha meg szeretné tartani, hogy a változók ne jelenjenek meg az előzményekben, parancsfájl segítségével kérheti a felhasználótól a hitelesítő adatait, és beállíthatja a környezeti változót.

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h, --segítség|Az env parancs súgótartalmának megjelenítése. |
|--show-érzékeny|Érzékeny/titkos környezeti változók megjelenítése.|

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
