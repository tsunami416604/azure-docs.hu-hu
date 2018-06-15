---
title: Azure Stream Analytics a Machine Learning végpontok használata
description: Ez a cikk ismerteti a gép nyelvi felhasználó által megadott függvények használata az Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: bdc6041258e4a5ecf602d19c0d912918f86af313
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911201"
---
# <a name="machine-learning-integration-in-stream-analytics"></a>A Stream Analytics tanulási integrációs számítógép
A Stream Analytics támogatja a felhasználó által definiált függvények, amelyek az Azure Machine Learning-végpont hívásához. Ez a szolgáltatás REST API-támogatása részleteit a a [Stream Analytics REST API-könyvtár](https://msdn.microsoft.com/library/azure/dn835031.aspx). Ez a cikk ezt a képességet a Stream Analytics sikeres végrehajtásához szükség kiegészítő információkat tartalmazza. Oktatóanyag is közzé lett, és elérhető [Itt](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Áttekintés: Azure Machine Learning-terminológia
A Microsoft Azure Machine Learning segítségével létrehozása, tesztelése és telepítése az adataihoz prediktív elemzési megoldások együttműködési, fogd és vidd eszközt biztosít. Ez az eszköz neve a *Azure Machine Learning Studio*. A studio segítségével kommunikál a Machine Learning erőforrások és könnyen létrehozása, tesztelése és a Tervező többször. Ezeket az erőforrásokat és a definíciójukat alatt van.

* **Munkaterület**: A *munkaterület* egy olyan tároló, amely tárolja az összes többi Machine Learning erőforrása együtt a felügyeleti és ellenőrzési tárolója.
* **Kísérlet**: *kísérletek* adatszakértőkön át a adatkészletek használják, és a gépi tanulási modell betanításához hozza létre.
* **Végpont**: *végpontok* szolgáltatások bemeneti adatként, alkalmazza a megadott gépi tanulási modellek és térjen vissza a pontozott kimeneti használt Azure Machine Learning objektum.
* **Webszolgáltatás pontozási**: A *webservice pontozási* olyan végpontok gyűjteménye, fent említett.

Minden egyes végpont API-k vannak a kötegelt végrehajtási és szinkron végrehajtása. A Stream Analytics szinkron végrehajtási használja. Az adott szolgáltatás nevű egy [kérelem-válasz szolgáltatás](../machine-learning/studio/consume-web-services.md) AzureML Studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Gépi tanulási, amelyekre szükség van a Stream Analytics-feladatok
A Stream Analytics alkalmazásában feladat feldolgozást, egy kérelem-válasz végpont egy [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), és a swagger-definíciójában minden szükséges, a sikeres végrehajtáshoz. A Stream Analytics egy további végpontot, amely hoz létre a swagger-végpont URL-címét, a kapcsolat és visszaadása alapértelmezett UDF definícióját a felhasználó rendelkezik.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfiguráljon egy Stream Analytics és a gépi tanulási UDF REST API-n keresztül
REST API-k használatával konfigurálhatja az Azure Machine nyelvi függvényeinek meghívása a feladatot. A lépések a következők:

1. Stream Analytics-feladat létrehozása
2. Adja meg a bemeneti
3. Adja meg egy kimeneti
4. Hozzon létre egy felhasználói függvény (UDF)
5. Az UDF meghívja a Stream Analytics átalakítást írása
6. Indítsa el a feladatot

## <a name="creating-a-udf-with-basic-properties"></a>Egy UDF alaptulajdonságait létrehozása
Tegyük fel, az alábbi példakód létrehoz egy skaláris UDF nevű *newudf* , amely az Azure Machine Learning-végpont van kötve. Vegye figyelembe, hogy a *végpont* API súgólap a kiválasztott szolgáltatás található (URI-szolgáltatás) és a *apiKey* szolgáltatások fő lapján található.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Példa kérelemtörzset:  

````
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
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Alapértelmezett UDF RetrieveDefaultDefinition végpont meghívása
A vázat UDF létrehozása után az UDF teljes definíciója van szükség. A RetreiveDefaultDefinition végpont segíti, hogy az alapértelmezett definíciója skaláris függvényt, amely az Azure Machine Learning-végpont van kötve. Az alábbi tartalmat igényel tehet szert az alapértelmezett UDF definíciója skaláris függvényt, amely az Azure Machine Learning-végpont van kötve. Azt nem adja meg a tényleges végpontja, akkor már megadott PUT kérés során. A Stream Analytics meghívja a végpont, ha explicit módon megadott megadva, a kérelemben. Ellenkező esetben azt használja az eredetileg hivatkozott. Itt az UDF vesz egy karakterlánc-paramétert (mondat), és értéket ad vissza, egyetlen kimeneti típusú karakterlánc, ami azt jelenti, hogy a mondatok "véleményeket" feliratát.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Példa kérelemtörzset:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

A kimeneti minta e keresse meg valami szeretné alatt.  

````
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
````

## <a name="patch-udf-with-the-response"></a>A válasz javítás UDF-ben
Most már az UDF kell javítani az előző válaszban, az alább látható módon.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

A kérelem törzsében (RetrieveDefaultDefinition kimenete):

````
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
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Az UDF hívására Stream Analytics átalakítása megvalósítása
Most már az UDF-ben (Itt nevű scoreTweet) lekérdezni az összes bemeneti esemény, és egy kimeneti esemény választ írni.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
