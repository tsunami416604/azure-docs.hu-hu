---
title: Runbookok futtatása Azure Automation hibrid Runbook-feldolgozón
description: Ez a cikk a hibrid Runbook-feldolgozói szerepkörrel rendelkező, a helyi adatközpontban vagy a felhőben működő gépek runbookok futtatásával kapcsolatos információkat tartalmaz.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6f41263bfb930d3aab41fd8ace86cd6afb0ace26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850575"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid Runbook-feldolgozón

A runbookok struktúrája nem különbözik a hibrid Runbook-feldolgozón futó Azure Automation-és runbookok. Az egyes legvalószínűbben használt runbookok jelentősen eltérnek. Ez a különbség azért van, mert a hibrid Runbook-feldolgozót célzó runbookok jellemzően a helyi számítógépen lévő erőforrásokat, vagy a helyi környezetben lévő erőforrásokat kezelik. A Azure Automation runbookok általában az Azure-felhőben lévő erőforrásokat kezelik.

Ha hibrid Runbook-feldolgozón futtatja a runbookok-t, akkor a hibrid feldolgozót üzemeltető gépen belül kell szerkesztenie és tesztelni a runbookok. A gazdagépen minden PowerShell-modul és hálózati hozzáférés szükséges a helyi erőforrások kezeléséhez és eléréséhez. Miután tesztelte a runbook a hibrid munkavégző gépen, feltöltheti azt a Azure Automation-környezetbe, ahol a hibrid feldolgozón futtatható. Fontos tudni, hogy a Windows helyi rendszerfiókja vagy a Linux rendszerhez készült speciális felhasználói fiók `nxautomation` alatt futó feladatok. Ez a viselkedés a hibrid Runbook-feldolgozók runbookok készítésekor is finom különbségeket mutat be. Ezeket a módosításokat a runbookok írásakor kell megtekinteni.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Runbook indítása hibrid Runbook-feldolgozón

A [Runbook indítása Azure Automation](automation-starting-a-runbook.md) ismerteti a Runbook indításának különböző módszereit. A hibrid Runbook Worker egy **RunOn** lehetőséget ad, ahol megadhatja a hibrid Runbook-feldolgozó csoport nevét. Ha meg van adva egy csoport, akkor a runbook a rendszer lekéri és futtatja az adott csoport egyik feldolgozója. Ha ez a beállítás nincs megadva, akkor a Azure Automation a megszokott módon fut.

Amikor elindít egy runbook a Azure Portalban, egy futtatási lehetőség jelenik meg, ahol kiválaszthatja az **Azure** -t vagy a **hibrid**feldolgozót. Ha a **hibrid**feldolgozót választja, akkor kiválaszthatja a csoportot egy legördülő listából.

