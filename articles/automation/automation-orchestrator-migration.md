---
title: Migrálás a Orchestrator-ből a Azure Automationba
description: Ismerteti, hogyan lehet áttelepíteni a runbookok és az integrációs csomagokat a System Center Orchestrator a Azure Automationba.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: c7df6e31cd021fc61129131f9bd02acc7b96e2ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457552"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrálás a Orchestrator-ből a Azure Automationba (bétaverzió)

A [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) runbookok az integrációs csomagok azon tevékenységein alapulnak, amelyek kifejezetten a Orchestrator vannak írva, míg a Azure Automation a Windows powershellen alapulnak.  A Azure Automation [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) hasonló megjelenést mutatnak a Orchestrator runbookok, és azok tevékenységei a PowerShell-parancsmagokat, a gyermek runbookok és az eszközöket jelölik.

A [System Center Orchestrator áttelepítési eszközkészlet](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) olyan eszközöket tartalmaz, amelyek segítséget nyújtanak a Runbookok a Orchestrator-ből Azure Automation való átalakításában.  A runbookok átalakításán kívül az integrációs csomagokat olyan tevékenységekkel kell konvertálnia, amelyeket a runbookok a Windows PowerShell-parancsmagokkal rendelkező integrációs modulokhoz használ.  

A következőkben a Orchestrator-runbookok Azure Automationra való átalakításának alapszintű folyamata.  Ezeket a lépéseket az alábbi szakaszokban részletesen ismertetjük.

