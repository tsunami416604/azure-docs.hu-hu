---
title: azcopy-feladatok megjelenítése | Microsoft Docs
description: Ez a cikk a azcopy Jobs show paranccsal kapcsolatos tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ffb16936ec96f30ad7d8e05babbadb85e01a71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281939"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Részletes információkat jelenít meg a megadott AZONOSÍTÓJÚ feladatról.

## <a name="synopsis"></a>Áttekintés

Ha a rendszer csak a AZONOSÍTÓJÚ feladatot adja meg jelző nélkül, akkor a rendszer visszaküldi a feladatokhoz tartozó folyamat összegzését.

A parancs futtatásakor megjelenő bájtok száma és százalékos készültsége csak a feladatokban befejezett fájlokat tükrözi. Nem tükrözik a részben befejezett fájlokat.

Ha a `with-status` jelző be van állítva, a rendszer megjeleníti a megadott értékkel rendelkező feladatban szereplő adatátvitelek listáját.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="options"></a>Lehetőségek

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|Megjeleníti a show parancs súgójának tartalmát.|
|--with-status sztring|Csak az adott állapotú feladatok átvitelének listázása, elérhető értékek: elindítva, sikeres, sikertelen|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy jobs](storage-ref-azcopy-jobs.md)
