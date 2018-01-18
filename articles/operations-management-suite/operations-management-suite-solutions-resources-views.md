---
title: "Nézetek az Operations Management Suite (OMS) megoldások |} Microsoft Docs"
description: "Az Operations Management Suite (OMS) megoldások rendszerint tartalmazza egy vagy több nézetet, amelyen az adatok megjelenítése.  Ez a cikk ismerteti, hogyan hozta létre az adatforrásnézet-tervezőből nézet exportálása és megoldásra. "
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: c103ee748446c4819b7925af04d90c22225a21a3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Nézetek az Operations Management Suite (OMS) megoldások (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentum megoldások létrehozásához az OMS Szolgáltatáshoz, amely jelenleg előzetes verziójúak. Az alábbiakban a séma van változhat.    
>
>

[Az Operations Management Suite (OMS) megoldások](operations-management-suite-solutions.md) rendszerint tartalmazza egy vagy több nézetet, amelyen az adatok megjelenítése.  Ez a cikk ismerteti, hogyan által létrehozott nézetre exportálása a [adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md) adja hozzá a megoldásra.  

> [!NOTE]
> Ebben a cikkben a minták használható paramétereket és változókat, amelyek a szükséges vagy közös felügyeleti megoldás és a [létrehozása kezelési megoldásai Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)
>
>

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy most már tudja, hogyan [felügyeleti megoldás létrehozása](operations-management-suite-solutions-creating.md) és a megoldás fájl struktúráját.

## <a name="overview"></a>Áttekintés
Egy nézet tartalmazza a megoldásra, hozzon létre egy **erőforrás** ki azt a a [megoldásfájlt](operations-management-suite-solutions-creating.md).  A JSON-NÁ, amely leírja a nézet részletes konfigurációs pedig általában összetett, ha valami nem, hogy egy tipikus megoldás Szerző tudnak manuális létrehozása.  A leggyakoribb módja, ha a nézet használatával a [adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md), exportálni, és adja hozzá a részletes konfigurációját a megoldáshoz.

Az alapvető lépéseken nézet hozzáadása a megoldás a következők:  Az alábbi szakaszokban részletesen ismertetett egyes lépéseit.

1. A nézet exportálja egy fájlba.
2. A nézet erőforrás létrehozása a megoldásban.
3. Adja hozzá a részleteinek megtekintése.

## <a name="export-the-view-to-a-file"></a>A nézet exportálja egy fájlba
Kövesse az utasításokat, [napló Analytics adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md) nézet exportálja egy fájlba.  Az exportált fájl JSON formátumú azonos lesz [a fájl az elemek](operations-management-suite-solutions-solution-file.md).  

A **erőforrások** nézet fájl elem típussal rendelkező erőforrás lesz **Microsoft.OperationalInsights/workspaces** , amely az OMS-munkaterület jelöli.  Ez az elem lesz a subelement típussal rendelkező **nézetek** , amely jelenti. a nézet és a részletes konfigurációs adatokat tartalmaz.  Ez az elem részleteinek másolása lesz, és átmásolja a megoldás.

## <a name="create-the-view-resource-in-the-solution"></a>A megoldásban a nézet létrehozása
Adja hozzá a következő nézet erőforrást a **erőforrások** a megoldásfájlt eleme.  Ez a változókat, amelyek az alábbiakban található, hogy hozzá kell adnia is használja.  Vegye figyelembe, hogy a **irányítópult** és **OverviewTile** tulajdonságainak helyőrzők, amely felülírja az exportált nézet fájlból a megfelelő tulajdonságokkal.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Adja hozzá a következő változók változók eleme, a fájl, és cserélje le a megoldás az értékeket.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Vegye figyelembe, hogy a teljes nézet erőforrás átmásolhatja az exportált nézet fájlból, de a megoldásban működéshez az alábbi módosításokat kell.  

* A **típus** a nézet az írható módról lehet átkapcsolni kell erőforrás **nézetek** való **Microsoft.OperationalInsights/workspaces**.
* A **neve** munkaterület neve is módosítani kell a nézet erőforrás tulajdonságot.
* A munkaterület függőség kell lehet eltávolítani, mert a munkaterület-erőforrás nincs megadva a megoldásban.
* **DisplayName** tulajdonságot hozzá kell adni a nézetet.  A **azonosító**, **neve**, és **DisplayName** összes egyeznie kell.
* A szükséges paraméterek portpároknak egyezniük meg kell változtatni a paraméterek nevei.
* Változók a megoldás definiált legyen, és a megfelelő tulajdonságokat szerepel.

### <a name="log-analytics-api-version"></a>Napló Analytics API-verzió
A Resource Manager-sablon definiált összes Naplóelemzési erőforrás rendelkezik egy tulajdonság **apiVersion** , amely meghatározza, hogy az erőforrást kell használnia az API verzióját.  Ebben a verzióban különbözik a nézet, amelynek a lekérdezéseket, amelyekkel a [örökölt és a frissített lekérdezési nyelv](../log-analytics/log-analytics-log-search-upgrade.md).  

 Az alábbi táblázat felsorolja a napló Analytics API-verziók a nézetek az örökölt és frissített munkaterületek: 

| Munkaterület-verzió | API-verzió | Lekérdezés |
|:---|:---|:---|
| 1-es verzió (örökölt)   | 2015 11-01. dátumú előnézeti | A hagyományos formátumú.<br> Példa: Írja be az esemény EventLevelName = hiba =  |
| v2 (frissítése) | 2015 11-01. dátumú előnézeti | A hagyományos formátumú.  Telepítse a frissített formátumra alakítja át.<br> Példa: Írja be az esemény EventLevelName = hiba =<br>Konvertálva: esemény &#124; Ha EventLevelName == "Error"  |
| v2 (frissítése) | 2017-03-03 – előzetes | Frissítési formátumban. <br>Példa: Az esemény &#124; Ha EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>A nézet részletek megadása
A nézet erőforrás az exportált nézet fájl tartalmazza a két elem a **tulajdonságok** nevű elem **irányítópult** és **OverviewTile** a nézet részletes konfigurációját tartalmazza.  Ez a két elem és azok tartalmát másolja a **tulajdonságok** elem a megoldásfájlt a nézet erőforrás.

## <a name="example"></a>Példa
Például a következő példában egy egyszerű megoldást fájl nézetet.  Folytatást jelző pontokra (...) jelennek meg a **irányítópult** és **OverviewTile** terület okokból tartalmát.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>További lépések
* További részleteket létrehozásának [megoldások](operations-management-suite-solutions-creating.md).
* Tartalmaznak [a felügyeleti megoldás az automatizálási runbookok](operations-management-suite-solutions-resources-automation.md).
