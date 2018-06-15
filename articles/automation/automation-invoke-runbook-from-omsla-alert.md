---
title: Azure Automation-runbook hívása Log Analytics-riasztásból
description: A cikkben megtudhatja, hogyan lehet meghívni egy Automation-runbook egy Azure Log Analytics-riasztás alapján.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a3b14bd8409226772d210f60dadd525960f7890
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192662"
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Azure Automation-runbook hívása Log Analytics-riasztásból

Konfigurálhat egy riasztást az Azure Log Analyticsben, hogy riasztásbejegyzést hozzon létre, amikor az eredmények megfelelnek a feltételeknek. Ez a riasztás azután automatikusan Azure Automation-runbookot futtathat a hiba automatikus kijavításának megkísérlése érdekében. 

Egy riasztás például hosszabb ideig megnövekedett processzorhasználatot jelezhet. Vagy jelezheti, ha egy üzleti alkalmazás működése szempontjából kritikus alkalmazásfolyamat meghiúsul. A runbook ekkor egy megfelelő eseményt írhat a Windows eseménynaplójába.  

A riasztás konfigurálásakor a runbookok meghívásának következő két alternatívája közül választhat:

* Egy webhook használata.
   * Ez az egyetlen elérhető, ha a Naplóelemzési munkaterület nem kapcsolódik az Automation-fiók.
   * Ha már rendelkezik a Naplóelemzési munkaterület kapcsolódó Automation-fiók, a beállítás akkor továbbra is elérhető.  

* A runbook közvetlen kiválasztása.
   * Ez a beállítás csak akkor, ha a munkaterületet csatolva van egy Automation-fiók érhető el.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Runbook meghívása webhook használatával

