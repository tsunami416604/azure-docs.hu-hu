---
title: Azure Monitor munkafüzetek és Azure Resource Manager sablonok
description: Egyszerűsítse a komplex jelentéskészítést az előre összeállított és egyéni paraméteres Azure Monitor Azure Resource Manager-sablonokkal üzembe helyezett munkafüzetek használatával
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658404"
---
# <a name="programmatically-manage-workbooks"></a>Munkafüzetek programozott kezelése

Az erőforrás-tulajdonosok lehetőségük van a munkafüzetek programozott módon történő létrehozására és kezelésére Resource Manager-sablonok használatával. 

Ez olyan esetekben lehet hasznos, mint például:
* Szervezeti vagy tartomány-specifikus elemzési jelentések üzembe helyezése az erőforrás-telepítésekkel együtt. Előfordulhat például, hogy üzembe helyezi az új alkalmazások vagy virtuális gépek szervezeti teljesítményével és meghibásodásával kapcsolatos munkafüzeteket.
* Szabványos jelentések vagy irányítópultok üzembe helyezése meglévő erőforrások munkafüzetek használatával.

A munkafüzet a kívánt alcsoportban/erőforrás-csoportban és a Resource Manager-sablonokban megadott tartalommal lesz létrehozva.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager sablon a munkafüzetek telepítéséhez
1. Nyisson meg egy olyan munkafüzetet, amelyet programozott módon szeretne üzembe helyezni.
2. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
3. Nyissa meg a _speciális szerkesztő_ az eszköztáron található _</>_ gombbal.
4. A szerkesztőben váltson a _sablon típusára_ _Resource Manager-sablonra_.
5. A létrehozáshoz használt Resource Manager-sablon megjelenik a szerkesztőben. Másolja a tartalmat, és használja a as-t, vagy egyesítse egy nagyobb sablonnal, amely a célként megadott erőforrást is telepíti.

    ![A Resource Manager-sablonnak a munkafüzet felhasználói felületéről való beszerzését bemutató kép](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Minta Azure Resource Manager sablon
Ez a sablon bemutatja, hogyan helyezhet üzembe egy egyszerű munkafüzetet, amely a következőt jeleníti meg: ""Helló világ!"alkalmazás!"
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Sablon paraméterei

| Paraméter | Magyarázat |
| :------------- |:-------------|
| `workbookDisplayName` | A katalógusban vagy a mentett listán használt munkafüzet rövid neve. Egyedinek kell lennie az erőforráscsoport és a forrás hatókörében |
| `workbookType` | A katalógus, amelyben a munkafüzet megjelenik. A támogatott értékek a következők: munkafüzet, `tsg`, Azure Monitor stb. |
| `workbookSourceId` | Annak az erőforrás-példánynak az azonosítója, amelyhez a munkafüzet társítva lesz. Az új munkafüzet megjelenik az adott erőforrás-példánnyal kapcsolatban – például az erőforrás tartalomjegyzékében a _munkafüzetben_. Ha azt szeretné, hogy a munkafüzet megjelenjen a Azure Monitor munkafüzet-katalógusában, használja az erőforrás-azonosító helyett _Azure monitor_ karakterláncot. |
| `workbookId` | A munkafüzet-példány egyedi GUID azonosítója. A _[newGuid ()]_ használatával hozzon létre automatikusan egy új GUID azonosítót. |
| `kind` | Annak megadására szolgál, hogy a létrehozott munkafüzet megosztott vagy privát. A megosztott munkafüzetekhez és a _felhasználókhoz_ _megosztott_ értéket használjon magánjellegűként. |
| `location` | Az Azure-beli hely, ahol a munkafüzet létre lesz hozva. A _[resourceGroup (). location]_ használatával hozza létre ugyanazt a helyet, mint az erőforráscsoportot. |
| `serializedData` | A munkafüzetben használandó tartalmat vagy adattartalmat tartalmazza. Az érték beszerzéséhez használja a munkafüzetek felhasználói felületének Resource Manager-sablonját. |

### <a name="workbook-types"></a>Munkafüzetek típusai
A munkafüzetek típusai határozzák meg, hogy melyik munkafüzet-gyűjteménybe írja be az új munkafüzet-példányt. A lehetőségek a következők:

| Típus | Gyűjtemény helye |
| :------------- |:-------------|
| `workbook` | A legtöbb jelentésben használt alapértelmezett érték, beleértve a munkafüzetek Application Insights, Azure Monitor stb. gyűjteményét.  |
| `tsg` | A hibaelhárítási útmutatók gyűjteménye Application Insights |
| `usage` | A _további_ katalógus a _használat_ alatt Application Insights |

### <a name="limitations"></a>Korlátozások
Technikai okokból ez a mechanizmus nem használható munkafüzet-példányok létrehozására a Application Insights _munkafüzetek_ galériájában. Dolgozunk ennek a korlátozásnak a kezelésén. Addig is javasoljuk, hogy a hibaelhárítási útmutató (workbookType: `tsg`) használatával helyezzen üzembe Application Insights kapcsolódó munkafüzeteket.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használják a munkafüzetek az új Azure Monitor a [tárolási élményhez](../insights/storage-insights-overview.md).

