---
title: "Hibakezelés az Azure Automation grafikus runbookokban | Microsoft Docs"
description: "Ez a cikk ismerteti a hibakezelési logika megvalósítását az Azure Automation grafikus runbookokban."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
ms.openlocfilehash: 9bbaf7bac013dffb941f15bdccd2ab2996f1576e
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Hibakezelés az Azure Automation grafikus runbookokban

Az egyik legfontosabb figyelembe veendő runbooktervezési alapelv a runbookokkal kapcsolatban felmerülő különböző helyzetek azonosítása. Az ilyen helyzetek közé tartoznak a sikeres műveletek, a várt hibaállapotok és a váratlan hibafeltételek.

A runbookoknak hibakezelési képességekkel is kell rendelkezniük. Ha érvényesíteni szeretné egy tevékenység kimenetét, vagy a grafikus runbookokkal szeretne kezelni egy hibát, akkor használhat egy Windows PowerShell-kóddal végzett tevékenységet, feltételes logikát határozhat meg a tevékenység kimeneti hivatkozására, vagy egyéb módszert is alkalmazhat.          

Gyakran előfordul, hogy ha egy runbooktevékenységgel kapcsolatban megszakítást nem okozó hiba merül fel, akkor a rendszer az az után következő tevékenységeket a hibától függetlenül feldolgozza. A hiba valószínűleg kivételt hoz létre, a lényeg azonban az, hogy a következő tevékenység is lefuthat. A PowerShell ilyen módon kezeli a hibákat.    

A végrehajtás során előforduló PowerShell-hibák megszakítást okozó vagy megszakítást nem okozó típusúak lehetnek. A megszakítást okozó és nem okozó hibák közötti különbség a következő:

* **Megszakítást okozó hiba**: A végrehajtás során bekövetkező súlyos hiba, amely teljes egészében megszakítja a parancs (vagy szkript) végrehajtását. Ilyenek például a nem létező parancsmagok, a parancsmag futását megakadályozó szintaktikai hibák vagy az egyéb végzetes hibák.

* **Megszakítást nem okozó hiba**: Nem súlyos hiba, amely a hiba ellenére engedélyezi a végrehajtást. Ilyenek például a műveleti hibák, például a „fájl nem található” vagy az engedélyekkel kapcsolatos problémák.

Az Azure Automation grafikus runbookok kiegészültek a hibakezelés képességével is. A kivételeket mostantól nem megszakító hibákká változtathatja, valamint hibahivatkozásokat hozhat létre a tevékenységek között. Ez a folyamat lehetővé teszi a runbook szerzője számára a hibák észlelését, és a létrejött vagy váratlan feltételek kezelését.  

## <a name="when-to-use-error-handling"></a>Mikor érdemes hibakezelést használni?

A kritikus tevékenységekkel kapcsolatos hibák vagy kivételek felmerülésekor fontos megakadályozni a runbook következő tevékenységének feldolgozását, és megfelelően kezelni a hibát. Ez kritikus fontosságú abban az esetben, ha a runbookok valamilyen üzleti vagy szolgáltatási műveleti folyamatot támogatnak.

A runbook szerzője minden egyes tevékenységhez, amely hibát eredményezhet, hozzáadhat egy hibahivatkozást, amely bármely más tevékenységre mutathat.  A céltevékenység bármilyen típusú lehet, többek között kóddal végzett tevékenységek, parancsmag meghívása, másik runbook meghívása stb.

Emellett a céltevékenység kimenő hivatkozásokat is tartalmazhat. Ezek lehetnek szokványos hivatkozások vagy hibahivatkozások. Ez azt jelenti, hogy a runbook szerzője összetett hibakezelési logikát valósíthat meg anélkül, hogy kóddal végzett tevékenységekre kellene hagyatkoznia. Az ajánlott eljárás egy általános funkciókkal rendelkező dedikált hibakezelési runbook létrehozása, ez azonban nem kötelező. A PowerShell-kóddal végzett tevékenységben szereplő hibakezelési logika pedig nem az egyetlen alternatíva.  

Vegyünk példaként egy olyan runbookot, amely egy virtuális gépet próbál elindítani, majd telepíteni rá egy alkalmazást. Ha a virtuális gép nem indul el megfelelően, akkor két műveletet végez:

1. Értesítést küld a problémáról.
2. Elindít egy másik runbookot, amely automatikusan új virtuális gépet helyez üzembe.

Az egyik megoldás, ha a hibahivatkozás egy, az 1. lépést kezelő tevékenységre mutat. A **Write-Warning** parancsmagot például összekapcsolhatja a 2. lépéshez szükséges tevékenységgel, például a **Start-AzureRmAutomationRunbook** parancsmaggal.

Ezt a viselkedést a további runbookokban való használat céljából általánosíthatja is, ha a két tevékenységet külön hibakezelési runbookba helyezi a korábban ajánlott útmutatás szerint. A hibakezelési runbook meghívása előtt egyéni üzenetet hozhat létre az eredeti runbook adataiból, majd paraméterként továbbviheti azt a hibakezelési runbookba.

## <a name="how-to-use-error-handling"></a>A hibakezelés használata

Minden tevékenység rendelkezik a kivételeket nem megszakító hibákká módosító konfigurációval. Alapértelmezés szerint ez a beállítás le van tiltva. Javasoljuk, hogy engedélyezze ezt a beállítást minden olyan tevékenységnél, amely esetében kezelni szeretné a hibákat.  

A konfiguráció engedélyezésével biztosíthatja, hogy a tevékenységben mind a megszakítást okozó, mind a megszakítást nem okozó hibákat megszakítást nem okozó hibaként kezelje a rendszer, amelyeket hibahivatkozás segítségével kezelhet.  

A konfiguráció beállítását követően hozza létre a hiba kezelésére irányuló tevékenységet. Ha egy tevékenység hibát eredményez, akkor a rendszer a kimenti hibahivatkozásokat követi a szokásos hivatkozások helyett, még akkor is, ha a tevékenység normál kimenetet is eredményez.<br><br> ![Példa egy Automation runbook hibahivatkozásra](media/automation-runbook-graphical-error-handling/error-link-example.png)

Az alábbi példában egy runbook egy virtuális gép számítógép nevét tartalmazó változót ad vissza. Ezután a következő tevékenységgel megkísérli a virtuális gép elindítását.<br><br> ![Példa egy Automation runbook hibakezelésére](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A **Get-AutomationVariable** tevékenység és a **Start-AzureRmVm** a kivételek hibákká történő konvertálására vannak konfigurálva.  Ha probléma adódik a változó lekérésével vagy a virtuális gép indításával, akkor hiba jön létre.<br><br> ![Egy Automation runbook hibakezelési tevékenységének beállításai](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Az ezen tevékenységekből származó hibahivatkozások egyetlen **hibakezelés** tevékenységbe (kóddal végzett tevékenység) áramlanak. Ez a tevékenység egy egyszerű PowerShell-kifejezéssel van konfigurálva, amely a *Throw* (eldobás) kulcsszó használatával leállítja a feldolgozást, az *$Error.Exception.Message* segítségével pedig lekéri az aktuális kivételt leíró üzenetet.<br><br> ![Példa egy Automation runbook hibakezelési kódra](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Következő lépések

* További információk a hivatkozásokról és a grafikus runbookokban szereplő hivatkozástípusokról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md#links-and-workflow).

* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
