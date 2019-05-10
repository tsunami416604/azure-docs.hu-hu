---
title: Egészségügyi integrációs Bevezetés az Azure Deployment Manager bevezetése
description: Ismerteti, hogyan lehet a szolgáltatás telepítése több választható régióban az Azure Deployment Manager keresztül. Ellenőrizze a telepítés előtt minden régióban elérhetőek stabilitását biztonságos üzembe helyezési eljárásokat jeleníti meg.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 1006cc902cf4b6f763d86165a039a1fec5dc97a1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467088"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Vezessen be egészségügyi integrációs Bevezetés az Azure Deployment Manager (nyilvános előzetes verzió)

[Az Azure Deployment Manager](./deployment-manager-overview.md) végezhető el az Azure Resource Manager-erőforrások előkészített kibocsátások. Az erőforrások telepítése régiónként rendezett módon. A beépített állapot-ellenőrzése az Azure Deployment Manager figyelheti kibocsátások, és automatikusan stop problémás kibocsátások, így csökkentheti a méretezési csoport a hatás és hibáinak elhárítása. Ez a funkció csökkentheti a szolgáltatás elérhetetlensége regressziót a frissítések által okozott.

## <a name="health-monitoring-providers"></a>Állapotfigyelési szolgáltatók

Annak érdekében, hogy lehető egészségügyi integráció, a Microsoft dolgozott a egyes felső szolgáltatás állapotfigyelési cégeket azzal, hogy Ön egy egyszerű, másolási és beillesztési megoldással állapot-ellenőrzések integrálhatja az üzemelő példányok. Ha még nem használ állapotfigyelő, az alábbiak nagyszerű megoldások a kezdéshez:

