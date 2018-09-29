---
title: Runbookok futtatása az Azure Automation hibrid Runbook-feldolgozón
description: Ez a cikk ismerteti, a helyi adatközpontban vagy a felhőbeli szolgáltató és a hibrid forgatókönyv-feldolgozói szerepkör gépeken futó runbookok.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a0b5188605874a04f0341cde1a68487c8a50df84
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431814"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid Runbook-feldolgozón

Azure Automation és a egy hibrid Runbook-feldolgozón rendszerű futó runbookok struktúrája nincs különbség van. Az egyes használt Runbookok valószínűleg jelentős különbség azonban mivel a hibrid Runbook-feldolgozók általában célzó runbookokhoz magát a helyi számítógépet vagy a helyi környezetben, ahol központilag telepítették, miközben a runbookok erőforrásokon-erőforrások kezelése Az Azure Automation általában kezelheti az erőforrásokat az Azure-felhőben.

Runbookok futtatása hibrid Runbook-feldolgozón hoz létre, amikor szerkesztése, és tesztelje a runbookokat a gépen, amelyen a hibrid feldolgozó. A gazdagép rendelkezik az összes PowerShell-modulok és hálózati hozzáférés kezelése és a helyi erőforrások eléréséhez szükséges. Egy runbook szerkeszthetők, és a tesztelt, a hibrid feldolgozói gépen, majd feltöltheti az Azure Automation környezet, amelyben ez a hibrid feldolgozói futtatható. Fontos tudni, hogy a feladatok futnak a windows a helyi rendszerfiókot vagy egy speciális felhasználói fiók alatt **nxautomation** linuxon, amely megjelentetni finom eltérések egy hibrid Runbook-feldolgozó Ez legyen a runbookok létrehozásakor figyelembe venni.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Runbook indítása a hibrid Runbook-feldolgozón

[Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md) runbook elindítása a különböző módszereket írja le. Hibrid Runbook-feldolgozó hozzáad egy **RunOn** lehetőséget, ahol megadhatja a hibrid forgatókönyv-feldolgozó csoport nevére. Ha egy csoport van megadva, majd a runbook lekért és futtassa a dolgozók a csoport egyik. Ha ez a beállítás nincs megadva, majd fut az Azure Automationben szokásos módon.

Amikor elindít egy runbookot az Azure Portalon, lehetősége lesz a **futtathatók** , amelyen kiválaszthatja a beállítást **Azure** vagy **hibrid feldolgozó**. Ha **hibrid feldolgozó**, akkor jelölje be a csoport lehetőséget a legördülő listából.

