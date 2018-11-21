---
title: Az Orchestrator-ről az Azure Automation szolgáltatásban
description: Ismerteti a runbookok és integrációs csomagokat a System Center Orchestrator migrálása az Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 80b0523f8442e30e6af329263be454fa545933d6
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275282"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Az Orchestrator-ről az Azure Automation szolgáltatásban (bétaverzió)
A Runbookok [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) közben az Azure Automation runbookjai Windows Powershellen alapulnak kifejezetten az Orchestrator számára írt integrációs csomag alapulnak.  [Grafikus runbookok](automation-runbook-types.md#graphical-runbooks) az Azure Automationben rendelkezik egy hasonló megjelenési az Orchestrator runbookjai az eszközök, a PowerShell parancsmagok és a gyermek runbookok jelölő tevékenységeik.

A [System Center Orchestrator áttelepítési eszközkészlet](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) segítséget nyújt a runbookok Orchestrator átalakítása az Azure Automation olyan eszközt tartalmaz.  Mellett konvertálása magukat a runbookokat, a tevékenységeket, a runbookokat az integrációs csomagokat kell konvertálnia integrációs modulok Windows PowerShell-parancsmagokkal.  

Következő alakítása. az Orchestrator runbookjai az Azure Automationhöz alapszintű során a rendszer.  Az alábbi szakaszokban részletesen ismertetett egyes lépéseket.

1. Töltse le a [System Center Orchestrator áttelepítési eszközkészlet](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) , amely az eszközök és a modulok ebben a cikkben tárgyalt tartalmazza.
2. Importálás [szokásos tevékenységek modul](#standard-activities-module) az Azure Automationbe.  Ez magában foglalja a konvertált verziók konvertált runbookok által esetleg használt szokásos az Orchestrator-tevékenységeket.
3. Importálás [System Center Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules) az Azure Automationbe, az ezen a System Center hozzáférő runbookok által használt integrációs csomagok.
4. Egyéni és harmadik féltől származó integrációs csomagok segítségével a [Integration Pack Converter](#integration-pack-converter) , majd importálja az Azure Automationben.
5. A konvertálás Orchestrator runbookok használatával a [Runbook Converter](#runbook-converter) , és telepítse az Azure Automationben.
6. Manuálisan szükséges Orchestrator adategységek létrehozása az Azure Automationben, mivel a Runbook átalakító nem alakítja át ezeket az erőforrásokat.
7. Konfigurálja a [hibrid Runbook-feldolgozó](#hybrid-runbook-worker) a helyi adatközpontban, akik hozzáférhetnek a helyi erőforrások konvertált runbookok futtatására.

## <a name="service-management-automation"></a>Automatizált szolgáltatásfelügyelet
[A Szolgáltatáskezelési automatizálás](https://technet.microsoft.com/library/dn469260.aspx) (SMA) tárolja, és a helyi adatközpontban, például az Orchestrator runbookok futtatásáért és Azure Automation, használja az ugyanazon integrációs modulok. A [Runbook Converter](#runbook-converter) alakítja át az Orchestrator-runbookok grafikus runbookok – amelyek nem támogatottak az SMA.  Továbbra is telepíthet a [szabványos tevékenységek modul](#standard-activities-module) és [System Center Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules) az SMA rendszerbe, azonban manuálisan kell [a runbookok újraírási](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó
Az Orchestrator Runbookjai tárolt adatbázis-kiszolgáló és runbook-kiszolgálók, mind a helyi adatközpontban futtathatók.  Az Azure Automation Runbookjai az Azure-felhőben vannak tárolva, és futtathatók a helyi adatok center használatával egy [hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md).  Ez a hogyan általában fog futtatni a runbookok konvertálni az Orchestratorból, mivel azok tervezték, hogy a helyi kiszolgálókon futnak.

## <a name="integration-pack-converter"></a>Integrációs csomag átalakító
Az integrációs csomag Converter konvertálja használatával létrehozott integrációs csomagokat a [az Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) alapján a Windows PowerShell parancsmag, amely importálhatók az Azure Automation integrációs modulok, vagy A Szolgáltatáskezelési automatizálás.  

Az integrációs csomag Converter futtatásakor megnyílik egy varázsló, amely lehetővé teszi, hogy válasszon ki egy integrációs csomag (oip) fájlt.  A varázsló majd felsorolja a tevékenységeket, integrációs csomag tartalmazza, és lehetővé teszi, hogy válassza ki, amely át lesz telepítve.  Amikor a varázsló végzett, az integrációs modul, amely tartalmazza a megfelelő parancsmag minden az eredeti integrációs csomag tevékenységei hoz létre.

### <a name="parameters"></a>Paraméterek
Egy integrációs csomag tevékenységének tulajdonságokat az integrációs modul a megfelelő parancsmag paramétereire konvertálja.  Windows PowerShell-parancsmagok egy halmaza rendelkezik [általános paraméterek](https://technet.microsoft.com/library/hh847884.aspx) , amely az összes parancsmag használható.  Például a - Verbose paramétert a kimenetben a működésével kapcsolatos részletes információkat a parancsmag eredményez.  Előfordulhat, hogy nincs parancsmag olyan paraméterre, amelynek a neve megegyezik egy általános paraméter.  Ha egy tevékenység neve megegyezik egy általános paraméter tulajdonságot, a varázsló kérni fogja, hogy a paraméter egy másik nevet.

### <a name="monitor-activities"></a>Tevékenységek monitorozása
A kezdés az Orchestrator runbookjai figyelése egy [figyelése tevékenység](https://technet.microsoft.com/library/hh403827.aspx) , és futtassa a folyamatosan Várakozás által egy adott esemény.  Az Azure Automation nem támogatja a figyelő a runbookok, így figyelő tevékenységeket az integrációs csomag nem lesz átalakítva.  Ehelyett egy helyőrző parancsmag az integrációs modul figyelése tevékenység jön létre.  Ez a parancsmag nem funkcióval rendelkezik, de lehetővé teszi a konvertált runbookok, amelyek segítségével telepítve.  Ez a forgatókönyv nem tudják futtatni az Azure Automationben, de úgy, hogy módosítja is telepíthető.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrációs csomagok, amely nem konvertálható
Nem OIT a létrehozott integrációs csomagokat nem lehet konvertálni az integrációs csomag Converter az. Is találhatók néhány eszköz, amely jelenleg nem konvertálható a Microsoft által biztosított integrációs csomagokat.  Ezeket az integrációs csomagokat a konvertált verzióit importálta [letöltésre](#system-center-orchestrator-integration-modules) , hogy az Azure Automation vagy a Service Management Automation telepíthető.

## <a name="standard-activities-module"></a>Szokásos tevékenységek modul
Az orchestrator foglal magába, [szokásos tevékenységek](https://technet.microsoft.com/library/hh403832.aspx) , amelyek nem szerepelnek az integrációs csomag, de sok runbookok által használt.  A szokásos tevékenységek modul az egyenértékű a parancsmag minden ezeket a tevékenységeket tartalmazó integrációs modul létrehozása.  Telepítenie kell az integrációs modul az Azure Automationben egy szabványos tevékenység konvertált runbookokat importálása előtt.

A konvertált forgatókönyvek is, a szokásos tevékenységek modul parancsmagjai használhatják valakit, aki ismeri az Orchestrator az Azure Automation új runbookok összeállításához.  A szokásos tevékenységek összes funkciójának parancsmagokkal hajtható végre, amíg azok eltérően működhet.  A konvertált szokásos tevékenységek modul parancsmagjai ugyanaz, mint a megfelelő tevékenységeket működik, és a ugyanazokat a paramétereket használja.  Ez segít az Azure Automation-runbookokkal való áttérés a meglévő Orchestrator runbook szerzője.

## <a name="system-center-orchestrator-integration-modules"></a>A System Center Orchestrator integrációs modulok
A Microsoft biztosít [integrációs csomagok](https://technet.microsoft.com/library/hh295851.aspx) automatizálásához a System Center-összetevő és az egyéb termékek runbookok készítéséhez.  Ezeket az integrációs csomagokat némelyike OIT jelenleg alapulnak, de nem jelenleg konvertálható integrációs modulok ismert problémák miatt.  [A System Center Orchestrator integrációs modulok](https://www.microsoft.com/download/details.aspx?id=49555) ezeket az integrációs csomagokat, amelyek az Azure Automation és a Service Management Automation importálhatók konvertált verzióját tartalmazza.  

Az eszköz RTM verzió az integrációs csomag Converter az átalakítható OIT alapján integrációs csomagok frissített verzióit teszi közzé.  Útmutatást is nyújtunk segítséget nyújt a runbookok az integrációs csomag nem alapján OIT alakítása.

## <a name="runbook-converter"></a>Runbook-átalakító
A Runbook átalakító konvertálja az Orchestrator runbookjai [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) , amely az Azure Automationbe importálhatók.  

Egy olyan parancsmaggal, nevű Runbook Converter valósul meg egy PowerShell-modul **ConvertFrom-SCORunbook** , amely végrehajtja az átalakítást.  Ha az eszköz telepíti, egy PowerShell-munkamenetet, amely betölti a parancsmag egy parancsikont hoz létre.   

Következő egy Orchestrator-runbook konvertálja és importálja azt az Azure Automation alapszintű során a rendszer.  A következő szakaszok további részletekkel szolgálnak az eszköz használatával, és a konvertált forgatókönyvek használata.

1. Egy vagy több runbook exportálhatja az Orchestrator.
2. A runbook minden tevékenység esetén integrációs modulok beszerzése.
3. Konvertálja az Orchestrator runbookjai az exportált fájlban.
4. Tekintse át a naplókban ellenőrzése az átalakítás és minden szükséges manuális feladatok meghatározása.
5. A konvertált runbookok importálása az Azure Automationbe.
6. Minden szükséges adategységek létrehozása az Azure Automationben.
7. Szerkessze a runbookot, az Azure Automationben módosításához szükséges tevékenységeket.

### <a name="using-runbook-converter"></a>Runbook Converter használatával
A szintaxist a **ConvertFrom-SCORunbook** a következő:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - exportálási átalakítani a runbookokat tartalmazó fájl elérési útját.
* Modul - vesszővel tagolt a runbookok tevékenységeket tartalmazó integrációs modulok listáját.
* E - hozhat létre a mappa elérési útját alakítja át a grafikus runbookokban.

A következő példaparancs a runbookok nevű exportálási fájlba konvertálja **MyRunbooks.ois_export**.  Ezek a runbookok használata az Active Directory és a Data Protection Manager integrációs csomagokat.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Naplófájlok
A Runbook átalakító a következő naplófájlokat hoz létre a konvertált runbook ugyanazon a helyen.  Ha a fájlok már léteznek, majd azok felülírja az utolsó átalakítás származó adatokkal.

| Fájl | Tartalom |
|:--- |:--- |
| Runbook Converter - Progress.log |Részletes lépéseket a beleértve minden egyes tevékenységhez konvertálása sikerült információkat és minden egyes tevékenységhez nem konvertálja figyelmeztetés átváltási. |
| Runbook Converter - Summary.log |Az utolsó átalakítás, beleértve a figyelmeztetéseket összegzése és követés e feladatok végrehajtásához, például a konvertált runbook szükséges változó létrehozásához szükséges. |

### <a name="exporting-runbooks-from-orchestrator"></a>Runbookok exportálása az Orchestratorból
A Runbook átalakító együttműködik a runbookokat tartalmazó Orchestrator exportálása fájlba.  Minden Orchestrator-runbook egy megfelelő Azure Automation-runbook hoz létre az exportált fájlban.  

Az Orchestrator runbook exportálásához kattintson a jobb gombbal a Runbook Designerben a runbook nevét, és válassza ki **exportálása**.  Exportálja az összes runbook egy mappában, kattintson a jobb gombbal a mappára, és válassza a neve **exportálása**.

### <a name="runbook-activities"></a>Runbook-tevékenységek
A Runbook átalakító az Orchestrator runbook egyes tevékenységeinek alakítja át a megfelelő tevékenységet az Azure Automationben.  Ezekre a tevékenységekre, amely nem konvertálható egy helyőrző tevékenység a runbookban figyelmeztető szöveg jön létre.  A konvertált runbook importálása az Azure Automationbe, után meg kell cserélnie e tevékenységek bármelyike érvényes a szükséges funkciókat ellátó tevékenységek.

Az Orchestrator tevékenységeket a [szabványos tevékenységek modul](#standard-activities-module) lesz átalakítva.  Van néhány szokásos Orchestrator tevékenységek, amelyek nem ebben a modulban azonban és nem lesznek konvertálva.  Ha például **Platformesemény küldése** nem nincs Azure Automation-megfelelője, mivel az esemény egy adott Orchestrator.

[Tevékenységek figyelése](https://technet.microsoft.com/library/hh403827.aspx) nem lesznek konvertálva, mert nincs hozzájuk megfelelője az Azure Automationben.  A kivétel a figyelő a tevékenységek [konvertálni az integrációs csomagok](#integration-pack-converter) , amely a helyőrző tevékenység lesz átalakítva.

Minden olyan tevékenységet egy [konvertálni az integrációs csomag](#integration-pack-converter) számmá alakítja, ha az integrációs modul elérési útját megadja a **modulok** paraméter.  A System Center integrációs csomagok esetén használhatja a [System Center Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Erőforrások az orchestrator programhoz
A Runbook átalakító csak a runbookok, nem más Orchestrator erőforrások, például számlálókat, változókat és kapcsolatok alakítja.  Számlálók nem támogatottak az Azure Automationben.  Változók és a kapcsolatok támogatottak, de manuálisan létre kell hoznia őket.  A naplófájlok fogja tájékoztatni Önt, ha a runbook igényel az ilyen erőforrások, és adja meg a megfelelő erőforrásokat hozhat létre az Azure Automationben a konvertált runbook megfelelő működéséhez szükséges.

Egy runbook például felhasználhatja egy változó feltölti egy adott érték egy tevékenységben.  A konvertált runbook tevékenység konvertálja, majd adjon meg egy változó objektum neve megegyezik az Orchestrator változó az Azure Automationben.  Ez lesz Megjegyzendő, a **Runbook Converter - Summary.log** az átalakítás után létrehozott fájl.  Manuálisan létrehozni a runbook használata előtt az Azure Automationben a változóeszköz kell.

### <a name="input-parameters"></a>Bemeneti paraméterek
Az Orchestrator Runbookjai bemeneti paramétereket fogadja el a **adatok inicializálása** tevékenység.  Ha a runbook alakít át tartalmazza ezt a tevékenységet, majd egy [bemeneti paraméter](automation-graphical-authoring-intro.md#runbook-input-and-output) az Azure Automation runbook jön létre a tevékenység minden egyes paraméterhez.  A [munkafolyamat-parancsprogram vezérlő](automation-graphical-authoring-intro.md#activities) tevékenységet hoz létre a konvertált runbook kérdezi le, és minden paraméter adja vissza.  A runbook tevékenységek bemeneti paramétert használó tekintse meg a kimeneti ezt a tevékenységet.

Ez a stratégia használt oka a leginkább tükrözi az a funkciók az Orchestrator runbook.  Az új grafikus runbookokban tevékenységek hivatkozzon közvetlenül a bemeneti paraméterek bemeneti Runbook adatforrást használ.

### <a name="invoke-runbook-activity"></a>Runbook meghívása tevékenységhez
Az Orchestrator Runbookjai elindíthatnak más runbookokat, és a **Runbook meghívása** tevékenység. Ha a runbook alakít át ezt a tevékenységet tartalmaz, és a **Várakozás a befejezésre** beállítás engedélyezve van, akkor a konvertált runbook, létrejön egy runbook-tevékenység.  Ha a **Várakozás a befejezésre** a beállítás nincs engedélyezve, akkor a munkafolyamat-parancsprogram-tevékenység létrehozása, amely **Start-AzureAutomationRunbook** a runbook elindításához.  Miután importálta a konvertált runbookot az Azure Automationbe, módosítania kell ezt a tevékenységet a tevékenységben megadott információkkal.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [A Szolgáltatáskezelési automatizálás](https://technet.microsoft.com/library/dn469260.aspx)
* [Hibrid Runbook-feldolgozó](automation-hybrid-runbook-worker.md)
* [Az orchestratorral összefüggő szokásos tevékenységek](https://technet.microsoft.com/library/hh403832.aspx)
* [Letöltés a System Center Orchestrator áttelepítési eszközkészlet](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
