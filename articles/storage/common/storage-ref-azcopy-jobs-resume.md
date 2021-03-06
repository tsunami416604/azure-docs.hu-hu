---
title: azcopy-feladatok folytatása | Microsoft Docs
description: Ez a cikk a azcopy-feladatok folytatására szolgáló parancsra vonatkozó tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dd25bec04d651c01d622f0652a29a65069421786
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281956"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Folytatja a meglévő feladatot a megadott AZONOSÍTÓJÚ feladattal.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="options"></a>Lehetőségek

|Beállítás|Leírás|
|--|--|
|--cél-sas-karakterlánc|A megadott AZONOSÍTÓJÚ feladathoz tartozó cél SAS|
|– karakterlánc kizárása|Szűrő: a sikertelen átvitel (ek) kizárása a feladatok folytatásakor. A fájlokat ";" karakterrel kell elválasztani.|
|-h,-– Súgó|A folytatási parancshoz tartozó súgótartalom megjelenítése.|
|--karakterlánc belefoglalása|Szűrő: csak ezeket a sikertelen átviteleket adja meg a feladatok folytatásakor. A fájlokat ";" karakterrel kell elválasztani.|
|--Source-sas karakterlánc |a megadott AZONOSÍTÓJÚ feladathoz tartozó forrás SAS-je.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
