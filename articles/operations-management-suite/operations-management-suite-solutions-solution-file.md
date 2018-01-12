---
title: "Létrehozása kezelési megoldásai Operations Management Suite (OMS) |} Microsoft Docs"
description: "Megoldások bővítése Operations Management Suite (OMS), adja meg a csomagolt felügyeleti lehetőségeket, amelyek az ügyfelek az OMS-munkaterület adhat hozzá.  Ez a cikk részletesen hogyan hozhat létre a saját környezetében használható felügyeleti megoldás, vagy szeretné elérhetővé tenni az ügyfelek számára."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ace3042cc00cedd005955cdfb82c557fd4a8fb2
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="creating-a-management-solution-file-in-operations-management-suite-oms-preview"></a>A felügyeleti megoldás fájl létrehozása az Operations Management Suite (OMS) (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentum megoldások létrehozásához az OMS Szolgáltatáshoz, amely jelenleg előzetes verziójúak. Az alábbiakban a séma van változhat.  

Az Operations Management Suite (OMS) megoldások használják, mint [Resource Manager-sablonok](../azure-resource-manager/resource-manager-template-walkthrough.md).  A fő feladat hozhatnak létre megoldások hogyan van tanulási hogyan [sablon szerzői](../azure-resource-manager/resource-group-authoring-templates.md).  Ez a cikk ismerteti egyedi megoldások és a szokásos megoldás erőforrások konfigurálása használt sablonokat.


## <a name="tools"></a>Eszközök

Megoldás fájlok szövegszerkesztőben használhatja, de javasoljuk, hogy a következő cikkekben ismertetett módon a Visual Studio vagy Visual Studio Code nyújtott szolgáltatásokat kihasználva.

- [Létrehozása és telepítése a Visual Studio használatával Azure erőforráscsoport-sablonok](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [A Visual Studio Code Azure Resource Manager-sablonok használata](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>struktúra
A felügyeleti megoldás fájl alapvető szerkezete megegyezik egy [Resource Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md#template-format), amely a következőképpen történik.  A legfelső szintű elem és azok tartalmát, a megoldás az alábbi szakaszok mindegyikének ismerteti.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paraméterek
[Paraméterek](../azure-resource-manager/resource-group-authoring-templates.md#parameters) értékek, amelyekre szüksége van a felhasználótól a felügyeleti megoldás telepítésekor.  Standard paramétert, amely minden megoldás fog rendelkezni, és az adott megoldáshoz szükség szerint további paramétereket is hozzáadhat.  Hogyan felhasználók nyújtják a paraméterértékek való telepítésekor a megoldás függ az adott paraméter és a megoldás telepítésének módját.

Amikor a felhasználó telepíti a felügyeleti megoldás keresztül a [Azure piactér](operations-management-suite-solutions.md#finding-and-installing-management-solutions) vagy [Azure gyors üzembe helyezési sablonokat](operations-management-suite-solutions.md#finding-and-installing-management-solutions) megadását kéri az [OMS munkaterületet, és az Automation-fiók](operations-management-suite-solutions.md#oms-workspace-and-automation-account).  Ezek használhatók a szabványos paraméterek értékeit feltöltéséhez.  Nem kéri a felhasználót, hogy közvetlenül a szabványos paraméterek értékének megadására, de meg kell minden további paraméterek értékének megadására.

Amikor a felhasználó telepíti a megoldás [egy másik módszer](operations-management-suite-solutions.md#finding-and-installing-management-solutions), meg kell adniuk egy értéket az összes szabványos és az összes további paraméterei.

Az alábbiakban látható egy minta paraméter.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Az alábbi táblázatban a paraméter attribútumait.

| Attribútum | Leírás |
|:--- |:--- |
| type |A paraméter adattípusa. A bemeneti vezérlő jelenik meg a felhasználói adatok típusától függ.<br><br>logikai érték - a legördülő listából<br>karakterlánc - szövegmező<br>int - szövegmező<br>SecureString - jelszó mező<br> |
| category |A paraméter nem kötelező kategóriát.  Paraméterek ugyanabba a kategóriába sorolhatók. |
| vezérlő |További funkciók karakterlánc-paraméter.<br><br>datetime - Datetime vezérlő jelenik meg.<br>GUID - Guid-érték automatikusan jön létre, és a paraméter nem jelenik meg. |
| leírás |A paraméter nem kötelező leírása.  Megjelenik az adatokat a buborékban megjelenő mellett a paraméter. |

### <a name="standard-parameters"></a>Szabványos paraméterek
Az alábbi táblázat a minden felügyeleti megoldások szabványos paraméterek.  Ezek az értékek fel van töltve, a felhasználó helyett adatkérés el a megoldás az Azure piactér vagy gyorsindítási sablonok telepítésekor.  Ha a megoldás telepítve van egy másik módszerrel a felhasználó értékeket kell adnia a számukra.

> [!NOTE]
> A felhasználói felület az Azure piactér és gyorsindítási sablonok által várt paraméterekkel a paraméterek nevei a táblában.  Ha különböző paraméternevek használja majd a felhasználót a rendszer kéri a számukra, és azok nem automatikusan tölti fel.
>
>

| Paraméter | Típus | Leírás |
|:--- |:--- |:--- |
| Fióknév |karakterlánc |Azure Automation-fiók nevét. |
| pricingTier |karakterlánc |A Naplóelemzési munkaterület- és Azure Automation-fiók tarifacsomagot. |
| regionId |karakterlánc |Az Azure Automation-fiók területet. |
| Megoldás neve |karakterlánc |A megoldás neve.  Ha a megoldás gyorsindítási sablonok keresztül telepíti, majd meg kell határozni megoldás neve paraméterként úgy határozhatja meg kell adnia egy felhasználói helyette igénylő karakterlánc. |
| workspaceName |karakterlánc |Napló Analytics munkaterület neve. |
| workspaceRegionId |karakterlánc |A Naplóelemzési munkaterület területet. |


Az alábbiakban olvashatja a szabványos paraméterek, másolja és illessze be a megoldásfájlt szerkezete.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Tekintse meg a paraméterértékeket a megoldás a szintaxissal más elemei **paraméter ("név" paraméternek)**.  Például a munkaterület neve eléréséhez használja **parameters('workspaceName')**

## <a name="variables"></a>Változók
[Változók](../azure-resource-manager/resource-group-authoring-templates.md#variables) szüksége lesz a megoldásról a további értékek.  Ezek az értékek nem érhetők el a felhasználó telepíti a megoldást.  A szerző biztosítania meg egy helyet, ahol értékeket, amelyeket az lehet, hogy több alkalommal a megoldás teljes kezelésére szolgálnak. El kell helyezni minden olyan értéket adott változók figyelésekor rögzített kódolási azokat a megoldáshoz a **erőforrások** elemet.  Ez a kód olvashatóbbá teszi és könnyen módosíthatja ezeket az értékeket az újabb verziókban.

Az alábbiakban látható egy példa egy **változók** elem megoldások használt általános paraméterekkel.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Tekintse meg a változó a megoldással a szintaxissal **változók ("változó neve")**.  Például a megoldás neve változó eléréséhez használja **variables('SolutionName')**.

Azt is megadhatja, komplex változók értékeinek beállítja, hogy több.  Ezek akkor igazán hasznosak-kezelési megoldásokban ahol több különböző típusú erőforrások tulajdonság meghatározásakor.  Például sikerült átalakítása a megoldás változók a következőhöz fent látható.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Ebben az esetben hivatkozik, a megoldással a szintaxissal változók értékeinek **variables('variable name').property**.  Például a megoldás neve változó eléréséhez használja **variables('Solution'). Név**.

## <a name="resources"></a>További források
[Erőforrások](../azure-resource-manager/resource-group-authoring-templates.md#resources) határozza meg a különböző erőforrások, amelyek a felügyeleti megoldás telepíteni és konfigurálni fog.  Ez a sablon, a legnagyobb, és a legösszetettebb része lesz.  A struktúra és a teljes leírását az erőforrás-elemek [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Különböző erőforrások, amelyek általában meghatározzák részletes leírást talál további cikkeit a jelen dokumentációban. 


### <a name="dependencies"></a>Függőségek
A **dependsOn** elem azt adja meg a [függőségi](../azure-resource-manager/resource-group-define-dependencies.md) egy másik erőforrás.  A megoldás telepítésekor egy erőforrás nem jön létre, amíg az összes függősége létrejött.  A megoldás lehet például [runbookot](operations-management-suite-solutions-resources-automation.md#runbooks) használatával telepített egy [erőforrás feladat](operations-management-suite-solutions-resources-automation.md#automation-jobs).  A feladat erőforrás lenne erőforrástól függ a runbook győződjön meg arról, hogy a runbook létrehozása, a feladat létrehozása előtt.

### <a name="oms-workspace-and-automation-account"></a>OMS-munkaterület és Automation-fiók
Megoldások szükséges egy [OMS-munkaterület](../log-analytics/log-analytics-manage-access.md) nézeteket tartalmaz, és egy [Automation-fiók](../automation/automation-security-overview.md#automation-account-overview) magában foglalja a runbookok és kapcsolódó erőforrások.  Ezek előtt elérhetőnek kell lennie a megoldás az erőforrások jönnek létre, és nem lehet megadni, a megoldás magát.  A felhasználó fog [adjon meg egy munkaterület és a fiók](operations-management-suite-solutions.md#oms-workspace-and-automation-account) amikor azok a megoldás üzembe helyezéséhez, de a szerző vegye figyelembe a következő szempontokat.

## <a name="solution-resource"></a>Megoldás erőforrás
Minden egyes megoldáshoz szükségesek egy erőforrás bejegyzés a **erőforrások** elem, amely meghatározza a megoldás magát.  Ez lesz az olyan típusú **Microsoft.OperationsManagement/solutions** és az alábbi szerkezettel rendelkezik. Ez magában foglalja [szabványos paraméterek](#parameters) és [változók](#variables) , amely általában meghatározásához használják a megoldás tulajdonságait.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Függőségek
A megoldás erőforrás rendelkeznie kell egy [függőségi](../azure-resource-manager/resource-group-define-dependencies.md) minden más erőforráshoz, a megoldás, mert azokat léteznie kell a megoldás hozhatók létre.  Az egyes erőforrásokra vonatkozó bejegyzés hozzáadásával ehhez a **dependsOn** elemet.

### <a name="properties"></a>Tulajdonságok
A megoldás erőforrás tulajdonságokkal rendelkezik, az alábbi táblázatban.  Ez magában foglalja az erőforrások hivatkozik, és szerepelnie kell a megoldás, amely meghatározza, hogyan kezeli az erőforrás a megoldás telepítése után.  A megoldás az egyes erőforrások kell szerepelnie, akár a **referencedResources** vagy a **containedResources** tulajdonság.

| Tulajdonság | Leírás |
|:--- |:--- |
| workspaceResourceId |A Naplóelemzési munkaterület formájában azonosító  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Munkaterületnevet\>*. |
| referencedResources |Az erőforrások listájához a a megoldás, nem lehet eltávolítani, a megoldás eltávolításakor. |
| containedResources |Az erőforrások listájához a a megoldás, amely el kell távolítani a megoldás eltávolításakor. |

A fenti példa egy megoldást egy runbookot, egy ütemezést, és tekintse meg a rendszer.  Az ütemezés és a runbook *hivatkozott* a a **tulajdonságok** elem, ezért nem eltávolítva a megoldás eltávolításakor.  A nézet *tartalmazott* , a rendszer eltávolítja a megoldás eltávolításakor.

### <a name="plan"></a>Felkészülés
A **terv** entitás a megoldás erőforrás tulajdonságokkal rendelkezik, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| név |A megoldás neve. |
| verzió: |A megoldás a szerző által meghatározott verziója. |
| termék |A megoldás azonosításához egyedi karakterlánc. |
| Közzétevő |A megoldás közzétevője. |



## <a name="sample"></a>Sample
A következő helyeken megoldás erőforrással megoldásfájlok mintáit tekintheti meg.

- [Automation-erőforrások](operations-management-suite-solutions-resources-automation.md#sample)
- [Keresés és a riasztás erőforrások](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>További lépések
* [Adja hozzá a mentett keresések és riasztások](operations-management-suite-solutions-resources-searches-alerts.md) a kezelési megoldással.
* [Nézetek hozzáadása](operations-management-suite-solutions-resources-views.md) a kezelési megoldással.
* [Adja hozzá a runbookok és egyéb automatizálási erőforrások](operations-management-suite-solutions-resources-automation.md) a kezelési megoldással.
* További részleteit [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).
* Keresési [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates) példákért különböző Resource Manager-sablonok.
