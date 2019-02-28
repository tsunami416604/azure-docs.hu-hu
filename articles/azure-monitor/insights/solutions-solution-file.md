---
title: Hozzon létre egy felügyeleti megoldás fájlt az Azure-ban |} A Microsoft Docs
description: Felügyeleti megoldások adja meg, hogy az ügyfelek adhat hozzá az Azure-környezet csomagolt felügyeleti forgatókönyvek.  Ez a cikk részletesen hogyan használható a saját környezetben felügyeleti megoldásokat hozhat létre, vagy szeretné elérhetővé tenni az ügyfelek számára.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0e2f6a8bd8bc8e6f0dd8ba1ec642bff50c9a7977
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984033"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Hozzon létre egy felügyeleti megoldás fájlt az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez a felügyeleti megoldások létrehozásához az Azure-ban, jelenleg előzetes verzióban érhető el előzetes dokumentációjában talál. Semmilyen sémát, az alábbiakban a változhat.  

Felügyeleti megoldások az Azure-ban megvalósított [Resource Manager-sablonok](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  Megtudhatja, hogyan hozhat létre megoldásokat a fő feladat a tanulási útmutató [sablon készítése](../../azure-resource-manager/resource-group-authoring-templates.md).  Ebben a cikkben használt megoldások és a tipikus megoldásokkal kapcsolatos forrásanyagok konfigurálása sablonok egyedi részleteit.


## <a name="tools"></a>Eszközök

Bármilyen szövegszerkesztővel segítségével dolgozhat a megoldásfájlok, de javasoljuk, hogy az alábbi cikkekben leírtak szerint a Visual Studióban vagy a Visual Studio Code megadott kihasználva.

- [Létrehozása és telepítése az Azure erőforráscsoport-sablonok a Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [A Visual Studio Code-ban az Azure Resource Manager-sablonok használata](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>struktúra
Az alapszintű struktúrát egy felügyeleti megoldás fájl pedig ugyanaz, mint egy [Resource Manager-sablon](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), amely a következőképpen történik.  A legfelső elemeket és azok tartalmát, a megoldás az alábbi szakaszokban mindegyike ismerteti.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paraméterek
[Paraméterek](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) értékek, amelyekre szüksége van a felhasználótól való telepítésekor a felügyeleti megoldás.  Standard paraméterei, amely az összes megoldáshoz fog rendelkezni, és az adott megoldáshoz szükség szerint további paramétereket is hozzáadhat.  Hogyan felhasználók fog adja meg a paraméterértékeket a megoldás telepítéskor függ az adott paraméter, és hogyan a megoldás telepítése folyamatban van.

Ha egy felhasználó [telepíti a felügyeleti megoldás](solutions.md#install-a-monitoring-solution) keresztül az Azure Marketplace-en vagy az Azure gyorsindítási sablonok kiválasztása bekapcsolják a [Log Analytics-munkaterületet és Automation-fiók](solutions.md#log-analytics-workspace-and-automation-account).  Ezek használhatók a standard szintű paraméterek értékeinek feltöltéséhez.  Közvetlenül a standard szintű paraméterek értékének megadására nem kéri a felhasználótól, de azokat a rendszer kéri a további paraméterek értékének megadására.


Az alábbiakban látható egy példa a paraméter.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Az alábbi táblázatban a paraméter attribútumait ismerteti.

| Attribútum | Leírás |
|:--- |:--- |
| type |A paraméter adattípusa. A bemeneti vezérlőelem jelenik meg a felhasználó az adatok típusától függ.<br><br>logikai – a legördülő listából<br>karakterlánc - szövegmező<br>int - szövegmező<br>SecureString - jelszó mező<br> |
| category |A kategória nem kötelező paraméter.  Paraméterek ugyanahhoz a kategóriához vannak csoportosítva. |
| Vezérlő |További funkciókat lekérdezésikarakterlánc-paramétereket.<br><br>dátum és idő – dátum és idő vezérlőelem jelenik meg.<br>GUID - érték a GUID azonosító automatikusan jön létre, és a paraméter nem jelenik meg. |
| leírás |A paraméter szükséges, leírását.  Megjelenik egy információk buborékra a paraméter mellett. |

### <a name="standard-parameters"></a>Szabványos paraméterek
Az alábbi táblázat felsorolja az összes felügyeleti megoldások szabványos paramétereit.  Ezeket az értékeket a felhasználó helyett adatkérés el a megoldás az Azure Marketplace vagy gyorsindítási sablonok telepítésekor a rendszer kitölti.  Ha a megoldás telepítve van egy másik módszerrel a felhasználó értékeket kell megadnia a számukra.

> [!NOTE]
> A felhasználói felület az Azure Marketplace-en és a gyorsindítási sablonok, a várt paraméter nevét a táblázatban.  Ha paraméterek különböző neveket használ majd a felhasználó felszólítást kap a számukra, és azok nem automatikusan kitöltődnek.
>
>

| Paraméter | Typo | Leírás |
|:--- |:--- |:--- |
| accountName |sztring |Az Azure Automation-fiók nevét. |
| pricingTier |sztring |Log Analytics-munkaterületet és az Azure Automation-fiók tarifacsomagját. |
| regionId |sztring |Az Azure Automation-fiók régiója. |
| megoldás neve |sztring |A megoldás nevére.  A megoldás gyorsindítási sablonok használatával helyez üzembe, majd meg kell határozni solutionName paraméterként így meghatározhatja, hogy inkább igénylő a felhasználó számára adjon meg egy karakterláncot. |
| workspaceName |sztring |Log Analytics-munkaterület neve. |
| workspaceRegionId |sztring |A Log Analytics-munkaterület régiója. |


Következő, másolja és illessze be a megoldásfájlt a szabványos paraméterek struktúráját.  

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


Tekintse meg a paraméterértékeket a más elemek, a megoldás a szintaxissal **paraméter ("a paraméter neve")**.  Ha például a munkaterület nevének elérését, használja **parameters('workspaceName')**

## <a name="variables"></a>Változók
[Változók](../../azure-resource-manager/resource-group-authoring-templates.md#variables) többi része a felügyeleti megoldást használni kívánt érték.  Ezek az értékek nem jelennek meg a megoldást telepítő felhasználónak a.  A szerző biztosít egy egyetlen helyen, ahol felügyelheti az értékeket, amelyeket lehet, hogy több alkalommal a megoldás teljes szolgálnak. El kell helyezni minden olyan értéket adott a változók helyett sorszámozás az azokat a megoldást a **erőforrások** elemet.  Ez olvashatóbbá teszi a kódot, és egyszerűen módosíthatja ezeket az értékeket későbbi verziókban.

Az alábbiakban egy példát a egy **változók** megoldásokban használt szokásos paraméterekkel rendelkező elemet.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Tekintse meg a változó értéke a szintaxissal a megoldáson keresztül **változók ("változó neve")**.  Ha például SolutionName változó eléréséhez használja **variables('SolutionName')**.

Azt is megadhatja, összetett változók értékeinek beállítja, hogy több.  Az alábbiak különösen hasznos lehet az eszközfelügyeleti megoldások, ahol több különböző típusú erőforrások tulajdonság határoz.  Például akkor lehetett átalakítása a megoldás változók, a következőhöz fent látható.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Ebben az esetben tekintse meg a változó értéke a szintaxissal a megoldáson keresztül **variables('variable name').property**.  Például a megoldás neve változó eléréséhez használja **variables('Solution'). Név**.

## <a name="resources"></a>További források
[Erőforrások](../../azure-resource-manager/resource-group-authoring-templates.md#resources) határozza meg a különböző erőforrások, amelyek a felügyeleti megoldás telepíteni és konfigurálni fog.  Ez a sablon a legnagyobb és legösszetettebb része lesz.  A struktúra és a teljes leírását az erőforrás-elemek is igénybe [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Különböző erőforrásokhoz, amelyek általában határoz más cikkek ebben a dokumentációban részletes leírást talál. 


### <a name="dependencies"></a>Függőségek
A **dependsOn** elem azt határozza meg a [függőségi](../../azure-resource-manager/resource-group-define-dependencies.md) egy másik erőforrás.  Telepítette a megoldást, ha egy erőforrás nem jön létre, mindaddig, amíg a létrehozott összes függőségét.  A megoldás Előfordulhat, hogy például [elindít egy runbookot](solutions-resources-automation.md#runbooks) amikor segítségével telepítve van egy [erőforrás feladat](solutions-resources-automation.md#automation-jobs).  A feladat erőforrás lenne, győződjön meg arról, hogy a runbook létrejött-e, mielőtt a feladat jön létre, a runbook erőforrástól függ-e.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-munkaterületet és Automation-fiók
Felügyeleti megoldások szükséges egy [Log Analytics-munkaterület](../../azure-monitor/platform/manage-access.md) nézeteket tartalmaznak, és a egy [Automation-fiók](../../automation/automation-security-overview.md#automation-account-overview) a runbookok és kapcsolódó erőforrásokat tartalmaznak.  Ezek előtt kell lenniük az erőforrásokat a megoldás jönnek létre, és nem lehet megadni, magát a megoldásban.  A felhasználó fog [adja meg a munkaterület és fiók](solutions.md#log-analytics-workspace-and-automation-account) amikor azokat üzembe helyezheti megoldását, de a szerző, vegye figyelembe a következőket.


## <a name="solution-resource"></a>Megoldás-erőforrás
Minden megoldáshoz egy erőforrás bejegyzést a **erőforrások** elem, amely meghatározza a megoldás magát.  Ez a típusú lesz **Microsoft.OperationsManagement/solutions** és az alábbi struktúrával rendelkeznek. Ez magában foglalja [szabványos paraméterek](#parameters) és [változók](#variables) , amelyek jellemzően a megoldás tulajdonságainak definiálásához.


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
A megoldás erőforrásnak rendelkeznie kell egy [függőségi](../../azure-resource-manager/resource-group-define-dependencies.md) minden egyéb erőforrás a megoldásban, mivel a megoldás létrehozása előtt léteznie kell.  Egy bejegyzés az egyes erőforrások hozzáadásával ehhez a **dependsOn** elemet.

### <a name="properties"></a>Tulajdonságok
A megoldás erőforrás tulajdonságokkal rendelkezik, az alábbi táblázatban.  Ez magában foglalja az erőforrások hivatkozik, és a megoldás, amely meghatározza, hogyan kezeli az erőforrás a megoldás telepítése után által tartalmazott.  A megoldás minden egyes erőforrás vagy a szerepelnie kell a **referencedResources** vagy a **containedResources** tulajdonság.

| Tulajdonság | Leírás |
|:--- |:--- |
| workspaceResourceId |A képernyőn a Log Analytics-munkaterület azonosítója  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Munkaterületnév\>*. |
| referencedResources |A megoldás, nem lehet eltávolítani, ha a megoldás a rendszer eltávolítja az erőforrások listájában. |
| containedResources |A megoldás, amely a megoldás eltávolításakor el kell távolítani az erőforrások listájában. |

A fenti példában van egy runbookot, egy ütemezés és a nézet a megoldás.  Az ütemezés és a runbook *hivatkozott* a a **tulajdonságok** elemet, így azok nem törlődnek a megoldás eltávolításakor.  A nézet *tartalmazott* , a rendszer eltávolítja a megoldás eltávolításakor.

### <a name="plan"></a>Felkészülés
A **terv** entitás a megoldás erőforrás tulajdonságokkal rendelkezik, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| név |A megoldás nevére. |
| version |A megoldás a szerző által meghatározott verziója. |
| termék |A megoldás azonosításához egyedi karakterlánc. |
| publisher |Adott megoldás kiadójával. |



## <a name="next-steps"></a>További lépések
* [Adja hozzá a mentett keresések és a riasztások](solutions-resources-searches-alerts.md) a felügyeleti megoldáshoz.
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Adja hozzá a runbookok és a más Automation-erőforrások](solutions-resources-automation.md) a felügyeleti megoldáshoz.
* Ismerje meg az adatait [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md).
* Keresés [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates) minták a különböző Resource Manager-sablonok.
