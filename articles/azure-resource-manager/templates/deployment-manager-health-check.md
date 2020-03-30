---
title: Az állapotintegráció bevezetése – Azure Deployment Manager
description: Bemutatja, hogyan telepíthet egy szolgáltatást számos régióban az Azure Deployment Manager használatával. Biztonságos üzembe helyezési eljárásokat mutat be a telepítés stabilitásának ellenőrzéséhez, mielőtt az összes régióban bevezetne.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273799"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Az állapotintegráció bevezetése az Azure Deployment Managerben (nyilvános előzetes verzió)

[Az Azure Deployment Manager](./deployment-manager-overview.md) lehetővé teszi az Azure Resource Manager erőforrásainak szakaszos bevezetését. Az erőforrások rendezett módon, régiónként vannak telepítve. Az Azure Deployment Manager integrált állapotfelmérése figyelheti a bevezetéseket, és automatikusan leállíthatja a problémás bevezetéseket, így hibaelhárítást és a hatás méretének csökkentése érdekében. Ez a szolgáltatás csökkentheti a szolgáltatások elérhetetlenségét a frissítésekre való regressziók miatt.

## <a name="health-monitoring-providers"></a>Egészségügyi monitoring szolgáltatók

Annak érdekében, hogy az állapotintegráció a lehető legegyszerűbb legyen, a Microsoft együttműködik néhány, a szolgáltatás állapotfigyelő vállalataival, hogy egy egyszerű másolási/beillesztési megoldást biztosítson az állapotellenőrzések és a központi telepítések integrálásához. Ha még nem használ állapotfigyelőt, a következő megoldásokkal kezdheti a következőket:

