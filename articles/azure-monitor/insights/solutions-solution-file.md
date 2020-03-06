---
title: Felügyeleti megoldás fájljának létrehozása az Azure-ban | Microsoft Docs
description: A felügyeleti megoldások olyan csomagolt felügyeleti forgatókönyveket biztosítanak, amelyeket az ügyfelek hozzáadhatnak az Azure-környezethez.  Ez a cikk részletesen ismerteti, hogyan hozhat létre a saját környezetében használt felügyeleti megoldásokat, illetve hogyan teheti elérhetővé az ügyfelek számára.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395624"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Felügyeleti megoldás fájljának létrehozása az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció az Azure-ban jelenleg előzetes verzióban elérhető felügyeleti megoldások létrehozásához. Az alább ismertetett sémák változhatnak.  

Az Azure-beli felügyeleti megoldások [Resource Manager-sablonokként](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)valósulnak meg.  A felügyeleti megoldások létrehozásával kapcsolatos legfontosabb feladat a [sablon](../../azure-resource-manager/templates/template-syntax.md)készítésének megtanulása.  Ez a cikk a megoldásokhoz használt sablonok egyedi részleteit és a tipikus megoldási erőforrások konfigurálásának módját ismerteti.


## <a name="tools"></a>Eszközök

Bármely szövegszerkesztővel dolgozhat a megoldás fájljaival, de javasoljuk, hogy a Visual Studióban vagy a Visual Studio Code-ban elérhető funkciókat a következő cikkekben leírtak szerint használja fel.

