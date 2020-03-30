---
title: Felügyeleti megoldásfájl létrehozása az Azure-ban | Microsoft dokumentumok
description: A felügyeleti megoldások olyan csomagolt felügyeleti forgatókönyveket kínálnak, amelyeket az ügyfelek hozzáadhatnak az Azure-környezetükhöz.  Ez a cikk részletesen ismerteti, hogyan hozhat létre felügyeleti megoldásokat a saját környezetében, vagy elérhetővé kell tenni az ügyfelek számára.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275177"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Felügyeleti megoldásfájl létrehozása az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció felügyeleti megoldások létrehozásához az Azure-ban, amelyek jelenleg előzetes verzióban. Az alábbiakban ismertetett sémák változhatnak.  

Az Azure felügyeleti [megoldásai Erőforrás-kezelő sablonként vannak megvalósítva.](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)  A fő feladata a tanulás, hogyan kell a szerző kezelési megoldások tanulás, hogyan kell [a sablon tanait](../../azure-resource-manager/templates/template-syntax.md).  Ez a cikk a megoldásokhoz használt sablonok és a tipikus megoldási erőforrások konfigurálásának egyedi részleteit tartalmazza.


## <a name="tools"></a>Eszközök

Bármilyen szövegszerkesztővel dolgozhat a megoldásfájlokkal, de javasoljuk, hogy használja a Visual Studio vagy a Visual Studio Code szolgáltatásait az alábbi cikkekben leírtak szerint.

