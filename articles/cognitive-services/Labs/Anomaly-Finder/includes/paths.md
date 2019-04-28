---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309501"
---
<a name="paths"></a>
## <a name="paths"></a>Elérési utak

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>A kért adatsorozat adatpontjainak alkalommal anomáliadetektálási pontok észleléséhez
```
POST /anomalydetection
```


#### <a name="parameters"></a>Paraméterek

|Típus|Name (Név)|Leírás|Séma|
|---|---|---|---|
|**Törzs**|**body**  <br>*Szükséges*|A time series adatpontok és az időszakot, ha szükséges.|[Kérelem](#request)|


#### <a name="responses"></a>Válaszok

|HTTP-kód|Leírás|Séma|
|---|---|---|
|**200**|Sikeres művelet.|< [válasz](#response) > tömb|
|**400**|JSON-kérelem nem elemezhető.|Nincs tartalom|
|**403**|A megadott tanúsítvány nem fogadja el a kiszolgálót.|Nincs tartalom|
|**405**|Metoda není Povolena.|Nincs tartalom|
|**500**|Belső kiszolgálóhiba.|Nincs tartalom|


#### <a name="consumes"></a>Használ fel

* `application/json`


#### <a name="produces"></a>Eredményez

* `application/json`


#### <a name="tags"></a>Címkék

* anomalydetection