- [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióval](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Azure Resource Manager-sablonok használata a Visual Studio Code-ban](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktúra
A felügyeleti megoldás fájljának alapstruktúrája megegyezik egy [Resource Manager-sablonnal](../../azure-resource-manager/templates/template-syntax.md#template-format), amely a következő.  Az alábbi részek a megoldás legfelső szintű elemeit és azok tartalmát ismertetik.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paraméterek
A [Paraméterek](../../azure-resource-manager/templates/template-syntax.md#parameters) olyan értékek, amelyekre szükség van a felhasználótól a felügyeleti megoldás telepítésekor.  Az összes megoldáshoz szabványos paraméterek tartoznak, és az adott megoldáshoz szükséges további paramétereket adhat hozzá.  A felhasználók által a megoldás telepítésekor megadott paraméterek az adott paramétertől és a megoldás telepítésének módjától függenek.

Amikor egy felhasználó az Azure Marketplace-en vagy az Azure rövid útmutató sablonjain keresztül [telepíti a felügyeleti megoldást](solutions.md#install-a-monitoring-solution) , a rendszer a [log Analytics munkaterület és az Automation-fiók](solutions.md#log-analytics-workspace-and-automation-account)kiválasztását kéri.  Ezek a szabványos paraméterek értékeinek feltöltésére szolgálnak.  A felhasználó nem kéri közvetlenül a szabványos paraméterek értékének megadását, de a rendszer kéri, hogy adjon meg értékeket a további paraméterek számára.


Alább látható egy minta paraméter.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A következő táblázat a paraméter attribútumait ismerteti.

| Attribútum | Leírás |
|:--- |:--- |
| típus |A paraméter adattípusa. A felhasználó számára megjelenített beviteli vezérlő az adattípustól függ.<br><br>bool – legördülő lista<br>karakterlánc – szövegmező<br>int-Text Box<br>SecureString – jelszó mező<br> |
| kategória |A paraméter nem kötelező kategóriája.  Az azonos kategóriába tartozó paraméterek együtt vannak csoportosítva. |
| control |További funkciók a karakterlánc-paraméterekhez.<br><br>datetime – datetime típusú vezérlő jelenik meg.<br>GUID – a GUID azonosító automatikusan létrejön, és a paraméter nem jelenik meg. |
| Leírás |A paraméter leírását nem kötelező megadni.  A paraméter melletti információs buborékban jelenik meg. |

### <a name="standard-parameters"></a>Szabványos paraméterek
Az alábbi táblázat az összes felügyeleti megoldás szabványos paramétereit sorolja fel.  Ezeket az értékeket a rendszer a felhasználó számára tölti fel ahelyett, hogy az Azure Marketplace-ről vagy a gyors üzembe helyezési sablonokból telepíti a megoldást.  Ha a megoldás másik módszerrel van telepítve, a felhasználónak meg kell adnia az értékeket.

> [!NOTE]
> Az Azure Marketplace-en és a gyors üzembe helyezési sablonokban lévő felhasználói felület a táblázatban szereplő paraméterek nevét várja.  Ha eltérő paramétereket használ, a rendszer kérni fogja a felhasználótól, és ezek nem lesznek automatikusan kitöltve.
>
>

| Paraméter | Típus | Leírás |
|:--- |:--- |:--- |
| accountName |Karakterlánc |Azure Automation fiók neve. |
| pricingTier |Karakterlánc |A Log Analytics munkaterület és Azure Automation fiók díjszabási szintje. |
| regionId |Karakterlánc |A Azure Automation-fiók régiója. |
| solutionName |Karakterlánc |A megoldás neve.  Ha a gyors üzembe helyezési sablonok segítségével telepíti a megoldását, akkor a solutionName paramétert kell meghatároznia, hogy egy karakterláncot határozzon meg, így a felhasználónak meg kell adnia egyet. |
| workspaceName |Karakterlánc |Log Analytics munkaterület neve. |
| workspaceRegionId |Karakterlánc |A Log Analytics munkaterület régiója. |


Az alábbiakban a megoldás fájljába másolható és beilleszthető szabványos paraméterek szerkezete látható.  

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


A megoldás más elemeiben a paraméterek értékeit a szintaxis **paraméterei ("paraméter neve")** alapján tekintheti meg.  Ha például a munkaterület nevét szeretné elérni, használja a **paramétereket ("workspaceName").**

## <a name="variables"></a>Változók
A [változók](../../azure-resource-manager/templates/template-syntax.md#variables) olyan értékek, amelyeket a felügyeleti megoldás további részében fog használni.  Ezek az értékek nem lesznek elérhetők a megoldást telepítő felhasználó számára.  Céljuk, hogy a szerző számára egyetlen helyet biztosítsanak, ahol olyan értékeket kezelhetnek, amelyek több alkalommal is felhasználhatók a megoldás során. A megoldáshoz tartozó összes értéket olyan változókban kell elhelyezni, amelyek nem az **erőforrások** elemben rögzített kódolással rendelkeznek.  Így a kód olvashatóbbá válik, és lehetővé teszi, hogy a későbbi verziókban könnyedén módosítsa ezeket az értékeket.

Az alábbi példa egy, a megoldásokban használt jellemző paraméterekkel rendelkező **változók** elemre mutat példát.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

A megoldás változó értékeit a szintaxis **változói ("változó neve")** alapján tekintheti meg.  Például a SolutionName változó eléréséhez használjon **változókat ("SolutionName")** .

Meghatározhat olyan összetett változókat is, amelyek több halmazt tartalmaznak.  Ezek különösen olyan felügyeleti megoldásokban hasznosak, ahol több tulajdonságot határoz meg különböző típusú erőforrásokhoz.  Például átalakíthatja a fentiekben bemutatott megoldás-változókat a következőre.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Ebben az esetben a megoldáson keresztül változó értékeket tekint a szintaxis **változói ("változó neve"). tulajdonsággal**.  Ha például a megoldás neve változót szeretné elérni, használja a **változókat ("megoldás"). Név**.

## <a name="resources"></a>További források
Az [erőforrások](../../azure-resource-manager/templates/template-syntax.md#resources) határozzák meg azokat a különböző erőforrásokat, amelyeket a felügyeleti megoldás telepíteni és konfigurálni fog.  Ez lesz a sablon legnagyobb és legbonyolultabb része.  Megtekintheti az erőforrás-elemek szerkezetét és leírását [Azure Resource Manager sablonok létrehozásához](../../azure-resource-manager/templates/template-syntax.md#resources).  A különböző erőforrások, amelyeket általában meghatároz, az ebben a dokumentációban található egyéb cikkekben talál részletes leírást. 


### <a name="dependencies"></a>Függőségek
A **dependsOn** elem egy másik erőforrás [függőségét](../../azure-resource-manager/templates/define-resource-dependency.md) határozza meg.  Ha a megoldás telepítve van, a rendszer nem hozza létre az erőforrást, amíg az összes függőségét nem hozták létre.  Előfordulhat például, hogy a megoldás [elindít egy runbook](solutions-resources-automation.md#runbooks) , ha a [feladatot erőforrással](solutions-resources-automation.md#automation-jobs)telepíti.  A runbook erőforrástól függ, hogy a runbook létrejött-e a feladatokhoz.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-munkaterületet és Automation-fiók
A felügyeleti megoldásokhoz [log Analytics munkaterületre](../../azure-monitor/platform/manage-access.md) van szükség, hogy a runbookok és a kapcsolódó erőforrásokat tartalmazó nézeteket és [Automation-fiókot](../../automation/automation-security-overview.md#automation-account-overview) tartalmazzon.  Ezeknek elérhetőnek kell lenniük a megoldás erőforrásainak létrehozása előtt, és a megoldásban nem kell őket meghatározni.  A felhasználónak [meg kell adnia egy munkaterületet és fiókot](solutions.md#log-analytics-workspace-and-automation-account) a megoldás telepítésekor, de a szerzőnek a következő szempontokat kell figyelembe vennie.


## <a name="solution-resource"></a>Megoldás erőforrása
Minden megoldáshoz szükség van egy erőforrás-bejegyzésre az **erőforrások** elemben, amely maga a megoldást határozza meg.  Ez a **Microsoft. OperationsManagement/Solutions** típust fogja tartalmazni, és a következő szerkezettel rendelkezik. Ez olyan [szabványos paramétereket](#parameters) és [változókat](#variables) tartalmaz, amelyek jellemzően a megoldás tulajdonságainak meghatározására szolgálnak.


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
A megoldás erőforrásának a megoldás létrehozása [előtt meg kell egyeznie a megoldás](../../azure-resource-manager/templates/define-resource-dependency.md) minden más erőforrásával.  Ezt úgy teheti meg, hogy hozzáad egy bejegyzést az egyes erőforrásokhoz a **dependsOn** elemben.

### <a name="properties"></a>Tulajdonságok
A megoldás erőforrásának tulajdonságai a következő táblázatban láthatók.  Ez magában foglalja a megoldás által hivatkozott és a megoldásban foglalt erőforrásokat, amely meghatározza, hogy az erőforrás hogyan legyen felügyelve a megoldás telepítése után.  A megoldásban szereplő összes erőforrásnak szerepelnie kell a **referencedResources** vagy a **containedResources** tulajdonságban.

| Tulajdonság | Leírás |
|:--- |:--- |
| workspaceResourceId |A Log Analytics munkaterület azonosítója az űrlapon *\<ERŐFORRÁSCSOPORT azonosítója >/providers/Microsoft.OperationalInsights/workspaces/\<munkaterület neve\>* . |
| referencedResources |Azon erőforrások listája, amelyeket nem szabad eltávolítani a megoldás eltávolításakor. |
| containedResources |Azon erőforrások listája, amelyeket el kell távolítani a megoldás eltávolításakor. |

A fenti példa egy runbook, egy ütemtervet és egy nézetet tartalmazó megoldás.  Az ütemterv és a runbook a **Tulajdonságok** elemre *hivatkoznak* , így azok nem törlődnek a megoldás eltávolításakor.  A rendszer a nézetet is *tárolja* , ezért a rendszer eltávolítja a megoldás eltávolításakor.

### <a name="plan"></a>Csomag
A megoldás erőforrásának **csomag** entitása a következő táblázatban található tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| név |A megoldás neve. |
| verzió |A megoldásnak a szerző által meghatározott verziója. |
| termék |Egyedi karakterlánc a megoldás azonosításához. |
| publisher |A megoldás közzétevője. |



## <a name="next-steps"></a>További lépések
* [Mentett keresések és riasztások hozzáadása](solutions-resources-searches-alerts.md) a felügyeleti megoldáshoz.
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Vegyen fel runbookok és egyéb Automation-erőforrásokat](solutions-resources-automation.md) a felügyeleti megoldásba.
* A [Azure Resource Manager-sablonok létrehozási](../../azure-resource-manager/templates/template-syntax.md)részleteinek megismerése.
* Más Resource Manager-sablonokból származó mintákhoz is kereshet Azure-beli [Gyorsindítás sablonokat](https://azure.microsoft.com/documentation/templates) .