Használja a **RunOn** paramétert. A következő parancs segítségével elindíthat egy test-Runbook nevű runbook egy MyHybridGroup nevű hibrid Runbook Worker csoporton a Windows PowerShell használatával.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> A **RunOn** paraméter hozzá lett adva a **Start-AzureAutomationRunbook** parancsmaghoz a (z) Microsoft Azure PowerShell verziójának 0.9.1. Ha korábban már telepítve van, [töltse le a legújabb verziót](https://azure.microsoft.com/downloads/) . Ezt a verziót csak olyan munkaállomáson kell telepíteni, ahol a runbook a PowerShellből indítja el. Nem kell telepítenie a munkavégző számítógépre, ha nem kívánja elindítani a runbookok a számítógépről. "

## <a name="runbook-permissions"></a>Runbook engedélyek

A hibrid Runbook-feldolgozón futó runbookok nem használhatja ugyanazt a metódust, amelyet általában az Azure-erőforrásokhoz való runbookok-hitelesítéshez használ, mivel nem az Azure-ban érik el az erőforrásokat. A runbook megadhatja a saját hitelesítését a helyi erőforrásokhoz, vagy konfigurálhatja [a hitelesítést az Azure-erőforrások](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
)felügyelt identitásai használatával. Megadhat egy futtató fiókot is, amely felhasználói környezetet biztosít az összes runbookok.

### <a name="runbook-authentication"></a>Runbook-hitelesítés

Alapértelmezés szerint a runbookok a helyi rendszerfiók környezetében fut a Windows rendszerhez, és egy speciális felhasználói `nxautomation` fiók a Linux rendszerhez a helyszíni számítógépen, ezért meg kell adnia saját hitelesítését az általuk elért erőforrásokhoz.

A runbook olyan parancsmagokkal használhatja a [hitelesítő adatokat](automation-credentials.md) és [tanúsítványokat](automation-certificates.md) , amelyek lehetővé teszik a hitelesítő adatok megadását, így a különböző erőforrásokhoz is elvégezheti a hitelesítést. Az alábbi példa egy runbook egy részét mutatja be, amely újraindítja a számítógépet. Hitelesítő adatokat kér le a hitelesítő adatokból és a számítógép nevét egy változó objektumból, majd ezeket az értékeket használja a restart-Computer parancsmaggal.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Használhatja a [InlineScript](automation-powershell-workflow.md#inlinescript)is, amely lehetővé teszi, hogy a [PSCredential Common paraméterrel](/powershell/module/psworkflow/about/about_workflowcommonparameters)megadott hitelesítő adatokkal rendelkező számítógépen kódokat futtasson.

### <a name="runas-account"></a>Futtató fiók

Alapértelmezés szerint a hibrid Runbook-feldolgozó a Windows helyi rendszerét és egy speciális felhasználói `nxautomation` fiókot használ a Linux rendszerhez a runbookok végrehajtásához. Ahelyett, hogy a runbookok saját hitelesítéssel lássa el a helyi erőforrásokat, megadhat egy futtató fiókot egy hibrid feldolgozói csoport számára. Meg kell adnia egy [hitelesítő adatot](automation-credentials.md) , amely hozzáféréssel rendelkezik a helyi erőforrásokhoz, és az összes runbookok ezen hitelesítő adatok alatt fut, ha a csoport egy hibrid Runbook-feldolgozóján fut.

A hitelesítő adatok felhasználónevének a következő formátumok egyikében kell lennie:

* tartomány \ Felhasználónév
* username@domain
* username (a helyszíni számítógép helyi számítógépén lévő fiókok esetében)

Egy hibrid feldolgozói csoport futtató fiókjának megadásához kövesse az alábbi eljárást:

1. Hozzon létre egy [hitelesítő eszközt](automation-credentials.md) a helyi erőforrásokhoz való hozzáféréssel.
2. Nyissa meg az Automation-fiókot a Azure Portal.
3. Válassza ki a **hibrid munkavégző csoportok** csempét, majd válassza ki a csoportot.
4. Válassza **a minden beállítás** , majd a **hibrid feldolgozói csoport beállításai**lehetőséget.
5. A **Futtatás** alapértelmezettről egyénirevaló módosítása.
6. Válassza ki a hitelesítő adatokat, majd kattintson a **Mentés**gombra.

### <a name="managed-identities-for-azure-resources"></a>Felügyelt identitások az Azure-erőforrásokhoz

Az Azure-beli virtuális gépeken futó hibrid Runbook-feldolgozók felügyelt identitásokat használhatnak az Azure-erőforrásokhoz az Azure-erőforrásokban való hitelesítéshez. Számos előnnyel jár a felügyelt identitások használata az Azure-erőforrásokhoz a futtató fiókokon keresztül.

* Nincs szükség a futtató tanúsítvány exportálására, majd a hibrid Runbook-feldolgozóba való importálására.
* Nincs szükség a futtató fiók által használt tanúsítvány megújítására
* Nem kell kezelnie a futtató kapcsolódási objektumot a runbook-kódban

Ha egy hibrid Runbook-feldolgozón felügyelt identitást szeretne használni az Azure-erőforrásokhoz, a következő lépéseket kell elvégeznie:

1. Azure-beli virtuális gép létrehozása
2. [Felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuális gépen](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [A virtuális gép hozzáférésének biztosítása egy erőforráscsoport számára a Resource Managerben](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Hozzáférési jogkivonat beszerzése a virtuális gép rendszer által hozzárendelt felügyelt identitásának használatával](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Telepítse a Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) szolgáltatást a virtuális gépre.

Miután az előző lépések befejeződik, a runbook használatával `Connect-AzureRmAccount -Identity` hitelesítheti magát az Azure-erőforrásokban. Ez a konfiguráció csökkenti a futtató fiók használatának és a tanúsítványnak a futtató fiókhoz való felügyeletének szükségességét.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Automation futtató fiók

Az erőforrások Azure-ban való üzembe helyezéséhez szükséges automatizált felépítési folyamat részeként szükség lehet a helyszíni rendszerekhez való hozzáférésre, hogy támogassa a feladatait vagy lépéseit a telepítési sorozatban. Az Azure-ban a futtató fiók használatával történő hitelesítés támogatásához telepítenie kell a futtató fiók tanúsítványát.

A következő PowerShell-runbook, **export-RunAsCertificateToHybridWorker**, exportálja a futtató tanúsítványt a Azure Automation-fiókjából, és letölti és importálja a helyi számítógép tanúsítványtárolóba egy olyan hibrid feldolgozón, amely csatlakoztatva van ugyanahhoz a fiókhoz. A lépés elvégzése után ellenőrzi, hogy a feldolgozó sikeresen tud-e hitelesíteni az Azure-ban a futtató fiók használatával.

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
> A **Add-AzureRmAccount** mostantól egy alias a **kapcsolat-AzureRmAccount**. Ha nem látja a **AzureRMAccount**, használhatja a **AzureRMAccount**, vagy frissítheti a modulokat az Automation-fiókban.

Mentse az *export-RunAsCertificateToHybridWorker* runbook a számítógépre egy `.ps1` bővítménnyel. Importálja az Automation-fiókjába, és szerkessze a runbook, és módosítsa a `$Password` változó értékét a saját jelszavával. Tegye közzé, majd futtassa a runbook. Célozza meg a hibrid munkavégző csoportot, amely a futtató fiók használatával fogja futtatni és hitelesíteni a runbookok. A feladatsor azt jelenti, hogy a tanúsítványt a helyi számítógép tárolójába importálja, és több sorral együtt követi. Ez a viselkedés attól függ, hogy hány Automation-fiókot határoz meg az előfizetésben, és ha a hitelesítés sikeres.

## <a name="job-behavior"></a>A feladatok viselkedése

A feladatok kezelése némileg eltér a hibrid Runbook-feldolgozóknál, mint amikor az Azure-beli Sandboxon futnak. Az egyik legfontosabb különbség, hogy a hibrid Runbook-feldolgozók esetében nincs korlát a feladatok időtartamára. Az Azure-beli munkaterületeken futtatott runbookok a [méltányos megosztás](automation-runbook-execution.md#fair-share)miatt 3 óráig tartanak. Hosszan futó runbook esetén győződjön meg arról, hogy az esetleges újraindításra rugalmas. Például ha a hibrid munkavégzőt futtató gép újraindul. Ha a hibrid feldolgozó gazdagép újraindul, akkor a futó runbook-feladatok a kezdetektől vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontján indulnak újra. A runbook-feladatok többszöri újraindítása után a rendszer felfüggeszti a felfüggesztést.

## <a name="run-only-signed-runbooks"></a>Csak aláírt Runbookok futtatása

A hibrid Runbook-feldolgozók úgy konfigurálhatók, hogy csak az aláírt runbookok futtassanak néhány konfigurációval. A következő szakasz azt ismerteti, hogyan állíthatja be a hibrid Runbook-feldolgozókat aláírt [Windows Hybrid Runbook Worker](#windows-hybrid-runbook-worker) és [Linux Hybrid Runbook Worker](#linux-hybrid-runbook-worker) futtatásához

> [!NOTE]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak az aláírt runbookok futtassa, a nem aláírt runbookok **nem** lesznek végrehajtva a munkavégzőn.

### <a name="windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker

#### <a name="create-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A következő példa létrehoz egy önaláírt tanúsítványt, amely a runbookok aláírására használható. A minta létrehozza a tanúsítványt, és exportálja azt. A rendszer később importálja a tanúsítványt a hibrid Runbook-feldolgozókba. Az ujjlenyomatot is visszaadja, ezt az értéket később a tanúsítványra való hivatkozáshoz használja a rendszer.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>A hibrid Runbook-feldolgozók konfigurálása

Másolja a létrehozott tanúsítványt a csoport minden hibrid Runbook-feldolgozója számára. Futtassa a következő szkriptet a tanúsítvány importálásához és a hibrid feldolgozó konfigurálásához a runbookok aláírás-ellenőrzésének használatához.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Runbookok aláírása a tanúsítvány használatával

Ha a hibrid Runbook-feldolgozók csak aláírt runbookok használatára vannak konfigurálva, a hibrid Runbook-feldolgozón használni kívánt runbookok alá kell írniuk. A következő PowerShell-minta használatával írja alá a runbookok.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

A runbook aláírása után importálni kell az Automation-fiókjába, és közzé kell tennie az aláírási blokkban. A runbookok importálásával kapcsolatos további információkért lásd: [Runbook importálása fájlból Azure Automationba](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker

Ha Linux hibrid Runbook-feldolgozón szeretné aláírni a runbookok, a hibrid Runbook-feldolgozónak rendelkeznie kell a számítógépen a [GPG](https://gnupg.org/index.html) végrehajtható fájlokkal.

#### <a name="create-a-gpg-keyring-and-keypair"></a>GPG-kulcstartó és-kulcspár létrehozása

A kulcstartó és a kulcspár létrehozásához a hibrid Runbook Worker-fiókot `nxautomation`kell használnia.

A `sudo` használatával jelentkezzen be `nxautomation` fiókként.

```bash
sudo su – nxautomation
```

A `nxautomation` fiók használata után létrehozza a GPG-kulcspárt.

```bash
sudo gpg --generate-key
```

A GPG végigvezeti a kulcspár létrehozásának lépésein. Meg kell adnia egy nevet, egy e-mail-címet, a lejárati időt, a hozzáférési kódot, és várnia kell a gép megfelelő entrópia-azonosítóját a kulcs generálásához.

Mivel a GPG könyvtárat a sudo hozta létre, módosítania kell a tulajdonosát a `nxautomation`következőre:.

Futtassa a következő parancsot a tulajdonos módosításához.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>A kulcstartó elérhetővé tétele a hibrid Runbook Worker számára

A kulcstartó létrehozása után elérhetővé kell tennie a hibrid Runbook Worker számára. Módosítsa a beállítási fájlt `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` úgy, hogy az tartalmazza a következő példát a szakasz`[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Aláírás ellenőrzésének ellenőrzése bekapcsolva

Ha az aláírás-ellenőrzés le van tiltva a gépen, be kell kapcsolni. Futtassa az alábbi parancsot az aláírás-ellenőrzés engedélyezéséhez. Cserélje `<LogAnalyticsworkspaceId>` le a kifejezést a munkaterület-azonosítóra.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Runbook aláírása

Az aláírás-ellenőrzés konfigurálása után a következő paranccsal lehet aláírni a runbook:

```bash
gpg –-clear-sign <runbook name>
```

Az aláírt runbook neve `<runbook name>.asc`fog megjelenni.

Az aláírt runbook mostantól a Azure Automationba tölthetők fel, és normál runbook is végrehajthatók.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a runbook elindításához használható különböző módszerekről, tekintse meg a [runbook](automation-starting-a-runbook.md)megkezdése Azure Automationban című témakört.
* A Azure Automation PowerShell-runbookok a szöveges szerkesztővel való használatának különböző módjairól a következő témakörben talál további információt: [Runbook szerkesztése Azure Automation](automation-edit-textual-runbook.md)
* Ha a runbookok nem fejeződik be sikeresen, tekintse át a hibaelhárítási útmutatót a [runbook végrehajtásával](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)kapcsolatos hibákról.
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/en-us/powershell/scripting/overview).
