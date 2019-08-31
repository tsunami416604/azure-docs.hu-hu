---
title: azcopy-feladatok folytatása | Microsoft Docs
description: Ez a cikk a azcopy-feladatok folytatására szolgáló parancsra vonatkozó tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195788"
---
# <a name="azcopy-jobs-resume"></a>azcopy-feladatok folytatása

Folytatja a meglévő feladatot a megadott AZONOSÍTÓJÚ feladattal.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|--cél-sas-karakterlánc|Az adott JobId céljának cél SAS-je.|
|– karakterlánc kizárása|Szűrő A sikertelen átvitel (ek) kizárása a feladatok folytatásakor. A fájlokat ";" karakterrel kell elválasztani.|
|-h,-– Súgó|A folytatási parancshoz tartozó súgótartalom megjelenítése.|
|--karakterlánc belefoglalása|Szűrő: csak ezeket a sikertelen átviteleket adja meg a feladatok folytatásakor. A fájlokat ";" karakterrel kell elválasztani.|
|--Source-sas karakterlánc |a forrás SAS-forrása az adott JobId.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
