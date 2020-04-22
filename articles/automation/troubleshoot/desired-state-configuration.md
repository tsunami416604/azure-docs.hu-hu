---
title: Az Azure Automation állapotkonfigurációjának (DSC) hibaelhárítása
description: Ez a cikk az Azure Automation state configuration (DSC) hibaelhárításával kapcsolatos információkat tartalmazza.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679307"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Az Azure Automation állapotkonfigurációjával (DSC) kapcsolatos problémák elhárítása

Ez a cikk az Azure Automation state configuration (DSC) konfigurációinak összeállítása vagy üzembe helyezése során felmerülő hibaelhárítási problémákról nyújt tájékoztatást.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="diagnosing-an-issue"></a>Probléma diagnosztizálása

Amikor fordítási vagy üzembe helyezési hibát kap a konfigurációhoz, az alábbi néhány lépés sel segít a probléma diagnosztizálásában.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Győződjön meg arról, hogy a konfiguráció fordítása sikeresen megtörtént a helyi számítógépen

Az Azure Automation State Configuration (DSC) a PowerShell kívánt állapotkonfigurációjára (DSC) épül. A DSC nyelv és szintaxis dokumentációját a [PowerShell DSC-dokumentumokban](https://docs.microsoft.com/powershell/scripting/overview)találja.

A DSC-konfiguráció helyi számítógépen való összeállításával felderítheti és megoldhatja a gyakori hibákat, például a következőket:

   - Hiányzó modulok
   - Szintaktikai hibák
   - Logikai hibák

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC naplók megtekintése a csomóponton

Ha a konfiguráció fordítása sikeres, de sikertelen, ha egy csomópontra alkalmazva, részletes információkat találhat a DSC-naplókban. A naplók megkereséséről a Hol [találhatók a DSC eseménynaplói.](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)

Az [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) modul segítséget nyújt a DSC-naplókból származó részletes információk elemzésében. Ha kapcsolatba lép az ügyfélszolgálattal, a probléma diagnosztizálásához ezekre a naplókra van szükség.

A modult `xDscDiagnostics` a helyi számítógépre telepítheti a [Stabil verziómodul telepítése című](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)témakörben található utasítások alapján.

A modul `xDscDiagnostics` Azure-gépen történő telepítéséhez használja az [Invoke-AzVMRunCommand parancsot.](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) Az Azure **Portalon** a Futtatás parancs beállítását is használhatja, ha követi a [PowerShell-parancsfájlok futtatása a Windows virtuális gép futtatása parancssal című parancsfájlok futtatásában](../../virtual-machines/windows/run-command.md)található lépéseket.

Az **xDscDiagnostics**használatával kapcsolatos további tudnivalókért olvassa el [az xDscDiagnosztikai eszközök használata a DSC-naplók elemzéséhez című témakört.](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) Lásd még: [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Győződjön meg arról, hogy a csomópontok és az Automation munkaterület rendelkezik a szükséges modulokkal

A DSC a csomópontra telepített moduloktól függ. Az Azure Automation State Configuration használatakor importálja a szükséges modulokat az Automation-fiókba a [Modulok importálása](../shared-resources/modules.md#importing-modules)című részben leírt lépésekkel. A konfigurációk a modulok adott verzióitól is függhetnek. További információt a [Modulok hibaelhárítása című témakörben talál.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Eset: Speciális karakterekkel rendelkező konfiguráció nem törölhető a portálról

### <a name="issue"></a>Probléma

Amikor megpróbál törölni egy DSC-konfigurációt a portálról, a következő hibaüzenet jelenik meg:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Ok

Ez a hiba egy ideiglenes probléma, amelyet a tervek szerint meg kell oldani.

### <a name="resolution"></a>Megoldás:

Használja az [Remove-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 parancsmag a konfiguráció törléséhez.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Eset: Nem sikerült regisztrálni a DSC-ügynököt

### <a name="issue"></a>Probléma

[A Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) vagy egy másik DSC-parancsmag esetén a következő hibaüzenet jelenik meg:

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

### <a name="cause"></a>Ok

Ezt a hibát általában a tűzfal okozza, amely a számítógép proxykiszolgáló mögött van, vagy más hálózati hibák.

### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép hozzáfér-e a DSC megfelelő végpontjaihoz, majd próbálkozzon újra. A szükséges portok és címek listáját a [hálózattervezés](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Eset: Az állapotjelentések nem engedélyezett válaszkódot adnak vissza

### <a name="issue"></a>Probléma

Amikor regisztrál egy csomópontot az Azure Automation állapotkonfigurációjával, az alábbi hibaüzenetek egyike jelenik meg:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Ok

A problémát hibás vagy lejárt tanúsítvány okozza. Lásd: [A tanúsítvány lejárata és újbóli regisztrálása.](../automation-dsc-onboarding.md#re-registering-a-node)

A problémát az is okozhatja, hogy egy proxykonfiguráció nem engedélyezi a hozzáférést a ***.azure-automation.net**. További információ: [A magánhálózatok konfigurálása](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Megoldás:

Az alábbi lépésekkel újra regisztrálhatja a hibás DSC-csomópontot.

1. lépés - Törölje a csomópont regisztrációjának törlését.

1. Az Azure Portalon keresse meg az > (automation-> **állapotkonfiguráció)** **elemre.** -> **Automation Accounts**
2. Válassza **a Csomópontok**lehetőséget, és kattintson a problémás csomópontra.
3. A csomópont regisztrációjának törléséhez kattintson a **Regisztráció megszüntetése** gombra.

2. lépés - Távolítsa el a DSC kiterjesztést a csomópontról.

1. Az Azure Portalon keresse meg **az otthoni** -> **virtuális gép** -> (sikertelen csomópont) -> **Bővítmények**.
2. Válassza a **Microsoft.Powershell.DSC**, a PowerShell DSC bővítmény lehetőséget.
3. A bővítmény eltávolításához kattintson az **Eltávolítás** gombra.

3. lépés - Távolítsa el az összes hibás vagy lejárt tanúsítványt a csomópontról.

A rendszer a rendszera rendszeregyik emelt szintű PowerShell-parancssorból származó sikertelen csomóponton futtassa az alábbi parancsokat:

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

4. lépés - Regisztrálja újra a meghibásodott csomópontot.

1. Az Azure Portalon keresse meg az **Otthoni** -> **automatizálási fiókok** -> (az Automation-fiók) -> **állapotkonfiguráció (DSC)**
2. Válassza **a Csomópontok lehetőséget.**
3. Kattintson a **Hozzáadás** parancsra.
4. Válassza ki a meghibásodott csomópontot.
5. Kattintson a **Csatlakozás gombra,** és adja meg a kívánt beállításokat.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Eset: A csomópont állapota sikertelen, és "Nem található" hiba

### <a name="issue"></a>Probléma

A csomópont állapota Sikertelen állapotú, és a következő hibaüzenetet tartalmazza:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomópont egy konfigurációs névhez van rendelve, például **ABC**, csomópontkonfiguráció (MOF fájl) név helyett, például **ABC. Webserver**.

### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a csomópontot a csomópont konfigurációs nevével rendeli hozzá, és nem a konfiguráció nevét.
* Csomópontkonfigurációt rendelhet egy csomóponthoz az Azure Portal használatával vagy egy PowerShell-parancsmaggal.

  * Az Azure Portalon keresse meg az **Otthoni** -> **automatizálási fiókok** -> (az Automation-fiók) -> **állapotkonfiguráció (DSC)**, majd válasszon egy csomópontot, és kattintson **a Csomópont-konfiguráció hozzárendelése gombra.**
  * Használja a [Set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) parancsmag.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Eset: Nem jöttek létre csomópontkonfigurációk (MOF-fájlok) a konfiguráció fordításakor

### <a name="issue"></a>Probléma

A DSC fordítási feladat a következő hibával függeszti fel:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Ok

Amikor a DSC-konfigurációban a `Node` kulcsszót `$null`követő kifejezés kiértékeli a értéket, a rendszer nem hoz létre csomópontkonfigurációkat.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja az alábbi megoldások egyikét:

* Győződjön meg arról, `Node` hogy a kulcsszó melletti kifejezés a konfigurációs definícióban nem null értékűre van kiértékelve.
* Ha a konfiguráció összeállításakor [konfigurációs adatokat](../automation-dsc-compile.md) ad át, győződjön meg arról, hogy a konfiguráció által a konfigurációs adatoktól elvárt értékeket adja át.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Eset: A DSC-csomópont jelentése beragad a Folyamatban állapotban

### <a name="issue"></a>Probléma

A DSC ügynök kimenetek:

```error
No instance found with given property values
```

### <a name="cause"></a>Ok

Frissítette a Windows Management Framework (WMF) verzióját, és megsérült a Windows Management Instrumentation (WMI).

### <a name="resolution"></a>Megoldás:

Kövesse a [DSC ismert problémáinak és korlátainak](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)utasításait.

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Eset: Nem lehet használni egy hitelesítő adatot a DSC-konfigurációban

### <a name="issue"></a>Probléma

A DSC fordítási feladat felfüggesztve a hibával:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Ok

Hitelesítő adatokat használt egy konfigurációban, de `ConfigurationData` nem `PSDscAllowPlainTextPassword` biztosította a megfelelő takarási értéket az egyes csomópont-konfigurációkhoz.

### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy adja át a megfelelő `ConfigurationData` értékére, `PSDscAllowPlainTextPassword` hogy igaz legyen a konfigurációban említett minden csomópontkonfigurációhoz. Lásd: [DSC-konfigurációk összeállítása az Azure Automation állapotkonfigurációjában.](../automation-dsc-compile.md)

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Eset: "Hibafeldolgozási bővítmény" hiba a DSC-bővítményből való bevezetéskor

### <a name="issue"></a>Probléma

DSC-bővítmény használata esetén hiba lép fel a következő hibával:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha a csomóponthoz olyan csomópontkonfigurációs nevet rendelnek, amely nem létezik a szolgáltatásban.

### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy olyan névvel rendeli hozzá a csomópontot, amely pontosan megegyezik a szolgáltatásban lévő névvel.
* Dönthet úgy, hogy nem adja meg a csomópont konfigurációjának nevét, amely a csomópont bevezetéséhez vezet, de nem rendel hozzá csomópont-konfigurációt.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Eset: "Egy vagy több hiba történt" hiba a PowerShell használatával csomópont regisztrálásakor

### <a name="issue"></a>Probléma

A [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) vagy [a Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)használatával csomópont regisztrálásakor a következő hibaüzenet jelenik meg:

```error
One or more errors occurred.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha megpróbál regisztrálni egy csomópontot egy külön előfizetésben, amelyet az Automation-fiók használ.

### <a name="resolution"></a>Megoldás:

Kezelje az előfizetéses csomópontot úgy, mintha egy külön felhőhöz vagy a helyszíni höz lenne definiálva. Regisztrálja a csomópontot az alábbi bevezetési beállítások egyikével:

* Windows – [Fizikai/virtuális Windows-gépek a helyszínen vagy az Azure/AWS-től eltérő felhőben.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines)
* Linux – [Fizikai/virtuális Linux-gépek a helyszínen vagy az Azure-tól eltérő felhőben.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Eset: Hibaüzenet - "Nem sikerült kiépíteni"

### <a name="issue"></a>Probléma

Csomópont regisztrálásakor a következő hibaüzenet jelenik meg:

```error
Provisioning has failed
```

### <a name="cause"></a>Ok

Ez az üzenet akkor fordul elő, ha probléma van a csomópont és az Azure közötti kapcsolattal.

### <a name="resolution"></a>Megoldás:

Határozza meg, hogy a csomópont egy virtuális magánhálózat (VPN) vagy más problémák az Azure-hoz való csatlakozás. Lásd: [Hibaelhárítás bevezetési megoldások esetén](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Eset: Hiba általános hibával, amikor egy konfigurációt alkalmaz Linux alatt

### <a name="issue"></a>Probléma

A konfiguráció Linuxon történő alkalmazásakor a következő hibát tartalmazó hiba lép fel:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Ok

Ha a **/tmp** hely `noexec`a beállítás, akkor a DSC jelenlegi verziója nem tudja alkalmazni a konfigurációkat.

### <a name="resolution"></a>Megoldás:

Távolítsa `noexec` el a kapcsolót a **/tmp** helyről.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Eset: Az átfedésben lévő csomópontkonfiguráció-nevek hibás kiadást eredményezhetnek

### <a name="issue"></a>Probléma

Ha egyetlen konfigurációs parancsfájlt használ több csomópontkonfiguráció létrehozásához, és néhány csomópontkonfiguráció-név más nevek részhalmaza, a fordítási szolgáltatás végül rossz konfigurációt kaphat. Ez a probléma csak akkor fordul elő, ha egyetlen parancsfájlhasználatával hozza létre a csomópontonkénti konfigurációkat, és csak akkor, ha a névátfedés a karakterlánc elején fordul elő. Erre példa egy konfigurációs parancsfájl, amely a parancsmagok használatával kivonatként átadott csomópontadatokon alapuló konfigurációk létrehozására szolgál, és a csomópontadatai közé tartoznak a **kiszolgáló** és az **1kiszolgáló**nevű kiszolgálók.

### <a name="cause"></a>Ok

Ez egy ismert probléma a fordítási szolgáltatással.

### <a name="resolution"></a>Megoldás:

A legjobb megoldás az, ha helyileg vagy CI/CD-folyamatban fordít, és a csomópontkonfigurációs MOF-fájlokat közvetlenül feltölti a szolgáltatásba. Ha a szolgáltatásban való összeállítás követelmény, a következő legjobb megoldás a fordítási feladatok felosztása, hogy ne kerüljön átfedés a nevekbe.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Eset: Az átjáró időmeghosszabbítási hibája a DSC konfigurációs feltöltésén

#### <a name="issue"></a>Probléma

DSC-konfiguráció feltöltésekor `GatewayTimeout` hibaüzenet jelenik meg. 

### <a name="cause"></a>Ok

A dsc-konfigurációk, amelyek lefordítása hosszú időt vesz igénybe, ezt a hibát okozhatják.

### <a name="resolution"></a>Megoldás:

A DSC-konfigurációk elemzéséhez az `ModuleName` [Importálás-DSCResource-hívások](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) paraméterét explicit módon is használhatja.

## <a name="next-steps"></a>További lépések

Ha a fenti probléma nem jelenik meg, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**