| ![Azure deployment manager állapotfigyelő szolgáltató datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure deployment manager állapotfigyelő szolgáltató helye24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure deployment manager állapotfigyelő szolgáltató hullámfrontja](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, a vezető felügyeleti és elemzési platform a modern felhőkörnyezetekben. Tekintse [meg, hogyan integrálja a Datadog az Azure Deployment Managerrel.](https://www.datadoghq.com/azure-deployment-manager/)|Site24x7, az all-in-one magán- és nyilvános felhőszolgáltatások figyelési megoldása. Tekintse [meg, hogyan integrálható a Site24x7 az Azure Deployment Managerrel.](https://www.site24x7.com/azure/adm.html)| Wavefront, a többfelhős alkalmazáskörnyezetek figyelési és elemzési platformja. Tekintse [meg, hogyan integrálható a Wavefront az Azure Deployment Managerrel.](https://go.wavefront.com/wavefront-adm/)|

## <a name="how-service-health-is-determined"></a>A szolgáltatás állapotának meghatározása

[Az állapotfigyelő szolgáltatók](#health-monitoring-providers) számos mechanizmust kínálnak a szolgáltatások figyelésére és a szolgáltatások állapotával kapcsolatos problémák riasztására. [Az Azure Monitor](../../azure-monitor/overview.md) egy ilyen ajánlat példája. Az Azure Monitor segítségével riasztásokat hozhat létre, ha bizonyos küszöbértékek túllépése. Például a memória és a cpu-kihasználtság impera túl a várt szintet, amikor új frissítést telepít a szolgáltatásra. Értesítést kap, korrekciós műveleteket is végrehajthat.

Ezek az egészségügyi szolgáltatók általában REST API-kat kínálnak, így a szolgáltatás monitorainak állapotát programozott módon lehet vizsgálni. A REST API-k vagy jön vissza egy egyszerű kifogástalan/nem megfelelő jelzés (a HTTP-válaszkód határozza meg), és/vagy részletes információkat a jeleket kap.

Az Azure Deployment Manager új *healthCheck* lépése lehetővé teszi a http-kódok deklarálását, amelyek kifogástalan állapotú szolgáltatást jeleznek, vagy összetettebb REST-eredmények esetén megadhatja a reguláris kifejezéseket is, amelyek egyeznek, és kifogástalan választ jeleznek.

Az Azure Deployment Manager állapot-ellenőrzésekkel való beállításfolyamata:

1. Hozza létre az egészségügyi monitorok egy egészségügyi szolgáltató az Ön által kiválasztott.
1. Hozzon létre egy vagy több állapotfelmérési lépést az Azure Deployment Manager bevezetésének részeként. Töltse ki az állapotellenőrzés lépéseit a következő információkkal:

    1. Az API-t a REST API az állapotfigyelők (az egészségügyi szolgáltató által meghatározott).
    1. Hitelesítési adatok. Jelenleg csak az API-kulcs stílusú hitelesítés támogatott.
    1. [HTTP-állapotkódok](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) vagy reguláris kifejezések, amelyek kifogástalan választ határoznak meg. Vegye figyelembe, hogy megadhat reguláris kifejezéseket, amelyeknek meg kell egyezniük ahhoz, hogy a válasz kifogástalannak minősüljen, vagy olyan kifejezéseket adhat meg, amelyeknek mindennek meg kell egyeznie ahhoz, hogy a válasz kifogástalannak minősüljen. Mindkét módszer támogatott.

    A következő Json egy példa:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Az Azure Deployment Manager bevezetésének megfelelő időpontban hívja meg a healthCheck lépéseket. A következő példában egy állapot-ellenőrzési lépés meghívása a **stepGroup2** **postDeploymentSteps** alkalmazásban történik.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Egy példa áttekintéséhez olvassa el [az Oktatóanyag: Állapot-ellenőrzés használata az Azure Deployment Manager ben című témakört.](./deployment-manager-health-check.md)

## <a name="phases-of-a-health-check"></a>Az állapotfelmérés fázisai

Ezen a ponton az Azure Deployment Manager tudja, hogyan kell lekérdezni a szolgáltatás állapotát, és milyen fázisokban a bevezetés erre. Azonban az Azure Deployment Manager is lehetővé teszi a biztonsági beállítások az ellenőrzések időzítését. A healthCheck lépés végrehajtása 3 egymást követő fázisban történik, amelyek mindegyike konfigurálható időtartammal rendelkezik: 

1. Várakozás

    1. A központi telepítési művelet befejezése után a virtuális gépek újraindulhatnak, újrakonfigurálhatják az új adatok alapján, vagy akár első alkalommal is elindulhatnak. Időbe telik, amíg a szolgáltatások megkezdik az egészségügyi jelek kibocsátását, amelyeket az egészségügyi felügyeleti szolgáltató valami hasznossá kell összesítenie. Ebben a viharos folyamat során előfordulhat, hogy nincs értelme ellenőrizni a szolgáltatás állapotát, mivel a frissítés még nem ért el állandó sult állapotot. Sőt, a szolgáltatás lehet oszcilláló között egészséges és egészségtelen államok, mint az erőforrások rendezése. 
    1. A várakozási fázisban a szolgáltatás állapota nem figyelt. Ez arra szolgál, hogy az üzembe helyezett erőforrások számára időt, hogy sütni, mielőtt az állapot-ellenőrzési folyamat. 
1. Rugalmas

    1. Mivel nem lehet tudni, hogy minden esetben mennyi ideig tart az erőforrások sütni, mielőtt azok stabilak, a rugalmas fázis lehetővé teszi a rugalmas időszak között, amikor az erőforrások potenciálisan instabil, és amikor azok fenntartásához szükséges egészséges stabil Állami.
    1. A rugalmas fázis kezdetekor az Azure Deployment Manager megkezdi a megadott REST-végpont lekérdezését a szolgáltatás állapotának rendszeres időközönként. A lekérdezési időköz konfigurálható. 
    1. Ha az állapotfigyelő jön vissza a jeleket, amelyek jelzik, hogy a szolgáltatás nem megfelelő állapotú, ezeket a jeleket figyelmen kívül hagyja, a rugalmas fázis folytatódik, és a lekérdezés folytatódik. 
    1. Amint az állapotfigyelő visszatér a szolgáltatás kifogástalan állapotjelző jelei, a rugalmas fázis befejeződik, és a HealthyState fázis kezdődik. 
    1. Így a rugalmas fázisban megadott időtartam az a maximális idő, amely a szolgáltatás állapotának lekérdezésére fordítható, mielőtt a kifogástalan választ kötelezőnek tekintené. 
1. Kifogástalan állam

    1. A HealthyState fázisban a szolgáltatás állapota folyamatosan lekérdezése a rugalmas fázissal azonos időközönként történik. 
    1. A szolgáltatás várhatóan az állapotfigyelő szolgáltató tól származó kifogástalan jelzőjeleket kell karbantartania a megadott időtartam teljes időtartama alatt. 
    1. Ha bármikor egy nem megfelelő válasz észlelése, az Azure Deployment Manager leállítja a teljes bevezetés, és adja vissza a REST-választ a nem megfelelő szolgáltatás jelzéseket.
    1. Miután a HealthyState időtartama befejeződött, a healthCheck befejeződött, és a központi telepítés folytatódik a következő lépésig.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtudhatja, hogyan integrálhatja az állapotfigyelést az Azure Deployment Managerben. Folytassa a következő cikkel, hogy megtudja, hogyan telepítheti a Deployment Manager.

> [!div class="nextstepaction"]
> [Oktatóanyag: állapotfelmérés integrálása az Azure Deployment Managerbe](./deployment-manager-tutorial-health-check.md)