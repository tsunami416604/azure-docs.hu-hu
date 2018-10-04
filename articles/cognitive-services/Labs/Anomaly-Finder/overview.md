---
title: Mi az Anomáliaészlelő? – A Microsoft Cognitive Services |} A Microsoft Docs
description: Anomáliaészlelő speciális algoritmusok használatával az idősorozat-adatok utaló rendellenességek észlelésére és a Microsoft Cognitive Services információkat ad vissza.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 2a0715d3becf695600ed84edbae38151acf055a8
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246842"
---
# <a name="what-is-anomaly-finder"></a>Mi az Anomáliaészlelő?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Anomáliaészlelő lehetővé teszi, hogy idővel-adatok figyelése és észlelje a rendellenességeket, amelyek úgy, hogy automatikusan alkalmazza a megfelelő statisztikai modell függetlenül iparági, a forgatókönyv vagy kötet adataiban alkalmazkodik gépi tanulással. Időbeli adatsorok használatával bemenetként, az Anomáliadetektálási kereső API-t adja vissza egy adatpont egy anomáliadetektálási e meghatározza, hogy a várt értékkel, és a Vizualizáció alsó és felső korlátja. Előre összeállított AI-szolgáltatás Anomáliaészlelő nincs szükség semmilyen machine learning-szaktudását túli RESTful API használatának megértéséhez. Ezáltal a fejlesztési egyszerű és sokoldalú mert együttműködik minden olyan idősorozat-adatok és is beépíthető streamelési adatok rendszerek. Anomáliaészlelő magában foglalja a használati esetek – például pénzügyi eszközök kezelése csalás, ellopják, piacot és az esetleges üzleti incidensek módosítása vagy IoT-eszköz forgalom figyelése névtelenség megőrzése széles körű vég. Ez a megoldás is is lehet forrássá alakítva azokat a végfelhasználók – megismerheti az adatok, a költségkeret-beállítási változásai, adja vissza, befektetés, vagy a felhasználói tevékenység egy szolgáltatás részeként.
Próbálja ki az Anomáliadetektálási kereső API-t, és ezáltal jobban megértheti, az adatokat. 

Tekintse meg, mit hozhat létre az API-val:

* Ismerje meg a várt értékek az idősorozatban található korábbi adatok alapján előre
* Állapítani, hogy egy adatpont anomáliát kívül korábbi minta
* Hozzon létre egy sávon kívüli "normál" értékek tartományán megjelenítése

![Anomaly_Finder](./media/anomaly_detection1.png) 

1. ábra: Értékesítési bevétel rendellenességek észlelése

![Anomaly_Finder](./media/anomaly_detection2.png)

2. ábra: Megoszlásától észlelheti a szolgáltatáskérések

## <a name="requirements"></a>Követelmények

- Minimális adatok bemeneti time series: 13 adatok legalább pontjai nem egyértelmű periodicitás a time series legalább 4 ciklusokat, adatokat a time Series ismert periodicitás pontjait. 
- Adatok sértetlensége: elkülöníti a adatpontok azonos időközének és a nem hiányzó pontok sorozat idő. 

## <a name="identify-anomalies"></a>Utaló rendellenességek észlelésére

Anomáliadetektálás API eredményt adja vissza, amely bármely adott adatpontok: a rendellenességeket, vagy nem-e, és további információkat tartalmaz a következők szerint
* Az időszak – az API-t az anomáliadetektálási pontok észleléséhez használt gyakorisága.
* WarningText – a lehetséges figyelmeztető információkat.
* ExpectedValue – az előre jelzett érték szerint a tanulási modell alapján
* IsAnomaly - arról, hogy az adatpontok-e rendellenességeket, vagy nincs eredmény
* IsAnomaly_Neg – hogy-e az adatpontok rendellenességek negatív irányba (DIP) eredménye
* IsAnomaly_Pos – hogy-e az adatpontok rendellenességek pozitív irányba (ugrásszerűen) eredménye
* UpperMargin - ExpectedValue és UpperMargin összege határozza meg, a felső határa, amely adatpont van továbbra is úgy Gondoltuk szokásos módon
* LowerMargin - (ExpectedValue - LowerMargin) meghatározza, hogy az alsó határérték, hogy adatpont van továbbra is úgy Gondoltuk szokásos módon

> [!Note]
> UpperMargin és LowerMargin körül tényleges idősorozat normál értékek megjelenítése egy sávon kívüli létrehozására is használható. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Alsó és felső korlát utáni a válasz feldolgozása a beállítása

Anomáliadetektálás API-t, hogy egy adatpont anomáliadetektálási vagy nem alapértelmezett eredményt adja vissza, és a felső és alsó határérték ExpectedValue és UpperMargin/LowerMargin alapján. A legtöbb esetben ezek az alapértelmezett értékek megfelelően működnek. Néhány esetben azonban különböző esik, mint az alapértelmezett is szükséges. Az ajánlott eljárás szerint módosíthatja a dinamikus határokon UpperMargin vagy LowerMargin egy coefficiency alkalmazza.

### <a name="examples-with-1152-as-coefficiency"></a>1/1.5-ös/2 coefficiency, a példákat

![Alapértelmezett érzékenység](./media/sensitivity_1.png)

![1.5-ös érzékenység](./media/sensitivity_1.5.png)

![2 érzékenység](./media/sensitivity_2.png)

Mintaadatok kérése

[!INCLUDE [Request](./includes/request.md)]

Példa JSON-válasz

[!INCLUDE [Response](./includes/response.md)]
