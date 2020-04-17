---
title: Az Azure Automation kívánt állapotkonfigurációjának (DSC) hibáinak elhárítása
description: Ez a cikk a kívánt állapotkonfiguráció (DSC) hibaelhárításáról nyújt tájékoztatást
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f0034d06c852d4d71284093879a456d296d98715
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457467"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Az Azure Automation kívánt állapotkonfigurációjával (DSC) kapcsolatos problémák elhárítása

Ez a cikk a Kívánt állapotkonfiguráció (DSC) szolgáltatással kapcsolatos hibaelhárítási problémákról nyújt tájékoztatást.

## <a name="diagnosing-an-issue"></a>Probléma diagnosztizálása

Ha hibákat észlel a konfigurációk összeállítása kor vagy üzembe helyezése az Azure State Configuration alkalmazásban, íme néhány lépés a probléma diagnosztizálásához.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Győződjön meg arról, hogy a konfiguráció fordítása sikeresen megtörtént a helyi számítógépen

Az Azure State Configuration a PowerShell DSC-re épül. A DSC nyelv és szintaxis dokumentációját a [PowerShell DSC-dokumentumokban](https://docs.microsoft.com/powershell/scripting/overview)találja.

A DSC-konfiguráció helyi számítógépen való összeállításával felderítheti és megoldhatja a gyakori hibákat, például a következőket:

   - Hiányzó modulok
   - Szintaktikai hibák
   - Logikai hibák

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC naplók megtekintése a csomóponton

Ha a konfiguráció fordítása sikeres, de sikertelen, ha egy csomópontra alkalmazva, részletes információkat találhat a DSC-naplókban. A naplók megkereséséről a Hol [találhatók a DSC eseménynaplói.](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)

Az [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) modul segítséget nyújt a DSC-naplókból származó részletes információk elemzésében. Ha kapcsolatba lép az ügyfélszolgálattal, a probléma diagnosztizálásához ezekre a naplókra van szükség.

Az xDscDiagnostics modult a helyi számítógépre telepítheti [a Stabil verziómodul telepítése című](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)témakörben található utasítások alapján.

Az xDscDiagnostics modul Azure-gépen történő telepítéséhez használja az [Invoke-AzVMRunCommand parancsot.](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) A **portálról** a Futtatás parancs segítségével is használhatja a [PowerShell-parancsfájlok futtatása a Windows virtuális gép futtatása parancssal](../../virtual-machines/windows/run-command.md)című parancsban található lépéseket.

Az xDscDiagnostics használatával kapcsolatos további tudnivalókért olvassa el [az xDscDiagnosztikai eszközök használata a DSC-naplók elemzéséhez című témakört.](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) Lásd még: [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Győződjön meg arról, hogy a csomópontok és az Automation munkaterület rendelkezik a szükséges modulokkal

A DSC a csomópontra telepített moduloktól függ. Az Azure Automation State Configuration használatakor importálja a szükséges modulokat az Automation-fiókba a [modulok importálása](../shared-resources/modules.md#importing-modules)című részben felsorolt lépések használatával. A konfigurációk a modulok adott verzióitól is függhetnek. További információt a [Modulok hibaelhárítása című témakörben talál.](shared-resources.md#modules)

## <a name="common-errors-when-working-with-dsc"></a>Gyakori hibák a DSC-vel végzett munka során

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Eset: Speciális karakterekkel rendelkező konfiguráció nem törölhető a portálról

#### <a name="issue"></a>Probléma

Amikor megpróbál törölni egy DSC-konfigurációt a portálról, a következő hibaüzenet jelenik meg:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Ok

Ez a hiba egy ideiglenes probléma, amelyet a tervek szerint meg kell oldani.

#### <a name="resolution"></a>Megoldás:

* Használja az Az-cmdlet "Remove-AzAutomationDscConfiguration" a konfiguráció törléséhez.
* A parancsmag dokumentációja még nem lett frissítve.  Addig tekintse meg az AzureRM-modul dokumentációját.
  * [Eltávolítás-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Eset: Nem sikerült regisztrálni a DSC-ügynököt

#### <a name="issue"></a>Probléma

Amikor megpróbál futtatni, `Set-DscLocalConfigurationManager` vagy egy másik DSC-parancsmag, a következő hibaüzenet jelenik meg:

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

Ezt a hibát általában a tűzfal okozza, amely a számítógép proxykiszolgáló mögött van, vagy más hálózati hibák.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép hozzáfér-e az Azure Automation DSC megfelelő végpontjaihoz, majd próbálkozzon újra. A szükséges portok és címek listáját a [hálózattervezés](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Eset: Az állapotjelentések "Nem engedélyezett" válaszkódot adnak vissza

#### <a name="issue"></a>Probléma

Az állapotkonfigurációval (DSC) rendelkező csomópont regisztrálásakor az alábbi hibaüzenetek egyike jelenik meg:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Ok

A problémát hibás vagy lejárt tanúsítvány okozza.  További információt a [Tanúsítvány lejárata és újbóli regisztrálása című témakörben talál.](../automation-dsc-onboarding.md#re-registering-a-node)

A problémát az is okozhatja, hogy egy proxykonfiguráció nem engedélyezi a hozzáférést a ***.azure-automation.net**. További információ: [A magánhálózatok konfigurálása](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Megoldás:

Kövesse az alábbi lépéseket a hibás DSC-csomópont újbóli regisztrálásához.

Először törölje a csomópont regisztrálását a következő lépésekkel.

1. Az Azure Portalon az **Otthoni** -> **automatizálási fiókok**–> {Automation-fiók} -> **állapotkonfiguráció (DSC)** alatt
2. Kattintson a "Csomópontok", és kattintson a csomópont baj.
3. Kattintson a "Regisztráció törlése" gombra a csomópont regisztrációjának törléséhez.

Másodszor távolítsa el a DSC-bővítményt a csomópontról.

1. Az Azure Portalon, **a** -> **->** {Sikertelen csomópont) **-> bővítmények csoportban**
2. Kattintson a "Microsoft.Powershell.DSC" elemre.
3. Kattintson az "Eltávolítás" gombra a PowerShell DSC-bővítmény eltávolításához.

Harmadszor távolítsa el az összes hibás vagy lejárt tanúsítványt a csomópontról.

A rendszer a rendszera rendszeregyik emelt szintű PowerShell-parancssorból származó sikertelen csomóponton futtassa a következőket:

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

Végül regisztrálja újra a hibás csomópontot a következő lépésekkel.

1. Az Azure Portalon az **Otthoni** -> **automatizálási fiókok** –> {Automation-fiók} -> **állapotkonfiguráció (DSC)** alatt
2. Kattintson a "Csomópontok" gombra.
3. Kattintson a "Hozzáadás" gombra.
4. Válassza ki a meghibásodott csomópontot.
5. Kattintson a "Csatlakozás" gombra, és válassza ki a kívánt beállításokat.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Eset: A csomópont állapota sikertelen, és "Nem található" hiba

#### <a name="issue"></a>Probléma

A csomópont állapota **Sikertelen állapotú,** és a következő hibaüzenetet tartalmazza:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomópont egy konfigurációs névhez (például ABC) van rendelve csomópontkonfigurációs név helyett (például ABC). webkiszolgáló).

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a csomópontot "csomópontkonfigurációnév" és nem a "konfigurációnév" hozzárendelésével rendeli hozzá.
* Csomópontkonfigurációt rendelhet egy csomóponthoz az Azure Portal használatával vagy egy PowerShell-parancsmaggal.

  * Ha csomópontkonfigurációt szeretne hozzárendelni egy csomóponthoz az Azure Portal használatával, nyissa meg a **DSC-csomópontok** lapot, majd jelöljön ki egy csomópontot, és kattintson a **Csomópont konfigurációs gombának hozzárendelése** gombra.
  * Csomópontkonfiguráció hozzárendelése egy csomóponthoz PowerShell-parancsmag használatával, használja **a Set-AzureRmAutomationDscNode** parancsmagot

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Eset: Nem jöttek létre csomópontkonfigurációk (MOF-fájlok) a konfiguráció fordításakor

#### <a name="issue"></a>Probléma

A DSC fordítási feladat a következő hibával függeszti fel:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Ok

Ha a DSC-konfigurációban a **csomópont** kulcsszót `$null`követő kifejezés a , majd nem hoz létre csomópont-konfigurációkat.

#### <a name="resolution"></a>Megoldás:

Az alábbi megoldások bármelyike megoldja a problémát:

* Győződjön meg arról, hogy a konfigurációs definícióban a **csomópont** kulcsszó melletti kifejezés nem kiértékeli a $null.
* Ha a konfiguráció fordításakor konfigurációs adatokat ad át, győződjön meg arról, hogy a konfiguráció által megkívánt értékeket adja át a [ConfigurationData-ból.](../automation-dsc-compile.md)

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Eset: A DSC-csomópont jelentése "folyamatban" állapotba kerül

#### <a name="issue"></a>Probléma

A DSC ügynök kimenetek:

```error
No instance found with given property values
```

#### <a name="cause"></a>Ok

Frissítette a WMF verziót, és sérült a WMI.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához kövesse a [DSC ismert problémáiról és korlátozásairól](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) szóló cikk utasításait.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Eset: Nem lehet használni egy hitelesítő adatot a DSC-konfigurációban

#### <a name="issue"></a>Probléma

A DSC fordítási feladat a következő hibával lett felfüggesztve:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Ok

Hitelesítő adatokat használt egy konfigurációban, de nem biztosította a megfelelő **ConfigurationData-t** a **PSDscAllowPlainTextPassword** beállításához, hogy minden csomópontkonfigurációhoz igaz legyen.

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy adja át a megfelelő **ConfigurationData** beállításához **PSDscAllowPlainTextPassword** true minden csomópont konfiguráció, amely szerepel a konfigurációban. További információ: [DSC-konfigurációk összeállítása az Azure Automation állapotkonfigurációjában című témakörben.](../automation-dsc-compile.md)

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Eset: Bevezetés a dsc-bővítményből, "Hibafeldolgozási bővítmény" hiba

#### <a name="issue"></a>Probléma

DSC-bővítmény használata esetén a következő hibát tartalmazó hiba lép fel:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomóponthoz olyan csomópontkonfigurációs nevet rendelnek, amely nem létezik a szolgáltatásban.

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a csomópontot olyan csomópontkonfigurációs névvel rendeli hozzá, amely pontosan megegyezik a szolgáltatásban lévő névvel.
* Dönthet úgy, hogy nem adja meg a csomópont konfigurációjának nevét, ami a csomópont bevezetéséhez vezet, de nem rendel csomópont-konfigurációt.

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Forgatókönyv: Egy csomópont regisztrálása a PowerShell-lel a következő hibát adja vissza: "Egy vagy több hiba történt"

#### <a name="issue"></a>Probléma

Ha a vagy `Register-AzAutomationDSCNode` `Register-AzureRMAutomationDSCNode`használatával regisztrál egy csomópontot, a következő hibaüzenet jelenik meg.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha olyan csomópontot próbál regisztrálni, amely az Automation-fióknál külön előfizetésben él.

#### <a name="resolution"></a>Megoldás:

Kezelje az előfizetéses csomópontot úgy, mintha egy külön felhőben vagy a helyszínen élne.

A csomópont regisztrálásához kövesse az alábbi lépéseket.

* Windows – [Fizikai/virtuális Windows-gépek a helyszínen vagy az Azure/AWS-től eltérő felhőben.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines)
* Linux – [Fizikai/virtuális Linux-gépek a helyszínen vagy az Azure-tól eltérő felhőben.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Eset: Hibaüzenet - "Nem sikerült kiépíteni"

#### <a name="issue"></a>Probléma

Csomópont regisztrálásakor a következő hibaüzenet jelenik meg:

```error
Provisioning has failed
```

#### <a name="cause"></a>Ok

Ez az üzenet akkor fordul elő, ha kapcsolódási probléma van a csomópont és az Azure között.

#### <a name="resolution"></a>Megoldás:

Határozza meg, hogy a csomópont magánvirtuális hálózatban van-e, vagy más problémák merülnek fel az Azure-hoz való csatlakozással kapcsolatban.

További információt a [Hibaelhárítás a bevezetési megoldásoknál című](onboarding.md)témakörben talál.

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Forgatókönyv: Konfiguráció alkalmazása Linux alatt, hiba lép fel egy általános hiba

#### <a name="issue"></a>Probléma

A konfiguráció Linuxon történő alkalmazásakor a következő hibát tartalmazó hiba lép fel:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Ok

Az ügyfelek azonosították, hogy ha a `/tmp` hely a beállítás, `noexec`a DSC jelenlegi verziója nem fogja alkalmazni a konfigurációkat.

#### <a name="resolution"></a>Megoldás:

* Távolítsa `noexec` el a `/tmp` lehetőséget a helyről.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Eset: Az átfedésben lévő csomópontkonfiguráció-nevek hibás kiadást eredményezhetnek

#### <a name="issue"></a>Probléma

Ha egyetlen konfigurációs parancsfájlt használ több csomópont-konfiguráció létrehozására, és a csomópontkonfigurációk egy része mások részhalmazának neve, a fordítási szolgáltatás egyik problémája rossz konfiguráció hozzárendelését eredményezheti.  Ez csak akkor fordul elő, ha egyetlen parancsfájl használatával hozza létre a csomópontonkénti konfigurációkat, és csak akkor, ha a névátfedés a karakterlánc elején fordul elő.

Például ha egyetlen konfigurációs parancsfájlt használ a csomópontok on alapuló konfigurációk generálására, amelyek parancsmagként átadott a kivonatként, és a csomópont adatai tartalmaznak egy "kiszolgáló" és "1kiszolgáló" nevű kiszolgálót.

#### <a name="cause"></a>Ok

Ismert probléma a fordítási szolgáltatással.

#### <a name="resolution"></a>Megoldás:

A legjobb megoldás az lenne, hogy helyileg vagy CI/CD-folyamatban fordítson le, és a MOF-fájlokat közvetlenül a szolgáltatásba töltse fel.  Ha a szolgáltatásban való összeállítás követelmény, a következő legjobb megoldás az lenne, ha felosztanánk a fordítási feladatokat, hogy ne legyen átfedés a nevekben.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Eset: Az átjáró időmeghosszabbítási hibája a DSC konfigurációs feltöltésén

#### <a name="issue"></a>Probléma

DSC-konfiguráció feltöltésekor `GatewayTimeout` hibaüzenet jelenik meg. 

#### <a name="cause"></a>Ok

A dsc-konfigurációk, amelyek lefordítása hosszú időt vesz igénybe, ezt a hibát okozhatják.

#### <a name="resolution"></a>Megoldás:

A DSC-konfigurációk elemzéséhez bármikor módosítható, ha `ModuleName` a `Import-DscResource` hívások paraméterét explicit módon tartalmazza. További információ: [Using Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
