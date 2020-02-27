---
title: Hibakezelés az Azure Automation grafikus runbookokban
description: Ez a cikk ismerteti a hibakezelési logika megvalósítását az Azure Automation grafikus runbookokban.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: dec715ec6741f4429d8b1d4f620ef3cb82d4c1d3
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649976"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Hibakezelés az Azure Automation grafikus runbookokban

A Azure Automation grafikus runbook figyelembe venni kívánt alapvető tervezési elv annak a problémának az azonosítása, amelyet a runbook a végrehajtás során felmerülhet. Az ilyen helyzetek közé tartoznak a sikeres műveletek, a várt hibaállapotok és a váratlan hibafeltételek.

Gyakran előfordul, hogy ha egy runbook-tevékenységgel kapcsolatos megszakítást nem okozó hiba történik, a Windows PowerShell az alábbi tevékenységek feldolgozásával kezeli a tevékenységet, függetlenül attól, hogy milyen hiba történt. A hiba valószínűleg kivételt hoz létre, a lényeg azonban az, hogy a következő tevékenység is lefuthat.

A grafikus runbook tartalmaznia kell a hibák kezelésére szolgáló kódot a végrehajtási problémák kezeléséhez. Ha ellenőrizni szeretné egy tevékenység kimenetét, vagy hibát észlel, használhat PowerShell-kód tevékenységet, feltételes logikát definiálhat a tevékenység kimeneti hivatkozására, vagy alkalmazhat egy másik módszert.

Azure Automation grafikus runbookok javult a hibák kezelésére szolgáló képességgel. A kivételeket mostantól nem megszakító hibákká változtathatja, valamint hibahivatkozásokat hozhat létre a tevékenységek között. A továbbfejlesztett folyamat lehetővé teszi a runbook számára a hibák észlelését és a realizált vagy váratlan feltételek kezelését. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>PowerShell-hibák típusai

A runbook végrehajtása során esetlegesen fellépő PowerShell-hibák típusai: megszakítási hibák és nem megszakítási hibák.
 
### <a name="terminating-error"></a>Megszakítási hiba

A leállítási hiba súlyos hiba a végrehajtás során, amely teljesen megszakítja a parancs vagy a parancsfájl végrehajtását. Ilyenek például a nem létező parancsmagok, a parancsmag futását megakadályozó szintaktikai hibák és egyéb végzetes hibák.

### <a name="non-terminating-error"></a>Megszakítást nem okozó hiba

A megszakítást nem okozó hiba egy nem súlyos hiba, amely lehetővé teszi, hogy a végrehajtás a hiba feltétele ellenére is folytatódjon. Ilyenek például a működési hibák, például a fájl nem található hibák és az engedélyek problémái.

## <a name="when-to-use-error-handling"></a>Mikor érdemes hibakezelést használni?

Ha egy kritikus tevékenység hibát vagy kivételt jelez, használja a runbook a hibakezelés során. Fontos, hogy megakadályozza a runbook következő tevékenységének feldolgozását, és megfelelően kezelni a hibát. A hiba kezelése különösen kritikus fontosságú, ha a runbookok üzleti vagy szolgáltatási műveleti folyamatot támogat.

Minden olyan tevékenységnél, amely hibát okozhat, bármilyen más tevékenységre mutató hibaüzenetet adhat hozzá. A célként megadott tevékenység bármilyen típusú lehet, beleértve a kód tevékenységét, a parancsmag meghívását, egy másik runbook meghívását stb. A célként megadott tevékenység kimenő hivatkozásokat is tartalmazhat, amelyek rendszeres vagy hibás hivatkozásokat tartalmaznak. A hivatkozások lehetővé teszik, hogy a runbook a kód tevékenysége nélkül implementálja az összetett hibák kezelésére szolgáló logikát.

Az ajánlott eljárás egy dedikált hibakezelés runbook létrehozása közös funkciókkal, de ez a gyakorlat nem kötelező. Vegyünk például egy olyan runbook, amely megpróbál elindítani egy virtuális gépet, és telepít egy alkalmazást. Ha a virtuális gép nem indul el megfelelően, a következőket kell tennie:

1. Értesítés küldése erről a problémáról.
2. Elindítja egy másik runbook, amely automatikusan kiépít egy új virtuális gépet.

Az egyik megoldás az, ha a runbook egy olyan tevékenységre mutat, amely az első lépést kezeli. A runbook például összekapcsolhatja a **Write-Warning** parancsmagot a második lépés egy tevékenységével, például a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) parancsmaggal.

Ezt a viselkedést számos runbookok is használhatja, ha ezt a két tevékenységet külön hibakezelés runbook, a korábban javasolt útmutatást követve. Mielőtt az eredeti runbook meghívja ezt a hibát a runbook kezelésében, létrehozhat egy egyéni üzenetet az adataiból, majd átadhatja azt paraméterként a runbook kezeléséhez.

## <a name="how-to-use-error-handling"></a>A hibakezelés használata

A runbook minden tevékenysége rendelkezik olyan konfigurációs beállítással, amely a kivételeket a nem lezáró hibákra kapcsolja. Alapértelmezés szerint ez a beállítás le van tiltva. Azt javasoljuk, hogy engedélyezze ezt a beállítást minden olyan tevékenységnél, amelynél a runbook kezeli a hibákat. Ennek a konfigurációnak az engedélyezése biztosítja, hogy a runbook a tevékenység megszakításával és megszakításával kapcsolatos hibákat a nem megszakítást okozó hibaként kezelje.  

A konfigurációs beállítás engedélyezése után a runbook létrehozhat egy tevékenységet, amely kezeli a hibát. Ha a tevékenység hibát okoz, a rendszer a kimenő hibák hivatkozásait követi. A normál kapcsolatok nem lesznek követve, még akkor is, ha a tevékenység a normál kimenetet is létrehozza.<br><br> ![Példa egy Automation runbook hibahivatkozásra](media/automation-runbook-graphical-error-handling/error-link-example.png)

A következő példában egy runbook egy olyan változót kér le, amely egy virtuális gép számítógépnevét tartalmazza. Ezután megpróbálja elindítani a virtuális gépet a következő tevékenységgel.<br><br> ![Automatizálási runbook – példa](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A **Get-AutomationVariable** tevékenység és a [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) parancsmag úgy van konfigurálva, hogy a kivételeket a hibákra konvertálja. Ha problémák merülnek fel a változó beszerzése vagy a virtuális gép elindítása során, a kód hibákat generál.<br><br> ![Automation runbook hiba-kezelési tevékenység beállítások](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

Hiba történt a folyamatok ezen tevékenységekből egyetlen **hiba-felügyeleti** kód tevékenységre való csatolásakor. Ez a tevékenység egy egyszerű PowerShell-kifejezéssel van konfigurálva, amely a *Throw* kulcsszó használatával leállítja a feldolgozást, valamint `$Error.Exception.Message` az aktuális kivételt leíró üzenet beszerzéséhez.<br><br> ![Automation runbook hiba a kód kezelésére példa](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Következő lépések

* További információk a hivatkozásokról és a grafikus runbookokban szereplő hivatkozástípusokról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md#links-and-workflow).

* A runbook végrehajtásával, a runbook-feladatok figyelésével és egyéb technikai részletekkel kapcsolatos további tudnivalókért tekintse meg [a runbook végrehajtásáról szóló részt Azure Automation](automation-runbook-execution.md).