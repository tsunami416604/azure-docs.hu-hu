---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: fb02496d9c107a2c21acca6c65ef69fdfceb4597
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904692"
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
|**Törzs**|**Törzs**  <br>*Szükséges*|A time series adatpontok és az időszakot, ha szükséges.|[Kérelem](#request)|


#### <a name="responses"></a>Válaszok

|HTTP-kód|Leírás|Séma|
|---|---|---|
|**200**|Sikeres művelet.|< [válasz](#response) > tömb|
|**400**|JSON-kérelem nem elemezhető.|Nincs tartalom|
|**403-as**|A megadott tanúsítvány nem fogadja el a kiszolgálót.|Nincs tartalom|
|**405-ös**|Metoda není Povolena.|Nincs tartalom|
|**500**|Belső kiszolgálóhiba.|Nincs tartalom|


#### <a name="consumes"></a>Használ fel

* `application/json`


#### <a name="produces"></a>Eredményez

* `application/json`


#### <a name="tags"></a>Címkék

* anomalydetection



