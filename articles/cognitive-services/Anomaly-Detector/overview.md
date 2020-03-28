---
title: Mi az az Anomaly Detector API?
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektor API fejlett algoritmusai segítségével azonosíthatja az idősorozat-adatok anomáliáit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053714"
---
# <a name="what-is-the-anomaly-detector-api"></a>Mi az az Anomaly Detector API?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Anomáliadetektor API lehetővé teszi az idősorozat-adatok rendellenességeinek figyelését és észlelését gépi tanulással. Az Anomáliadetektor API alkalmazkodik a legjobban illeszkedő modellek automatikus azonosításával és alkalmazásával az adatokra, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől. Az idősorozat-adatok használatával az API meghatározza az anomáliadetektálás határait, a várt értékeket és az adatpontok anomáliák határait.

![Mintaváltozások észlelése a szolgáltatáskérésekben](./media/anomaly_detection2.png)

Az Anomália-érzékelő használata nem igényel semmilyen korábbi tapasztalata a gépi tanulás, és a RESTful API lehetővé teszi, hogy könnyen integrálja a szolgáltatást az alkalmazások és folyamatok.

## <a name="features"></a>Szolgáltatások

Az anomáliadetektor segítségével automatikusan észlelheti az anomáliákat az idősorozat-adatok ban, illetve valós időben történő előfordulásuk szerint.

|Szolgáltatás  |Leírás  |
|---------|---------|
|Észleli az anomáliákat, ahogy azok valós időben fordulnak elő. | Észlelje a streamelési adatok anomáliáit a korábban látott adatpontok használatával annak megállapítására, hogy a legutóbbi anomália. Ez a művelet létrehoz egy modellt az elküldött adatpontok használatával, és meghatározza, hogy a célpont anomália-e. Az API-t minden új adatponttal együtt meghívhat, így figyelheti az adatokat a létrehozás során. |
|Észlelése anomáliák az adatkészlet ben kötegként. | Az idősorozat segítségével észlelheti az adatokban esetlegesen esetlegesen fennálló rendellenességeket. Ez a művelet egy modellt hoz létre a teljes idősorozat-adatok használatával, és minden egyes pontot ugyanazzal a modellel elemez.         |
| További információk az adatokról. | Hasznos részleteket kaphat az adatokról és a megfigyelt anomáliákról, beleértve a várt értékeket, az anomáliahatárokat és a pozíciókat. |
| Állítsa be az anomáliadetektálási határokat. | Az Anomáliadetektor API automatikusan létrehozza az anomáliadetektálás határait. Állítsa be ezeket a határokat, hogy növelje vagy csökkentse az API érzékenységét az adatanomáliákkal szemben, és jobban illeszkedjen az adatokhoz. |

## <a name="demo"></a>Bemutató

Tekintse meg ezt az [interaktív bemutatót,](https://aka.ms/adDemo) hogy megtudja, hogyan működik az Anomáliadetektor.
A bemutató futtatásához létre kell hoznia egy anomáliadetektor erőforrást, és le kell szereznie az API-kulcsot és a végpontot.

## <a name="notebook"></a>Jegyzetfüzet

Ha meg szeretné tudni, hogyan hívhatja meg az Anomália-detektor API-t, próbálja ki ezt az [Azure-jegyzetfüzetet.](https://aka.ms/adNotebook) Ez a webüzemeltetésű Jupyter-jegyzetfüzet bemutatja, hogyan küldhet API-kérelmet, és hogyan jelenítheti meg az eredményt.

A jegyzetfüzet futtatásához hajtsa végre az alábbi lépéseket:

1. Szerezzen be egy érvényes Anomália-detektor API-előfizetési kulcsot és egy API-végpontot. Az alábbi szakasz ban található a regisztrációra vonatkozó utasítások.
1. Jelentkezzen be, és kattintson a Klónozás gombra a jobb felső sarokban.
1. A klónozási művelet befejezése előtt törölje a jelet a párbeszédpanel "nyilvános" jelölőnégyzetéből, ellenkező esetben a jegyzetfüzet, beleértve az előfizetési kulcsokat is, nyilvános lesz.
1. Kattintson **a Futtatás ingyenes számításon**
1. Jelölje ki az egyik jegyzetfüzetet.
1. Adja hozzá az érvényes Anomália-detektor API-előfizetési kulcsát a `subscription_key` változóhoz.
1. Módosítsa `endpoint` a változót a végpontra. Például:`https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. A felső menüsorban kattintson a **Cella**menügombra, majd **az Összes futtatása parancsra.**

## <a name="workflow"></a>Munkafolyamat

Az Anomáliadetektor API egy RESTful webszolgáltatás, amely megkönnyíti a http-kérelmek et és a JSON elemzésre képes programozási nyelvről történő hívását.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

A regisztráció után:

1. Vegye ki az idősorozat adatait, és alakítsa át érvényes JSON formátumba. A legjobb eredmények elérése érdekében az adatok előkészítésekor gyakorlati [tanácsokkal egészítheti](concepts/anomaly-detection-best-practices.md) ki.
1. Küldjön egy kérelmet az anomáliadetektor API-t az adatokkal.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="algorithms"></a>Algoritmusok

* Az alábbi technikai blogok a használt algoritmusokkal kapcsolatos információkért:
    * [Az Azure Anomaly Detector API bemutatása](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Az SR-CNN algoritmus áttekintése az Azure Anomáliadetektorban](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

A [Microsoft Time Series Anomaly Detection Service](https://arxiv.org/abs/1906.03821) (a KDD 2019 által elfogadott) című tanulmányában további információért olvashat a Microsoft által kifejlesztett SR-CNN algoritmusokról.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Csatlakozás az Anomáliadetektor-közösséghez

* Csatlakozzon az [Anomália-detektortanácsadók csoporthoz a Microsoft Teamsben](https://aka.ms/AdAdvisorsJoin)
* A [kijelölt, felhasználó által létrehozott tartalom](user-generated-content.md) megtekintése

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Az anomáliadetektor REST API-jával észleli az idősorozat-adatok anomáliáinak anomáliáit](quickstarts/detect-data-anomalies-csharp.md)
* Az Anomáliadetektor API [online demója](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Az Anomália-detektor [REST API-hivatkozása](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
