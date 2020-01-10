---
title: A Azure Automation kívánt állapot konfigurációjának (DSC) hibáinak megoldása
description: Ez a cikk a kívánt állapot-konfiguráció (DSC) hibaelhárításával kapcsolatos információkat tartalmaz.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3c3c9950aab9a5a422ebc9e858daded2888fd82e
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834262"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>A Azure Automation kívánt állapot-konfigurációval (DSC) kapcsolatos hibák elhárítása

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

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>forgatókönyv: az állapotjelentések a "jogosulatlan" válaszüzenetet adják vissza.

#### <a name="issue"></a>Probléma

Ha állapot-konfigurációval (DSC) regisztrál egy csomópontot, a következő hibaüzenetek egyike jelenik meg:

```error
The attempt to send status report to the server https://{your automation account url}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Ok

Ezt a problémát rossz vagy lejárt tanúsítvány okozza.  További információ: [tanúsítvány lejárata és újraregisztrálása](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration).

### <a name="resolution"></a>Felbontás

Az alábbi lépések végrehajtásával regisztrálja újra a sikertelen DSC-csomópontot.

Először törölje a csomópont regisztrációját a következő lépésekkel.

1. A Azure Portal a **Home** -> Automation- **fiókok**– > {az Automation-fiókja} – > **állapot konfigurálása (DSC)**
2. Kattintson a "csomópontok" elemre, majd kattintson a problémával rendelkező csomópontra.
3. Kattintson a "regisztráció megszüntetése" gombra a csomópont regisztrációjának megszüntetéséhez.

Másodszor, távolítsa el a DSC-bővítményt a csomópontból.

1. A Azure Portal a **kezdőlap** -> **virtuális gép** – > {meghiúsult csomópont} – > **bővítmények**
2. Kattintson a "Microsoft. PowerShell. DSC" elemre.
3. A PowerShell DSC-bővítmény eltávolításához kattintson az Eltávolítás gombra.

Harmadszor, távolítsa el az összes hibás vagy lejárt tanúsítványt a csomópontból.

Futtassa a következőt a hibás csomóponton egy rendszergazda jogú PowerShell-parancssorból:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

Végül a következő lépésekkel regisztrálja újra a hibás csomópontot.

1. A Azure Portal a **Home** -> Automation- **fiókok** – > {az Automation-fiókja} – > **állapot konfigurálása (DSC)**
2. Kattintson a "csomópontok" elemre.
3. Kattintson a Hozzáadás gombra.
4. Válassza a hibás csomópontot.
5. Kattintson a "kapcsolat" elemre, és válassza ki a kívánt beállításokat.

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

### <a name="cross-subscription"></a>Forgatókönyv: egy csomópont PowerShell-lel való regisztrálása a következő hibaüzenetet adja vissza: "egy vagy több hiba történt"

#### <a name="issue"></a>Probléma

Amikor `Register-AzAutomationDSCNode` vagy `Register-AzureRMAutomationDSCNode`használatával regisztrál egy csomópontot, a következő hibaüzenet jelenik meg.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha olyan csomópontot próbál meg regisztrálni, amely az Automation-fióktól eltérő előfizetésben él.

#### <a name="resolution"></a>Felbontás

Az előfizetési csomópontot úgy kezelje, mintha egy különálló felhőben vagy helyszíni módon él.

A csomópont regisztrálásához kövesse az alábbi lépéseket.

* Windows – [fizikai/virtuális Windows rendszerű számítógépek a helyszínen, vagy az Azure-tól/AWS-től eltérő felhőben](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux- [fizikai/virtuális Linux rendszerű számítógépek a helyszínen, vagy az Azure-on kívül más felhőben is](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="agent-has-a-problem"></a>Forgatókönyv: hibaüzenet – "kiépítés sikertelen"

#### <a name="issue"></a>Probléma

Csomópont regisztrálása esetén a következő hibaüzenet jelenik meg:

```error
Provisioning has failed
```

#### <a name="cause"></a>Ok

Ez az üzenet akkor fordul elő, ha kapcsolati probléma van a csomópont és az Azure között.

#### <a name="resolution"></a>Felbontás

Döntse el, hogy a csomópont egy magánhálózati virtuális hálózaton van-e, vagy más problémái vannak az Azure-hoz való csatlakozással.

További információ: [hibák elhárítása a megoldások](onboarding.md)bevezetése során.

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