1. Töltse le a [System Center Orchestrator áttelepítési eszközkészletet](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) , amely tartalmazza az ebben a cikkben tárgyalt eszközöket és modulokat.
2. [Szabványos tevékenységek modul](#standard-activities-module) importálása Azure Automationba.  Ide tartoznak a szabványos Orchestrator-tevékenységek konvertált verziói, amelyeket a konvertált runbookok is használhatnak.
3. Importálja a [System Center Orchestrator integrációs Azure Automation modulokat](#system-center-orchestrator-integration-modules) a runbookok által a System Center eléréséhez használt integrációs csomagokba.
4. Alakítsa át az egyéni és a külső felek integrációs csomagjait az [integrációs csomag átalakító](#integration-pack-converter) használatával, és importálja Azure Automationba.
5. Alakítsa át a Orchestrator runbookok a [Runbook-átalakító](#runbook-converter) használatával, és telepítse a Azure Automation.
6. Manuálisan hozza létre a szükséges Orchestrator-eszközöket a Azure Automationban, mivel a Runbook átalakító nem konvertálja ezeket az erőforrásokat.
7. Egy [hibrid Runbook-feldolgozót](#hybrid-runbook-worker) konfigurálhat a helyi adatközpontban a helyi erőforrásokhoz hozzáférő konvertált runbookok futtatásához.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="service-management-automation"></a>Szolgáltatáskezelési automatizálás

[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) tárolja és futtatja a runbookok a helyi adatközpontban, például a Orchestrator, és ugyanazt az integrációs modult használja, mint Azure Automation. A [Runbook Converter](#runbook-converter) a Orchestrator runbookok a grafikus runbookok alakítja, de az SMA nem támogatott.  Továbbra is telepítheti a [standard tevékenységek modult](#standard-activities-module) és a [System Center Orchestrator integrációs modulokat](#system-center-orchestrator-integration-modules) SMA-ba, de manuálisan kell [újraírnia a runbookok](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó

A Orchestrator található runbookok adatbázis-kiszolgálón tárolódnak, és a helyi adatközpontban is futnak a runbook-kiszolgálókon.  A Azure Automationban található runbookok az Azure-felhőben tárolódnak, és a helyi adatközpontban futtathatók [hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md)használatával.  Így általában a Orchestrator-ből konvertált runbookok fog futni, mivel azok helyi kiszolgálókon való futtatásra lettek tervezve.

## <a name="integration-pack-converter"></a>Integrációs csomag átalakító

Az integrációs csomag átalakítója átalakítja a [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) használatával létrehozott integrációs csomagokat a Windows powershellen alapuló integrációs modulokba, amelyek Azure Automation vagy Service Management Automationba importálhatók.  

Az Integration Pack átalakító futtatásakor egy varázsló jelenik meg, amely lehetővé teszi az integrációs csomag (. OIP) fájljának kiválasztását.  A varázsló ezután felsorolja az integrációs csomagban szereplő tevékenységeket, és lehetővé teszi a migrálni kívánt tevékenységek kiválasztását.  A varázsló befejezése után létrehoz egy integrációs modult, amely tartalmazza a megfelelő parancsmagot az eredeti integrációs csomagban lévő összes tevékenységhez.

### <a name="parameters"></a>Paraméterek

Az integrációs csomagban lévő tevékenységek bármely tulajdonsága az integrációs modul megfelelő parancsmagjának paramétereinek megfelelően lesz konvertálva.  A Windows PowerShell-parancsmagok olyan [általános paraméterekkel](https://technet.microsoft.com/library/hh847884.aspx) rendelkeznek, amelyek az összes parancsmaggal használhatók. A-verbose paraméter például egy parancsmagot eredményez a művelet részletes adatainak kiírásához.  Egyetlen parancsmag sem rendelkezhet ugyanazzal a névvel, mint a Common paraméterrel. Ha egy tevékenységnek ugyanaz a neve, mint a közös paraméternek, a varázsló kérni fogja, hogy adjon meg egy másik nevet a paraméter számára.

### <a name="monitor-activities"></a>Tevékenységek monitorozása

A Orchestrator runbookok figyelése egy [figyelő tevékenységgel](https://technet.microsoft.com/library/hh403827.aspx) kezdődik, és folyamatosan várakozik, hogy egy adott esemény meghívja őket.  A Azure Automation nem támogatja a runbookok figyelését, így az integrációs csomagban lévő figyelési tevékenységek nem lesznek konvertálva.  Ehelyett helyőrző parancsmag jön létre a figyelő tevékenység integrációs moduljában.  Ez a parancsmag nem rendelkezik funkcionalitással, de lehetővé teszi az azt használó átalakított runbook telepítését.  Ez a runbook nem fog tudni futni Azure Automationban, de telepíthető, hogy módosítható legyen.

### <a name="integration-packs-that-cannot-be-converted"></a>Nem konvertálható integrációs csomagok

A nem a OIT-sel létrehozott integrációs csomagok nem alakíthatók át az integrációs csomag átalakítóval. A Microsoft olyan integrációs csomagokat is biztosít, amelyek jelenleg nem alakíthatók át ezzel az eszközzel.  Az integrációs csomagok konvertált verziói [letölthetők](#system-center-orchestrator-integration-modules) , így Azure Automation vagy Service Management Automation telepíthetik őket.

## <a name="standard-activities-module"></a>Standard szintű tevékenységek modul

A Orchestrator olyan [szabványos tevékenységeket](https://technet.microsoft.com/library/hh403832.aspx) tartalmaz, amelyek nem szerepelnek az integrációs csomagban, de számos runbookok használják.  A standard szintű tevékenységek modul egy integrációs modul, amely minden egyes tevékenységhez megfelelő parancsmagot tartalmaz.  A standard tevékenységeket használó konvertált runbookok importálása előtt telepítenie kell ezt az integrációs modult Azure Automation.

Az átalakított runbookok támogatásán kívül a standard szintű tevékenységek modulban található parancsmagok a Orchestrator által ismert, új runbookok létrehozására használhatók a Azure Automationban.  Habár az összes szabványos tevékenység funkcióit parancsmagokkal is elvégezheti, különböző műveleteket hajthat végre.  Az átalakított szabványos tevékenységek modul parancsmagjai ugyanúgy fognak működni, mint a kapcsolódó tevékenységek, és ugyanazokat a paramétereket használják.  Ez segíthet a meglévő Orchestrator-runbook létrehozásában, hogy áttérjenek Azure Automation runbookok.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator integrációs modulok

A Microsoft [integrációs csomagokat](https://technet.microsoft.com/library/hh295851.aspx) biztosít a System Center összetevőinek és más termékeinek automatizálására szolgáló runbookok létrehozásához.  Ezen integrációs csomagok némelyike jelenleg a OIT alapul, de az ismert problémák miatt jelenleg nem alakítható át integrációs modulokra.  A [System Center Orchestrator integrációs moduljai](https://www.microsoft.com/download/details.aspx?id=49555) a következő integrációs csomagok konvertált verzióit tartalmazzák, amelyek importálhatók Azure Automationba és Service Management Automationba.  

Az eszköz RTM-verziója az integrációs csomagok az integrációs csomag átalakítóval konvertált OIT alapuló frissített verzióit teszi közzé.  Útmutatást nyújt a runbookok a OIT-alapú integrációs csomagokból származó tevékenységekkel való átalakításához is.

## <a name="runbook-converter"></a>Runbook-átalakító

A Runbook Converter a Orchestrator runbookok olyan [grafikus runbookok](automation-runbook-types.md#graphical-runbooks) alakítja át, amely importálható a Azure Automationba.  

A Runbook Converter egy PowerShell-modulként van megvalósítva, `ConvertFrom-SCORunbook` amely egy, az átalakítást végző parancsmagot tartalmaz.  Az eszköz telepítésekor a rendszer létrehozza a parancsmagot betöltő PowerShell-munkamenetre mutató parancsikont.   

A következő egy Orchestrator-runbook átalakításának alapszintű folyamata, és a Azure Automationba való importálása.  A következő szakaszokban további információkat talál az eszköz használatáról és a konvertált runbookok való működésről.

1. Egy vagy több runbookok exportálása a Orchestrator-ből.
2. Integrációs modulok beszerzése az runbook lévő összes tevékenységhez.
3. Alakítsa át a Orchestrator runbookok az exportált fájlban.
4. Az átalakítás ellenőrzéséhez és a szükséges manuális feladatok meghatározásához tekintse át a naplók információit.
5. Konvertált runbookok importálása Azure Automationba.
6. Hozza létre a szükséges eszközöket a Azure Automationban.
7. A szükséges tevékenységek módosításához szerkessze Azure Automation runbook.

### <a name="using-runbook-converter"></a>A Runbook Converter használata

A **ConvertFrom-SCORunbook** szintaxisa a következő:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – a konvertálandó runbookok tartalmazó exportfájl elérési útja.
* Modul – a runbookok lévő tevékenységeket tartalmazó integrációs modulok vesszővel tagolt listája.
* OutputFolder – a mappa elérési útja a konvertált grafikus runbookok létrehozásához.

A következő példában szereplő parancs átalakítja a runbookok egy **MyRunbooks. ois_export**nevű exportálási fájlba.  Ezek a runbookok a Active Directory és Data Protection Manager integrációs csomagokat használják.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Naplófájlok

A Runbook Converter a következő naplófájlokat hozza létre a konvertált Runbook megegyező helyen.  Ha a fájlok már léteznek, azok felül lesznek írva az utolsó konverzió információi alapján.

| Fájl | Tartalom |
|:--- |:--- |
| Runbook-átalakító – Progress. log |Az átalakítás részletes lépései, beleértve az egyes tevékenységek átalakított és figyelmeztetési információit, amelyeket a rendszer nem konvertált. |
| Runbook-átalakító – összefoglalás. log |Az utolsó konverzió összefoglalása, beleértve az összes figyelmeztetést és nyomon követő feladatokat, amelyeket el kell végeznie, például egy változó létrehozásához, amely szükséges a konvertált runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Runbookok exportálása a Orchestrator-ből

A Runbook-átalakító egy vagy több runbookok tartalmazó Orchestrator származó exportálási fájllal működik.  Létrehoz egy megfelelő Azure Automation runbook az exportálási fájl minden egyes Orchestrator-runbook.  

A runbook Orchestrator-ből való exportálásához kattintson a jobb gombbal a runbook nevére a Runbook Designer, majd válassza az **Exportálás**lehetőséget.  A mappában lévő összes runbookok exportálásához kattintson a jobb gombbal a mappa nevére, és válassza az **Exportálás**lehetőséget.

### <a name="runbook-activities"></a>Runbookban levő tevékenységek

A Runbook Converter a Orchestrator Runbook minden tevékenységét egy megfelelő tevékenységre konvertálja Azure Automationban.  Azon tevékenységek esetében, amelyek nem alakíthatók át, a runbook figyelmeztető szöveggel hozza létre a helyőrző tevékenységet.  A konvertált runbook Azure Automationba való importálása után a tevékenységek bármelyikét le kell cserélnie a szükséges funkciókat végrehajtó érvényes tevékenységekkel.

A [standard szintű tevékenységek modulban](#standard-activities-module) minden Orchestrator-tevékenység át lesz konvertálva.  Vannak olyan általános Orchestrator tevékenységek, amelyek nem szerepelnek ebben a modulban, de nem lesznek konvertálva.  Például nincs Azure Automation `Send Platform Event` egyenértékű, mert az esemény a Orchestrator jellemző.

A [figyelési tevékenységeket](https://technet.microsoft.com/library/hh403827.aspx) a rendszer nem konvertálja, mert a Azure Automationban nincsenek egyenértékűek.  A kivétel a [átalakított integrációs csomagokban](#integration-pack-converter) a helyőrző tevékenységre konvertált tevékenységek figyelése.

Az [átalakított integrációs csomagból](#integration-pack-converter) származó tevékenységek akkor lesznek átalakítva, ha az integrációs modul `modules` elérési útját megadja a paraméterrel. A System Center integrációs csomagjaihoz használhatja a [System Center Orchestrator integrációs modulokat](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Orchestrator-erőforrások

A Runbook Converter csak a runbookok konvertálja, nem más Orchestrator-erőforrásokat, például számlálókat, változókat vagy kapcsolatokat.  Azure Automation nem támogatottak a számlálók.  A változók és a kapcsolatok támogatottak, de ezeket manuálisan kell létrehoznia.  A naplófájlok arról tájékoztatnak, ha a runbook szüksége van ilyen erőforrásokra, és meg kell határoznia azokat a megfelelő erőforrásokat, amelyeket létre kell hoznia Azure Automation a konvertált runbook megfelelő működéséhez.

Előfordulhat például, hogy egy runbook változót használ egy tevékenység egy adott értékének feltöltéséhez.  Az átalakított runbook átalakítja a tevékenységet, és megad egy változó adategységet Azure Automation ugyanazzal a névvel, mint a Orchestrator változó.  Ezt a rendszer az átalakítás után létrehozott **Runbook Converter-Summary. log** fájlban fogja megjegyezni.  A runbook használata előtt manuálisan létre kell hoznia ezt a változót a Azure Automationban.

### <a name="input-parameters"></a>Bemeneti paraméterek

A Orchestrator runbookok fogadja a bemeneti paramétereket `Initialize Data` a tevékenységgel.  Ha az átalakítás alatt álló runbook tartalmazza ezt a tevékenységet, akkor a rendszer a tevékenység minden paramétere számára létrehoz egy [bemeneti paramétert](automation-graphical-authoring-intro.md#runbook-input-and-output) a Azure Automation runbook.  A rendszer létrehoz egy [munkafolyamat-parancsfájl-vezérlési](automation-graphical-authoring-intro.md#activities) tevékenységet a konvertált runbook, amely az egyes paramétereket lekéri és visszaadja.  A runbook összes olyan tevékenysége, amely egy bemeneti paramétert használ, a tevékenység kimenetére hivatkozik.

Ennek a stratégiának az az oka, hogy a legjobban tükrözze a Orchestrator runbook funkcióit.  Az új grafikus runbookok lévő tevékenységeknek közvetlenül a bemeneti paraméterekre kell hivatkoznia egy Runbook bemeneti adatforrás használatával.

### <a name="invoke-runbook-activity"></a>Runbook-tevékenység meghívása

A runbookok a Orchestrator más runbookok indítanak `Invoke Runbook` el a tevékenységgel. Ha az átalakítás alatt álló runbook tartalmazza ezt a tevékenységet `Wait for completion` , és a beállítás be van állítva, akkor létrejön egy runbook-tevékenység a konvertált runbook.  Ha a `Wait for completion` beállítás nincs megadva, akkor létrejön egy munkafolyamat-parancsfájl tevékenység, amely a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) használatával indítja el a runbook. A konvertált runbook Azure Automationba való importálása után módosítania kell a tevékenységet a tevékenységben megadott adatokkal.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Szolgáltatáskezelési automatizálás](https://technet.microsoft.com/library/dn469260.aspx)
* [hibrid runbook-feldolgozó](automation-hybrid-runbook-worker.md)
* [Orchestrator standard tevékenységek](https://technet.microsoft.com/library/hh403832.aspx)
* [A System Center Orchestrator áttelepítési eszközkészlet letöltése](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

