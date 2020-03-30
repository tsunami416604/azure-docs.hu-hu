---
title: Áttelepítés az Orchestratorról az Azure Automationbe
description: Bemutatja, hogyan telepítheti át a runbookokat és az integrációs csomagokat a System Center Orchestratorból az Azure Automationbe.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 528b961ca07ec86ad502ee1b589772e354564a3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421682"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Áttelepítés az Orchestratorról az Azure Automationbe (béta)
Runbookok a [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) alapulnak tevékenységek integrációs csomagok, amelyek kifejezetten az Orchestrator, míg runbookok az Azure Automation alapulnak a Windows PowerShell.  Az Azure Automation [ben lévő grafikus runbookok](automation-runbook-types.md#graphical-runbooks) megjelenése hasonló az Orchestrator runbookokhoz, amelyek a PowerShell-parancsmagokat, a gyermekrunbookokat és az eszközöket képviselő tevékenységeikkel rendelkeznek.

A [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) olyan eszközöket tartalmaz, amelyek segítséget nyújtanak a runbookok Orchestratorról az Azure Automationbe történő konvertálásához.  A runbookok átalakítása mellett maguknak a runbookokon kell konvertálnia az integrációs csomagokat a runbookok által használt tevékenységekkel a Windows PowerShell-parancsmagokkal való integrációs modulokká.  

A következőkben az Orchestrator runbookok Azure Automation-be történő konvertálásának alapvető folyamata látható.  A lépések mindegyikét részletesen ismertetjük az alábbi szakaszokban.

1. Töltse le a [System Center Orchestrator Áttelepítési eszközkészletet,](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) amely a cikkben tárgyalt eszközöket és modulokat tartalmazza.
2. [Standard tevékenységek modul importálása](#standard-activities-module) az Azure Automationbe.  Ez magában foglalja a konvertált runbookok által használható szabványos Orchestrator-tevékenységek konvertált verzióit.
3. System [Center Orchestrator integrációs modulok importálása az](#system-center-orchestrator-integration-modules) Azure Automationbe a System Center-t elérő runbookok által használt integrációs csomagok hoz.
4. Alakítson ki egyéni és harmadik féltől származó integrációs csomagokat az [integrációs csomagváltóval,](#integration-pack-converter) és importáljon az Azure Automationbe.
5. Alakítsa ki az Orchestrator runbookokat a [Runbook Converter](#runbook-converter) használatával, és telepítse az Azure Automationben.
6. Manuálisan hozza létre a szükséges Orchestrator-eszközöket az Azure Automationben, mivel a Runbook Converter nem konvertálja ezeket az erőforrásokat.
7. Konfigurálja a [hibrid Runbook-feldolgozót](#hybrid-runbook-worker) a helyi adatközpontban a helyi erőforrásokeléréséhez hozzáférő konvertált runbookok futtatásához.

## <a name="service-management-automation"></a>Szolgáltatáskezelési automatizálás
[A Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) a helyi adatközpontban , például az Orchestratorban tárolja és futtatja a runbookokat, és ugyanazokat az integrációs modulokat használja, mint az Azure Automation. A [Runbook Converter](#runbook-converter) konvertálja Orchestrator runbookok grafikus runbookok, bár amelyek nem támogatottak az SMA.  Továbbra is telepítheti a [Szabványos tevékenységek modul](#standard-activities-module) és a System Center [Orchestrator integrációs modulok](#system-center-orchestrator-integration-modules) SMA, de manuálisan kell [átírni a runbookok](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó
Az Orchestrator runbookjait egy adatbázis-kiszolgáló tárolja, és runbook-kiszolgálókon futtatja, mind a helyi adatközpontban.  Az Azure Automation runbookjait az Azure-felhőtárolja, és a helyi adatközpontban futtathatók egy [hibrid runbook-feldolgozó](automation-hybrid-runbook-worker.md)használatával.  Ez hogyan lesz általában fuss runbooks konvertált Orchestrator mivel azok célja, hogy fut a helyi kiszolgálókon.

## <a name="integration-pack-converter"></a>Integrációs csomag konverter
Az integrációs csomagkonverter az [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) használatával létrehozott integrációs csomagokat az Azure Automation vagy a Service Management Automation alkalmazásba importálható Windows PowerShell alapú integrációs modulokká alakítja.  

Az integrációs csomagkonverter futtatásakor megjelenik egy varázsló, amely lehetővé teszi az integrációs csomag (.oip) fájl kiválasztását.  A varázsló ezután felsorolja az integrációs csomagban található tevékenységeket, és lehetővé teszi az áttelepítésre kerülő tevékenységek kiválasztását.  A varázsló befejezésekor létrehoz egy integrációs modult, amely az eredeti integrációs csomag minden egyes tevékenységéhez tartalmaz egy megfelelő parancsmacsat.

### <a name="parameters"></a>Paraméterek
Az integrációs csomagban lévő tevékenység bármely tulajdonsága az integrációs modul megfelelő parancsmagparamétereinek paramétereivé alakul át.  A Windows PowerShell-parancsmagok [olyan gyakori paraméterekkel](https://technet.microsoft.com/library/hh847884.aspx) rendelkeznek, amelyek az összes parancsmaggal használhatók.  A -Verbose paraméter például azt eredményezi, hogy egy parancsmag részletes információkat ad ki a működéséről.  Egyetlen parancsmagnak sem lehet olyan paramétere, amelynek neve megegyezik a közös paraméter nevével.  Ha egy tevékenység nek ugyanaz a neve, mint egy általános paraméternek, a varázsló kérni fogja, hogy adjon meg egy másik nevet a paraméternek.

### <a name="monitor-activities"></a>Tevékenységek monitorozása
Az Orchestrator runbookok figyelése [egy figyelőtevékenységgel](https://technet.microsoft.com/library/hh403827.aspx) kezdődik, és folyamatosan fut, várva egy adott esemény meghívására.  Az Azure Automation nem támogatja a monitor runbookok, így az integrációs csomag figyelő tevékenységei nem konvertálódnak.  Ehelyett egy helyőrző parancsmag jön létre a figyelőtevékenység integrációs moduljában.  Ez a parancsmag nem rendelkezik funkciókkal, de lehetővé teszi az azt használó konvertált runbookok telepítését.  Ez a runbook nem fog tudni futni az Azure Automationben, de telepíthető, így módosíthatja azt.

### <a name="integration-packs-that-cannot-be-converted"></a>Nem konvertálható integrációs csomagok
Az OIT-tal nem létrehozott integrációs csomagok nem konvertálhatók az Integrációs csomag konverterrel. Vannak olyan integrációs csomagok is, amelyeket a Microsoft jelenleg nem konvertálhat ezzel az eszközzel.  Ezen integrációs csomagok konvertált verziói [letölthetők,](#system-center-orchestrator-integration-modules) így telepíthetők az Azure Automation vagy a Service Management Automation szolgáltatásba.

## <a name="standard-activities-module"></a>Szabványos tevékenységek modul
Az Orchestrator olyan [szabványos tevékenységeket](https://technet.microsoft.com/library/hh403832.aspx) tartalmaz, amelyek nem szerepelnek az integrációs csomagban, de számos runbook használja őket.  A Standard Activities modul egy integrációs modul, amely minden egyes tevékenységhez tartalmaz egy parancsmag-egyenértéket.  Az integrációs modult telepítenie kell az Azure Automationben, mielőtt importálna egy szabványos tevékenységet használó konvertált runbookokat.

Az átalakított runbookok támogatása mellett a szabványos tevékenységek modul parancsmagjait az Orchestrator tanusítványai is használhatják új runbookok létrehozásához az Azure Automationben.  Bár az összes szabványos tevékenység funkciója parancsmagokkal elvégezhető, eltérően működhetnek.  Az átalakított szabványos tevékenységek modul parancsmagjai ugyanúgy működnek, mint a megfelelő tevékenységek, és ugyanazokat a paramétereket használják.  Ez segíthet a meglévő Orchestrator runbook szerzője az Azure Automation runbookok való áttérés.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator integrációs modulok
A Microsoft [integrációs csomagokat](https://technet.microsoft.com/library/hh295851.aspx) biztosít a Runbookok készítéséhez a System Center-összetevők és egyéb termékek automatizálásához.  Ezen integrációs csomagok némelyike jelenleg oit-alapú, de ismert problémák miatt jelenleg nem konvertálható integrációs modulokká.  [A System Center Orchestrator integrációs modulok](https://www.microsoft.com/download/details.aspx?id=49555) ezen integrációs csomagok konvertált verzióit tartalmazzák, amelyek importálhatók az Azure Automation és service Management Automation alkalmazásba.  

Az eszköz RTM verziójával közzétesszük az integrációs csomagok oit-alapú frissített verzióit, amelyek az Integrációs Csomag konverterrel konvertálhatók.  Útmutatást is kap, hogy segítséget nyújtson a runbookok konvertálásában az integrációs csomagok nem oit-alapú tevékenységek használatával.

## <a name="runbook-converter"></a>Runbook konverter
A Runbook Converter konvertálja az Orchestrator runbookokat [grafikus runbookokká,](automation-runbook-types.md#graphical-runbooks) amelyek importálhatók az Azure Automationbe.  

Runbook Converter valósítja meg a PowerShell-modul egy parancsmag nevű **ConvertFrom-SCORunbook,** amely végrehajtja az átalakítást.  Az eszköz telepítésekor létrehoz egy parancsikont egy PowerShell-munkamenethez, amely betölti a parancsmast.   

A következőkben az Orchestrator runbook átalakításának és az Azure Automationbe importálásának alapvető folyamata látható.  A következő szakaszok további részleteket tartalmaznak az eszköz használatával és a konvertált runbookokkal való munkáról.

1. Exportáljon egy vagy több runbookot az Orchestratorból.
2. Integrációs modulok beszerzése a runbook összes tevékenységéhez.
3. Konvertálja az Orchestrator runbookokat az exportált fájlban.
4. Tekintse át a naplókban szereplő információkat az átalakítás érvényesítéséhez és a szükséges manuális feladatok meghatározásához.
5. Importálta a konvertált runbookokat az Azure Automationbe.
6. Hozza létre a szükséges eszközöket az Azure Automationben.
7. A runbook az Azure Automationben a szükséges tevékenységek módosításához.

### <a name="using-runbook-converter"></a>Runbook converter használata
A **ConvertFrom-SCORunbook** szintaxisa a következő:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – A konvertálandó runbookokat tartalmazó exportálási fájl elérési útja.
* Modul – Vesszővel tagolt lista a runbookokban lévő tevékenységeket tartalmazó integrációs modulok listájáról.
* OutputFolder - A konvertált grafikus runbookok létrehozásához a mappa elérési útja.

A következő példa parancs átalakítja a runbookokat egy **MyRunbooks.ois_export**nevű exportfájlban.  Ezek a runbookok az Active Directory és az Adatvédelmi kezelő integrációs csomagjait használják.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Naplófájlok
A Runbook Converter a következő naplófájlokat hozza létre ugyanazon a helyen, mint a konvertált runbook.  Ha a fájlok már léteznek, akkor felülírják őket az utolsó konverzióból származó adatokkal.

| Fájl | Tartalom |
|:--- |:--- |
| Runbook Converter - Progress.log |Az átalakítás részletes lépései, beleértve a sikeresen átalakított tevékenységekre vonatkozó információkat, valamint a nem konvertált tevékenységekre vonatkozó figyelmeztetést. |
| Runbook Converter - Summary.log |Az utolsó konverzió összegzése, beleértve a figyelmeztetéseket és a nyomon követési feladatokat, amelyeket végre kell hajtania, például a konvertált runbookhoz szükséges változó létrehozását. |

### <a name="exporting-runbooks-from-orchestrator"></a>Runbookok exportálása az Orchestratorból
A Runbook Converter egy vagy több runbookot tartalmazó Orchestrator-exportálási fájllal működik.  Létrehoz egy megfelelő Azure Automation-runbookot az exportfájlban lévő minden Orchestrator runbookhoz.  

Ha runbookot szeretne exportálni az Orchestrator ból, kattintson a jobb gombbal a runbook nevére a Runbook Designer alkalmazásban, és válassza az **Exportálás parancsot.**  A mappában lévő összes runbook exportálásához kattintson a jobb gombbal a mappa nevére, és válassza az **Exportálás parancsot.**

### <a name="runbook-activities"></a>Runbookban levő tevékenységek
A Runbook Converter konvertálja az Orchestrator runbook minden egyes tevékenységét az Azure Automation megfelelő tevékenységé.  Azon tevékenységek esetében, amelyek nem konvertálhatók, egy helyőrző tevékenység jön létre a runbook figyelmeztető szöveggel.  Miután importálta az átalakított runbookot az Azure Automationbe, le kell cserélnie ezeket a tevékenységeket a szükséges funkciókat végző érvényes tevékenységekre.

A [Standard Activities modulban](#standard-activities-module) lévő orchestrator-tevékenységek konvertálódnak.  Vannak olyan szabványos Orchestrator-tevékenységek, amelyek azonban nem szerepelnek ebben a modulban, és nem konvertálódnak.  Például **a Küldő platformesemény** nem rendelkezik Azure Automation-megfelelővel, mivel az esemény az Orchestrator-ra jellemző.

[A figyelő tevékenységek](https://technet.microsoft.com/library/hh403827.aspx) nem konvertálódnak, mivel az Azure Automationben nincs egyenértékű velük.  A kivétel a [konvertált integrációs csomagokban](#integration-pack-converter) lévő tevékenységek figyelése, amelyeket a rendszer helyőrző tevékenységgé alakít át.

A [konvertált integrációs csomagból](#integration-pack-converter) származó tevékenységek konvertálódnak, ha megadja az **integrációs** modul elérési útját a modul paraméterrel.  A System Center integrációs csomagok hoz használhatja a [System Center Orchestrator integrációs modulokat.](#system-center-orchestrator-integration-modules)

### <a name="orchestrator-resources"></a>Orchestrator erőforrások
A Runbook Converter csak konvertálja runbookok, nem más Orchestrator-erőforrások, például számlálók, változók vagy kapcsolatok.  Számlálók nem támogatottak az Azure Automation.  A változók és a kapcsolatok támogatottak, de manuálisan kell létrehozniőket.  A naplófájlok tájékoztatja, ha a runbook ilyen erőforrásokat igényel, és adja meg a megfelelő erőforrásokat, amelyeket létre kell hoznia az Azure Automationben a konvertált runbook megfelelő működéséhez.

Például egy runbook egy változó használatával egy adott érték feltöltésére egy tevékenységben.  A konvertált runbook konvertálja a tevékenységet, és megad egy változóeszközt az Azure Automationben az Orchestrator változó nevével megegyező névvel.  Ezt a **Runbook Converter - Summary.log** fájlban kell megjegyezni, amely az átalakítás után jön létre.  A runbook használata előtt manuálisan kell létrehoznia ezt a változóeszközt az Azure Automationben.

### <a name="input-parameters"></a>Bemeneti paraméterek
Runbooks az Orchestrator fogadja el a bemeneti paramétereket az **Adatok inicializálása** tevékenység.  Ha a konvertálandó runbook tartalmazza ezt a tevékenységet, majd az Azure Automation-runbook [bemeneti paraméter](automation-graphical-authoring-intro.md#runbook-input-and-output) jön létre a tevékenység minden egyes paraméteréhez.  A [munkafolyamat-parancsfájl vezérlőtevékenység](automation-graphical-authoring-intro.md#activities) jön létre a konvertált runbook, amely beolvassa, és visszaadja az egyes paramétereket.  A runbookban egy bemeneti paramétert használó tevékenységek a tevékenység kimenetére hivatkoznak.

Ennek a stratégiának az az oka, hogy a legjobban tükrözze az Orchestrator runbook funkcióit.  Az új grafikus runbookokban végzett tevékenységeknek közvetlenül a Runbook bemeneti adatforráshasználatával történő bemeneti paraméterekre kell vonatkozniuk.

### <a name="invoke-runbook-activity"></a>Runbook-tevékenység meghívása
Runbookok az Orchestrator-ban indítsa el a többi runbookok a **Runbook-tevékenység meghívása.** Ha a konvertált runbook tartalmazza ezt a tevékenységet, és a Várakozás a **befejezésre** beállítás be van állítva, majd egy runbook-tevékenység jön létre a konvertált runbookban.  Ha a **Várakozás a befejezésre** beállítás nincs beállítva, akkor létrejön egy munkafolyamat-parancsfájl-tevékenység, amely **a Start-AzureAutomationRunbook** használatával indítja el a runbookot.  Miután importálta az átalakított runbookot az Azure Automationbe, módosítania kell ezt a tevékenységet a tevékenységben megadott adatokkal.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Szolgáltatáskezelési automatizálás](https://technet.microsoft.com/library/dn469260.aspx)
* [hibrid runbook-feldolgozó](automation-hybrid-runbook-worker.md)
* [Orchestrator Standard tevékenységek](https://technet.microsoft.com/library/hh403832.aspx)
* [A System Center Orchestrator áttelepítési eszközkészletének letöltése](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

