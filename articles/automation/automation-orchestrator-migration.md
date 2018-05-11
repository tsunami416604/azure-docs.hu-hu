---
title: Azure Automation szolgáltatásbeli Orchestrator áttelepítése
description: Ismerteti, hogyan telepíthetők át a runbookok és integrációs csomagokat a System Center Orchestrator Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4d692e4dc639cbd290b465980da2319ac590627c
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Az Orchestrator áttelepítése az Azure Automation (béta)
A Runbookok [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) tevékenységei során az Azure Automation runbookjai Windows PowerShell alapuló kimondottan az orchestrator alkalmazáshoz írt integrációs csomagok alapulnak.  [Grafikus forgatókönyvek](automation-runbook-types.md#graphical-runbooks) az Azure Automationben rendelkezik egy hasonló megjelenési az Orchestrator runbookjai az eszközök, a PowerShell parancsmagok és a runbookok képviselő tevékenységek.

A [System Center Orchestrator áttelepítési eszközkészlet](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) segítséget nyújt a runbookok adatelemmé Orchestrator Azure Automation része.  Mellett konvertálása a runbookok magukat, az integrációs csomagokat a tevékenységek a runbookok használó kell konvertálnia integrációs modulok a Windows PowerShell-parancsmagokkal.  

Az alábbiakban az a folyamat alapvetően az Azure Automation az Orchestrator runbookjai átalakításához.  A lépések részletes leírását lásd az alábbi szakaszokban.

1. Töltse le a [System Center Orchestrator áttelepítési eszközkészlet](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) , amely az eszközök és a cikkben szereplő modulok tartalmazza.
2. Importálás [szokásos tevékenységek modul](#standard-activities-module) az Azure Automation szolgáltatásbeli.  Ez magában foglalja a konvertált runbookok által használható szabványos Orchestrator-tevékenységeket konvertált verzióit.
3. Importálás [System Center Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules) az adott integrációs csomagok a System Center elérő runbookok által használt Azure Automation-be.
4. Egyéni és harmadik féltől származó integrációs csomagok használatával alakítsa át a [Integration Pack konverter](#integration-pack-converter) , és importálja az Azure Automation.
5. A konvertálás Orchestrator runbookok használatával a [Runbook konverter](#runbook-converter) és az Azure Automationben telepítse.
6. Manuálisan szükséges Orchestrator eszközök létrehozása az Azure Automationben óta a Runbook konverter nem konvertálja a ezeket az erőforrásokat.
7. Konfigurálja a [hibrid forgatókönyv-feldolgozó](#hybrid-runbook-worker) a helyi adatközpontban, akik hozzáférhetnek a helyi erőforrások konvertált runbookok futtatására.

## <a name="service-management-automation"></a>Automatizált szolgáltatásfelügyelet
[A Szolgáltatáskezelési automatizálás](http://technet.microsoft.com/library/dn469260.aspx) (SMA) tárolja, és a helyi adatközpontban, például az Orchestrator felelős a runbookok, és az Azure Automation használja az ugyanazon integrációs modulok. A [Runbook konverter](#runbook-converter) konvertálja az Orchestrator runbookjai grafikus forgatókönyvek azonban ami nem támogatott az SMA.  Továbbra is telepíthet a [szabványos tevékenységek modul](#standard-activities-module) és [System Center Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules) az SMA rendszerbe, de manuálisan kell [a runbookok újraírási](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó
Az Orchestrator Runbookjai egy kiszolgálón tárolja, és futtassa a runbook-kiszolgálók, mind a helyi adatközpontban.  Az Azure Automation Runbookjai az Azure felhőben vannak tárolva, és futtathat a helyi adatok center használatával a [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md).  Ez a hogyan általában fog futni az Orchestrator alakítja át, mivel az a célja, hogy a helyi kiszolgálón futtassa a runbookok.

## <a name="integration-pack-converter"></a>Integrációs csomag konverter
Az integrációs csomag átalakító integrációs csomagok használatával létrehozott alakítja át a [Orchestrator Integration Toolkit (oit) használatával Kezdeményezett](http://technet.microsoft.com/library/hh855853.aspx) alapján, hogy importálni lehessen Azure Automation vagy a Szolgáltatáskezelési automatizálás a Windows PowerShell-integrációs modulok.  

Az integrációs csomag átalakító futtatásakor megnyílik egy varázsló, amely lehetővé teszi, hogy válassza ki az integrációs csomagot (oip).  A varázsló majd integrációs csomag részeként tevékenységek listája, és választhatja ki, amely át lesz telepítve.  A varázsló befejeződése után, az integrációs modul, amely tartalmazza a megfelelő parancsmag minden az eredeti integrációs csomag tevékenységei hoz létre.

### <a name="parameters"></a>Paraméterek
Az integrációs csomag tevékenység bármely tulajdonságok paramétereit a megfelelő parancsmag az integrációs modul alakulnak.  A Windows PowerShell-parancsmagok rendelkeznek [közös paraméterek](http://technet.microsoft.com/library/hh847884.aspx) , amely az összes parancsmag használható.  Például a - Verbose paraméter hatására a parancsmag kimeneti a működésével kapcsolatos részletes információkat.  Előfordulhat, hogy nincs parancsmag olyan paraméterre, amelynek a neve megegyezik egy közös paraméter.  Ha egy tevékenység rendelkezik közös paraméterként azonos nevű tulajdonsággal, a varázsló kérni fogja, hogy adja meg egy másik nevet a paraméterhez.

### <a name="monitor-activities"></a>A figyelő tevékenységek
Útmutató az Orchestrator runbookjai figyelése egy [figyelése](http://technet.microsoft.com/library/hh403827.aspx) , és futtassa a folyamatosan váró adott esemény hívható.  Azure Automation nem támogatja a figyelő runbookok, így az integrációs csomag figyelő tevékenységeket nem lehet konvertálni.  Ehelyett az integrációs modul, a figyelés tevékenység a helyőrző parancsmag jön létre.  Ez a parancsmag nem funkciókkal rendelkezik, de lehetővé teszi a bármely konvertált telepítendő használó runbookot.  Ez a forgatókönyv nem tudják az Azure Automationben futtatásához, de így módosíthatja is telepíthető.

### <a name="integration-packs-that-cannot-be-converted"></a>Nem konvertálható integrációs csomagok
OIT nem létrehozott integrációs csomagokat nem lehet konvertálni az integrációs csomag konverternek. Van még néhány eszköz, amely jelenleg nem lehet konvertálni a Microsoft által biztosított integrációs csomagokat.  Ezeket az integrációs csomagokat konvertált verziói lettek [letöltésre](#system-center-orchestrator-integration-modules) , hogy az Azure Automation vagy a Service Management Automation telepíthető.

## <a name="standard-activities-module"></a>Szokásos tevékenységek modul
Az orchestrator készletét tartalmazza [szokásos tevékenységek](http://technet.microsoft.com/library/hh403832.aspx) , amelyek nem szerepelnek az integrációs csomag, de sok runbookok által használt.  A szokásos tevékenységek modul az egyenértékű parancsmag minden ezeket a tevékenységeket tartalmazó integrációs modul létrehozása.  Telepítenie kell az integrációs modul az Azure Automationben szokásos tevékenység használható konvertált runbookokat importálása előtt.

Mellett konvertált forgatókönyvek támogatása, a szokásos tevékenységek modul parancsmagjai is használható valaki ismeri a Orchestrator hozhat létre új Azure Automation runbookjai.  A szokásos tevékenységeket összes parancsmagokkal hajtható végre, amíg azok másképp működhet.  A konvertált szokásos tevékenységek modul parancsmagjainak ugyanaz, mint a megfelelő tevékenységeket működik, és a ugyanazokkal a paraméterekkel használja.  Ez segít a meglévő Orchestrator-runbook Szerző az áttérés Azure Automation-forgatókönyv.

## <a name="system-center-orchestrator-integration-modules"></a>A System Center Orchestrator integrációs modulok
A Microsoft biztosít [integrációs csomagok](http://technet.microsoft.com/library/hh295851.aspx) automatizálásához a System Center-összetevők és egyéb termékek runbookok készítéséhez.  Néhány ezeket az integrációs csomagokat a OIT jelenleg alapulnak, de nem jelenleg konvertálható integrációs modulok ismert problémák miatt.  [A System Center Orchestrator integrációs modulok](https://www.microsoft.com/download/details.aspx?id=49555) ezeket az integrációs csomagokat, amelyek az Azure Automation- és Service Management Automation importálható konvertált verzióját tartalmazza.  

Ez az eszköz RTM verzió az integrációs csomag konverternek átalakítható OIT alapján integrációs csomagok frissített verzióit közzé lesz téve.  Útmutatás is nyújtanak segítséget az integrációs csomagok OIT alapuló tevékenységek segítségével runbookokat alakítása.

## <a name="runbook-converter"></a>Runbook konverter
A Runbook konverter konvertálja az Orchestrator runbookjai [grafikus forgatókönyvek](automation-runbook-types.md#graphical-runbooks) , hogy importálni lehessen Azure Automation.  

Egy parancsmaggal nevű Runbook konverter valósul meg egy PowerShell-modul **ConvertFrom-SCORunbook** , amely az átalakítást hajt végre.  Az eszköz telepítésekor hoz létre egy PowerShell-munkamenetet, amely betölti a parancsmag egy parancsikont.   

Az alábbiakban az a folyamat alapvetően egy Orchestrator-runbook konvertálja és importálja a fájlt az Azure Automation.  A következő szakaszok további részletekkel szolgálnak az eszközzel és konvertált runbookok használatával.

1. Az Orchestrator exportálja a runbookokat.
2. Szerezze be a runbook minden tevékenység integrációs modulok.
3. Alakítsa át az Orchestrator runbookjai az exportált fájlban.
4. Tekintse át az átalakítás érvényesítéséhez, és minden szükséges manuális feladatok megállapíthatja a naplókban lévő információk.
5. Azure Automation, átalakított runbookokat importálni.
6. Minden szükséges eszközök létrehozása az Azure Automationben.
7. Az Azure Automationben szükséges tevékenységeket módosításához a runbook szerkesztése.

### <a name="using-runbook-converter"></a>Runbook konverter használatával
A szintaxisának **ConvertFrom-SCORunbook** a következőképpen történik:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath - tartalmazó alakítsa át a runbookokat exportálás fájl elérési útját.
* A modul - vesszővel elválasztott a runbookok tevékenységeket tartalmazó integrációs modulok felsorolása.
* OutputFolder - mappa elérési útját létrehozásához grafikus forgatókönyvek alakítja.

A következő példaparancs alakítja át a runbookok nevű exportálási fájlba **MyRunbooks.ois_export**.  Ezek a runbookok használja az Active Directory és a Data Protection Manager integrációs csomagokat.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>Naplófájlok
A Runbook konverter a következő naplófájlokat hoz létre a konvertált runbook ugyanazon a helyen.  Ha a fájlok már léteznek, majd azok felülírja az utolsó átalakítás származó információkkal.

| Fájl | Tartalom |
|:--- |:--- |
| Runbook konverter - Progress.log |Az átalakítás, beleértve a minden egyes tevékenységhez sikeresen konvertálni információkat és minden egyes tevékenységhez konvertálva figyelmeztetés részletes lépéseket. |
| Runbook konverter - Summary.log |Az utolsó átalakítás, beleértve a figyelmeztetéseket összefoglalását és feladatokat, például létrehozhat egy változó megadása kötelező, a konvertált forgatókönyv végrehajtásához nyomon követéséhez. |

### <a name="exporting-runbooks-from-orchestrator"></a>Az Orchestrator runbookok exportálása
A Runbook konverter működik az Orchestrator, amely tartalmaz egy vagy több runbook exportálási fájlba.  Az exportált fájlban a megfelelő Azure Automation-runbook minden Orchestrator-runbook hoz létre.  

Az Orchestrator runbook exportálásához kattintson a jobb gombbal a Runbook Designerben a runbook nevét, és válassza ki **exportálása**.  Szeretné exportálni az összes runbook egy mappát, kattintson a jobb gombbal a mappára, és válassza a neve **exportálása**.

### <a name="runbook-activities"></a>Runbook-tevékenységek
A Runbook konverter az Orchestrator runbook tevékenységeiről a megfelelő tevékenységet az Azure Automationben alakítja át.  Ezekre a tevékenységekre, amely nem lehet konvertálni egy helyőrző tevékenység a runbookban figyelmeztető szöveg jön létre.  Miután az Azure Automation a konvertált runbookot importál, le kell cserélnie ezek a tevékenységek bármelyike érvényes a szükséges funkciókat ellátó tevékenységek.

Az Orchestrator tevékenységeket a [szabványos tevékenységek modul](#standard-activities-module) konvertálja.  Nincsenek néhány szokásos Orchestrator-tevékenységeket, amelyeken nem ebben a modulban, ha nem történik meg.  Például **Platformesemény küldése** rendelkezik Azure Automation egyenértékű, mivel az esemény egy adott Orchestrator.

[Tevékenységek figyelését](https://technet.microsoft.com/library/hh403827.aspx) nem lettek konvertálva, mert nincs megfelelője számukra az Azure Automationben.  A kivétel figyelő tevékenységek [konvertálni az integrációs csomagok](#integration-pack-converter) , amely a helyőrző tevékenység konvertálja.

A tevékenység egy [konvertálni az integrációs csomag](#integration-pack-converter) kívánja megadni a elérési útját az integrációs modul konvertálja a **modulok** paraméter.  A System Center integrációs csomagok használhatják a [System Center Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Erőforrások az orchestrator programhoz
A Runbook konverter csak a runbookok, nem más Orchestrator erőforrások, például számlálókat, változókat és kapcsolatok alakítja.  Teljesítményszámlálók az Azure Automationben nem támogatottak.  Változók és a kapcsolatok támogatottak, de manuálisan kell létrehoznia őket.  A naplófájlok fog arról tájékoztatják, ha a runbook igényel ezekhez az erőforrásokhoz, és adja meg a megfelelő erőforrásokra, amelyekre szüksége van az Azure Automationben megfelelően működjenek a konvertált runbook létrehozásához.

Egy runbook például egy változó egy adott értéket egy tevékenységben feltöltéséhez használhat.  A konvertált runbook konvertálja a tevékenységet, majd adja meg a változó eszköz az Azure Automationben neve megegyezik az Orchestrator változó.  Ez a kísérheti a **Runbook konverter - Summary.log** az átalakítás után létrehozott fájl.  Akkor hozzon létre manuálisan a változóeszköz Azure Automation forgatókönyv használata előtt.

### <a name="input-parameters"></a>A bemeneti paraméterek
Az Orchestrator Runbookjai a bemeneti paraméterek fogadja el a **adatok inicializálása** tevékenység.  Ha a runbook konvertálni tartalmazza ennek a tevékenységnek, majd egy [bemeneti paraméter](automation-graphical-authoring-intro.md#runbook-input-and-output) az Azure Automation runbook létrehozása a tevékenység mindegyik paraméterére vonatkozóan.  A [munkafolyamat parancsprogram-vezérlők](automation-graphical-authoring-intro.md#activities) tevékenységet hoz létre a konvertált runbook kéri le, és visszaadja az egyes paramétereket.  A tevékenységből származó bármely tevékenységek a runbookban, amelyek egy bemeneti paraméter tekintse meg a kimeneti.

Az, hogy használja-e ezt a stratégiát azért legjobban tükrözik az Orchestrator runbook funkciók.  Új grafikus forgatókönyvek tevékenységek közvetlenül bemeneti paraméterek, a forgatókönyv bemeneti adatforrást használó utal.

### <a name="invoke-runbook-activity"></a>Runbook meghívása tevékenységhez
Az Orchestrator Runbookjai elindíthatnak más runbookokat, a **Runbook meghívása** tevékenység. Ha a runbook konvertálni tartalmazza ennek a tevékenységnek és a **Várakozás a befejezésre** beállítás, akkor a runbook-tevékenység az jön létre a konvertált runbook.  Ha a **Várakozás a befejezésre** beállítása, majd a munkafolyamat-parancsprogram-tevékenység hoz létre, amely használja **Start-AzureAutomationRunbook** a runbook elindításához.  Miután az Azure Automation a konvertált runbookot importál, módosítania kell ezt a tevékenységet tevékenységben megadott adatokkal.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A System Center 2012 – Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [A Szolgáltatáskezelési automatizálás](https://technet.microsoft.com/library/dn469260.aspx)
* [Hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md)
* [Az orchestrator szokásos tevékenységek](http://technet.microsoft.com/library/hh403832.aspx)
* [Letöltés a System Center Orchestrator áttelepítési eszközkészlet](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
