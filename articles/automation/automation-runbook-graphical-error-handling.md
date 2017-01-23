---
title: "Hibakezelés az Azure Automation grafikus runbookokban | Microsoft Docs"
description: "Ez a cikk ismerteti a hibakezelési logika megvalósítását az Azure Automation grafikus runbookokban."
services: automation
documentationcenter: 
author: mgoedtel
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
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Hibakezelés az Azure Automation grafikus runbookokban

Az egyik legfontosabb figyelembe veendő runbooktervezési alapelv a runbookokkal kapcsolatban felmerülő különböző helyzetek, például a várt hibaállapotok, illetve a váratlan hibák feltételeinek azonosítása.  Ennek megfelelően az észleléshez a runbookoknak hibakezelési képességekkel kell rendelkezniük.  A grafikus runbookok esetében, ha érvényesíteni szeretné egy tevékenység kimenetét, vagy a megfelelő módon szeretne kezelni egy hibát, akkor lehetősége van a folyamatot egy PowerShell-kóddal végzett tevékenységgel folytatni, feltételes logikát meghatározni a tevékenység kimeneti hivatkozására, vagy egyéb módszert alkalmazni.          

A runbookok végrehajtásakor gyakran előfordul, hogy ha egy tevékenységgel kapcsolatban megszakítást nem okozó hiba merül fel, akkor a rendszer az az után következő tevékenységeket a hibától függetlenül feldolgozza.  Ez persze valószínűleg kivételt generál majd, a lényeg azonban az, hogy a következő tevékenység is lefuthat. A viselkedés hátterében a PowerShell nyelv hibakezelése áll.    

A végrehajtás során előforduló PowerShell-hibák megszakítást okozó vagy megszakítást nem okozó típusúak lehetnek.  A különbségek a következők:

* Megszakítást okozó hiba: A végrehajtás során bekövetkező súlyos hiba, amely teljes egészében megszakítja a parancs (vagy szkript) végrehajtását. Ilyenek például a nem létező parancsmagok, a parancsmag futását megakadályozó szintaktikai hiba vagy az egyéb végzetes hibak.

* Megszakítást nem okozó hiba: Nem súlyos hiba, amely a hiba ellenére engedélyezi a végrehajtást. Ilyenek például a műveleti hibák (a fájl nem található, az engedélyekkel kapcsolatos problémák stb.).

Az Azure Automation grafikus runbookok kiegészültek a hibakezelés képességével is, így a kivételeket mostantól nem megszakító hibákká változtathatja, valamint hibahivatkozásokat hozhat létre a tevékenységek között. Ez lehetővé teszi a runbook szerzője számára a hibák észlelését, és módot biztosít a létrejött vagy váratlan feltétel kezelésére.  

## <a name="when-to-use"></a>A következő esetekben használja

A munkafolyamat futtatásának szabályozása azért fontos, mert ezáltal biztosítható, hogy a kritikus tevékenységekkel kapcsolatos hibák vagy kivételek felmerülésekor a runbook ne lépjen tovább a következő tevékenységhez, és megfelelően kezelni lehessen a hibát.  Ez különösen fontos abban az esetben, ha a runbookok valamilyen üzleti vagy szolgáltatási műveleti folyamatot támogatnak.

A runbook szerzője minden egyes tevékenységhez, amely hibát eredményezhet, hozzáadhat egy hibahivatkozást, amely bármely más tevékenységre mutathat.  A céltevékenység bármilyen típusú lehet: kóddal végzett tevékenység, parancsmag meghívása, másik runbook meghívása vagy bármi más. 

A céltevékenység továbbá kimenő hivatkozásokat is tartalmazhat, amelyek szokásos hivatkozások vagy hibahivatkozások is lehetnek, ezáltal a runbook szerzője összetett hibakezelési logikát építhet be anélkül, hogy kóddal végzett tevékenységet kelljen igénybe vennie.  Bár az ajánlott eljárás egy általános funkciókkal rendelkező dedikált hibakezelési runbook létrehozása, ez nem kötelező, a PowerShell-kóddal végzett tevékenységben szereplő hibakezelési logika pedig nem az egyetlen alternatíva.  

Vegyünk példaként egy olyan runbookot, amely egy virtuális gépet próbál elindítani, majd telepíteni rá egy alkalmazást, a virtuális gép azonban nem indul el megfelelően. Ekkor két műveletet végez: 

1. Értesítést küld a problémáról.
2. Elindít egy másik runbookot, amely automatikusan új virtuális gépet helyez üzembe. 

Az egyik megoldás, ha a hibahivatkozás egy, az 1. lépést kezelő tevékenységre mutat, ilyen például a **Write-Warning** parancsmag, és össze van kapcsolva a 2. lépéshez szükséges tevékenységgel, például a **Start-AzureRmAutomationRunbook** parancsmaggal. 

Ezt a viselkedést a további runbookokban való használat céljából általánosíthatja is, ha a két tevékenységet külön hibakezelési runbookba helyezi a korábban ajánlott útmutatás szerint.  A hibakezelési runbook meghívása előtt egyéni üzenetet hozhat létre az eredeti runbook adataiból, majd paraméterként továbbviheti azt a hibakezelési runbookba. 

## <a name="how-to-use"></a>A használat módja

Minden tevékenység rendelkezik a kivételeket nem megszakító hibákká módosító konfigurációval. A beállítás alapértelmezés szerint le van tiltva.  Ezt beállítást minden olyan tevékenységnél érdemes engedélyezni, amely esetében kezelni szeretné a hibákat.  A konfiguráció engedélyezésével biztosíthatja, hogy a tevékenységben mind a megszakítást okozó, mind a megszakítást nem okozó hibákat megszakítást nem okozó hibaként kezelje a rendszer, amelyeket később hibahivatkozás segítségével kezelhet.  A konfiguráció beállítását követően hozza létre a hiba kezelésére irányuló tevékenységet.  Ha egy tevékenység hibát eredményez, akkor a rendszer a kimenti hibahivatkozásokat követi a szokásos hivatkozások helyett még akkor is, ha a tevékenység normál kimenetet is eredményezett.<br><br> ![Példa egy Automation runbook hibahivatkozásra](media/automation-runbook-graphical-error-handling/error-link-example.png)

Az alábbi egyszerű példában egy olyan runbook szerepel, amely egy virtuális gép számítógépnevét tartalmazó változót ad vissza, majd a következő tevékenységgel megkísérli a virtuális gép elindítását.<br><br> ![Példa egy Automation runbook hibakezelésére](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

A **Get-AutomationVariable** tevékenység és a **Start-AzureRmVm** a kivételek hibákká történő konvertálására vannak konfigurálva.  Ha probléma adódik a változó lekérésével vagy a virtuális gép indításával, akkor hiba jön létre.<br><br> ![Egy Automation runbook hibakezelési tevékenységének beállításai](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Az ezen tevékenységekből származó hibahivatkozások egyetlen **Hibakezelés** tevékenységbe (kóddal végzett tevékenység) áramlanak, amely egy egyszerű PowerShell-kifejezéssel van konfigurálva, amely a *Throw* (eldobás) kulcsszó használatával leállítja a feldolgozást, az *$Error.Exception.Message* segítségével pedig lekéri az aktuális kivételt leíró üzenetet.<br><br> ![Példa egy Automation runbook hibakezelési kódra](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Következő lépések

* További információk a hivatkozásokról és a grafikus runbookokban szereplő hivatkozástípusokról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md#links-and-workflow).

* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza 


<!--HONumber=Jan17_HO1-->


