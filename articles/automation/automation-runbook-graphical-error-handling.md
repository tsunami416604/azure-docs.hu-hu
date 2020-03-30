---
title: Hibakezelés az Azure Automation grafikus runbookokban
description: Ez a cikk ismerteti a hibakezelési logika megvalósítását az Azure Automation grafikus runbookokban.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367074"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Hibakezelés az Azure Automation grafikus runbookokban

Az Azure Automation grafikus runbookhoz figyelembe veendő legfontosabb tervezési elv a runbook által a végrehajtás során tapasztalt problémák azonosítása. Az ilyen helyzetek közé tartoznak a sikeres műveletek, a várt hibaállapotok és a váratlan hibafeltételek.

Gyakran előfordul, hogy nem végződő hiba fordul elő egy runbook-tevékenység, a Windows PowerShell kezeli a tevékenység feldolgozásával minden tevékenység, amely követi, függetlenül attól, hogy a hiba. A hiba valószínűleg kivételt hoz létre, a lényeg azonban az, hogy a következő tevékenység is lefuthat.

A grafikus runbook nak tartalmaznia kell a végrehajtási problémák kezelésére hibakezelési kódot. Egy tevékenység kimenetének érvényesítéséhez vagy egy hiba kezeléséhez használhatja a PowerShell-kódtevékenységet, feltételes logikát definiálhat a tevékenység kimeneti kapcsolatán, vagy alkalmazhat más módszert.

Az Azure Automation grafikus runbookok kiegészültek a hibakezelés képességével is. A kivételeket mostantól nem megszakító hibákká változtathatja, valamint hibahivatkozásokat hozhat létre a tevékenységek között. A továbbfejlesztett folyamat lehetővé teszi, hogy a runbook hibákat észleljen, és kezelje a realizált vagy váratlan körülményeket. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="powershell-error-types"></a>PowerShell-hibatípusok

A RunBook végrehajtása során előforduló PowerShell-hibák típusai a hibák és a nem végződési hibák.
 
### <a name="terminating-error"></a>Megszüntetési hiba

A megszüntetési hiba súlyos hiba a végrehajtás során, amely teljesen leállítja a parancsot vagy parancsfájlok végrehajtását. Ilyenek például a nem létező parancsmagok, a parancsmag futását megakadályozó szintaktikai hibák és más végzetes hibák.

### <a name="non-terminating-error"></a>Nem végződési hiba

A nem megszüntetési hiba nem súlyos hiba, amely lehetővé teszi a végrehajtás folytatását a hibaállapot ellenére. Ilyenek például a működési hibák, például a fájl nem talált hibák és engedélyekkel kapcsolatos problémák.

## <a name="when-to-use-error-handling"></a>Mikor érdemes hibakezelést használni?

Használja a hibakezelést a runbookban, ha egy kritikus tevékenység hibát vagy kivételt okoz. Fontos, hogy megakadályozza a runbook következő tevékenységének feldolgozását, és megfelelően kezelje a hibát. A hiba kezelése különösen fontos, ha a runbookok támogatják az üzleti vagy szolgáltatási műveletek folyamata.

Minden olyan tevékenységhez, amely hibát okozhat, bármely más tevékenységre mutató hibahivatkozást adhat hozzá. A céltevékenység bármilyen típusú lehet, beleértve a kódtevékenységet, a parancsmag meghívását, egy másik runbook meghívását és így tovább. A céltevékenység nek lehetnek kimenő hivatkozásai is, akár rendszeres, akár hibahivatkozások. A hivatkozások lehetővé teszik, hogy a runbook összetett hibakezelési logikát valósítson meg kódtevékenység alkalmazása nélkül.

Az ajánlott eljárás egy dedikált hibakezelési runbook közös funkciókkal, de ez a gyakorlat nem kötelező. Vegyünk például egy runbookot, amely megpróbál elindítani egy virtuális gépet, és telepíteni egy alkalmazást. Ha a virtuális gép nem indul el megfelelően, akkor:

1. Értesítést küld a problémáról.
2. Elindítja egy másik runbook, amely automatikusan egy új virtuális gép helyett.

Az egyik megoldás az, hogy egy hibahivatkozást a runbook mutat egy tevékenységet, amely kezeli az első lépés. Például a runbook csatlakoztathatja a `Write-Warning` parancsmast egy tevékenységhez a második lépéshez, például a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) parancsmaghoz.

Ezt a viselkedést számos runbookban való használatra is általánosíthatja, ha ezt a két tevékenységet külön hibakezelési runbookba helyezi. Mielőtt az eredeti runbook meghívja ezt a hibakezelési runbookot, létrehozhat egy egyéni üzenetet az adataiból, majd paraméterként átadhatja azt a hibakezelési runbooknak.

## <a name="how-to-use-error-handling"></a>A hibakezelés használata

A runbook minden tevékenysége rendelkezik egy konfigurációs beállítással, amely a kivételeket nem végződő hibákká alakítja. Alapértelmezés szerint ez a beállítás le van tiltva. Azt javasoljuk, hogy engedélyezze ezt a beállítást minden olyan tevékenység, ahol a Runbook kezeli a hibákat. Ez a beállítás biztosítja, hogy a runbook kezeli mind a leállítási és a nem végződési hibák a tevékenység nem végződési hibák, egy hibahivatkozás használatával.  

A konfigurációs beállítás engedélyezése után a runbook hozzon létre egy tevékenységet, amely kezeli a hibát. Ha a tevékenység hibát okoz, a rendszer követi a kimenő hibahivatkozásokat. A rendszeres kapcsolatokat nem követik, még akkor sem, ha a tevékenység rendszeres kimenetet is eredményez.<br><br> ![Példa egy Automation runbook hibahivatkozásra](media/automation-runbook-graphical-error-handling/error-link-example.png)

A következő példában egy runbook lekéri a virtuális gép számítógépnevét tartalmazó változót. Ezután megpróbálja elindítani a virtuális gép a következő tevékenység.<br><br> ![Automatizálási runbook hibakezelés példa](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A `Get-AutomationVariable` tevékenység és a [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) parancsmag úgy van konfigurálva, hogy a kivételeket hibákká konvertálja. Ha problémák merülnek fel a változó beszerzése vagy a virtuális gép indítása, a kód hibákat generál.<br><br> ![Automation runbook hibakezelési](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)tevékenység beállításai .

A hibakapcsolatok ezekből a `error management` tevékenységekből egyetlen kódtevékenységhez áramlanak. Ez a tevékenység egy egyszerű PowerShell-kifejezéssel van konfigurálva, amely a kulcsszót használja a `throw` feldolgozás leállításához, valamint `$Error.Exception.Message` az aktuális kivételt leíró üzenet lekérő üzenet lekérő.<br><br> ![Automatizálási runbook hibakezelési kód példa](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>További lépések

* További információk a hivatkozásokról és a grafikus runbookokban szereplő hivatkozástípusokról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md#links-and-workflow).

* Ha többet szeretne megtudni a runbook-végrehajtásról, a runbook-feladatok figyeléséről és egyéb technikai részletekről, olvassa el [a Runbook-végrehajtás az Azure Automationben című témakört.](automation-runbook-execution.md)