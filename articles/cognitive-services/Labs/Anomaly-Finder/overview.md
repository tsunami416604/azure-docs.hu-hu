---
title: Mi az az Anomáliadetektálási kereső? -Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Anomáliadetektálási kereső speciális algoritmusok segítségével felmérheti a sorozat időadatok rendellenességeinek és kognitív Microsoft-szolgáltatásokban adatokat ad vissza.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348179"
---
# <a name="what-is-anomaly-finder"></a>Mi az az Anomáliadetektálási kereső?

Anomáliadetektálási kereső időbeli-adatok figyelése és a machine Learning segítségével úgy, hogy a jobb oldali statisztikai modell függetlenül iparági, a forgatókönyv vagy kötet automatikusan alkalmazza az egyedi adatait alkotási rendellenességek észlelését teszi lehetővé. Idősor használja bemeneti adatként, az Anomáliadetektálási kereső API értéket ad vissza attól adatpont az anomáliadetektálási határozza meg a várt érték, és a képi megjelenítéshez tartozó alsó és felső határai. Előre elkészített AI szolgáltatásként Anomáliadetektálási kereső gépi tanulási szakértői RESTful API használatának megértése túl nincs szükség. Így fejlesztési egyszerű és sokoldalú mert bármely idő adatsorozat adatokkal dolgozik, és is beépítve streamadatok rendszerek. Anomáliadetektálási kereső magában foglalja a használati esetek – például pénzügyi eszközök kezelése csalás, lopás, piacok, és lehetséges üzleti incidensek módosítása vagy IoT eszköz forgalom figyelése közben megőrződnek névtelenség széleskörű időtartama. Ez a megoldás is képes aranykészleteiket a végfelhasználók-adatok, ugyanis változásai ismertetése, térjen vissza a befektetés vagy a felhasználói tevékenység egy szolgáltatás részeként.
Próbálja ki az Anomáliadetektálási kereső API-t, és mélyrehatóbb ismereteket szerezhet az adatok. 

Tekintse meg az API-t hozhat létre:

* Ismerje meg a várt értékeinek idősorozatban található korábbi adatok alapján
* Mondja el, hogy adatpont-e az anomáliadetektálási kívül korábbi minta
* A sávon kívüli "normál" értékek tartományán megjelenítéséhez készítése

![Anomaly_Finder](./media/anomaly_detection1.png) 

1. ábra: Értékesítési bevétel rendellenességek észlelését

![Anomaly_Finder](./media/anomaly_detection2.png)

2. ábra: A szolgáltatáskérések mintát változások keresése

## <a name="requirements"></a>Követelmények

- Bemeneti minimális adatok time series: 13 adatok minimális pontjai nélkül egyértelmű gyakoriság, a time series idősorozatban az ismert periodikusságát pontjai legalább 4 ciklusok adatok. 
- Adatok sértetlensége: adatpontok vannak elválasztva egymástól ugyanolyan időköz és a nem hiányzó pontok adatsorozat idő. 

## <a name="identify-anomalies"></a>Eltéréseinek azonosítása

Anomáliadetektálás API eredményt adja vissza, amely akár egyetlen adott adatpontok rendellenességeket, vagy nem, és további információkat tartalmaz az alábbiak szerint
* Időszak - gyakorisága, amelyet az API-t használ a anomáliadetektálási pontok.
* WarningText - a lehetséges figyelmeztető információkat.
* ExpectedValue – az előre jelzett érték a Learning alapuló modell
* IsAnomaly - arról, hogy az adatpontok-e rendellenességek észlelését, vagy nincs eredmény
* IsAnomaly_Neg - az eredmény az adatpont van rendellenességeket negatív irányban (immerzióban)
* IsAnomaly_Pos - az eredmény az adatpont van rendellenességeket pozitív irányban (igényeiben jelentkező)
* A felső határa, amely adatpont van még re normál határozza meg a ExpectedValue és UpperMargin UpperMargin-
* LowerMargin - (ExpectedValue - LowerMargin) határozza meg, az alsó határa, hogy az adatpontok az továbbra is vélhetően normál futtatáshoz

> [!Note]
> UpperMargin és LowerMargin körül normál értékek megjelenítése idősor tényleges sáv létrehozásához használható. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Beállítja az alsó és felső határai, a feladás egy vagy több a válasz feldolgozása

Anomáliadetektálás API adatpont-e az anomáliadetektálási vagy nem az alapértelmezett eredményt adja vissza, és a felső és alsó határa ExpectedValue és UpperMargin/LowerMargin alapján. Ezeket az alapértelmezett értékeket kell rendben működik a legtöbb esetben. Bizonyos esetekben azonban szükséges a különböző határai az alapértelmezettnél néhányat a meglévők közül. Az ajánlott eljárás szerint alkalmazza a coefficiency UpperMargin vagy LowerMargin úgy, hogy a dinamikus határain.

### <a name="examples-with-1152-as-coefficiency"></a>1/1.5-ös/2-es, coefficiency példák

![Alapértelmezett érzékenység](./media/sensitivity_1.png)

![1.5-ös érzékenység](./media/sensitivity_1.5.png)

![2 érzékenység](./media/sensitivity_2.png)

Mintaadatokat igénylése

[!INCLUDE [Request](./includes/request.md)]

JSON mintaválasz

[!INCLUDE [Response](./includes/response.md)]
