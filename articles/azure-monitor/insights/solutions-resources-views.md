---
title: Nézetek az irányítási megoldásokban | Microsoft dokumentumok
description: 'A felügyeleti megoldások általában egy vagy több nézetet tartalmaznak az adatok megjelenítéséhez.  Ez a cikk azt ismerteti, hogy miként exportálható a Nézettervező által létrehozott és felügyeleti megoldásba való beillesztése. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663045"
---
# <a name="views-in-management-solutions-preview"></a>Nézetek a felügyeleti megoldásokban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció felügyeleti megoldások létrehozásához, amelyek jelenleg előzetes verzióban. Az alábbiakban ismertetett sémák változhatnak.    


[A felügyeleti megoldások](solutions.md) általában egy vagy több nézetet tartalmaznak az adatok megjelenítéséhez.  Ez a cikk azt ismerteti, hogy miként exportálható a [Nézettervező](../../azure-monitor/platform/view-designer.md) által létrehozott és felügyeleti megoldásba való beillesztése.  

> [!NOTE]
> A jelen cikkben szereplő minták olyan paramétereket és változókat használnak, amelyek a felügyeleti megoldásokhoz szükségesek vagy közösek, és amelyeket a Tervezés és [felügyeleti megoldás létrehozása az Azure-ban ismertetett.](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már ismeri a felügyeleti megoldás és a megoldásfájl szerkezetének [létrehozását.](solutions-creating.md)

## <a name="overview"></a>Áttekintés
Ha egy nézetet szeretne felvenni egy felügyeleti megoldásba, hozzon létre egy **erőforrást** a [megoldásfájlban.](solutions-creating.md)  A JSON, amely leírja a nézet részletes konfigurációáltalában összetett, bár, és nem valami, hogy egy tipikus megoldás szerzője képes lenne manuálisan létrehozni.  A leggyakoribb módszer a nézet létrehozása a [Nézettervező](../../azure-monitor/platform/view-designer.md)használatával, exportálása, majd részletes konfigurációja hozzáadása a megoldáshoz.

A nézet megoldáshoz való hozzáadásának alapvető lépései a következők.  Minden lépést az alábbi szakaszok részletesebben ismertetünk.

1. Exportálja a nézetet fájlba.
2. Hozza létre a nézeterőforrást a megoldásban.
3. Adja meg a nézet részleteit.

## <a name="export-the-view-to-a-file"></a>A nézet exportálása fájlba
A nézet fájlba való exportálásához kövesse a [Log Analytics Nézettervező](../../azure-monitor/platform/view-designer.md) utasításait.  Az exportált fájl JSON formátumú lesz, és ugyanazok az [elemek lesznek, mint a megoldásfájl](solutions-solution-file.md).  

A nézetfájl **erőforráseleme** olyan erőforrással rendelkezik, amely a Log Analytics-munkaterületet reprezentatot **képviseli.**  Ez az elem olyan alelemmel rendelkezik, amely nek olyan **nézettípusa** lesz, amely a nézetet jelöli, és részletes konfigurációját tartalmazza.  Másolja az elem részleteit, majd másolja be a megoldásba.

## <a name="create-the-view-resource-in-the-solution"></a>A nézeterőforrás létrehozása a megoldásban
Adja hozzá a következő **resources** nézeterőforrást a megoldásfájl erőforráseleméhez.  Ez az alábbiakban ismertetett változókat használja, amelyeket szintén hozzá kell adnia.  Vegye figyelembe, hogy az **Irányítópult** és az **OverviewTile** tulajdonságok olyan helyőrzők, amelyeket felül ír az exportált nézetfájl megfelelő tulajdonságaival.

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

Adja hozzá a következő változókat a megoldásfájl változók eleméhez, és cserélje le az értékeket a megoldás értékeire.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Ne feledje, hogy a teljes nézeterőforrást átmásolhatja az exportált nézetfájlból, de a következő módosításokat kell végrehajtania ahhoz, hogy a megoldásban működjön.  

* A nézeterőforrás **típusát** **nézetekről** **Microsoft.OperationalInsights/workspaces**-re kell módosítani.
* A nézeterőforrás **névtulajdonságát** módosítani kell, hogy az tartalmazza a munkaterület nevét.
* A munkaterületfüggőséget el kell távolítani, mivel a munkaterületi erőforrás nincs definiálva a megoldásban.
* **A DisplayName** tulajdonságot hozzá kell adni a nézethez.  Az **azonosítónak**, **név**és **displayname azonosítónak** egyeznie kell.
* A paraméterneveket módosítani kell, hogy megfeleljenek a szükséges paraméterkészletnek.
* A változókat meg kell határozni az oldatban, és fel kell használni a megfelelő tulajdonságokban.

### <a name="log-analytics-api-version"></a>Log Analytics API-verziója
A Resource Manager-sablonban definiált összes Log Analytics-erőforrás rendelkezik egy **apiVersion** tulajdonsággal, amely meghatározza az erőforrás által használandó API-verziót.  Ez a verzió az [örökölt és a frissített lekérdezési nyelvet](../../azure-monitor/log-query/log-query-overview.md)használó lekérdezések esetén eltérő.  

 Az alábbi táblázat az örökölt és a frissített munkaterületek nézeteinek Log Analytics API-verzióit határozza meg: 

| Munkaterületi verzió | API-verzió | Lekérdezés |
|:---|:---|:---|
| v1 (örökölt)   | 2015-11-01-előzetes | Örökölt formátum.<br> Példa: Type=Event EventLevelName = Error  |
| v2 (korszerűsített) | 2015-11-01-előzetes | Örökölt formátum.  Telepítéskor frissített formátummá alakítva.<br> Példa: Type=Event EventLevelName = Error<br>Konvertált: Esemény &#124; ahol Az EventLevelName == "Hiba"  |
| v2 (korszerűsített) | 2017-03-03-előzetes | Frissítési formátum. <br>Példa: Esemény &#124; ahol az EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>A nézet részleteinek hozzáadása
Az exportált nézetfájlban lévő nézeterőforrás két elemet tartalmaz az **Irányítópult** és **overviewtile** nevű **tulajdonságelemben,** amelyek a nézet részletes konfigurációját tartalmazzák.  Másolja ezt a két elemet és azok tartalmát a megoldásfájl **nézeterőforrásának** tulajdonságelemébe.

## <a name="example"></a>Példa
Az alábbi minta például egy egyszerű megoldásfájlt jelenít meg egy nézetben.  A szóközök miatt ellipszisek (...) jelennek meg az **Irányítópult** és az **OverviewTile** tartalomhoz.

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




## <a name="next-steps"></a>További lépések
* Ismerje meg a [felügyeleti megoldások](solutions-creating.md)létrehozásának teljes részleteit.
* [Az Automation runbookok felvétele a felügyeleti megoldásba.](solutions-resources-automation.md)