Használja a **RunOn** paraméter. A következő paranccsal egy hibrid forgatókönyv-feldolgozó csoport nevű Windows PowerShell-lel MyHybridGroup a Test-Runbook nevű runbook indítása.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> A **RunOn** paraméter hozzá lett adva a **Start-AzureAutomationRunbook** 0.9.1 verziójában a Microsoft Azure PowerShell parancsmaggal. Érdemes [a legújabb verzió letöltéséhez](https://azure.microsoft.com/downloads/) Ha korábbi egy telepítve van. Csak ki kell ezt a verziót telepíteni, ahol kezdi a runbook a Powershellből munkaállomáson. Nem kell telepíteni a feldolgozó számítógépre, kivéve, ha a runbook indításához erről a számítógépről szeretne"

## <a name="runbook-permissions"></a>Runbook-engedélyek

Runbookok futtatása hibrid Runbook-feldolgozón nem használhatja ugyanazt a módszert, általában használt runbookok hitelesítése Azure-erőforrásokhoz, mivel az Azure-on kívüli erőforrások érnek el. A runbook tud biztosítani a helyi erőforrásokhoz a saját hitelesítéshez, vagy konfigurálhatja a hitelesítés [felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
), vagy megadhat egy futtató fiókot, felhasználói környezetet biztosít az összes runbook számára.

### <a name="runbook-authentication"></a>Runbook-hitelesítés

Alapértelmezés szerint a runbookok futnak a helyi rendszerfiók környezetében a Windows és a egy speciális felhasználói fiók **nxautomation** Linux rendszeren a helyi számítógépen, így azok hitelesítő adatokat kell megadnia a saját erőforrásokhoz férnek hozzá .

Használható [Credential](automation-credentials.md) és [tanúsítvány](automation-certificates.md) eszközök a runbookban a parancsmagok, amelyek lehetővé teszik, hogy adja meg hitelesítő adatait, így a különböző erőforrások elvégezheti a hitelesítést. Az alábbi példa bemutatja, hogy újraindítja a számítógépet a runbook egy részét. Ez lekéri a hitelesítő adatokat a hitelesítőadat-eszköz és a egy változóeszköz a számítógép nevét, és használja majd ezeket az értékeket a Restart-Computer parancsmag.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Is [InlineScript](automation-powershell-workflow.md#inlinescript), amely lehetővé teszi, hogy egy másik számítógépen, által megadott hitelesítő adatokkal futtathatja kódblokkok a [PSCredential általános paraméterével](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Futtató fiók

Alapértelmezés szerint a hibrid Runbook-feldolgozó használja a helyi System for Windows és a egy speciális felhasználói fiók **nxautomation** runbookok végrehajtása linuxhoz. Adja meg a helyi erőforrások saját hitelesítési runbookok, helyett megadhat egy **RunAs** fiókra a hibrid feldolgozói csoportban. Adja meg, hogy egy [hitelesítőeszközt](automation-credentials.md) , amely rendelkezik a helyi erőforrásokhoz való hozzáférést, és minden runbook futni ezeket a hitelesítő adatokat, a csoport egy hibrid Runbook-feldolgozón futtatásakor.

A felhasználói nevet a hitelesítésnek kell lennie a következő formátumok egyikében:

* tartomány\felhasználónév
* username@domain
* felhasználónév (fiókok esetében a helyi számítógép helyi)

Az alábbi eljárás segítségével megadhat egy futtató fiókot egy hibrid feldolgozói csoport:

1. Hozzon létre egy [hitelesítőeszközt](automation-credentials.md) helyi erőforrásokhoz való hozzáférést.
2. Nyissa meg az Automation-fiókot az Azure Portalon.
3. Válassza ki a **hibrid Feldolgozócsoportok** csempére, majd válassza ki a csoporthoz.
4. Válassza ki **minden beállítás** , majd **hibrid feldolgozócsoport beállításai**.
5. Változás **futtató** a **alapértelmezett** való **egyéni**.
6. Válassza ki a hitelesítő adatokat, és kattintson a **mentése**.

### <a name="managed-identities-for-azure-resources"></a>Felügyelt identitások az Azure-erőforrásokhoz

Hibrid Runbook-feldolgozók Azure-beli virtuális gépeken futó Azure-erőforrások felügyelt identitások használatával az Azure erőforrásokban való hitelesítéshez. Nincsenek felügyelt identitások használatával az Azure-erőforrásokhoz a futtató fiókok keresztül, számos előnnyel jár.

* Nincs szükség a Futtatás mint tanúsítvány exportálása, és importálhatja a hibrid Runbook-feldolgozó
* Nincs szükség a használja a futtató fiók tanúsítványának megújítása
* Nem kell kezelni a futtató kapcsolat objektumot a runbook-kódban

Egy felügyelt identitás használata az Azure-erőforrások egy hibrid Runbook-feldolgozón szeretné a következő lépéseket:

1. Az Azure virtuális gép létrehozása
2. [Az Azure-erőforrások felügyelt identitások konfigurálása a virtuális Gépen](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [A virtuális gép hozzáférést biztosítani az erőforráscsoport a Resource Managerben](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [A virtuális gép felügyelt identitással rendszer által hozzárendelt hozzáférési jogkivonatot kapjon] (.. / active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [A Windows a hibrid Runbook Worker telepítése](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) a virtuális gépen.

Miután végzett a fenti lépéseket, `Connect-AzureRmAccount -Identity` a a runbookot, hogy az Azure erőforrásokban való hitelesítéshez. Ez csökkenti a futtató fiók használatát, és kezelheti a futtató fiók tanúsítványa szükséges.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="automation-run-as-account"></a>Automation futtató fiók

Az automatizált összeállítási folyamatból, az Azure-erőforrások üzembe helyezésének részeként szükség lehet a feladat vagy lépések támogatásához a központi telepítési sorrendben a helyszíni rendszerekhez való hozzáférést. A futtató fiók használatával történő hitelesítésre támogatása érdekében a futtató fiók tanúsítványát telepítenie kell.

A következő PowerShell-runbook *Export-RunAsCertificateToHybridWorker*, exportálja a Futtatás mint tanúsítványt az Azure Automation-fiók és letölti és importálja a hibrid a helyi gép tanúsítványtárolójába feldolgozó ugyanazzal a fiókkal csatlakozik. A lépés befejezése után ellenőrzi a feldolgozó sikeresen hitelesítik az Azure-ban a futtató fiókot.

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

> [!IMPORTANT]
> **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használhat **Add-AzureRmAccount**, vagy frissítheti az a modulokat az Automation-fiókban.

Mentse a *Export-RunAsCertificateToHybridWorker* runbookot, hogy a számítógép egy `.ps1` bővítmény. Importálja az Automation-fiók és szerkeszteni a runbookot, a változó értékének megmódosítása `$Password` egy saját jelszót. Közzététele, és futtassa a runbookot a hibrid feldolgozócsoport futtató és a futtató fiók használatával runbookok hitelesítése célzó. A feladatstream jelentéseket helyre importálhatja a tanúsítványt a helyi számítógép tárolójába való, és attól függően, hogy hány Automation-fiókok az előfizetésében határozza meg, és ha sikeres hitelesítés után több sorokkal követi.

## <a name="job-behavior"></a>Feladat viselkedés

Feladatok kezelése némileg eltérő hibrid Runbook-feldolgozók mint Azure próbakörnyezetbe lefordítja a futtatás esetén. Egy fő különbséggel, hogy nincs korlátozva a hibrid Runbook-feldolgozók feladat időtartama. Az Azure-ban futtatott Runbookok próbakörnyezetbe lefordítja korlátozva, 3 óra, mert a [igazságos elosztás](automation-runbook-execution.md#fair-share). Ha rendelkezik egy hosszú ideig futó runbook érdekében, hogy rugalmas lehetséges újraindítja a számítógépet, például ha a gép, amelyen a hibrid feldolgozó újraindítja a számítógépet szeretné. Ha a hibrid feldolgozói gazdagép gép újraindul, majd minden futó runbook-feladat újraindítja az elejéről, vagy a PowerShell-munkafolyamati runbookok az utolsó ellenőrzőponttól. Ha egy runbook-feladat több mint 3 alkalommal újraindul, majd fel van függesztve.

## <a name="run-only-signed-runbooks"></a>Csak aláírt Runbookok futtatása

Hibrid Runbook-feldolgozók beállítható úgy, hogy csak aláírt runbookok futtatása a konfigurálást. A következő szakasz azt ismerteti, hogyan állíthatja be a hibrid Runbook-feldolgozók aláírt runbookok futtatására, és arról, hogyan jelentkezhet a runbookok.

> [!NOTE]
> Miután konfigurálta a csak aláírt runbookok futtatása hibrid Runbook-feldolgozók, runbookok, amely rendelkezik **nem** lett lesz aláírva a feldolgozón végrehajtása sikertelen.

### <a name="create-signing-certificate"></a>Aláíró tanúsítvány létrehozása

Az alábbi példa létrehoz egy önaláírt tanúsítványt, a runbookok az aláíráshoz használható. A minta létrehozza a tanúsítványt, és exportálja azt. A tanúsítványt később importálja az a hibrid Runbook-feldolgozók. Az ujjlenyomat adja vissza, a rendszer később hivatkozik a tanúsítványra.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="configure-the-hybrid-runbook-workers"></a>A hibrid Runbook-feldolgozók konfigurálása

Másolja ki a létrehozott csoport minden egyes hibrid Runbook-feldolgozó tanúsítványt. Futtassa a következő szkriptet, importálja a tanúsítványt, és a hibrid feldolgozó használnak aláírás-ellenőrzést a runbookok konfigurálása.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>A Runbookok a tanúsítvány aláírása

A hibrid Runbook-feldolgozók használatára konfigurált csak aláírt runbookok, be kell jelentkeznie a runbookokat, amelyek a hibrid Runbook-feldolgozón használni fog. Az alábbi minta PowerShell használatával a runbookok aláírásához.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

A runbook aláírt azt kell importálja az Automation-fiók, és közzéteheti az aláírásblokkot. Runbookok importálása kapcsolatban lásd: [runbook importálása egy fájlból az Azure Automationbe](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a runbookok nem sikeresen elvégezte, tekintse át a hibaelhárítási útmutató az [runbook végrehajtási hibák](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).

## <a name="next-steps"></a>További lépések

* A runbook indítása használható különböző módszerekkel kapcsolatos további tudnivalókért lásd: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md).
* A PowerShell és a PowerShell-munkafolyamati runbookok az Azure Automationben a szöveges szerkesztő használatával való munkához különböző eljárások megismeréséhez tekintse meg [az Azure Automation Runbook szerkesztése](automation-edit-textual-runbook.md)
