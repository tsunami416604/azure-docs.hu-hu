---
title: Machine Learning-végpontok használata az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan használhatja a gépi nyelv felhasználó által definiált függvények az Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426198"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Azure Machine Learning Studio (klasszikus) integráció a Stream Analytics szolgáltatásban (előzetes verzió)
A Stream Analytics támogatja a felhasználó által definiált függvényeket, amelyek az Azure Machine Learning Studio (klasszikus) végpontjaihoz hívnek. A REST API-támogatás a [Stream Analytics REST API-könyvtárában](https://msdn.microsoft.com/library/azure/dn835031.aspx)található. Ez a cikk kiegészítő információkat tartalmaz a funkció sikeres megvalósításához a Stream Analytics szolgáltatásban. A bemutató is kifüggesztett, és [itt](stream-analytics-machine-learning-integration-tutorial.md)érhető el .

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Áttekintés: Azure Machine Learning Studio (klasszikus) terminológia
A Microsoft Azure Machine Learning Studio (klasszikus) egy együttműködésen alapuló, húzással használható eszközt biztosít az adatokon alapuló prediktív elemzési megoldások létrehozásához, teszteléséhez és üzembe helyezéséhez. Ezt az eszközt az *Azure Machine Learning Studio (klasszikus)* nevű. A stúdió segítségével kommunikálhat a Machine Learning-erőforrásokkal, és egyszerűen készíthet, tesztelhet és itegthet a terven. Ezek az erőforrások és azok meghatározásai az alábbiakban találhatók.

* **Munkaterület:** A *munkaterület* egy tároló, amely az összes többi Machine Learning-erőforrást egy tárolóban tárolja a felügyelet és a vezérlés érdekében.
* **Kísérlet:** *A kísérleteket* adatszakértők hozták létre adatkészletek és gépi tanulási modell betanítása érdekében.
* **Végpont:** *A végpontok* az Azure Machine Learning Studio (klasszikus) objektum, amely a funkciók beviteli, egy adott gépi tanulási modell alkalmazása és a kimenet eredményül kapott kimenet.
* **Pontozási webszolgáltatás:** A *pontozási webszolgáltatás* a fent említett végpontok gyűjteménye.

Minden végpont rendelkezik api-k kötegvégrehajtásés szinkron végrehajtás. A Stream Analytics szinkron végrehajtást használ. Az adott szolgáltatás neve [egy kérés/válasz szolgáltatás](../machine-learning/studio/consume-web-services.md) az Azure Machine Learning Studio (klasszikus).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>A Stream Analytics-feladatokhoz szükséges gépi tanulási erőforrások
A Stream Analytics-feladat feldolgozása céljából egy kérés/válasz végpont, egy [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)és egy swagger definíció mind szükségesek a sikeres végrehajtáshoz. A Stream Analytics rendelkezik egy további végpontdal, amely a swagger-végpont URL-címét állítja össze, megkeresi a felületet, és egy alapértelmezett UDF-definíciót ad vissza a felhasználónak.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Stream Analytics és Machine Learning UDF konfigurálása REST API-n keresztül
Rest API-k használatával konfigurálhatja a feladatot az Azure Machine Language függvények hívására. Ennek lépései a következők:

1. Stream Analytics-feladat létrehozása
2. Bemenet definiálása
3. Kimenet definiálása
4. Felhasználó által definiált függvény (UDF) létrehozása
5. Az UDF-et meghívja tanoncként megszólító Stream Analytics-átalakítás írása
6. A feladat indítása

## <a name="creating-a-udf-with-basic-properties"></a>UdF létrehozása alapvető tulajdonságokkal
Például a következő mintakód létrehoz egy skaláris UDF nevű *newudf,* amely kötődik egy Azure Machine Learning Studio (klasszikus) végpont. Vegye figyelembe, hogy a *végpont* (szolgáltatás URI) megtalálható az API súgólapján a kiválasztott szolgáltatás és az *apiKey* megtalálható a szolgáltatások főoldalán.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Példa kérelem törzse:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>A RetrieveDefaultDefinition végpont hívása az alapértelmezett UDF-hez
A csontváz UDF létrehozása után szükség van az UDF teljes definíciójára. A RetrieveDefaultDefinition végpont segítségével lekérheti az Azure Machine Learning Studio (klasszikus) végponthoz kötött skaláris függvény alapértelmezett definícióját. Az alábbi hasznos tartalom megköveteli, hogy az alapértelmezett UDF-definíció egy Skaláris függvény, amely kötődik egy Azure Machine Learning-végponthoz. Nem adja meg a tényleges végpontot, mivel a PUT-kérelem során már megadta. A Stream Analytics meghívja a kérelemben megadott végpontot, ha az explicit módon meg van adva. Ellenkező esetben az eredetileg hivatkozottat használja. Itt az UDF egyetlen karakterlánc-paramétert (egy mondatot) vesz fel, és egyetlen típusú karakterláncot ad vissza, amely a mondat "hangulat" címkéjét jelzi.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Példa kérelem törzse:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

A minta kimenete ez úgy néz ki, mint az alábbiakban.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Patch UDF a válasz
Most az UDF-et az előző válaszsal kell foltozni, az alábbiak szerint.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Kérelem törzse (kimenet a RetrieveDefaultDefinition-ből):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>A Stream Analytics átalakításának megvalósítása az UDF hívásához
Most lekérdezés az UDF (itt nevezett scoreTweet) minden bemeneti esemény, és írjon választ, hogy az esemény egy kimenetre.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
