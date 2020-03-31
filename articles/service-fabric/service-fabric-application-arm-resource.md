---
title: Üzembe helyezés és frissítés az Azure Resource Managerrel
description: Ismerje meg, hogyan telepítheti az alkalmazásokat és szolgáltatásokat egy Service Fabric-fürtre egy Azure Resource Manager-sablon használatával.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610250"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Alkalmazások és szolgáltatások kezelése Azure Resource Manager-erőforrásokként

Az Azure Resource Manager en keresztül alkalmazásokat és szolgáltatásokat helyezhet üzembe a Service Fabric-fürtre. Ez azt jelenti, hogy ahelyett, hogy alkalmazásokat telepítene és kezelne a PowerShellen vagy a CLI-n keresztül, miután meg kellett várnia, hogy a fürt készen álljon, most már kifejezheti az alkalmazásokat és szolgáltatásokat a JSON-ban, és telepítheti őket ugyanabban az Erőforrás-kezelő sablonban, mint a fürtben. Az alkalmazásregisztráció, -kiépítés és -telepítés folyamata egy lépésben történik.

Ez az ajánlott módszer a fürtben szükséges beállítási, cégirányítási vagy fürtfelügyeleti alkalmazások üzembe helyezéséhez. Ez magában foglalja a [patch vezénylési alkalmazás](service-fabric-patch-orchestration-application.md), watchdogs, vagy minden olyan alkalmazás, amelynek futnia kell a fürtben, mielőtt más alkalmazások vagy szolgáltatások telepítése. 

Adott esetben az alkalmazásokat erőforrás-kezelői erőforrásként kezelheti a következők javítása érdekében:
* Naplózási nyomvonal: Az Erőforrás-kezelő minden műveletet naplóz, és részletes *tevékenységnaplót* vezet, amely segít nyomon követni az ezen alkalmazásokon és a fürtön végrehajtott módosításokat.
* Szerepköralapú hozzáférés-vezérlés (RBAC): Fürtök és a fürtön telepített alkalmazások elérésének kezelése ugyanazzal az Erőforrás-kezelő sablonnal végezhető el.
* Az Azure Resource Manager (az Azure Portalon keresztül) egyablakos megoldássá válik a fürt és a kritikus alkalmazások központi telepítésének kezeléséhez.

A következő kódrészlet a sablonon keresztül kezelhető különböző típusú erőforrásokat mutatja be:

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


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Új alkalmazás hozzáadása az Erőforráskezelő sablonhoz

1. Készítse elő a fürt Erőforrás-kezelő sablonját a telepítéshez. Erről további információt a [Service Fabric-fürt létrehozása az Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md) című témakörben talál.
2. Gondoljon néhány olyan alkalmazásra, amelyet a fürtben szeretne telepíteni. Vannak-e olyan, hogy mindig fut, hogy más alkalmazások is függőségek? Tervezi a fürtirányítási vagy beállítási alkalmazások üzembe helyezését? Az ilyen típusú alkalmazások at a legjobban egy Erőforrás-kezelő sablonon keresztül lehet kezelni, ahogy azt fentebb már tárgyaltuk. 
3. Miután kitalálta, hogy milyen alkalmazásokat szeretne telepíteni így, az alkalmazásokat kell csomagolni, cipzáras, és hozott egy fájlmegosztást. A megosztást egy REST-végponton keresztül kell elérhetővé tenni az Azure Resource Manager számára az üzembe helyezés során.
4. Az Erőforrás-kezelő sablonban, a fürtdeklaráció alatt írja le az egyes alkalmazások tulajdonságait. Ezek a tulajdonságok közé tartozik a replika vagy példányszám és az erőforrások (más alkalmazások vagy szolgáltatások) közötti függőségi láncok. Az átfogó tulajdonságok listáját a [REST API Swagger spec című témakörben található.](https://aka.ms/sfrpswaggerspec) Ne feledje, hogy ez nem helyettesíti az alkalmazás vagy a szolgáltatás jegyzékfájljait, hanem a fürt Erőforrás-kezelő sablonjának részeként leírja a bennük lévő katajzatokat. Az alábbiakban egy mintasablon található, amely magában foglalja egy állapotmentes *serviceService1* és egy állapotalapú *serviceService2* üzembe helyezését az *Application1*részeként:

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
   > Az *apiVersion-t* `"2019-03-01"`a -ra kell állítani. Ez a sablon a fürttől függetlenül is telepíthető, feltéve, hogy a fürt már telepítve van.

5. Telepíteni! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>A Service Fabric erőforrásszolgáltatóalkalmazás-erőforrásának eltávolítása
A következő elindítja az alkalmazáscsomagot, hogy a fürtből kilegyen építve, és ez megtisztítja a felhasznált lemezterületet:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
A Microsoft.ServiceFabric/clusters/application/application egyszerű eltávolítása az ARM sablonból nem oldja meg az alkalmazást

>[!NOTE]
> Miután az eltávolítás befejeződött, többé nem szabad látnia a csomag verzióját az SFX-ben vagy az ARM-ban. Nem törölheti azt az alkalmazástípus-verzió-erőforrást, amelyen az alkalmazás fut; Az ARM/SFRP ezt megakadályozza. Ha megpróbálja kiépíteni a futó csomagot, az SF futtatótime megakadályozza azt.


## <a name="manage-an-existing-application-via-resource-manager"></a>Meglévő alkalmazás kezelése az Erőforrás-kezelőn keresztül

Ha a fürt már működik, és néhány alkalmazás, amelyet kezelni szeretne, mivel az Erőforrás-kezelő erőforrásai már telepítve vannak rajta, az alkalmazások eltávolítása és újratelepítése helyett használhatja a PUT-hívást ugyanazon API-k használatával az alkalmazások lehívásához erőforrás-kezelői erőforrásként kell elismerni. További információkért tekintse meg [a Mi a Service Fabric-alkalmazás erőforrás-modell?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Ahhoz, hogy a fürtfrissítés figyelmen kívül hagyja a nem megfelelő állapotú alkalmazásokat, az ügyfél a "maxPercentUnhealthyApplications: 100" értéket a "upgradeDescription/healthPolicy" szakaszban adhatja meg; az összes beállítás részletes leírása a [Service Fabrics REST API fürtfrissítési házirend dokumentációjában](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)található.

## <a name="next-steps"></a>További lépések

* A [Service Fabric CLI](service-fabric-cli.md) vagy a [PowerShell](service-fabric-deploy-remove-applications.md) használatával más alkalmazásokat telepíthet a fürtre. 
* [A Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)

