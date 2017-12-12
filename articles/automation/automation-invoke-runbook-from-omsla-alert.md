---
title: "Azure Automation-runbook hívása Log Analytics-riasztásból | Microsoft Docs"
description: "Ez a cikk az Automation-runbookok Microsoft OMS Log Analytics-riasztásokból való meghívásának áttekintését tartalmazza."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: 10b445f8fcaa80182119e47f37ffb11240a46869
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Azure Automation-runbook hívása OMS Log Analytics-riasztásból

Ha egy riasztás úgy van konfigurálva a Log Analyticsben, hogy riasztásbejegyzést hozzon létre, ha az eredmények megfelelnek egy adott feltételnek (például ha hosszabb ideig megnövekedett processzorhasználat tapasztalható, vagy egy adott üzleti alkalmazás működése szempontjából kritikus valamely alkalmazásfolyamat meghiúsul, és egy megfelelő eseményt ír a Windows eseménynaplójába), a riasztás képes automatikusan lefuttatni egy Automation-runbookot a hiba automatikus javításának megkísérlésére.  

A riasztás konfigurálásakor a runbookok meghívásának következő két alternatívája közül választhat:

1. Egy webhook használata.
   * Ha az OMS-munkaterület nincs Automation-fiókhoz társítva, csak ez a lehetőség áll rendelkezésre.
   * Azonban akkor is elérhető, ha már csatlakoztatta az OMS-munkaterületet egy Automation-fiókhoz.  

2. A runbook közvetlen kiválasztása.
   * Ez a lehetőség csak akkor áll rendelkezésre, ha az OMS-munkaterület csatlakoztatva van egy Automation-fiókhoz.

## <a name="calling-a-runbook-using-a-webhook"></a>Runbook meghívása webhook használatával

