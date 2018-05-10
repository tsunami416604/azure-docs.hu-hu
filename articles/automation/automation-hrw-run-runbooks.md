---
title: Azure Automation hibrid forgatókönyv-feldolgozót a runbookot
description: Ez a cikk tájékoztatást ad azokról a helyi adatközpontban, illetve a hibrid forgatókönyv-feldolgozó szerepkörrel rendelkező felhőszolgáltatóként gépeken futó runbookok.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 1ed82a199fbee44d3cd9df7f5416c4e243d9d191
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>A hibrid forgatókönyv-feldolgozók a futó runbookot

Nincs különbség a futó Azure Automation és a hibrid forgatókönyv-feldolgozót a futó runbookok struktúrában. Az összes használt Runbookok valószínűleg jelentős különbség azonban mivel a hibrid forgatókönyv-feldolgozók általában célzó runbookok kezelheti az erőforrásokat a helyi számítógép vagy a helyi környezetben, ahol központilag telepítették, a runbookok közben erőforrásokon Azure Automation szolgáltatásbeli általában az Azure felhőalapú erőforrások kezelésére.

Amikor szerzőként futtatni a hibrid forgatókönyv-feldolgozók runbookok, szerkesztheti, és a gépen, amelyen a hibrid feldolgozó runbookok tesztelésére. A gazdaszámítógépen rendelkezik az összes, a PowerShell-modulok és a hálózati hozzáférés kezelése és a helyi erőforrások eléréséhez szükséges. Amikor egy runbook szerkeszteni, és a tesztelni a hibrid feldolgozó gépen, majd feltöltheti az Azure Automation környezetben, ahol érhető el a hibrid feldolgozó futtatásához. Fontos tudni, hogy feladatok futtathatók a windows a helyi rendszerfiókot vagy egy különleges felhasználói fiókkal **nxautomation** Linux, amely vethet fel finom eltérések vannak a hibrid forgatókönyv-feldolgozók legyen a runbookok létrehozásakor figyelembe venni.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Runbook indítása a hibrid forgatókönyv-feldolgozó

[Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md) runbook indítása másik módszerét ismerteti. Hibrid forgatókönyv-feldolgozó hozzáadása egy **RunOn** beállítás, amelyen megadhatja a hibrid forgatókönyv-feldolgozó csoport nevét. Ha kiválaszt egy csoportot, majd a runbook lekérése és futtassa a dolgozók, a csoport egyik. Ha ez a beállítás nincs megadva, majd azt legyen futtatva az Azure Automationben normál.

Amikor elindít egy forgatókönyvet az Azure portálon, lehetősége lesz a **futtatnak** beállítás, ahol kiválaszthatja **Azure** vagy **Hibridfeldolgozó**. Ha **Hibridfeldolgozó**, jelölje be a csoport a legördülő listából.

