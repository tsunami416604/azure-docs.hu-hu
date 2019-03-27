---
title: Mi az az Anomáliadetektálási detector használatával API-t? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektálási detector használatával API korszerű algoritmusokat segítségével azonosíthatja a rendellenességeket az idősoros adatokat.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473199"
---
# <a name="what-is-the-anomaly-detector-api"></a>Mi az az Anomáliadetektálási detector használatával API-t?

A Anomáliadetektálási detector használatával API segítségével figyelheti, és az idősoros adatokat a machine learning észlelheti a rendellenességeket. Az Anomáliadetektálási detector használatával API alkalmazkodik automatikusan azonosításával és a záró titkunkat modellek alkalmazása az adatokhoz, iparági, a forgatókönyv és adatok mennyiségétől függetlenül. Használja az idősoros adatokat, az API határozza meg a határokat a rendellenességek észlelése, a várt értékek, és mely adatpontok rendellenességeket.

![A szolgáltatáskérések megoszlásától észlelése](./media/anomaly_detection2.png)

Az Anomáliadetektálási érzékelő használata nem szükséges minden korábbi tapasztalata az a Machine learning, és a RESTful API lehetővé teszi, hogy könnyedén integrálhatja a szolgáltatást az alkalmazások és folyamatok.

## <a name="features"></a>Szolgáltatások

Az Anomáliadetektálási detector használatával automatikusan észlelheti a rendellenességeket során az idősoros adatokat, vagy a valós idejű előforduló. 

|Szolgáltatás  |Leírás  |
|---------|---------|
|A valós idejű előforduló rendellenességek észlelését. | A streamelési adatok rendellenességek észlelése, hogy a legújabb buildszám-anomáliadetektálási korábban látott adatok pontok használatával. Ez a művelet egy modellt az adatpontok küldhet, és meghatározza, hogy a cél pont anomáliát használatával hoz létre. Hívja meg az API-t minden egyes új adatpont hoz létre, az adatok módon a létrehozást lehet figyelni. |
|A készlet egész rendellenességek észlelése kötegként. | A time series segítségével felismerheti a rendellenességeket, amelyek az adatok során előfordulhat, hogy létezik. Ez a művelet egy modell használatával az egész idősoros adatokat, minden egyes ugyanannak a modellnek az elemzett ponttal állít elő.         |
| Az adatokkal kapcsolatos további információkat kaphat. | Az adatok és minden megfigyelt rendellenességeket, beleértve a várt értékek, anomáliadetektálási határokat és pozíciók hasznos adatainak beolvasása. |
| Módosítsa a rendellenességek észlelése határokat. | Az Anomáliadetektálási detector használatával API automatikusan létrehozza az anomáliadetektálást szolgáló határokat. Ezek a hatókörök növelése vagy csökkentése érdekében az API-k bizalmas adatok rendellenességekre módosíthatja, és jobban illeszkednek az adatok. |

## <a name="demo"></a>Bemutató

Gyorsan megkezdheti az Anomáliadetektálási detector használatával API-val, próbálja meg egy [bemutatóra](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) , amely a böngészőben futtatható. Ez a bemutató webalkalmazás által üzemeltetett Jupyter notebook fut, és bemutatja, hogyan küldhet egy API-kérelem, és az eredmény megjelenítése.

A bemutató futtatásához kövesse az alábbi lépéseket:

1. Egy érvényes Anomáliadetektálási detector használatával API előfizetési kulcs és a egy API-végpont lekérése. A lenti utasítások a regisztráció rendelkezik. 
2. Jelentkezzen be, és a klónozás, kattintson a jobb felső sarkában.
3. Kattintson a **ingyenes számítási futtathatók**
4. Válasszon ki egy a notebookok ehhez a mintához.
5. Az Anomáliadetektálási detector használatával API érvényes előfizetési kulcs, adja hozzá a `subscription_key` változó. Módosítsa a `endpoint` változó a végponthoz. Például:`https://westus2.api.cognitive.microsoft.com`
1. A felső menüsávon kattintson **cella**, majd **összes futtatása**.

## <a name="workflow"></a>Munkafolyamat

A Anomáliadetektálási detector használatával API egy REST-alapú webszolgáltatás, így könnyen minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése, hívja meg.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Regisztrációt követő lépések:

1. Igénybe az idősoros adatokat, és a egy érvényes JSON formátumba konvertálja. Használat [ajánlott eljárások](concepts/anomaly-detection-best-practices.md) az adatok beolvasása a legjobb eredmények elérése érdekében előkészítésekor.
1. Az Anomáliadetektálási detector használatával API-t az adatok egy kérés küldése.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Rendellenességek észlelése az idősoros adatokat Anomáliadetektálási detector használatával REST API használatával](quickstarts/detect-data-anomalies-csharp.md)
* Az Anomáliadetektálási detector használatával API [online bemutatót](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Az Anomáliadetektálási érzékelő [REST API-referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)