A webhookok használatával egyetlen HTTP-kérés kiadásával indíthat adott runbookokat az Azure Automationben. Mielőtt beállíthatná, hogy a [Log Analytics-riasztás](../log-analytics/log-analytics-alerts.md#alert-rules) a runbookot egy webhook használatával hívja meg a riasztási művelet keretében, előbb létre kell hoznia egy webhookot az ezzel a módszerrel meghívni kívánt runbookhoz. Hajtsa végre a [webhookok létrehozását](automation-webhooks.md#creating-a-webhook) tárgyaló cikk lépéseit, és jegyezze fel a webhook URL-címét, hogy a riasztási szabály konfigurálása során hivatkozhasson rá.   

## <a name="calling-a-runbook-directly"></a>Runbookok közvetlen meghívása

Ha az OMS-munkaterületen telepítve és konfigurálva van az Automatizálás és vezérlés ajánlat, a riasztás Runbook-műveletek beállításának konfigurálásakor a **Runbook kiválasztása** legördülő menüben láthatja az összes runbookot, és kiválaszthatja közülük azt, amelyiket a riasztásra válaszként futtatni kíván. A kiválasztott runbook futtatható az Azure felhőben lévő munkaterületeken vagy egy hibrid runbook-feldolgozón. Ha a riasztást a runbook beállítás használatával hozta létre, a rendszer létre fog hozni egy webhookot a runbookhoz. A webhookot az Automation-fiókban a kiválasztott runbook webhook paneljére lépve tekintheti meg. A riasztás törlésével a webhook nem törlődik automatikusan, azonban a felhasználó manuálisan törölheti azt. Nem probléma, ha a webhook nincs törölve, csupán egy olyan árva elemmé válik, amelyet idővel törölni kell, hogy az Automation-fiók rendezett maradjon.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>A runbookok jellemzői (mindkét lehetőség esetében)

Érdemes megismernie a runbookok Log Analytics-riasztásokból való meghívására szolgáló két módszer jellemzőit, mielőtt konfigurálná a riasztásokat.

* Rendelkeznie kell egy **WebhookData** elnevezésű és **Objektum** típusú bemeneti runbookparaméterrel. Ez lehet kötelező vagy nem kötelező. A riasztás a keresési eredményeket a runbooknak ezen a bemeneti paraméteren keresztül adja át.

    ```powershell
    param  
        (  
        [Parameter (Mandatory=$true)]  
        [object] $WebhookData  
        )
    ```
*  A WebhookData PowerShell-objektummá való átalakításához kód szükséges.

    ```powershell
    $SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
    ```

    A *$SearchResult* egy objektumtömb, amelyben mindegyik objektum egy keresési eredmény értékeit tároló mezőket tartalmaz

## <a name="example-walkthrough"></a>Forgatókönyv – példa

Most a következő, valamilyen Windows-szolgáltatást indító grafikus runbook segítségével bemutatjuk, hogyan is működik ez a folyamat.<br><br> ![Windows-szolgáltatás grafikus runbookjának indítása](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

A runbook rendelkezik egy **Objektum** típusú, **WebhookData** nevű bemeneti paraméterrel, amely tartalmazza a riasztás által továbbított \*.SearchResult\* tömbben lévő webhookadatokat.<br><br> ![Runbook bemeneti paraméterei](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Esetünkben most létrehoztunk két egyéni mezőt a Log Analyticsben \*SvcDisplayName\_CF\* és \*SvcState\_CF\* néven, amelyek a szolgáltatás megjelenített nevét és állapotát (fut vagy leállítva) nyerik ki a rendszer-eseménynaplóba írt eseményből. Ezután létrehozunk egy riasztási szabályt a `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` keresési lekérdezéssel, hogy észlelni tudjuk, amikor a Nyomtatásisor-kezelő szolgáltatás leáll a Windows-rendszerben. Ez lehet bármely számunkra fontos szolgáltatás, de ebben a példában egy olyan már meglévő szolgáltatást választottunk, amely része a Windows operációs rendszernek. A riasztási művelet a konfigurációja szerint végrehajtja a példában használt runbookot a hibrid runbook-feldolgozón futva, amely engedélyezve van a célrendszeren.   

A runbookkódban lévő **Szolgáltatásnév lekérése a Log Analyticsből** tevékenység a JSON-formátumú karakterláncot objektumtípussá alakítja, és a \*SvcDisplayName\_CF\* elemre szűrve kinyeri a Windows-szolgáltatás megjelenített nevét, majd továbbítja ezt az értéket a következő tevékenységnek, amely ellenőrzi, hogy a szolgáltatás le van-e állítva, mielőtt megkísérelné újraindítani. Az *SvcDisplayName_CF* [egyéni mezőt](../log-analytics/log-analytics-custom-fields.md) a példa szemléltetéséhez hoztuk létre a Log Analyticsben.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Ha a szolgáltatás leáll, a Log Analyticsben lévő riasztási szabály egyezést észlel, majd aktiválja a runbookot, és továbbítja neki a riasztás környezetét. A runbook munkához lát, és ellenőrzi, hogy a szolgáltatás valóban leállt-e, és amennyiben igen, megkísérli újraindítani azt, majd ellenőrzi, hogy megfelelően elindult-e, és elküldi az eredményeket a kimenetre.     

Ha az Automation-fiók nincs az OMS-munkaterülethez társítva, alternatív megoldásként a riasztási szabályt a webhook művelettel úgy kell beállítania, hogy a runbookot aktiválja, továbbá konfigurálnia kell a runbookot, hogy az konvertálja a JSON-formátumú karakterláncot, és a \*.SearchResult\* tömbre szűrjön a fenti útmutatásoknak megfelelően.    

## <a name="next-steps"></a>Következő lépések

* A Log Analytics-riasztásokkal és létrehozásukkal kapcsolatos további információkért lásd: [Riasztások a Log Analyticsben](../log-analytics/log-analytics-alerts.md).

* A runbookok webhookkal való aktiválásával kapcsolatos további információkért lásd: [Azure Automation-webhookok](automation-webhooks.md).
