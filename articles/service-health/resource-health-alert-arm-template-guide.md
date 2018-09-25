---
title: Resource Manager-sablonok használata az Azure resource health-riasztások konfigurálása |} A Microsoft Docs
description: Hozzon létre riasztásokat programozott módon, amely értesíti, amint az Azure-erőforrások elérhetetlenné válnak.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/4/2018
ms.author: shtabriz
ms.openlocfilehash: b19a840e5d0d3434d3e3ff91e0d7af037d64fb5a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975360"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Resource Manager-sablonok használatával a resource health-riasztások konfigurálása

Ebből a cikkből megtudhatja, hogyan hozhat létre a Resource Health tevékenységnapló-riasztások programozott módon az Azure Resource Manager-sablonokkal és az Azure PowerShell használatával.

Az Azure Resource Health láthatók a jelenlegi és korábbi állapotát az Azure-erőforrások. Azure Resource Health-riasztások értesítheti, a közel valós idejű akkor, ha ezek az erőforrások állapota változik rendelkezik. Létrehozása a Resource Health riasztásokat programozott módon lehetővé teszik a felhasználók létrehozása és testre szabhatja a riasztásokat tömegesen.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi utasításokat, lesz szüksége az előzetesen beállításához néhány dolgot:

1. Telepítenie kell a [Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (`AzureRm`)
2. Kell [létrehozása vagy újbóli műveletcsoport](../monitoring-and-diagnostics/monitoring-action-groups.md) arra az esetre, konfigurálva

## <a name="instructions"></a>Utasítások
1. PowerShell használatával jelentkezzen be Azure-fiókjával, és válassza ki az előfizetést, amellyel kommunikálni szeretne

        Login-AzureRmAccount
        Select-AzureRmSubscription -Subscription <subscriptionId>

    > Használhat `Get-AzureRmSubscription` előfizetések listájának fér hozzá.

2. Keresse meg és mentse a teljes Azure Resource Manager-Azonosítót a műveleti csoport

        (Get-AzureRmActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Hozzon létre és mentsen egy Resource Manager-sablon, a Resource Health-riasztások `resourcehealthalert.json` ([lásd lejjebb](#resource-manager-template-for-resource-health-alerts))

4. Hozzon létre egy új Azure Resource Manager-alapú, ez a sablon használatával

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. A rendszer felszólítja a riasztás neve és korábban vágólapra másolt műveletcsoport erőforrás azonosítója:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resouceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Ha mindent sikeresen dolgozott, megerősítést fog kapni a PowerShellben

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Ne feledje, hogy ha azt tervezi, a teljes automatizálása, egyszerűen csak az 5. lépésben szereplő értékek nem kér a Resource Manager-sablon szerkesztéséhez.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Resource Manager-sablon a Resource Health-riasztások

Ez a alapvető sablon kiindulási pontként használhatja a Resource Health-riasztások létrehozásához. Ez a sablon írt módon fog működni, illetve fog regisztrálhatnak, értesítést az újonnan aktivált erőforrás minden hálózatállapot-események között egy előfizetésben található összes erőforrást.

> Ez a cikk alján olyan összetettebb értesítési sablont, amely a jel zaj arány képest ez a sablon Resource Health-riasztások esetén növelje is tartalmazza.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Azonban egy ehhez hasonló széleskörű riasztás általában nem ajánlott. Ismerje meg, hogyan tudjuk gazdagépcsoportjaira le ez a riasztás az alábbi ügyelünk események összpontosíthat.

### <a name="adjusting-the-alert-scope"></a>A riasztási hatókör módosítása

Resource Health-riasztások konfigurálhatók, három különböző hatókör esemény figyelése:

 * Előfizetés típusa
 * Erőforráscsoport-szintű
 * Erőforrás-szint

Az értesítési sablon az előfizetés szintjén van konfigurálva, de ha azt szeretné, hogy csak bizonyos erőforrásokhoz, vagy egy adott erőforráscsoporton belül erőforrások kaphat értesítést a riasztás konfigurálásához, egyszerűen kell módosítani a `scopes` a fenti szakasz a sablon.

Egy erőforrás-csoport szintű hatókörhöz a hatókörök szakasz hasonlóan kell kinéznie:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

És a egy erőforrás szintű hatókörhöz a hatókör szakasz hasonlóan kell kinéznie:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Például:`"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Nyissa meg az Azure Portalon, és tekintse meg az URL-címe, ez a karakterlánc lekérése az Azure-erőforrás megtekintésekor.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Az erőforrás módosítása típusokat, amelyek riasztást küldjön

Előfordulhat, hogy az előfizetés vagy az erőforráscsoport szintjén riasztások különböző típusú erőforrásokhoz. Ha szeretné korlátozni, riasztások csak egy bizonyos részét erőforrástípusok származnak, meghatározhatja, hogy a `condition` szakaszában a sablont, például így:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Jelen példában használjuk a `anyOf` burkoló resource health egy riasztást a feltételeknek megfelelő azt meg, így az adott erőforrástípusokra célzó értesítések engedélyezéséhez.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>A Resource Health-események, amelyek riasztást beállítása
Erőforrások állapottal kapcsolatos esemény rendelkezéseinknek, amikor azok Lépkedjen végig egy sorozat szakaszból áll, amelyek az állapotesemény állapotát jeleníti meg: `Active`, `InProgress`, `Updated`, és `Resolved`.

Előfordulhat, hogy csak szeretné értesíti, ha egy erőforrás akkor kerül sérült, ebben az esetben csak Értesítés kérése, ha a riasztásokat konfigurálni szeretné a `status` van `Active`. Azonban ha azt szeretné, a többi szakaszt is értesítést, hozzáadhatja ezeket az adatokat, például így:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Ha azt szeretné, hogy értesíthessük a Szolgáltatásállapot-események összes négy fázisát, eltávolíthatja ezt az állapotot minden egy helyen, és a figyelmeztető értesítést küld, függetlenül a `status` tulajdonság.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>"Ismeretlen" események elkerülése érdekében a Resource Health-riasztások beállítása

Az Azure Resource Health is tud jelentéseket, az erőforrások legfrissebb állapotának folyamatos figyelése által vizsgált indák használatával. A megfelelő jelentett egészségügyi állapotok: "Elérhető", "Nem érhető el" és "Csökkentett teljesítményű". Azonban az olyan esetekben, ahol a futtató és az Azure-erőforrás nem tud kommunikálni egymással, egy "Ismeretlen" állapotjelentés az erőforrás, és egy "Aktív" állapottal kapcsolatos esemény, amely akkor tekinthető.

Azonban amikor egy erőforrást az "Ismeretlen" jelenti, valószínű, hogy a megfelelő állapotot nem változott az utoljára pontos jelentés óta. Ha szeretné, hogy az "Ismeretlen" esemény-riasztások kiküszöböléséhez, logikát is megadhat a sablonban:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

Ebben a példában azt is csak értesítése az eseményeket, ahol a jelenlegi és korábbi működési állapota nem rendelkezik "Ismeretlen". Ez a változás lehet egy hasznos hozzáadása, ha a riasztásokat közvetlenül mobiltelefonját vagy e-mailek érkeznek.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Felhasználó által kezdeményezett események elkerülése érdekében a riasztás beállítása

Közelmúltbeli állapotesemények eseményindító által indított platform és felhasználó által kezdeményezett eseményeket. Csak értesítés küldése, amikor az állapottal kapcsolatos esemény az Azure platform okozza, lehetséges, hogy értelme.

Ez Gyerekjáték csak ilyen típusú események szűrése a riasztás konfigurálásához:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```

## <a name="recommended-resource-health-alert-template"></a>Ajánlott a Resource Health értesítési sablon

Használja a különböző szükséges az előző szakaszban leírt, létrehozhatunk egy átfogó riasztási sablon, amely maximalizálja a jel zaj viszony van konfigurálva.

Íme, mi javasoljuk, hogy használja:
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Azonban meg fogja tudja, milyen konfigurációk érvényesek az Ön számára leginkább, ezért az ebben a dokumentációban, hogy tanulási eszközök, hogy a saját testreszabási.

## <a name="next-steps"></a>További lépések

További információ a Resource Health:
-  [Az Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapot-ellenőrzések](resource-health-checks-resource-types.md)

Hozza létre a Service Health-riasztások:
-  [Service Health-riasztások beállítása](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) 
