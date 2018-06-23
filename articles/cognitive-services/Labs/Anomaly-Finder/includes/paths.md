---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348171"
---
<a name="paths"></a>
## <a name="paths"></a>Elérési utak

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Az anomáliadetektálási pontok észleléséhez az adatsorozat pontjainak alkalommal irányuló kérés.
```
POST /anomalydetection
```


#### <a name="parameters"></a>Paraméterek

|Típus|Name (Név)|Leírás|Séma|
|---|---|---|---|
|**Törzs**|**Törzs**  <br>*Szükséges*|Az idősor adatpont, valamint szükség esetén időszak.|[Kérelem](#request)|


#### <a name="responses"></a>Válaszok

|HTTP-kód|Leírás|Séma|
|---|---|---|
|**200**|A sikeres műveletet.|< [válasz](#response) > tömb|
|**400**|JSON-kérelmi nem tudja értelmezni.|Nincs tartalom|
|**403**|A megadott tanúsítvány nem fogadja el a kiszolgálón.|Nincs tartalom|
|**405**|A metódus nem engedélyezett.|Nincs tartalom|
|**500**|Belső kiszolgálóhiba.|Nincs tartalom|


#### <a name="consumes"></a>Felhasználva

* `application/json`


#### <a name="produces"></a>Hozza létre

* `application/json`


#### <a name="tags"></a>Címkék

* anomalydetection



