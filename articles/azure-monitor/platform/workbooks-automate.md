---
title: Azure Monitor-munkafüzetek és Azure Resource Manager-sablonok
description: Egyszerűsítse az összetett jelentéseket az Azure Resource Manager-sablonokon keresztül telepített, előre összeállított és egyéni paraméterezett Azure Monitor-munkafüzetekkel
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658404"
---
# <a name="programmatically-manage-workbooks"></a>Munkafüzetek programozott kezelése

Az erőforrás-tulajdonosok programozott módon hozhatják létre és kezelhetik munkafüzeteiket az Erőforrás-kezelő sablonjain keresztül. 

Ez olyan esetekben lehet hasznos, mint például:
* Org- vagy tartományspecifikus elemzési jelentések üzembe helyezése az erőforrások központi telepítései mellett. Például előfordulhat, hogy org-specifikus teljesítmény- és hibamunkafüzeteket telepít az új alkalmazásokhoz vagy virtuális gépekhez.
* Szabványos jelentések vagy irányítópultok telepítése a meglévő erőforrások munkafüzetei használatával.

A munkafüzet a kívánt al/erőforráscsoportban és az Erőforrás-kezelő sablonokban megadott tartalommal jön létre.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Azure Resource Manager-sablon munkafüzetek üzembe helyezéséhez
1. Nyisson meg egy programozottan telepíteni kívánt munkafüzetet.
2. Az eszköztárelemre kattintva átváltson _Edit_ a munkafüzetet szerkesztési módra.
3. Nyissa meg a _</>_ Speciális _szerkesztőt_ az eszköztár gombjával.
4. A szerkesztőben váltson _a Sablontípus_ és _az Erőforrás-kezelő sablon ra._
5. Megjelenik a szerkesztőben a létrehozandó Erőforrás-kezelő sablon. Másolja a tartalmat, és használja a hogy van, vagy egyesítse azt egy nagyobb sablont, amely szintén telepíti a cél erőforrást.

    ![Az Erőforráskezelő sablon beszerezése a munkafüzet felhasználói felületéről](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Minta Azure Resource Manager sablon
Ez a sablon bemutatja, hogyan helyezhet ő egy egyszerű munkafüzetet, amely "Hello World!" üzenetet jelenít meg.
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
| `workbookDisplayName` | A gyűjteményben vagy a Mentett listában használt munkafüzet rövid neve. Az erőforráscsoport és a forrás hatókörében egyedinek kell lennie |
| `workbookType` | Az a gyűjtemény, amely alatt a munkafüzet látható lesz. A támogatott értékek `tsg`közé tartozik a munkafüzet, az Azure Monitor stb. |
| `workbookSourceId` | Annak az erőforráspéldánynak az azonosítója, amelyhez a munkafüzet társítva lesz. Az új munkafüzet megjelenik ehhez az erőforráspéldányhoz kapcsolódóan – például az erőforrás _tartalomtáblájában_a Munkafüzet csoportban. Ha azt szeretné, hogy a munkafüzet megjelenjen az Azure Monitor munkafüzetgyűjteményében, használja az _Azure Monitor_ karakterláncot erőforrás-azonosító helyett. |
| `workbookId` | A munkafüzetpéldány egyedi guid-ja. A [newGuid()] segítségével automatikusan hozzon létre egy új guid.Use _[newGuid()]_ to automatically create a new guid. |
| `kind` | Itt adható meg, hogy a létrehozott munkafüzet megosztott vagy privát.It s to specify if the created workbook is shared or private. A megosztott _értéket_ a megosztott munkafüzetekben és a _felhasználók_ számára használja a privát munkafüzetekben. |
| `location` | Az Azure-hely, ahol a munkafüzet et létre kell hozni. _A[resourceGroup(.location]_ segítségével hozza létre az erőforráscsoporttal azonos helyen |
| `serializedData` | A munkafüzetben használandó tartalmat vagy hasznos tartalmat tartalmazza. Az erőforrás-kezelő sablon használata a munkafüzetek felhasználói felületéről az érték leéséhez |

### <a name="workbook-types"></a>Munkafüzet-típusok
A munkafüzettípusok határozzák meg, hogy az új munkafüzetpéldány melyik munkafüzetgyűjtemény-típus alatt jelenjen meg. A lehetőségek a következők:

| Típus | Galéria helye |
| :------------- |:-------------|
| `workbook` | A legtöbb jelentésben használt alapértelmezett beállítás, beleértve az Application Insights munkafüzet-gyűjteményét, az Azure Monitort stb.  |
| `tsg` | Az Alkalmazáselemzési útmutatók hibaelhárítási útmutatóinak gyűjteménye |
| `usage` | A _További_ galéria az Application Insights _használata_ csoportban |

### <a name="limitations"></a>Korlátozások
Technikai okokból ez a mechanizmus nem használható munkafüzetpéldányok _Workbooks_ létrehozására az Application Insights munkafüzet-gyűjteményében. Dolgozunk e korlátozás kezelésén. Addig is azt javasoljuk, hogy használja a Hibaelhárítási `tsg`útmutató gyűjtemény (workbookType: ) az Application Insights kapcsolódó munkafüzetek.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használják a munkafüzeteket az új [Azure Monitor for Storage-élmény működtetéséhez.](../insights/storage-insights-overview.md)

