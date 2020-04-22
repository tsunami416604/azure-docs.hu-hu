---
title: Sablon erőforrás-egészségügyi riasztások létrehozásához
description: Hozzon létre riasztásokat programozott módon, amelyek értesítik, ha az Azure-erőforrások elérhetetlenné válnak.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 60ff5bdf2f4f0dab94c18fd7c751869c1893ad65
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759016"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Erőforrás-állapotriasztások konfigurálása erőforrás-kezelősablonok használatával

Ez a cikk bemutatja, hogyan hozhat létre erőforrás-állapot-naplóriasztások at programozott módon az Azure Resource Manager-sablonok és az Azure PowerShell használatával.

Az Azure Resource Health folyamatosan tájékoztatja az Azure-erőforrások aktuális és korábbi állapotáról. Az Azure Resource Health-riasztások közel valós időben értesíthetik Önt, ha ezek az erőforrások állapota megváltozik. Az erőforrás-állapotriasztások programozott létrehozása lehetővé teszi a felhasználók számára, hogy tömegesen hozzanak létre és szabjanak testre riasztásokat.

> [!NOTE]
> Az erőforrás-állapotra vonatkozó riasztások jelenleg előzetes verzióban vannak elérhetők.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az ezen az oldalon található utasítások követéséhez néhány dolgot előre be kell állítania:

