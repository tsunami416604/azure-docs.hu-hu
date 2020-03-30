---
title: Az Azure Service Fabric Mesh szolgáltatásban futó alkalmazások automatikus méretezése
description: Megtudhatja, hogyan konfigurálhatja az automatikus méretezési szabályzatok a service fabric háló alkalmazás szolgáltatásaihoz.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461972"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Automatikus skálázási házirendek létrehozása egy Service Fabric Mesh alkalmazáshoz
Az alkalmazások Service Fabric Mesh szolgáltatásba való üzembe helyezésének egyik fő előnye, hogy könnyedén skálázhatja a szolgáltatásokat. Ezt a szolgáltatások különböző terhelésének kezelésére vagy a rendelkezésre állás javítására kell használni. Manuálisan skálázhatja a szolgáltatásokat, illetve ki, vagy automatikus skálázási szabályzatok beállítása.

[Automatikus skálázás](service-fabric-mesh-scalability.md#autoscaling-service-instances) lehetővé teszi, hogy dinamikusan skálázhatja a szolgáltatáspéldányok száma (horizontális skálázás). Az automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a szolgáltatáspéldányok kiépítését vagy eltávolítását a PROCESSZOR vagy a memória kihasználtsága alapján.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Automatikus skálázási házirend, eseményindító és mechanizmus létrehozásának lehetőségei
Minden méretezéshez automatikus skálázási házirend van definiálva. A házirend a YAML szolgáltatás erőforrásfájljában vagy a JSON központi telepítési sablonjában van definiálva. Minden skálázási szabályzat két részből áll: egy eseményindító és egy skálázási mechanizmus.

Az eseményindító határozza meg, ha egy automatikus skálázási házirend meghívása.  Adja meg az eseményindító (átlagos terhelés) és a figyelni kívánt metrika (CPU vagy memória) kívánt.  A felső és alsó terhelési küszöbértékek százalékban vannak megadva. A méretezési időköz határozza meg, hogy milyen gyakran ellenőrizze (másodpercben) a megadott kihasználtságot (például az átlagos CPU-terhelést) az összes jelenleg üzembe helyezett szolgáltatáspéldányban.  A mechanizmus akkor aktiválódik, ha a figyelt metrika az alsó küszöbérték alá csökken, vagy a felső küszöbérték fölé emelkedik.  

A skálázási mechanizmus határozza meg, hogyan kell végrehajtani a skálázási műveletet, amikor a házirend aktiválódik.  Adja meg a mechanizmus típusa (add/remove replika), a minimális és maximális replika száma (egész számként).  A szolgáltatás replikák száma soha nem lesz méretezve a minimális száma vagy a maximális száma felett.  Adja meg a méretezési növekményt egész számként is, amely a skálázási műveletben hozzáadandó vagy eltávolítandó replikák száma.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Automatikus skálázási házirend definiálása JSON-sablonban

A következő példa egy automatikus skálázási házirendet mutat be egy JSON-telepítési sablonban.  Az automatikus skálázási szabályzat a szolgáltatás skálázandó tulajdonságában deklarálva van deklarálva.  Ebben a példában egy CPU-átlagterhelési eseményindító van definiálva.  A mechanizmus akkor aktiválódik, ha az összes telepített példány átlagos CPU-terhelése 0,2 (20%) alá csökken. vagy 0,8 (80%) fölé emelkedik.  A CPU terhelését 60 másodpercenként ellenőrzik.  A skálázási mechanizmus példányok hozzáadására vagy eltávolítására van definiálva, ha a házirend aktiválódik.  A szolgáltatáspéldányok egy lépésben kerülnek hozzáadásra vagy eltávolításra.  Egy minimális példányszám és egy 40-es maximális példányszám is meg van határozva.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Automatikus skálázási házirend definiálása service.yaml erőforrásfájlban
A következő példa egy automatikus skálázási házirendet mutat be egy szolgáltatáserőforrás-fájlban (YAML).  Az automatikus skálázási szabályzat a skálázandó szolgáltatás tulajdonságaként deklarálva van deklarálva.  Ebben a példában egy CPU-átlagterhelési eseményindító van definiálva.  A mechanizmus akkor aktiválódik, ha az összes telepített példány átlagos CPU-terhelése 0,2 (20%) alá csökken. vagy 0,8 (80%) fölé emelkedik.  A CPU terhelését 60 másodpercenként ellenőrzik.  A skálázási mechanizmus példányok hozzáadására vagy eltávolítására van definiálva, ha a házirend aktiválódik.  A szolgáltatáspéldányok egy lépésben kerülnek hozzáadásra vagy eltávolításra.  Egy minimális példányszám és egy 40-es maximális példányszám is meg van határozva.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>További lépések
További információ a [szolgáltatások manuális méretezése](service-fabric-mesh-tutorial-template-scale-services.md)
