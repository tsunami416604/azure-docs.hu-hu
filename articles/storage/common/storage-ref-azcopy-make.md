---
title: azcopy make | Microsoft Docs
description: Ez a cikk a azcopy make paranccsal kapcsolatos tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16d7965b8d42a6fdd6ebd68307b01b322cfa7cc6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285237"
---
# <a name="azcopy-make"></a>azcopy make

Létrehoz egy tárolót vagy fájlmegosztást.

## <a name="synopsis"></a>Áttekintés

Hozzon létre egy tárolót vagy fájlmegosztást, amelyet a megadott erőforrás URL-címe képvisel.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|-h,-– Súgó|Súgótartalom megjelenítése a make parancshoz. |
|--kvóta-GB UInt32|Meghatározza a megosztás maximális méretét gigabájtban (GiB), a nulla érték azt jelenti, hogy elfogadja a Fájlszolgáltatások alapértelmezett kvótáját.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>További információ

- [azcopy](storage-ref-azcopy.md)