1. Telepítenie kell az [Azure PowerShell-modult](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Létre kell hoznia vagy újra fel kell [használnia egy olyan műveletcsoportot,](../azure-monitor/platform/action-groups.md) amely úgy van beállítva, hogy

## <a name="instructions"></a>Utasítások
1. A PowerShell használatával jelentkezzen be az Azure-ba a fiókjával, és válassza ki azt az előfizetést, amelyet kezelni szeretne

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Az `Get-AzSubscription` előfizetések listázásához használhatja, amelyekhez hozzáférése van.

2. A műveletcsoport teljes Azure Resource Manager-azonosítójának megkeresése és mentése

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Erőforrás-kezelősablon létrehozása és mentése az `resourcehealthalert.json` erőforrás-állapot-riasztásokhoz ([lásd az alábbi részleteket](#resource-manager-template-options-for-resource-health-alerts))

4. Új Azure Resource Manager-telepítés létrehozása ezzel a sablonnal

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. A rendszer kérni fogja, hogy írja be a korábban másolt riasztási név és műveletcsoport erőforrásazonosítóját:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Ha minden sikeresen működött, megerősítést kap a PowerShellben

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

Vegye figyelembe, hogy ha azt tervezi, hogy teljesen automatizálja ezt a folyamatot, egyszerűen meg kell szerkeszteni az Erőforrás-kezelő sablont, hogy ne kérje az 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Erőforrás-kezelő sablonbeállításai az Erőforrás-állapot riasztásokhoz

Ezt az alapsablont használhatja kiindulási pontként az erőforrás-állapot-riasztások létrehozásához. Ez a sablon írással fog működni, és feliratkozik, hogy értesítéseket kapjon az előfizetés összes újonnan aktivált erőforrás-állapoteseményéről.

> A cikk alján egy összetettebb riasztási sablont is tartalmaztunk, amely nek növelnie kell a forrásállapot-riasztások jel-zaj arányát ehhez a sablonhoz képest.

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

Azonban egy széles körű riasztást, mint ez általában nem ajánlott. Ismerje meg, hogyan tudjuk hatókör le ezt a riasztást, hogy összpontosítson az eseményeket, amelyek érdekelnek az alábbiakban.

### <a name="adjusting-the-alert-scope"></a>A riasztási hatókör módosítása

Az erőforrás-állapotjelző riasztások beállíthatók úgy, hogy három különböző hatókörben figyeljék az eseményeket:

 * Előfizetési szint
 * Erőforráscsoport szintje
 * Erőforrás-szint

A riasztási sablon az előfizetés szintjén van konfigurálva, de ha úgy szeretné konfigurálni a riasztást, hogy csak `scopes` bizonyos erőforrásokról vagy erőforrásokról értesítsen egy bizonyos erőforráscsoporton belül, egyszerűen módosítania kell a fenti sablon szakaszát.

Erőforráscsoport szintű hatókör esetén a hatókörök szakasznak a következőkre kell kinéznie:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Az erőforrásszintű hatókör esetében pedig a hatókörszakasznak így kell kinéznie:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Például:`"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Az Azure Portalon megkeresheti az URL-címet, amikor az Azure-erőforrást tekinti meg a karakterlánc leéséhez.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Az Önt figyelmeztető erőforrástípusok módosítása

Az előfizetés vagy erőforráscsoport szintjén lévő riasztások különböző típusú erőforrásokkal rendelkezhetnek. Ha azt szeretné, hogy a riasztások csak az erőforrástípusok egy bizonyos részhalmazából származhassanak, ezt a `condition` sablon szakaszában a következőképpen határozhatja meg:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Itt használjuk `anyOf` a burkoló, hogy az erőforrás-állapot riasztás, hogy megfeleljen az általunk megadott feltételek bármelyikének, amely lehetővé teszi a riasztások, amelyek az adott erőforrástípusok.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Az Önt figyelmeztető erőforrás-állapotesemények módosítása
Amikor az erőforrások állapoteseményen mennek keresztül, az állapotesemény állapotát képviselő szakaszok `Active` `In Progress`sorozatán mennek keresztül: , , `Updated`és `Resolved`.

Előfordulhat, hogy csak akkor szeretne értesítést kapni, ha egy erőforrás állapota nem megfelelő, `status` `Active`ebben az esetben a riasztást úgy szeretné beállítani, hogy csak akkor értesítse, ha a . Ha azonban a többi szakaszban is értesítést szeretne kapni, hozzáadhatja ezeket a részleteket, így:

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
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Ha azt szeretné, hogy értesítést kapjon mind a négy szakaszában az egészségügyi események, eltávolíthatja ezt a feltételt együtt, és a riasztás értesíti Önt, függetlenül attól, hogy a `status` tulajdonság.

> [!NOTE]
> Minden "anyOf" szakasznak csak egy mezőtípus-értéket kell tartalmaznia.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Az erőforrás-állapotriasztások módosítása az "Ismeretlen" események elkerülése érdekében

Az Azure Resource Health az erőforrások legfrissebb állapotát jelentheti, ha tesztfutók használatával folyamatosan figyeli őket. A vonatkozó jelentett állapotok a következők: "Elérhető", "Nem érhető el" és "Leromlott". Azonban olyan helyzetekben, amikor a futó és az Azure-erőforrás nem tud kommunikálni, egy "Ismeretlen" állapot az erőforrás, és ez tekinthető "aktív" állapotesemény.

Ha azonban egy erőforrás "Ismeretlen" jelentést készít, valószínű, hogy az állapota nem változott a legutóbbi pontos jelentés óta. Ha az "Ismeretlen" eseményekriasztásait szeretné kiküszöbölni, megadhatja ezt a logikát a sablonban:

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

Ebben a példában csak olyan eseményekről értesítünk, ahol az aktuális és a korábbi állapot nem rendelkezik "Ismeretlen" állapottal. Ez a módosítás hasznos lehet, ha az értesítéseket közvetlenül a mobiltelefonjára vagy e-mail-címére küldi. 

Vegye figyelembe, hogy lehetséges, hogy a currentHealthStatus és a previousHealthStatus tulajdonságok null értékűek legyenek bizonyos eseményekben. Ha például egy frissített esemény bekövetkezik, valószínű, hogy az erőforrás állapota nem változott az utolsó jelentés óta, csak azt, hogy további eseményadatok állnak rendelkezésre (pl. ok). Ezért a fenti záradék használata azt eredményezheti, hogy egyes riasztások nem aktiválódnak, mert a properties.currentHealthStatus és properties.previousHealthStatus értékek null értékűek lesznek.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>A riasztás módosítása a felhasználó által kezdeményezett események elkerülése érdekében

Az erőforrás-állapot eseményeket a platform által kezdeményezett és a felhasználó által kezdeményezett események aktiválhatják. Előfordulhat, hogy csak akkor küld értesítést, ha az állapotesemény az Azure platform okozza.

A riasztás egyszerűen konfigurálható úgy, hogy csak az ilyen típusú eseményekre szűrjön:

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
Ne feledje, hogy egyes eseményeknél az okmező null értékű lehet. Ez azt illeti, az egészségügyi átmenet történik (pl. elérhető a nem érhető el), és az esemény azonnal naplózza, hogy megakadályozzák az értesítési késedelmek. Ezért a fenti záradék használata azt eredményezheti, hogy a riasztás nem aktiválódik, mert a properties.clause tulajdonság értéke null értékű lesz.

## <a name="complete-resource-health-alert-template"></a>Teljes erőforrás-állapot riasztási sablon

Az előző szakaszban ismertetett különböző korrekciók at használva az alábbiakban egy mintasablon látható, amely a jel-zaj arány maximalizálására van beállítva. Ne feledje, a fenti kikötések, ahol a currentHealthStatus, previousHealthStatus, és okoz tulajdonság értékek null lehet bizonyos események.

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
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
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

Azonban tudni fogja a legjobban, hogy milyen konfigurációk hatékonyak az Ön számára, ezért használja az ebben a dokumentációban tanított eszközöket saját testreszabásához.

## <a name="next-steps"></a>További lépések

További információ az erőforrás-állapotról:
-  [Az Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapotellenőrzések](resource-health-checks-resource-types.md)


Szolgáltatásállapot-riasztások létrehozása:
-  [Riasztások konfigurálása a szolgáltatás állapotához](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Az Azure-tevékenységnapló eseménysémája](../azure-monitor/platform/activity-log-schema.md)
