---
title: Nézetek a felügyeleti megoldásokban | Microsoft Docs
description: 'A felügyeleti megoldások általában egy vagy több nézetet is tartalmaznak az adatmegjelenítéshez.  Ez a cikk azt ismerteti, hogyan exportálhatók a View Designer által létrehozott nézetek, és hogyan vehetők fel felügyeleti megoldásba. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663045"
---
# <a name="views-in-management-solutions-preview"></a>Nézetek a felügyeleti megoldásokban (előzetes verzió)
> [!NOTE]
> Ez a jelenleg előzetes verzióban elérhető felügyeleti megoldások létrehozásának előzetes dokumentációja. Az alább ismertetett sémák változhatnak.    


A [felügyeleti megoldások](solutions.md) általában egy vagy több nézetet is tartalmaznak az adatmegjelenítéshez.  Ez a cikk azt ismerteti, hogyan exportálhatók a [View Designer](../../azure-monitor/platform/view-designer.md) által létrehozott nézetek, és hogyan vehetők fel felügyeleti megoldásba.  

> [!NOTE]
> A cikkben szereplő minták olyan paramétereket és változókat használnak, amelyek szükségesek vagy közösek a felügyeleti megoldásokhoz, és az Azure-beli [felügyeleti megoldás kialakítása és](solutions-creating.md) létrehozása című témakörben olvashatók.
>
>

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már ismeri a [felügyeleti megoldás létrehozását](solutions-creating.md) és a megoldás fájljának szerkezetét.

## <a name="overview"></a>Áttekintés
Ahhoz, hogy egy nézetet egy felügyeleti megoldásban is felvegyen, létre kell hoznia egy **erőforrást** a [megoldás fájljában](solutions-creating.md).  A nézet részletes konfigurációját leíró JSON általában összetett, és nem valami, amit egy tipikus megoldás szerzője manuálisan tud létrehozni.  A leggyakoribb módszer a nézet létrehozása a [tervező](../../azure-monitor/platform/view-designer.md)használatával, exportálás, majd a megoldás részletes konfigurációjának hozzáadása.

A nézetnek a megoldáshoz való hozzáadásának alapvető lépései a következők.  Az alábbi szakaszokban részletesen ismertetjük az egyes lépéseket.

1. A nézet exportálása fájlba.
2. Hozza létre a nézet erőforrását a megoldásban.
3. Adja meg a nézet részleteit.

## <a name="export-the-view-to-a-file"></a>Nézet exportálása fájlba
A nézetek fájlba való exportálásához kövesse a következő témakör utasításait: [log Analytics View Designer](../../azure-monitor/platform/view-designer.md) .  Az exportált fájl JSON formátumú, és ugyanazokkal az elemekkel fog megjelenni [, mint a megoldás fájlja](solutions-solution-file.md).  

A nézet fájljának **erőforrások** elemében egy, a log Analytics munkaterületet jelképező **Microsoft. OperationalInsights/munkaterület** típusú erőforrás lesz.  Ez az elem egy olyan alelemmel fog rendelkezni, amely a nézetet jelképező **nézettel** rendelkezik, és tartalmazza annak részletes konfigurációját.  Másolja az elem részleteit, majd másolja be a megoldásba.

## <a name="create-the-view-resource-in-the-solution"></a>A nézet erőforrásának létrehozása a megoldásban
Adja hozzá az alábbi megtekintési erőforrást a megoldás fájljának **erőforrások** eleméhez.  Ez az alább leírt változókat használja, amelyeket szintén hozzá kell adnia.  Vegye figyelembe, hogy az **irányítópult** -és **OverviewTile** -tulajdonságok olyan helyőrzők, amelyeket a rendszer az exportált nézet megfelelő tulajdonságaival felülír.

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

Adja hozzá a következő változókat a megoldás fájljának változók eleméhez, és cserélje le az értékeket a megoldására.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Vegye figyelembe, hogy a teljes nézet erőforrást átmásolhatja az exportált nézetből, de a következő módosításokat kell elvégeznie ahhoz, hogy működjön a megoldásban.  

* A megtekintési erőforrás **típusát** módosítani kell a **nézetekről** a **Microsoft. OperationalInsights/munkaterületekre**.
* A nézet erőforráshoz tartozó **Name** tulajdonságot módosítani kell, hogy tartalmazza a munkaterület nevét.
* El kell távolítani a munkaterület függőségét, mert a munkaterület-erőforrás nincs definiálva a megoldásban.
* A **DisplayName** tulajdonságot fel kell venni a nézetbe.  Az **azonosító**, a **név**és a **DisplayName** értéknek egyeznie kell.
* A paraméterek nevét módosítani kell, hogy megfeleljenek a szükséges paramétereknek.
* A változókat a megoldásban kell meghatározni, és a megfelelő tulajdonságokban kell használni.

### <a name="log-analytics-api-version"></a>Log Analytics API-verzió
A Resource Manager-sablonban definiált összes Log Analytics-erőforráshoz tartozik egy **apiVersion** , amely meghatározza az erőforrás által használandó API verzióját.  Ez a verzió különbözik az [örökölt és a frissített lekérdezési nyelvet](../../azure-monitor/log-query/log-query-overview.md)használó lekérdezések nézeteinek.  

 A következő táblázat a régi és a frissített munkaterületek nézeteinek Log Analytics API-verzióit határozza meg: 

| Munkaterület verziója | API-verzió | Lekérdezés |
|:---|:---|:---|
| v1 (örökölt)   | 2015-11-01 – előzetes verzió | Örökölt formátum.<br> Példa: Type = Event EventLevelName = Error  |
| v2 (frissített) | 2015-11-01 – előzetes verzió | Örökölt formátum.  A telepítés frissített formátumára lett konvertálva.<br> Példa: Type = Event EventLevelName = Error<br>Konvertálva: esemény &#124; , ahol a EventLevelName = = "Error"  |
| v2 (frissített) | 2017-03-03 – előzetes verzió | Frissítési formátum. <br>Példa: esemény &#124; , ahol a EventLevelName = = "Error"  |


## <a name="add-the-view-details"></a>A nézet részleteinek hozzáadása
Az exportált nézetben található erőforrás nézet az **irányítópult** és a **OverviewTile** nevű **Tulajdonságok** elemben két elemet tartalmaz, amelyek tartalmazzák a nézet részletes konfigurációját.  Másolja ezt a két elemet és azok tartalmát a megoldási fájlban lévő erőforrás megtekintése elem **Tulajdonságok** elemébe.

## <a name="example"></a>Példa
Az alábbi minta például egy egyszerű megoldási fájlt mutat be, amely nézettel rendelkezik.  Az **irányítópultok** és a **OverviewTile** esetében az ellipszisek (...) láthatók.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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




## <a name="next-steps"></a>Következő lépések
* A [felügyeleti megoldások](solutions-creating.md)létrehozásának részletes ismertetését itt találja.
* Az [Automation-runbookok belefoglalása a felügyeleti megoldásba](solutions-resources-automation.md).
