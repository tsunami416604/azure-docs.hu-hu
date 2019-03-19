---
title: Üzembe helyezése és frissítése az alkalmazások és szolgáltatások az Azure Resource Manager |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe alkalmazásokat és szolgáltatásokat egy Azure Resource Manager-sablon használatával a Service Fabric-fürt.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e41647140373fcf637cad55af62764bd87826a62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849346"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Az Azure Resource Manager-erőforrások, alkalmazások és szolgáltatások kezelése

Telepíthet alkalmazásokat és szolgáltatásokat az alakzatot a Service Fabric-fürtön az Azure Resource Manageren keresztül. Ez azt jelenti, hogy üzembe helyezése, és a PowerShell vagy parancssori felület alkalmazások kezelése után készen áll a fürt várnia, helyett mostantól alkalmazásokat és szolgáltatásokat JSON az express és telepíteni kell őket a fürt azonos Resource Manager-sablon. Alkalmazás regisztrálása, kiépítés és üzembe helyezés összes folyamata egy lépésben történik.

Ez az az ajánlott módszer, hogy bármely telepítés, a cégirányítási vagy a fürt felügyeleti alkalmazások, amelyekre szüksége van a fürtben üzembe helyezhet. Ez magában foglalja a [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), Watchdogs, vagy olyan alkalmazásokat, amelyek a fürt más alkalmazások vagy szolgáltatások telepítése előtt futtatnia kell. 

Ha alkalmazható, kezelhet alkalmazásokat Resource Manager-erőforrásként javítása érdekében:
* Auditnapló: Resource Manager naplózza minden művelet, és a egy részletes tartja *tevékenységnapló* , amellyel nyomon követése a végzett módosítások ezeket az alkalmazásokat és a fürtjét.
* Szerepköralapú hozzáférés-vezérlés (RBAC): Fürtök, valamint a fürtön üzembe helyezett alkalmazásokhoz való hozzáférés felügyelete megteheti ugyanazon a Resource Manager-sablon.
* Az Azure Resource Manager-(Azure-portálon) lesz egy egy-stop-vásárlása a fürt és a kritikus fontosságú alkalmazások központi telepítésének kezelése.

A következő kódrészlet azt mutatja be, a különböző típusú erőforrások, amelyek egy sablon használatával kezelhetők:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>A Resource Manager-sablon egy új alkalmazás hozzáadása

1. A fürt Resource Manager-sablon előkészítése az üzembe helyezéshez. Lásd: [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md) ezzel kapcsolatban további információt.
2. Gondolja át néhány alkalmazás azt tervezi, hogy a fürt üzembe helyezése. -E függőségek eltarthat azokat is, a rendszer mindig fut, amely más alkalmazások? Tervezi a fürt cégirányítási és a telepítés alkalmazások telepítését? Ezek az alkalmazások számos legjobb felügyelt egy Resource Manager-sablon használatával fentiekben leírtak szerint. 
3. Ön rendelkezik szükséges milyen alkalmazásokat szeretne ezzel a módszerrel telepített, az alkalmazások után csomagolva, zip és egy fájlmegosztás elhelyezése. A megosztás kell lennie az Azure Resource Manager üzembe helyezése során felhasználásához REST-végponton keresztül érhető el.
4. A Resource Manager-sablon, a fürt deklarace alább található mindegyik alkalmazás tulajdonságait írják le. E tulajdonságok közé tartozik a replika- vagy példányszámot, és bármilyen (más alkalmazások vagy szolgáltatások) erőforrások közötti függőségi láncok. Átfogó tulajdonságok listáját lásd: a [REST API Swagger-specifikációja](https://aka.ms/sfrpswaggerspec). Vegye figyelembe, hogy ez nem helyettesíti az alkalmazás vagy szolgáltatás jegyzékfájljainak, de inkább néhány mutatja be a őket a fürt Resource Manager-sablon részeként. Íme egy minta-sablont, amely tartalmazza az állapotmentes szolgáltatás telepítése *Service1* és a egy állapotalapú szolgáltatás *Service2* részeként *Alkalmaz1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > A *API-verzió* értékre kell állítani `"2017-07-01-preview"`. Ez a sablon is üzembe helyezhetők a fürt függetlenül mindaddig, amíg a fürt már telepítve lett.

5. Üzembe helyezhetők. 

## <a name="manage-an-existing-application-via-resource-manager"></a>Resource Manager-n keresztül a meglévő alkalmazások kezelése

Ha a fürt már fel, és néhány alkalmazást, hogy szeretné, Resource Manager erőforrások már telepítve lettek, ahelyett, hogy eltávolítaná az alkalmazások kezeléséhez, és újbóli őket, használhatja az azonos API-k használatával szeretné, hogy az alkalmazások Put művelet meghívásával beolvasása arra vonatkozik, mint a Resource Manager-erőforrások. 

> [!NOTE]
> A vásárló lehetővé teszik a nem megfelelő alkalmazások figyelmen kívül a fürtfrissítések adhat meg "maxPercentUnhealthyApplications: 100 – a "upgradeDescription/healthPolicy" szakaszban; a rendszer részletes leírását, hogy minden beállítás [szolgáltatás hálók REST API-t fürtben házirend dokumentációja](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>További lépések

* Használja a [Service Fabric parancssori felület](service-fabric-cli.md) vagy [PowerShell](service-fabric-deploy-remove-applications.md) más alkalmazásokat a fürtön üzembe helyezéséhez. 
* [A Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)

