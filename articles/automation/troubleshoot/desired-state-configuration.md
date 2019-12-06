---
title: A Azure Automation kívánt állapot-konfigurációval (DSC) kapcsolatos hibák elhárítása
description: Ez a cikk a kívánt állapot-konfiguráció (DSC) hibaelhárításával kapcsolatos információkat tartalmaz.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a45ed90b2b2c4a3a4f8eb11c4618c11e6d66761
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849360"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>A kívánt állapot konfigurációjának (DSC) hibáinak megoldása

Ez a cikk a kívánt állapot-konfigurációval (DSC) kapcsolatos hibák elhárításával kapcsolatos információkat tartalmaz.

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>A kívánt állapot-konfiguráció (DSC) hibaelhárításának lépései

Ha a konfigurációk az Azure állapot-konfigurációban való fordításával vagy üzembe helyezésével kapcsolatos hibákkal rendelkezik, néhány lépés a probléma diagnosztizálásához.

1. **Győződjön meg arról, hogy a konfiguráció sikeresen lefordításra került a helyi gépen:**  Az Azure állapot-konfiguráció a PowerShell DSC-re épül. A DSC nyelvét és szintaxisát a [POWERSHELL DSC docs](https://docs.microsoft.com/powershell/scripting/overview)dokumentációjában találja.

   A DSC-konfiguráció helyi gépen való fordításával felderítheti és elháríthatja a gyakori hibákat, például a következőket:

   - **Hiányzó modulok**
   - **Szintaktikai hibák**
   - **Logikai hibák**

2. **A csomóponton található DSC-naplók megtekintése:** Ha a konfiguráció sikeresen lefordítva, de a csomópontra való alkalmazása meghiúsul, részletes információkat talál a naplókban. További információ a DSC-naplók kereséséről: [Hol találhatók a DSC-eseménynaplók](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Emellett a [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) segíthet a DSC-naplók részletes adatainak elemzésében. Ha felveszi a kapcsolatot a támogatási szolgálattal, a rendszer ezeket a naplókat igényli a probléma diagnosztizálásához.

   A **xDscDiagnostics** -et a helyi gépen telepítheti a [STABLE verzió telepítése](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)című részben található utasítások segítségével.

   A **XDscDiagnostics** Azure-beli gépen való telepítéséhez használja az [az VM Run-Command](/cli/azure/vm/run-command) vagy a [meghívás-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). A portálon a **parancs futtatása** lehetőséget is használhatja a [Windows rendszerű virtuális gépen futtatott PowerShell-parancsfájlok futtatása](../../virtual-machines/windows/run-command.md)című cikkben ismertetett lépéseket követve.

   További információ a **xDscDiagnostics**használatáról: a [XDSCDIAGNOSTICS használata a DSC-naplók elemzéséhez](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), valamint a [xDscDiagnostics-parancsmagok](https://github.com/PowerShell/xDscDiagnostics#cmdlets)létrehozásához.
3. **Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület rendelkezik a szükséges modulokkal:** A kívánt állapot-konfiguráció a csomópontra telepített moduloktól függ.  Azure Automation állapot konfigurációjának használatakor importálja az Automation-fiókba a szükséges modulokat az [importálási modulok](../shared-resources/modules.md#import-modules)részben ismertetett lépések segítségével. A konfigurációk a modulok adott verzióihoz is tartozhatnak.  További információ: [modulok hibakeresése](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>A kívánt állapot konfigurációjának (DSC) használatakor előforduló gyakori hibák

### <a name="unsupported-characters"></a>Forgatókönyv: speciális karaktereket tartalmazó konfiguráció nem törölhető a portálról

#### <a name="issue"></a>Probléma

Amikor egy DSC-konfigurációt próbál meg törölni a portálról, a következő hibaüzenet jelenik meg:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Ok

Ez a hiba egy ideiglenes probléma, amelynek megoldódik a megoldása.

#### <a name="resolution"></a>Felbontás

* A konfiguráció törléséhez használja az az parancsmag "Remove-AzAutomationDscConfiguration" parancsot.
* A parancsmag dokumentációja még nem frissült.  Addig is tekintse meg a AzureRM modul dokumentációját.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Forgatókönyv: nem sikerült regisztrálni a DSC-ügynököt

#### <a name="issue"></a>Probléma

`Set-DscLocalConfigurationManager` vagy egy másik DSC-parancsmag futtatására tett kísérlet során a következő hibaüzenetet kapja:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Ok

Ezt a hibát általában egy tűzfal okozza, a gép a proxykiszolgáló mögött vagy más hálózati hiba miatt.

#### <a name="resolution"></a>Felbontás

Ellenőrizze, hogy a számítógép rendelkezik-e hozzáféréssel a Azure Automation DSC megfelelő végpontokhoz, és próbálkozzon újra. A szükséges portok és címek listáját itt tekintheti meg: [Network Planning](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Forgatókönyv: a csomópont "nem található" hiba miatt sikertelen állapotú.

#### <a name="issue"></a>Probléma

A csomópontnak van egy **sikertelen** állapotú jelentése, amely a következő hibát tartalmazza:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomópontot egy konfiguráció neveként (például ABC) rendeli hozzá a csomópont-konfiguráció neve helyett (például ABC. Webkiszolgáló).

#### <a name="resolution"></a>Felbontás

* Győződjön meg arról, hogy a csomópontot a "csomópont-konfiguráció neve" értékkel rendeli hozzá, nem pedig a "konfiguráció nevét".
* Csomópont-konfigurációt Azure Portal vagy PowerShell-parancsmaggal hozzárendelhet egy csomóponthoz.

  * Ha Azure Portal használatával szeretne csomópont-konfigurációt hozzárendelni egy csomóponthoz, nyissa meg a **DSC-csomópontok** lapot, majd válasszon ki egy csomópontot, és kattintson a **csomópont-konfiguráció kiosztása** gombra.
  * Csomópont-konfiguráció PowerShell-parancsmaggal való hozzárendeléséhez használja a **set-AzureRmAutomationDscNode** parancsmagot.

### <a name="no-mof-files"></a>Forgatókönyv: a konfiguráció lefordításakor a rendszer nem állított be csomópont-konfigurációkat (MOF-fájlokat)

#### <a name="issue"></a>Probléma

A DSC-fordítási feladata a következő hibával felfüggeszthető:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Ok

Ha a DSC-konfigurációban a **Node** kulcsszót követő kifejezés kiértékeli a `$null`, akkor a rendszer nem állít elő csomópont-konfigurációt.

#### <a name="resolution"></a>Felbontás

A következő megoldások bármelyike elháríthatja a problémát:

* Győződjön meg arról, hogy a konfigurációs definícióban a **csomópont** kulcsszava melletti kifejezés nem $nullra van kiértékelve.
* Ha átadja a ConfigurationData a konfiguráció fordításakor, győződjön meg arról, hogy átadja a konfiguráció által igényelt várt értékeket a [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Forgatókönyv: a DSC-csomópont jelentés beragad "folyamatban" állapotba

#### <a name="issue"></a>Probléma

A DSC-ügynök kimenetei:

```error
No instance found with given property values
```

#### <a name="cause"></a>Ok

Frissítette a WMF-verziót, és sérült a WMI.

#### <a name="resolution"></a>Felbontás

A probléma megoldásához kövesse a [DSC ismert problémák és korlátozások](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) című cikk utasításait.

### <a name="issue-using-credential"></a>Forgatókönyv: a hitelesítő adatok nem használhatók DSC-konfigurációban

#### <a name="issue"></a>Probléma

A DSC-fordítási feladatot felfüggesztette a következő hibával:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Ok

Egy konfigurációban használta a hitelesítő adatokat, de nem adott meg megfelelő **ConfigurationData** , hogy az egyes csomópont-konfigurációk esetében a **PSDscAllowPlainTextPassword** True értékre legyen állítva.

#### <a name="resolution"></a>Felbontás

* Győződjön meg arról, hogy a megfelelő **ConfigurationData** adja meg, hogy a konfigurációban említett minden egyes csomópont-konfiguráció esetében a **PSDscAllowPlainTextPassword** értéke igaz legyen. További információ: [eszközök Azure Automation DSC-ben](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Forgatókönyv: a DSC-bővítmény előkészítése, "hiba a bővítmény feldolgozásakor" hiba

#### <a name="issue"></a>Probléma

A DSC-bővítmény használatával történő előkészítés során a hibát tartalmazó hiba fordul elő:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomóponthoz olyan csomópont-konfigurációs nevet rendelnek, amely nem létezik a szolgáltatásban.

#### <a name="resolution"></a>Felbontás

* Győződjön meg arról, hogy a csomópontot olyan csomópont-konfiguráció nevével rendeli hozzá, amely pontosan megegyezik a szolgáltatásban szereplő névvel.
* Dönthet úgy is, hogy nem tartalmazza a csomópont-konfiguráció nevét, amely a csomópont bevezetését eredményezi, de nem rendel hozzá csomópont-konfigurációt.

### <a name="failure-linux-temp-noexec"></a>Forgatókönyv: konfiguráció alkalmazása Linuxon, hiba történt általános hiba esetén

#### <a name="issue"></a>Probléma

A Linux rendszerű konfiguráció alkalmazásakor a hibát tartalmazó hiba fordul elő:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Ok

Az ügyfelek azonosítottak, hogy ha a `/tmp` helye `noexec`ra van állítva, a DSC jelenlegi verziója nem fogja tudni alkalmazni a konfigurációkat.

#### <a name="resolution"></a>Felbontás

* Távolítsa el a `noexec` lehetőséget a `/tmp` helyről.

### <a name="compilation-node-name-overlap"></a>Forgatókönyv: az átfedésben lévő csomópont-konfigurációs nevek helytelen kiadást eredményezhetnek

#### <a name="issue"></a>Probléma

Ha egyetlen konfigurációs parancsfájlt használ több csomópontos konfiguráció létrehozásához, és néhány csomópont-konfigurációnak van egy olyan neve, amely mások részhalmaza, akkor a fordítási szolgáltatás egyik problémája a helytelen konfiguráció hozzárendelését eredményezheti.  Ez csak akkor történik meg, ha egyetlen parancsfájl használatával állít elő konfigurációkat egy csomóponton, és csak akkor, ha a név átfedésben van a karakterlánc elején.

Például, ha egyetlen konfigurációs szkriptet használ a konfigurációk létrehozásához a parancsmagok használatával szórótábla átadott csomópont-adat alapján, és a csomópont adatai tartalmazzák a "kiszolgáló" és a "1kiszolgáló üzemmódját" nevű kiszolgálót.

#### <a name="cause"></a>Ok

A fordítási szolgáltatással kapcsolatos ismert probléma.

#### <a name="resolution"></a>Felbontás

A legjobb megoldás a helyi vagy CI/CD-folyamat fordítása, és a MOF-fájlok feltöltése közvetlenül a szolgáltatásba.  Ha a szolgáltatás fordítása követelmény, a következő legjobb megoldás a fordítási feladatok felosztása, így nincs átfedés a nevek között.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
