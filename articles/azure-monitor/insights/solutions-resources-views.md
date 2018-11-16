---
title: Felügyeleti megoldások nézetekben |} A Microsoft Docs
description: 'Felügyeleti megoldások általában egy vagy több nézetek segítségével ábrázolhatja adatait tartalmazza.  Ez a cikk ismerteti, hogyan lehet exportálni az adatforrásnézet-tervezőből által létrehozott nézetet és a egy megoldás. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: f00c11881da7624809b1253361c17285666d9ba3
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715477"
---
# <a name="views-in-management-solutions-preview"></a>Nézetek az eszközkezelési megoldások (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentum, jelenleg előzetes verzióban elérhető kezelési megoldások létrehozásához. Semmilyen sémát, az alábbiakban a változhat.    


[Felügyeleti megoldások](solutions.md) általában egy vagy több nézetek segítségével ábrázolhatja adatait tartalmazza.  Ez a cikk bemutatja, hogyan exportálja által létrehozott nézetre a [adatforrásnézet-tervezőből](../../log-analytics/log-analytics-view-designer.md) és a egy megoldás.  

> [!NOTE]
> Ebben a cikkben a minták használata, paraméterek és változók, kötelező vagy közös felügyeleti megoldások és az itt ismertetett [tervezés és felépítés felügyeleti megoldás az Azure-ban](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már megismerkedett az [felügyeleti megoldás létrehozása](solutions-creating.md) és a egy megoldás-fájl szerkezete.

## <a name="overview"></a>Áttekintés
Nézet tartalmazza a felügyeleti megoldás, hozzon létre egy **erőforrás** a hozzá tartozó a [megoldásfájlt](solutions-creating.md).  A nézet részletes konfigurációs leíró JSON-t pedig jellemzően összetett, ha valami nem, hogy egy tipikus megoldás Szerző tudná manuális létrehozása.  A leggyakrabban használt módszer, ha a nézet használata a [adatforrásnézet-tervezőből](../../log-analytics/log-analytics-view-designer.md), exportálhatja, és a részletes konfigurációs hozzáadása a megoldáshoz.

Nézet hozzáadása egy megoldás alapvető lépéseit az alábbiak szerint.  Minden lépés az alábbi szakaszokban részletesen ismertetjük.

1. A nézet exportálja egy fájlba.
2. A nézet erőforrás létrehozása a megoldásban.
3. Adja hozzá a részleteinek megtekintése.

## <a name="export-the-view-to-a-file"></a>A nézet exportálja egy fájlba
Kövesse az utasításokat, [Log Analytics Nézettervező](../../log-analytics/log-analytics-view-designer.md) nézet exportálása fájlba.  Az exportált fájl azonos JSON formátumban lesznek [elemek, a megoldás fájllal](solutions-solution-file.md).  

A **erőforrások** elem a nézet fájl lesz típusú erőforrás **Microsoft.OperationalInsights/workspaces** , amely jelzi, hogy a Log Analytics-munkaterületet.  Ez az elem lesz egy alelem típusú **nézetek** , amely a nézet jelöl, és a részletes konfigurációs tartalmaz.  Másolja vágólapra az adatokat, ezt az összetevőt, és másolja a megoldás.

## <a name="create-the-view-resource-in-the-solution"></a>A megoldás a nézet erőforrás létrehozása
Adja hozzá a következő nézet erőforrást a **erőforrások** elem a megoldásfájl.  Ez a változókat, amelyek az alábbiakban tekintheti át, hogy hozzá kell adnia is használja.  Vegye figyelembe, hogy a **irányítópult** és **OverviewTile** tulajdonságait a rendszer a helyőrzők, amely felülírja az exportált nézet fájlból a megfelelő tulajdonságokkal.

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

Adja hozzá a következő változókat a megoldásfájl változók elemének, és cserélje le az értékeket, mint a megoldását.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Vegye figyelembe, hogy a teljes nézet erőforrás sikerült másolja az exportált nézet fájlból, de kell, hogy működjön a megoldás a következő módosításokat.  

* A **típus** a nézet erőforrás kell módosítható **nézetek** való **Microsoft.OperationalInsights/workspaces**.
* A **neve** tulajdonság az erőforrás megtekintése a munkaterület neve is módosítani kell.
* A munkaterület függőség kell lehet eltávolítani, mert a munkaterület-erőforráshoz nincs definiálva a megoldásban.
* **DisplayName** tulajdonságot kell adni a nézetet.  A **azonosító**, **neve**, és **DisplayName** összes egyeznie kell.
* Paraméterek nevei a szükséges paraméterek készletét megfelelően módosítani kell.
* Változók kell definiálva a megoldásban, és használja a megfelelő tulajdonságokat.

### <a name="log-analytics-api-version"></a>Log Analytics API-verzió
Egy Resource Manager-sablonban definiált összes Log Analytics erőforrás rendelkezik egy tulajdonság **apiVersion** , amely meghatározza, hogy használja az erőforrás API-verzió.  Ez a verzió nem egyezik a nézetek lekérdezésekkel, amelyek használják a [örökölt és a frissített lekérdezési nyelv](../../log-analytics/log-analytics-queries.md).  

 Az alábbi táblázat felsorolja a nézetek a Log Analytics API-verziók a régebbi és a frissített munkaterületeken: 

| Munkaterület-verzió | API-verzió | Lekérdezés |
|:---|:---|:---|
| V1 (örökölt)   | 2015. 11. 01-előzetes verzió | Örökölt formátum.<br> Példa: Az esemény Error = hiba =  |
| v2 (frissítve) | 2015. 11. 01-előzetes verzió | Örökölt formátum.  Telepítse a frissített formátumra alakítja át.<br> Példa: Az esemény Error = hiba =<br>Konvertálva: esemény &#124; ahol Error == "Error"  |
| v2 (frissítve) | 2017-03-03-előzetes verzió | Frissítési formátum. <br>Példa: Event &#124; ahol Error == "Error"  |


## <a name="add-the-view-details"></a>Adja hozzá a részleteinek megtekintése
A nézet exportált fájlban a nézet erőforrás tartalmazni fogja a két elem a **tulajdonságok** nevű elem **irányítópult** és **OverviewTile** tartalmazza a részletes a nézet konfigurációja.  Ez a két elem és azok tartalmát, másolja a **tulajdonságok** elem a nézet erőforrás a megoldásfájl.

## <a name="example"></a>Példa
Például a következő minta bemutatja egy egyszerű megoldást a fájl nézetet.  Három pont (...) jelennek meg a **irányítópult** és **OverviewTile** terület okokból tartalmát.

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
* Ismerje meg a részleteket létrehozásának [felügyeleti megoldások](solutions-creating.md).
* Például [a felügyeleti megoldás az Automation-runbookok](solutions-resources-automation.md).
