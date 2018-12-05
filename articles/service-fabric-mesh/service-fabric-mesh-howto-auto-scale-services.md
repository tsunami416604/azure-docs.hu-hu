---
title: Automatikus méretezése egy Azure Service Fabric-háló-ben futó alkalmazás |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat a szolgáltatások egy Service Fabric-háló alkalmazás automatikus skálázás házirendeket.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9ed4c98af94b56876266d5425111bc3f842c5e87
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892352"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>A Service Fabric-háló alkalmazások automatikus skálázási házirendek létrehozása
Az alkalmazások a Service Fabric Meshben való üzembe helyezésének egyik legfőbb előnye, hogy a szolgáltatásokat egyszerűen lehet horizontálisan le- és felskálázni. Ez akkor hasznos, ha változó mértékű terhelés éri a szolgáltatásokat, vagy ha javítani szeretne a rendelkezésre álláson. Méretezheti a szolgáltatásokat, vagy manuálisan, vagy az automatikus skálázás házirendeket állíthat be.

[Automatikus skálázás](service-fabric-mesh-scalability.md#autoscaling-service-instances) lehetővé teszi, hogy dinamikusan méretezheti a szolgáltatás-példányok (horizontális skálázás). Automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a kiépítés vagy a CPU és memória-kihasználtság alapján szolgáltatáspéldányok eltávolítása.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Az automatikus méretezés a házirendet, az eseményindító és mechanizmus létrehozásának lehetőségeit
Az automatikus skálázási szabályzat minden egyes szolgáltatás méretezésére van meghatározva. A szabályzat vagy a YAML erőforrás fájlt, vagy a JSON-üzembe helyezési sablon van meghatározva. Minden egyes méretezési szabályzat két részből áll: egy eseményindítót és a egy skálázási mechanizmus.

Az eseményindító határozza meg, amikor az automatikus skálázás házirend hív-e.  Adja meg, milyen típusú trigger (átlagos terhelés) és a metrika figyelése (CPU és memória).  A felső és alsó betöltési küszöbérték százalékban megadva. A méretezési csoport időköz határozza meg, milyen gyakran szeretné ellenőrizni (másodpercben) a megadott kihasználtság (például az átlagos processzorterhelés) a jelenleg üzemelő szolgáltatásban-példányok között.  A mechanizmus akkor aktiválódik, ha a figyelt metrika az alsó küszöbérték alá csökken, vagy növeli a felső küszöbérték felett.  További információkért lásd: [AutoScalingTrigger](/rest/api/servicefabric/sfmeshrp-model-autoscalingtrigger).

A skálázási mechanizmus a skálázási művelet végrehajtása a szabályzat akkor aktiválódik, amikor határozza meg.  Adja meg, milyen típusú mechanizmus (replika hozzáadása/eltávolítása), a minimális és maximális replika számít (egész szám).  A szolgáltatás replikák száma soha nem lesz skálázva a minimális száma kisebb vagy nagyobb a maximális száma.  A méretezési csoport növekmény is adja meg egy egész számot, amely a hozzáadott vagy eltávolított egy skálázási műveletet a replikák száma.  További információkért lásd: [AutoScalingMechanism](/rest/api/servicefabric/sfmeshrp-model-autoscalingmechanism).

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Az automatikus skálázási szabályzat a JSON-sablon meghatározása

Az alábbi példa bemutatja egy automatikus skálázási szabályzat a JSON a központi telepítési sablont.  Az automatikus skálázási szabályzat skálázását a szolgáltatás egy tulajdonságában van deklarálva.  Ebben a példában a Processzor átlagos terhelés trigger definiálva van.  A mechanizmus akkor aktiválódik, ha az átlagos Processzorhasználat 0.2-es (20 %-os) vagy goes fent 0,8 (80 %) alatti összes üzembe helyezett példányok csepp betöltése.  A CPU-terhelést 60 másodpercenként jelölőnégyzet be van jelölve.  A skálázási mechanizmus hozzáad vagy eltávolít példányokat a szabályzat akkor aktiválódik, ha az definiálva van.  Szolgáltatáspéldány fog hozzáadásának vagy eltávolításának egy lépésekben.  A példányok minimális száma egy és a egy 40 példányok maximális száma is definiálva van.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Az automatikus skálázási szabályzat definiálása service.yaml találhatók
Az alábbi példa bemutatja egy automatikus skálázási szabályzat egy erőforrás (YAML) fájlban.  Az automatikus skálázási szabályzat van deklarálva, a szolgáltatás skálázását tulajdonsága.  Ebben a példában a Processzor átlagos terhelés trigger definiálva van.  A mechanizmus akkor aktiválódik, ha az átlagos Processzorhasználat 0.2-es (20 %-os) vagy goes fent 0,8 (80 %) alatti összes üzembe helyezett példányok csepp betöltése.  A CPU-terhelést 60 másodpercenként jelölőnégyzet be van jelölve.  A skálázási mechanizmus hozzáad vagy eltávolít példányokat a szabályzat akkor aktiválódik, ha az definiálva van.  Szolgáltatáspéldány fog hozzáadásának vagy eltávolításának egy lépésekben.  A példányok minimális száma egy és a egy 40 példányok maximális száma is definiálva van.

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
                metricName: cpu
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
Ismerje meg, hogyan [szolgáltatás manuális méretezése](service-fabric-mesh-tutorial-template-scale-services.md)