| ![Azure-beli manager állapot figyelő szolgáltató datadoggal](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Azure-beli manager állapot figyelő szolgáltató site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure-beli manager állapot figyelő szolgáltató wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadoggal vezető figyelése és az analytics platform a modern felhőalapú környezetek. Lásd: [Datadoggal együttműködéséről az Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, teljes körű privát és nyilvános felhőbeli szolgáltatások figyelési megoldást. Lásd: [Site24x7 együttműködéséről az Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, a monitorozási és elemzési platform az többfelhős környezetek számára. Lásd: [Wavefront együttműködéséről az Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Hogyan határozza meg, a service health

[Állapotfigyelési szolgáltatók](#health-monitoring-providers) szolgáltatások figyelésre és riasztásra service health problémákról több mechanizmust kínálnak. [Az Azure Monitor](/services/monitor/) egy ilyen ajánlat mutat példát. Az Azure Monitor segítségével riasztásokat hozhat létre, amikor a meghatározott küszöbérték túllépése. Például a memória és CPU-kihasználtság hirtelen várt szintek túl új frissítést a szolgáltatáshoz való központi telepítésekor. Az értesítés, amikor javítási műveleteket végezhet.

Ilyen egészségügyi szolgáltatók általában kínálnak REST API-k, úgy, hogy a szolgáltatás figyelők állapotának programozott módon kell vizsgálni. A REST API-k vagy térjen vissza, az egyszerű megfelelő vagy nem megfelelő jel (határozza meg a HTTP-válaszkód), illetve részletes információkat a jelek fogadja.

Az új *healthCheck* lépés az Azure Deployment Manager lehetővé teszi, hogy a HTTP-kódrészletek, amelyek jelzik a kifogástalan állapotú szolgáltatási deklarálja, vagy összetettebb REST eredményt, még akkor is adja meg a reguláris kifejezések jelző, ha azok megfelelnek a kifogástalan állapotú válasz.

A folyamat a kezdeti beállítását az Azure Deployment Manager állapot-ellenőrzések:

1. Az állapotfigyelők tetszőleges egészségügyi szolgáltató használatával hozzon létre.
1. Hozzon létre egy vagy több healthCheck lépéseket az Azure Deployment Manager bevezetés részeként. Adja meg a healthCheck lépéseket a következő információkat:

    1. Az URI-t, a REST API-hoz a Health figyeli (az egészségügyi szolgáltató által meghatározott).
    1. Hitelesítési adatok. Jelenleg csak az API-kulcs stílus hitelesítést is támogatja.
    1. [HTTP-állapotkódok](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) vagy reguláris kifejezésekkel, amelyek meghatározzák a megfelelő választ. Vegye figyelembe, hogy a reguláris kifejezések, mely minden kell egyezik meg a válasz lehet benyújtani kifogástalan, vagy rendelkezhetnek, amelyek bármely meg kell egyeznie a válasz lehet benyújtani kifogástalan kifejezések rendelkezhetnek. Mindkét módszer használata támogatott.

    A következő Json-ja egy példa:

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

1. Hívja meg a healthCheck lépéseket az Azure Deployment Manager bevezetés során a megfelelő időben. A következő példában egy állapot-ellenőrzési lépést a meghívott **postDeploymentSteps** , **stepGroup2**.

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

Megtudhatja, hogyan egy példa, lásd: [oktatóanyag: Állapot-ellenőrzés használata az Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Állapot-ellenőrzés fázisai

Ezen a ponton az Azure Deployment Manager tudja, hogyan kérdezhet le, és mi fázisok ehhez a bevezetés során a szolgáltatás adminisztrátoránál. Azonban az Azure Deployment Manager is lehetővé teszi az ellenőrzések időzítése részletes konfigurációjáig. 3 egymást követő fázisban történik, amelyek mindegyike rendelkezik konfigurálható időtartamok healthCheck lépés hajtja végre: 

1. Várakozás

    1. Egy központi telepítési művelet befejezése után előfordulhat, hogy újraindítja a virtuális gépek rendszert, újrakonfigurálása az új adatok, vagy először is lépések alapján. Is szolgáltatások állapotának jelekkel, hogy valami hasznos állapotfigyelési szolgáltató kibocsátó időt vesz igénybe. A tumultuous folyamat során, előfordulhat, hogy nincs értelme szolgáltatásállapot kereséséhez, mivel a frissítés még nem érte el egy stabil állapotot. Sőt a szolgáltatás előfordulhat, hogy kell oszcilláló kifogástalan és nem kifogástalan állapot között, az erőforrások rendezéséhez. 
    1. A várakozási fázis során a service health nem áll felügyelet. Ezt lehetővé teszik az üzembe helyezett erőforrások használják az egészségügyi ellenőrzési folyamat megkezdése előtt os időt. 
1. Rugalmas

    1. Mivel az nem lehet tudni, hogy mennyi ideig minden esetben erőforrások os, mielőtt azok stabil, a rugalmas fázis egy rugalmas időszak között lehetővé teszi, hogy ha az erőforrások nem potenciálisan stabilak, és amikor a kifogástalan állapotú stabil fenntartásához szükséges fog tartani állapot.
    1. Amikor megkezdődik a rugalmas fázist, az Azure Deployment Manager kezdődik, a megadott REST végpontot service Health lekérdezésével rendszeres időközönként. A lekérdezési időköz nem konfigurálható. 
    1. Ha az állapotfigyelő, visszatér a jelek jelzi, hogy a szolgáltatás nem megfelelő állapotban, ezek a jeleket a rendszer figyelmen kívül hagyja, a rugalmas fázis továbbra is fennáll, és lekérdezési folytatódik. 
    1. Amint a állapotfigyelő vissza tartalmaz, jelek arról, hogy a szolgáltatás nem kifogástalan állapotú, a rugalmas fázisa véget ér, és a HealthyState fázis kezdődik. 
    1. Így az időtartam lett megadva a rugalmas fázis az a maximális, előtt adott megfelelő válasz kötelező a service health-lekérdezés is töltött időt. 
1. HealthyState

    1. HealthyState fázis során a szolgáltatás állapotát, a rugalmas fázis azonos időnként folyamatosan lekérdezve. 
    1. A szolgáltatás várhatóan megfelelő állapotú jelek figyelési állapot szolgáltatójáról karbantartása megadott teljes időtartama alatt. 
    1. Bármikor nem megfelelő állapotú választ észlelése esetén az Azure Deployment Manager állítsa le a teljes bevezetés, és a szerepkör végrehajtsa a nem megfelelő állapotú jelekkel REST választ adja vissza.
    1. Miután HealthyState idejére véget ért, a healthCheck befejeződött, és üzembe helyezés továbbra is fennáll, a következő lépéssel.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megismerhette integrálása az Azure Deployment Manager szolgáltatásállapot-figyelést. Folytassa a következő cikkben megtudhatja, hogyan helyezhet üzembe a Deployment Manager.

> [!div class="nextstepaction"]
> [Oktatóanyag: integrálása az Azure Deployment Manager állapot-ellenőrzése](./deployment-manager-tutorial-health-check.md)