Használja a **RunOn** paraméter. A következő paranccsal egy hibrid forgatókönyv-feldolgozó csoport nevű Windows PowerShell használatával MyHybridGroup a Test-Runbook nevű runbookot.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> A **RunOn** paraméter hozzá lett adva a **Start-AzureAutomationRunbook** verziójában 0.9.1 Microsoft Azure PowerShell parancsmag. Meg kell [a legújabb verzió letöltéséhez](https://azure.microsoft.com/downloads/) Ha egy korábbi egy telepítve van. Csak szeretné ezt a verziót telepíteni egy adott számítógépet a runbook PowerShell munkaállomáson. Nem kell telepíteni a munkavégző számítógépen, ha a runbook indításához az adott számítógépen szeretné"

## <a name="runbook-permissions"></a>Runbookokra vonatkozó engedélyek

A hibrid forgatókönyv-feldolgozók futó Runbookok ugyanezt a módszert, amelyet főként a runbookok óta érik el az Azure-on kívüli erőforrások hitelesítéséhez az Azure-erőforrások, nem használható. A runbook vagy adja meg a saját helyi erőforrások hitelesítéséhez, vagy megadhat egy futtató fiókot, felhasználói környezetet biztosít az összes runbook.

### <a name="runbook-authentication"></a>Runbook-hitelesítés

Alapértelmezés szerint a forgatókönyvek futtatása a helyi rendszerfiók környezetében a Windows és egy különleges felhasználói fiókkal **nxautomation** Linux rendszeren a helyi számítógépen, ezért meg kell adniuk az erőforrásokat, amelyek hozzáférnek a saját hitelesítési .

Használhat [hitelesítő adat](automation-credentials.md) és [tanúsítvány](automation-certificates.md) eszközök a runbookban a parancsmagokat, amelyek lehetővé teszik a hitelesítő adatok megadásához, így a különböző erőforrások elvégezheti a hitelesítést. A következő példa bemutatja, hogy újraindítja a számítógépet a runbook egy részét. Hitelesítő adatok lekéri a hitelesítőadat-eszköz és a változó eszköz a számítógép nevét, és ezután a Restart-Computer parancsmag ezeket az értékeket.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Kihasználhatja [InlineScript](automation-powershell-workflow.md#inlinescript), amely lehetővé teszi, hogy kódblokkokat, egy másik számítógépen által megadott hitelesítő adatokkal futtathatja a [PSCredential általános paraméter](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Futtató fiók

Alapértelmezés szerint a hibrid forgatókönyv-feldolgozó használja a Windows a helyi rendszer és egy különleges felhasználói fiókkal **nxautomation** Linux runbookok végrehajtása. Ahelyett, hogy a runbookok adja meg a saját helyi erőforrások hitelesítéséhez, megadhat egy **RunAs** fiókot használja a hibrid feldolgozócsoport. Megadhat egy [hitelesítőadat-eszköz](automation-credentials.md) , amely rendelkezik a helyi erőforrások elérését, és minden runbook futtatásához ezeket a hitelesítő adatokat a hibrid forgatókönyv-feldolgozó csoport használatakor.

A felhasználónév a hitelesítő adatokat kell lennie a következő formátumok egyikében:

* tartomány\felhasználónév
* username@domain
* felhasználónév (fiókok esetében a helyi számítógép helyi)

A következő eljárás használatával adjon meg egy hibrid feldolgozócsoport futtató fiókot:

1. Hozzon létre egy [hitelesítőadat-eszköz](automation-credentials.md) helyi erőforrásokhoz való hozzáférést.
2. Nyissa meg az Automation-fiók az Azure portálon.
3. Válassza ki a **hibrid dolgozó csoportok** csempére, majd válassza ki a csoport.
4. Válassza ki **összes beállítás** , majd **hibrid feldolgozócsoport beállításai**.
5. Változás **futtató** a **alapértelmezett** való **egyéni**.
6. Válassza ki a hitelesítő adatokat, és kattintson a **mentése**.

### <a name="automation-run-as-account"></a>Automatizálási futtató fiók

Az Azure-erőforrások telepítéséhez az automatizált felépítési folyamat részeként szüksége lehet a helyszíni rendszerek támogatásához egy feladat vagy a lépéseket a telepítési sorrendjét elérésére. Azure-ban a Futtatás mint fiók ellen-hitelesítés támogatásához telepítendő a Futtatás mint fiók tanúsítvány.

A következő PowerShell-forgatókönyv *Export-RunAsCertificateToHybridWorker*, exportálja a Futtatás mint tanúsítvány az Azure Automation-fiók és tölti le, és importálja azokat a helyi számítógép tanúsítványtárolójába a hibrid munkavégző ugyanazzal a fiókkal csatlakozik. Ha a lépés befejeződött, ellenőrzi a dolgozó sikeresen hitelesítheti a Futtatás mint fiók használatával.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

Mentse a *Export-RunAsCertificateToHybridWorker* a számítógépet a runbook egy `.ps1` bővítmény. Importálja a fájlt az Automation-fiók és szerkeszteni a runbookot, a változó értékének módosítása `$Password` a saját jelszavát. Közzététele, és futtassa a célcsoport-kezelési a hibrid feldolgozócsoport futtatásához, és a Futtatás mint fiókkal runbookok hitelesítéséhez a runbook. A feladatstream jelentés irányuló kísérlet: importálja a tanúsítványt a helyi számítógép tárolójában, és attól függően, hogy hány Automation-fiókok vannak definiálva az előfizetés és a sikeres hitelesítés esetén több sort tartalmazó követi.

## <a name="job-behavior"></a>Feladat viselkedése

Feladatok kezelése némileg eltérő hibrid forgatókönyv-feldolgozók mint Azure védőfalak működnek. Egy fő különbség az, hogy a hibrid forgatókönyv-feldolgozók feladat időtartama korlátozva van. Ha a hosszan futó runbook kívánt győződjön meg arról, hogy az refs lehetséges újraindítja a számítógépet, például ha a számítógép, amelyen a hibrid feldolgozó újraindul. Ha hibrid feldolgozó gazdaszámítógépen újraindul, majd minden futó runbook-feladat újraindítja az elejéről, vagy a PowerShell-munkafolyamati forgatókönyvek a legutóbbi ellenőrzőponttól. Ha a runbook-feladatok újraindítása legfeljebb 3-szor, majd azt fel van függesztve.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>A hibrid forgatókönyv-feldolgozó hibaelhárítási runbookok

Naplók minden hibridfeldolgozó C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes, helyileg tárolja. Hibrid feldolgozók is rögzítse a hibák és események a a Windows eseménynaplóban **alkalmazások és szolgáltatások Logs\Microsoft-SMA\Operational**. A munkavégző végre runbookokkal kapcsolatos eseményeket jegyez **alkalmazások és szolgáltatások Logs\Microsoft-Automation\Operational**. A **Microsoft-SMA** napló a runbook-feladat a dolgozó és a runbook a feldolgozási leküldött kapcsolódó számos további eseményeket is tartalmazza. Amíg a **Microsoft-automatizálás** Eseménynapló nem rendelkezik a runbook végrehajtása hibaelhárítás védelmével adatokkal sok eseményt, a runbook-feladat eredményének tartalmaz.

[Runbook kimenet és üzenetek](automation-runbook-output-and-messages.md) az Azure Automation hibrid küldi munkavállalók hasonlóan a runbook-feladatok futtatása a felhőben. A részletes és az állapot adatfolyamokat a módon más runbookok is engedélyezheti.

Ha a runbookok nem sikeres befejezését, és a feladat összegzése állapotot jelez, **felfüggesztett**, tekintse át a hibaelhárítási [hibrid forgatókönyv-feldolgozó: A runbook-feladat leállításafelfüggesztettállapotú](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).

## <a name="next-steps"></a>További lépések

* A runbook-indítási használható különböző módszerekkel kapcsolatos további információkért lásd: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md).
* A különböző eljárásokkal a PowerShell és a PowerShell-munkafolyamati forgatókönyvek az Azure Automationben a szöveges szerkesztővel használata a ismertetése: [az Azure Automationben Runbook szerkesztése](automation-edit-textual-runbook.md)