Egy webhook használatával egyetlen HTTP-kérés kiadásával indíthat adott runbookokat az Azure Automationben. Mielőtt beállíthatná, hogy a [Log Analytics-riasztás](../log-analytics/log-analytics-alerts.md#alert-rules) a runbookot egy webhook használatával hívja meg a riasztási művelet keretében, előbb [létre kell hoznia egy webhookot](automation-webhooks.md#creating-a-webhook) az ezzel a módszerrel meghívni kívánt runbookhoz. Jegyezze fel a webhook URL-címét, hogy a riasztási szabály konfigurálása során hivatkozhasson rá.   

## <a name="calling-a-runbook-directly"></a>Runbookok közvetlen meghívása

Telepítse, és az automatizálás és a Naplóelemzési munkaterület ajánlat vezérlő konfigurálása. A riasztás runbook-műveletek beállításának konfigurálásakor a **Runbook kiválasztása** legördülő menüben láthatja az összes runbookot, és kiválaszthatja közülük azt, amelyiket a riasztásra válaszként futtatni kíván. A kiválasztott runbook futtatható Azure-munkaterületen vagy egy hibrid runbook-feldolgozón. 

Ha a riasztást a runbook beállítás használatával hozta létre, a rendszer létre fog hozni egy webhookot a runbookhoz. A webhookot az Automation-fiókban a kiválasztott runbook webhook panelének megnyitásával tekintheti meg. 

A riasztás törlésével a webhook nem törlődik automatikusan. Ez nem probléma. A webhook csupán egy olyan árva elemmé válik, amelyet idővel manuálisan törölni kell, hogy az Automation-fiók rendezett maradjon.  

## <a name="characteristics-of-a-runbook"></a>A runbookok jellemzői

Érdemes megismernie a runbookok Log Analytics-riasztásokból való meghívására szolgáló két módszer jellemzőit, mielőtt konfigurálná a riasztásokat. 

A riasztási adatok JSON formátumban vannak, és egyetlen, **SearchResult** nevű tulajdonságban találhatók. Ez a formátum a szabvány hasznos adatokkal végzett runbook- és webhook-műveletekhez használható. Az egyéni hasznos adatokkal végzett webhook-műveletek esetében (például **IncludeSearchResults:True** művelet a **RequestBody** tulajdonságban) a tulajdonság értéke **SearchResults**.

Rendelkeznie kell egy **WebhookData** elnevezésű és **Objektum** típusú bemeneti runbookparaméterrel. Ez lehet kötelező vagy nem kötelező. A riasztás a keresési eredményeket a runbooknak ezen a bemeneti paraméteren keresztül adja át.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
A **WebhookData** PowerShell-objektummá való átalakításához kód is szükséges.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

A **$SearchResult** egy objektumtömb. Mindegyik objektum egy keresési eredmény értékeit tároló mezőket tartalmaz.


## <a name="example-walkthrough"></a>Forgatókönyv – példa

A grafikus runbookok következő példája bemutatja, hogyan működik ez a folyamat. Elindít egy Windows-szolgáltatást.

![Windows-szolgáltatást elindító grafikus runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

A runbook rendelkezik egy **Objektum** típusú, **WebhookData** nevű bemeneti paraméterrel. Ez tartalmazza a riasztás által továbbított, a **SearchResult** tömböt tartalmazó webhookadatokat.

![Runbook bemeneti paraméterei](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

Ebben a példában két egyéni mezőt hoztunk létre a Log Analyticsben: **SvcDisplayName_CF** és **SvcState_CF**. Ezek a mezők a szolgáltatás megjelenített nevét és állapotát (fut vagy leállítva) nyerik ki a rendszer-eseménynaplóba írt eseményből. Ezután létrehoztunk egy riasztási szabályt a következő keresési lekérdezéssel, hogy észlelni tudjuk, amikor a Nyomtatásisor-kezelő szolgáltatás leáll a Windows-rendszerben:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Ez lehet bármely számunkra fontos szolgáltatás. Ebben a példában egy olyan már meglévő szolgáltatást választottunk, amely része a Windows operációs rendszernek. A riasztási művelet a konfigurációja szerint végrehajtja a példában használt runbookot a hibrid runbook-feldolgozón futva, amely engedélyezve van a célrendszeren.   

A runbookkódban lévő **Szolgáltatásnév lekérése a Log Analyticsből** tevékenység a JSON-formátumú karakterláncot objektumtípussá alakítja, és a **SvcDisplayName_CF** elemre szűr. Kinyeri a Windows-szolgáltatás megjelenített nevét, majd továbbítja azt a következő tevékenységnek, amely ellenőrzi, hogy a szolgáltatás le van-e állítva, mielőtt megkísérelné újraindítani. Az **SvcDisplayName_CF** [egyéni mezőt](../log-analytics/log-analytics-custom-fields.md) a példa szemléltetéséhez hoztuk létre a Log Analyticsben.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Ha a szolgáltatás leáll, a Log Analyticsben lévő riasztási szabály egyezést észlel, aktiválja a runbookot, és továbbítja neki a riasztás környezetét. A runbook megkísérli ellenőrizni, hogy a szolgáltatás valóban leállt-e. Ha leállt, megkísérli újraindítani a szolgáltatást, ellenőrzi, hogy megfelelően elindult-e, és megjeleníti az eredményeket.     

Azt is megteheti a Naplóelemzési munkaterület csatolva az Automation-fiók nem rendelkezik, a riasztási szabály a webhook művelettel is konfigurálnia. A webhook aktiválja a runbookot. Továbbá konfigurálja a runbookot, hogy az konvertálja a JSON-formátumú karakterláncot, és a **SearchResult** tömbre szűrjön a fenti útmutatásoknak megfelelően.    

>[!NOTE]
> Ha a munkaterülete frissítve lett az [új Log Analytics lekérdezési nyelvre](../log-analytics/log-analytics-log-search-upgrade.md), akkor a webhook hasznos adatai módosultak. A formátum részletei: [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>További lépések

* A Log Analytics-riasztásokkal és létrehozásukkal kapcsolatos további információkért lásd: [Riasztások a Log Analyticsben](../log-analytics/log-analytics-alerts.md).

* A runbookok webhookkal való aktiválásával kapcsolatos további információkért lásd: [Azure Automation-webhookok](automation-webhooks.md).
