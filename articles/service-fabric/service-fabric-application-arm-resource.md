---
title: "Központi telepítése, és frissítse az alkalmazások és szolgáltatások az Azure Resource Manager |} Microsoft Docs"
description: "Útmutató: az alkalmazások és szolgáltatások Azure Resource Manager-sablonnal Service Fabric-fürt központi telepítése."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: d6cda201e4cf16549f296bf9873b1085effd3a45
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Alkalmazások és szolgáltatások kezelése az Azure Resource Manager erőforrásként

A Service Fabric-fürt keresztül Azure Resource Manager-kiszolgálóra telepíthető alkalmazások és szolgáltatások. Ez azt jelenti, hogy üzembe helyezése és kezelése a PowerShell vagy a parancssori felületen keresztül alkalmazások után várja meg a fürt készen áll arra, hogy, helyett most express alkalmazások és szolgáltatások a JSON-ban és telepítheti őket ugyanabban a Resource Manager-sablonban a fürt. A folyamat regisztrációja, telepítés, és a központi telepítés összes történik, egy lépésben.

Ez az ajánlott, hogy telepítsen minden telepítő, irányítás vagy fürtkezelő alkalmazások, amelyekre szüksége van a fürtben. Ez magában foglalja a [javítás Vezénylési alkalmazás](service-fabric-patch-orchestration-application.md), Watchdogs, vagy olyan alkalmazásokat, amelyek a fürt más alkalmazásokhoz vagy szolgáltatásokhoz központi telepítése előtt futtatnia kell. 

Ha lehetséges, kezelheti az alkalmazások erőforrás-kezelő erőforrásként javítása érdekében:
* Napló: erőforrás-kezelő naplózás minden művelet és tartja a részletes *tevékenységnapló* , amely segítségével nyomon követni ezek az alkalmazások és a fürt módosításai.
* Szerepköralapú hozzáférés-vezérlést (RBAC): a fürtök, valamint a fürtön központilag telepített alkalmazások hozzáférés-kezelés megteheti ugyanazon a Resource Manager-sablon.
* Az Azure Resource Manager (az Azure-portál) keresztül lesz egy egy-stop-üzemi a fürt és a kritikus alkalmazások központi telepítésének kezeléséhez.

Az alábbi kódrészletben láthatja a különféle típusú erőforrások, egy sablon használatával kezelhető:

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

1. A fürt Resource Manager-sablon előkészítése telepítéséhez. Lásd: [a Service Fabric-fürt létrehozása az Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md) itt olvashat.
2. Gondolja át az alkalmazások, a fürt telepítését tervezi egy részének. -E függőségek eltarthat azokat, a rendszer mindig fut, amely más alkalmazások? Tervezi a fürt irányítási és a telepítés alkalmazások telepítését? Ezek az alkalmazások rendezi legjobb felügyelt egy Resource Manager-sablon használatával kapcsolatban a fentiekben ismertetett módon. 
3. Ki milyen alkalmazásokat, amelyeket szeretne ilyen módon telepítve rendelkezik szerepelnek, ha az alkalmazások kell csomagolni, zip és fájlmegosztás elhelyezése. A megosztás kell lennie az Azure Resource Manager üzembe helyezése során felhasználásához REST-végpont elérhető.
4. A Resource Manager-sablon, a fürt deklaráció alatt minden alkalmazás tulajdonságait ismerteti. E tulajdonságok közé tartozik a replika- vagy példányszámot és bármilyen (más alkalmazások vagy szolgáltatások) erőforrások közötti függőségi láncok. Átfogó tulajdonságok listáját lásd: a [REST API Swagger Spec](https://github.com/Azure/azure-rest-api-specs/blob/current/specification/servicefabric/resource-manager/Microsoft.ServiceFabric/2017-07-01-preview/servicefabric.json). Vegye figyelembe, hogy ez nem helyettesíti az alkalmazás vagy szolgáltatás akkor jelentkezik, de ahelyett, hogy néhány foglalja össze a Resource Manager-sablon a fürt részeként. Íme egy minta-sablont, amely tartalmazza az állapot nélküli szolgáltatás telepítésére *Service1* és állapotalapú szolgáltatási *Service2* részeként *Alkalmaz1*:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only"
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name"
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version"
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file"
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The application name"
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Service1",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The service type name"
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Service2",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The service type name"
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
  > A *apiVersion* értékre kell állítani `"2017-07-01-preview"`. Ez a sablon is telepíthető a fürtre, függetlenül mindaddig, amíg a fürt már telepítve lett.

5. Telepítése! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Egy meglévő alkalmazást keresztül erőforrás-kezelő kezelése

Ha a fürt már működik-e, és néhány alkalmazást, hogy szeretné kezelni, erőforrás-kezelő erőforrások már telepítve lettek, az alkalmazások eltávolítása helyett, és újratelepíteni őket, használhatja a azonos API-k segítségével az alkalmazások PUT hívás beolvasása a nyugtázott erőforrás-kezelő erőforrásként. 


## <a name="next-steps"></a>Következő lépések

* Használja a [Service Fabric CLI](service-fabric-cli.md) vagy [PowerShell](service-fabric-deploy-remove-applications.md) a fürthöz más alkalmazások központi telepítése. 
* [A Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)

