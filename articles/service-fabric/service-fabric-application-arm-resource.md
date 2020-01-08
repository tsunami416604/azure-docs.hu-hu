---
title: Üzembe helyezés és frissítés Azure Resource Manager
description: Megtudhatja, hogyan helyezhet üzembe alkalmazásokat és szolgáltatásokat egy Service Fabric-fürtön egy Azure Resource Manager sablon használatával.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610250"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Alkalmazások és szolgáltatások kezelése Azure Resource Manager erőforrásként

Segítségével az Azure Resource Manageren keresztül helyezhet üzembe alkalmazásokat és szolgáltatásokat a Service Fabric-fürtön. Ez azt jelenti, hogy az alkalmazások PowerShell vagy CLI használatával történő üzembe helyezése és kezelése helyett a fürt felkészültségének megkezdése után már kipróbálhatja az alkalmazásokat és a szolgáltatásokat a JSON-ban, és üzembe helyezheti őket a fürttel azonos Resource Manager-sablonban. Az alkalmazásregisztráció, -kiépítés és -üzembehelyezés folyamata mind egy lépésben valósul meg.

Ez az ajánlott módszer a fürtben szükséges telepítési, irányítási vagy fürtözött felügyeleti alkalmazások üzembe helyezésére. Ez magában foglalja a javítás-előkészítési [alkalmazást](service-fabric-patch-orchestration-application.md), a watchdogokat, illetve a fürtön futtatni kívánt alkalmazásokat más alkalmazások vagy szolgáltatások telepítése előtt. 

Ha alkalmazható, az alkalmazásokat Resource Manager-erőforrásként kezelheti a fejlesztéshez:
* Naplózási nyomvonal: a Resource Manager naplózza az összes műveletet, és részletesen *naplózza a tevékenységeket* , amelyek segítségével nyomon követheti az ezen alkalmazások és a fürtön történt módosításokat.
* Szerepköralapú hozzáférés-vezérlés (RBAC): a fürtökhöz való hozzáférés kezelése, valamint a fürtön üzembe helyezett alkalmazások ugyanazon Resource Manager-sablonnal is elvégezhetők.
* A Azure Resource Manager (a Azure Portal használatával) a fürt és a kritikus fontosságú alkalmazások központi telepítésének egyablakos üzembe helyezése lesz.

A következő kódrészlet a sablonon keresztül felügyelhető erőforrások különböző típusait jeleníti meg:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Új alkalmazás hozzáadása a Resource Manager-sablonhoz

1. Készítse elő a fürt Resource Manager-sablonját a telepítéshez. További információ: [Service Fabric-fürt létrehozása Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md) .
2. Gondoljon néhány olyan alkalmazásra, amelyet a fürtön való üzembe helyezésre tervez. Vannak olyanok, amelyek mindig futni fognak, hogy más alkalmazások függőségeket is igénybe vehetnek? Tervezi a fürtök irányítását vagy az alkalmazások telepítését? Az ilyen típusú alkalmazásokat a legjobban egy Resource Manager-sablonnal felügyelik, a fentiekben leírtak szerint. 
3. Miután kitalálta, hogy milyen alkalmazásokat szeretne üzembe helyezni, az alkalmazásokat be kell csomagolni, kicsomagolni és megosztani egy fájlmegosztást. A megosztásnak elérhetőnek kell lennie egy REST-végponton keresztül, hogy az üzembe helyezés során Azure Resource Manager használni.
4. A Resource Manager-sablonban a fürt deklarációja alatt írja le az egyes alkalmazások tulajdonságait. Ezek a tulajdonságok közé tartoznak a replika vagy a példányszám, valamint az erőforrások (más alkalmazások vagy szolgáltatások) közötti függőségi láncok. Az átfogó tulajdonságok listájáért tekintse meg a [REST API hencegő specifikációt](https://aka.ms/sfrpswaggerspec). Vegye figyelembe, hogy ez nem váltja fel az alkalmazás vagy a szolgáltatás jegyzékfájlját, hanem a fürt Resource Manager-sablonjának részeként ismertet néhányat. Itt látható egy sablon, amely egy állapot nélküli szolgáltatás *service1* és egy állapot-nyilvántartó szolgáltatás *service2* üzembe helyezését foglalja magában a *Application1*részeként:

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
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
   > A *apiVersion* `"2019-03-01"`értékre kell állítani. Ez a sablon a fürttől függetlenül is telepíthető, feltéve, hogy a fürt már telepítve van.

5. Telepíteni! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Service Fabric erőforrás-szolgáltatói alkalmazás erőforrásának eltávolítása
A következő elindítja az alkalmazáscsomag kiépítés nélküli kihelyezését a fürtből, és ezzel törli a felhasznált lemezterületet:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Ha egyszerűen eltávolítja a Microsoft. ServiceFabric/Clusters/Application alkalmazást az ARM-sablonból, az nem fogja kiépíteni az alkalmazást

>[!NOTE]
> Az Eltávolítás befejezését követően nem tekintheti meg az SFX vagy az ARM csomag verzióját. Nem törölheti az alkalmazás által a-val futó Application Type-verzió erőforrását; A ARM/SFRP ezt nem teszi meg. Ha megpróbálja kiépíteni a futó csomagot, az SF Runtime megakadályozza.


## <a name="manage-an-existing-application-via-resource-manager"></a>Meglévő alkalmazás kezelése a Resource Manager használatával

Ha a fürt már használatban van, és néhány olyan alkalmazás, amelyet Resource Manager-erőforrásként szeretne felügyelni, az alkalmazások eltávolítása és újbóli üzembe helyezése helyett egy PUT hívást is használhat ugyanazzal az API-kkal, hogy az alkalmazások megkapják a a program Resource Manager-erőforrásként ismeri fel. További információkért lásd: Mi az [a Service Fabric Application Resource Model?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Ha engedélyezni szeretné, hogy a fürt a nem megfelelő állapotú alkalmazásokat hagyja figyelmen kívül, az ügyfél az "upgradeDescription/healthPolicy" szakaszban megadhatja a "maxPercentUnhealthyApplications: 100" kifejezést. az összes beállítás részletes leírása a [Service fabric REST API a fürt frissítési házirendjének dokumentációjában](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)található.

## <a name="next-steps"></a>Következő lépések

* A [Service FABRIC parancssori](service-fabric-cli.md) felület vagy a [PowerShell](service-fabric-deploy-remove-applications.md) használatával telepíthet más alkalmazásokat a fürtön. 
* [A Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)