- [Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Az Azure Resource Manager-sablonok használata a Visual Studio Code kódszerkesztőben](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktúra
A felügyeleti megoldásfájl alapstruktúrája megegyezik az [Erőforrás-kezelő sablonjával,](../../azure-resource-manager/templates/template-syntax.md#template-format)amely a következő.  Az alábbi szakaszok mindegyike a megoldás legfelső szintű elemeit és azok tartalmát ismerteti.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paraméterek
[A paraméterek](../../azure-resource-manager/templates/template-syntax.md#parameters) olyan értékek, amelyeket a felhasználónak meg kell követelnie a felügyeleti megoldás telepítésekor.  Vannak szabványos paraméterek, amelyminden megoldás lesz, és hozzáadhat további paramétereket, ha szükséges az adott megoldáshoz.  Az, hogy a felhasználók hogyan adják meg a paraméterértékeket a megoldás telepítésekor, az adott paramétertől és a megoldás telepítésének módjától függ.

Amikor egy felhasználó [telepíti a felügyeleti megoldást](solutions.md#install-a-monitoring-solution) az Azure Marketplace-en vagy az Azure QuickStart-sablonokon keresztül, a rendszer kéri, hogy válasszanak ki egy [Log Analytics-munkaterületet és automation-fiókot.](solutions.md#log-analytics-workspace-and-automation-account)  Ezek az egyes szabványos paraméterek értékeinek feltöltésére szolgálnak.  A rendszer nem kéri a felhasználót, hogy közvetlenül adja meg a szabványos paraméterek értékeit, de a rendszer kéri, hogy adjon meg értékeket a további paraméterekhez.


Az alábbiakban egy mintaparaméter látható.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Az alábbi táblázat egy paraméter attribútumait ismerteti.

| Attribútum | Leírás |
|:--- |:--- |
| type |A paraméter adattípusa. A felhasználó számára megjelenített beviteli vezérlő az adattípustól függ.<br><br>bool - Legördülő doboz<br>karakterlánc - Szövegdoboz<br>int - Szövegdoboz<br>securestring - Jelszó mező<br> |
| category |Nem kötelező kategória a paraméterhez.  Az ugyanabban a kategóriában lévő paraméterek csoportosítva vannak. |
| control |A karakterlánc-paraméterek további funkciói.<br><br>datetime - megjelenik a Datetime vezérlő.<br>guid - Guid érték automatikusan jön létre, és a paraméter nem jelenik meg. |
| leírás |A paraméter nem kötelező leírása.  Információs buborékban jelenik meg a paraméter mellett. |

### <a name="standard-parameters"></a>Szabványos paraméterek
Az alábbi táblázat az összes felügyeleti megoldás szabványos paramétereit sorolja fel.  Ezek az értékek a felhasználó számára vannak feltöltve, ahelyett, hogy rákérdezne, ha a megoldás telepítve van az Azure Marketplace-ről vagy a gyorsútmutató sablonokból.  A felhasználónak meg kell adnia az értékeket, ha a megoldás egy másik módszerrel van telepítve.

> [!NOTE]
> Az Azure Piactér és a gyorsútmutató-sablonok felhasználói felülete a tábla paraméterneveit várja.  Ha különböző paraméterneveket használ, akkor a rendszer kéri a felhasználótól, és nem tölti ki őket automatikusan.
>
>

| Paraméter | Típus | Leírás |
|:--- |:--- |:--- |
| accountName |sztring |Az Azure Automation-fiók neve. |
| pricingTier között |sztring |A Log Analytics munkaterület és az Azure Automation-fiók díjszabási szintje. |
| regionId |sztring |Az Azure Automation-fiók régiója. |
| solutionName |sztring |A megoldás neve.  Ha a megoldást rövid útmutató sablonokon keresztül telepíti, akkor a solutionName paramétert kell definiálnia, hogy definiáljon egy karakterláncot, amely hez a felhasználónak meg kell adnia egyet. |
| munkaterületNeve |sztring |A Log Analytics munkaterületének neve. |
| munkaterületRegionId |sztring |A Log Analytics munkaterület területe. |


A következőkben a szabványos paraméterek szerkezete, amelyet a megoldásfájlba másolhat és beilleszthet.  

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


A megoldás más elemeinek paraméterértékeire hivatkozik a szintaxis **paraméterekkel(''parameter name')**.  A munkaterület nevének eléréséhez például **paramétereket('workspaceName') kell használnia.**

## <a name="variables"></a>Változók
[A változók](../../azure-resource-manager/templates/template-syntax.md#variables) olyan értékek, amelyeket a felügyeleti megoldás többi részében használni fog.  Ezek az értékek nincsenek kitéve a megoldást telepítő felhasználónak.  Céljuk, hogy a szerző egy olyan helyet biztosítson a szerzőnek, ahol kezelheti azértékeket, amelyek többször is használhatók a megoldás során. A megoldásra jellemző értékeket változókban kell helyeznie, nem pedig az **erőforrások** elemben lévő kemény kódolással.  Ez olvashatóbbá teszi a kódot, és lehetővé teszi, hogy ezeket az értékeket a későbbi verziókban könnyen módosítsa.

A következő egy példa egy **változó** elem tipikus paramétereket használnak a megoldásokban.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

A megoldáson keresztül a szintaktikai **változókkal("változónév")** rendelkező változóértékekre hivatkozik.  A SolutionName változó eléréséhez például **a ('SolutionName') változókat**kell használnia.

Összetett változókat is definiálhat, amelyek több értékhalmazt tartalmaznak.  Ezek különösen hasznosak olyan felügyeleti megoldásokban, ahol több tulajdonságot határoz meg különböző típusú erőforrásokhoz.  Átstrukturálhatja például a fent látható megoldásváltozókat a következőkre.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Ebben az esetben a megoldáson keresztül a megoldáson keresztül a szintaktikai **változókkal('változó név').property.**  Például a Megoldás név változó eléréséhez **a ('Solution') változókat kell használnia. Név**.

## <a name="resources"></a>Források
[Az erőforrások](../../azure-resource-manager/templates/template-syntax.md#resources) határozzák meg a felügyeleti megoldás által telepíteni és konfigurálni kívánt különböző erőforrásokat.  Ez lesz a sablon legnagyobb és legösszetettebb része.  Az erőforráselemek szerkezetét és teljes leírását az [Azure Resource Manager-sablonok készítése című dokumentumban szerezheti](../../azure-resource-manager/templates/template-syntax.md#resources)be.  A jellemzően definiált különböző erőforrásokat a dokumentáció más cikkeiben is részletezi. 


### <a name="dependencies"></a>Függőségek
A **dependsOn** elem egy másik [erőforrástól való függőséget](../../azure-resource-manager/templates/define-resource-dependency.md) ad meg.  A megoldás telepítéseután az erőforrás csak akkor jön létre, ha az összes függősége létrejött.  Előfordulhat például, hogy a megoldás [runbookot indít](solutions-resources-automation.md#runbooks) el, ha egy [feladat-erőforrás](solutions-resources-automation.md#automation-jobs)használatával telepíti.  A feladat erőforrás függne a runbook erőforrás győződjön meg arról, hogy a runbook jön létre, mielőtt a feladat létrehozása.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterületi és automatizálási fiók
A felügyeleti megoldásokhoz [egy Log Analytics-munkaterületszükséges](../../azure-monitor/platform/manage-access.md) a nézetek, valamint egy [Automation-fiók](../../automation/automation-security-overview.md#automation-account-overview) a runbookok és a kapcsolódó erőforrások tárolására.  Ezeknek elérhetőnek kell lenniük a megoldás ban lévő erőforrások létrehozása előtt, és nem szabad meghatározni magát a megoldásban.  A felhasználó a megoldás üzembe helyezésekor [megad egy munkaterületet és egy fiókot,](solutions.md#log-analytics-workspace-and-automation-account) de szerzőként érdemes figyelembe venni a következő pontokat.


## <a name="solution-resource"></a>Megoldás erőforrás
Minden megoldáshoz szükség van **resources** egy erőforrás-bejegyzésre az erőforrás-elemben, amely magát a megoldást határozza meg.  Ez a **Microsoft.OperationsManagement/solutions** típusú lesz, és a következő struktúrával rendelkezik. Ez magában foglalja [a szabványos paramétereket](#parameters) és [változókat,](#variables) amelyek általában a megoldás tulajdonságainak meghatározására szolgálnak.


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
A megoldás erőforrás nak [függőséget](../../azure-resource-manager/templates/define-resource-dependency.md) kell a megoldás minden más erőforrását, mivel meg kell léteznie, mielőtt a megoldás hozható létre.  Ehhez adjon hozzá egy bejegyzést a **dependsOn** elem minden egyes erőforrásához.

### <a name="properties"></a>Tulajdonságok
A megoldás erőforrás a következő táblázatban található tulajdonságokkal rendelkezik.  Ez magában foglalja a megoldás által hivatkozott és tartalmazott erőforrásokat, amelyek meghatározzák, hogyan kezeli az erőforrást a megoldás telepítése után.  A megoldás minden erőforrásának szerepelnie kell a **hivatkozotterőforrások** vagy a **containedResources** tulajdonságban.

| Tulajdonság | Leírás |
|:--- |:--- |
| workspaceResourceId |A Log Analytics munkaterület azonosítója az * \<Erőforráscsoport-azonosító\<>/providers/Microsoft.OperationalInsights/workspaces/\>Workspace Name*formában. |
| hivatkozotterőforrások |A megoldásban lévő azon erőforrások listája, amelyeket a megoldás eltávolításakor nem szabad eltávolítani. |
| containedForrások |A megoldásban lévő erőforrások listája, amelyet el kell távolítani a megoldás eltávolításakor. |

A fenti példa egy runbookot, ütemezést és megtekintést.  Az ütemezés és a runbook *hivatkozik* a **tulajdonságok** elem, így azok nem törlődnek, ha a megoldás eltávolítása.  A nézet *et tartalmazza,* így a megoldás eltávolításakor törlődik.

### <a name="plan"></a>Felkészülés
A megoldási erőforrás **terventitása** az alábbi táblázatban található tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| név |A megoldás neve. |
| version |A megoldás nak a szerző által meghatározott verziója. |
| product |Egyedi karakterlánc a megoldás azonosításához. |
| közzétevő |A megoldás közzétevője. |



## <a name="next-steps"></a>További lépések
* [Mentett keresések és riasztások hozzáadása](solutions-resources-searches-alerts.md) a felügyeleti megoldáshoz.
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Runbookok és egyéb Automation-erőforrások hozzáadása](solutions-resources-automation.md) a felügyeleti megoldáshoz.
* Ismerje meg az [Azure Resource Manager-sablonok szerzői részleteit.](../../azure-resource-manager/templates/template-syntax.md)
* Az [Azure gyorsindítási sablonjaiban](https://azure.microsoft.com/documentation/templates) különböző Erőforrás-kezelősablonok mintáit keresheti.
