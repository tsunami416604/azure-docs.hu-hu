---
title: Runbookok futtatása Azure Automation hibrid Runbook-feldolgozón
description: Ez a cikk tájékoztatást nyújt arról, hogyan futtathatja a runbookok a helyi adatközpontban vagy a felhőben a hibrid Runbook-feldolgozóval rendelkező gépeken.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: a86139c7becaae996e343166088b416dd8d6404f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855633"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid runbook-feldolgozón

A hibrid Runbook-feldolgozót célzó runbookok általában a helyi számítógépen vagy a munkavégző üzembe helyezett helyi környezetben lévő erőforrásokkal kezelik az erőforrásokat. A Azure Automation runbookok általában az Azure-felhőben lévő erőforrásokat kezelik. Annak ellenére, hogy eltérő módon használják őket, a runbookok, amely egy hibrid Runbook-feldolgozón futó Azure Automation és runbookok fut, azonos a struktúrában.

Ha hibrid Runbook-feldolgozón futtat egy runbook, akkor a runbook a munkavégzőt futtató gépen kell szerkesztenie és tesztelni. A gazdagépen minden PowerShell-modul és hálózati hozzáférés szükséges a helyi erőforrások kezeléséhez és eléréséhez. Ha teszteli a runbook a hibrid Runbook Worker gépen, feltöltheti azt a Azure Automation-környezetbe, ahol futtatható a feldolgozón. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="plan-runbook-job-behavior"></a>Runbook-feladatok működésének megtervezése

A Azure Automation a hibrid Runbook-feldolgozók feladatait némileg eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Ha hosszan futó runbook rendelkezik, győződjön meg arról, hogy az esetleges újraindításra rugalmas. A feladat működésével kapcsolatos részletekért lásd: [hibrid Runbook Worker Jobs](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Ne feledje, hogy a hibrid Runbook-feldolgozókhoz tartozó feladatok a helyi rendszerfiók alatt futnak a **Windows rendszeren vagy** a Linuxon futó **nxautomation** -fiókban. Linux esetén győződjön meg arról, hogy a **nxautomation** -fiók rendelkezik hozzáféréssel ahhoz a helyhez, ahol a runbook-modulok vannak tárolva. Ha az [install-Module](/powershell/module/powershellget/install-module) parancsmagot használja, ügyeljen arra, hogy a `Scope` paraméter AllUsers megadásával biztosítsa, hogy a **nxautomation** -fiók hozzáférhessen. A Linux PowerShell-lel kapcsolatos további információkért lásd: [ismert problémák a PowerShell számára a nem Windows platformokon](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="set-up-runbook-permissions"></a>Runbook engedélyeinek beállítása

A következő módokon adhat meg engedélyeket a runbook a hibrid Runbook-kezelőn való futtatásához:

* Adja meg a runbook a saját hitelesítését a helyi erőforrásokhoz.
* A hitelesítés konfigurálása [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)használatával. 
* Adjon meg egy futtató fiókot, amely minden runbookok felhasználói környezetet biztosít.

## <a name="use-runbook-authentication-to-local-resources"></a>Runbook-hitelesítés használata helyi erőforrásokhoz

Ha olyan runbook készít elő, amely saját hitelesítést biztosít az erőforrásokhoz, használja a runbook [hitelesítő adatait](automation-credentials.md) és [tanúsítványait](automation-certificates.md) . Több parancsmag is rendelkezésre áll, amelyek lehetővé teszik a hitelesítő adatok megadását, hogy a runbook különböző erőforrásokhoz lehessen hitelesíteni. Az alábbi példa egy runbook egy részét mutatja be, amely újraindítja a számítógépet. Hitelesítő adatokat kér le a hitelesítő adatokból és a számítógép nevét egy változó objektumból, majd ezeket az értékeket használja a `Restart-Computer` parancsmaggal.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript](automation-powershell-workflow.md#inlinescript) -tevékenységet is használhat. `InlineScript`lehetővé teszi, hogy a [PSCredential Common paraméterében](/powershell/module/psworkflow/about/about_workflowcommonparameters)megadott hitelesítő adatokkal futtasson kódokat egy másik számítógépen.

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Runbook-hitelesítés használata felügyelt identitásokkal

Az Azure-beli virtuális gépek hibrid Runbook dolgozói felügyelt identitásokat használhatnak az Azure-erőforrásokhoz az Azure-erőforrásokhoz való hitelesítéshez. Az Azure-erőforrások felügyelt identitások használata a futtató fiókok helyett a következő előnyöket nyújtja:

* Exportálja a futtató tanúsítványt, majd importálja a hibrid Runbook-feldolgozóba.
* Újítsa meg a futtató fiók által használt tanúsítványt.
* Kezeli a futtató kapcsolatok objektumát a runbook-kódban.

A következő lépésekkel felügyelt identitást használhat egy hibrid Runbook-feldolgozón az Azure-erőforrásokhoz.

1. Hozzon létre egy Azure-beli virtuális gépet.
2. Felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuális gépen. Lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Adjon hozzáférést a virtuális géphez egy erőforráscsoporthoz a Resource Managerben. A [Resource Manager eléréséhez tekintse meg a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használatát](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)ismertető témakört.
4. Telepítse a hibrid Runbook-feldolgozót a virtuális gépre. Lásd: [Windows Hybrid Runbook Worker telepítése](automation-windows-hrw-install.md).
5. Frissítse a runbook, [hogy a AzAccount parancsmagot](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) használja a `Identity` paraméterrel az Azure-erőforrásokban való hitelesítéshez. Ez a konfiguráció csökkenti a futtató fiók használatának és a társított Fiókkezelés elvégzésének szükségességét.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`a hibrid Runbook-feldolgozók számára a rendszer által hozzárendelt identitást és egyetlen felhasználó által hozzárendelt identitást használ. Ha több felhasználó által hozzárendelt identitást használ a hibrid Runbook-feldolgozón, a Runbook meg `AccountId` kell adnia `Connect-AzAccount` a paramétert egy adott felhasználó által hozzárendelt identitás kiválasztásához.

## <a name="use-runbook-authentication-with-run-as-account"></a>Runbook-hitelesítés használata futtató fiókkal

Ahelyett, hogy a runbook saját hitelesítéssel lássa el a helyi erőforrásokat, megadhat egy futtató fiókot egy hibrid Runbook-feldolgozói csoport számára. Ehhez meg kell adnia egy olyan hitelesítőadat- [eszközt](automation-credentials.md) , amely hozzáfér a helyi erőforrásokhoz. Ezek az erőforrások közé tartoznak a tanúsítványtárolók, és minden runbookok ezen hitelesítő adatok alatt fut a csoport egy hibrid Runbook-feldolgozóján.

A hitelesítő adatok felhasználónevének a következő formátumok egyikében kell lennie:

* tartomány \ Felhasználónév
* username@domain
* username (a helyszíni számítógép helyi számítógépén lévő fiókok esetében)

A hibrid Runbook-feldolgozó csoportok futtató fiókjának megadásához kövesse az alábbi eljárást.

1. Hozzon létre egy [hitelesítő eszközt](automation-credentials.md) a helyi erőforrásokhoz való hozzáféréssel.
2. Nyissa meg az Automation-fiókot a Azure Portal.
3. Válassza ki a **hibrid munkavégző csoportok** csempét, majd válassza ki a csoportot.
4. Válassza a **minden beállítás**elemet, majd a **hibrid feldolgozói csoport beállításait**.
5. Módosítsa a **Futtatás** **alapértelmezett** értékét az **Egyéni**értékre.
6. Válassza ki a hitelesítő adatokat, majd kattintson a **Mentés**gombra.

### <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Futtató fiók tanúsítványának telepítése

Az erőforrások Azure-ban való üzembe helyezéséhez szükséges automatizált felépítési folyamat részeként szükség lehet a helyszíni rendszerekhez való hozzáférésre, hogy támogassa a feladatait vagy lépéseit a telepítési sorozatban. Ha a futtató fiók használatával szeretne hitelesítést biztosítani az Azure-ban, telepítenie kell a futtató fiók tanúsítványát.

A következő PowerShell-runbook, az **export-RunAsCertificateToHybridWorker néven exportálja**a futtató tanúsítványt a Azure Automation-fiókjából. A runbook letölti és importálja a tanúsítványt a helyi számítógép tanúsítványtárolójában egy olyan hibrid Runbook-feldolgozón, amely ugyanahhoz a fiókhoz csatlakozik. Miután befejezte ezt a lépést, a runbook ellenőrzi, hogy a feldolgozó sikeresen tud-e hitelesíteni az Azure-ban a futtató fiók használatával.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
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

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>A PowerShell-runbookok `Add-AzAccount` esetében `Add-AzureRMAccount` a és a álneve `Connect-AzAccount`a következőhöz:. Ha nem látja `Connect-AzAccount`a könyvtár elemeit, használhatja `Add-AzAccount`a vagy az Automation-fiókjában található modulokat is.

A futtató fiók előkészítésének befejezése:

1. Mentse az **export-RunAsCertificateToHybridWorker** runbook a számítógépre **. ps1** kiterjesztéssel.
2. Importálja az Automation-fiókjába.
3. Szerkessze a runbook, és módosítsa a `Password` változó értékét a saját jelszavára. 
4. Tegye közzé a runbook.
5. Futtassa a runbook, amely a futtató fiók használatával futtatja és hitelesíti a runbookok a hibrid Runbook-munkavégző csoportot. 
6. Vizsgálja meg a folyamat adatfolyamát, és figyelje meg, hogy a tanúsítvány importálására tett kísérlet a helyi számítógép tárolójába történik, és több sorral is elvégezhető. Ez a viselkedés attól függ, hogy hány Automation-fiókot határoz meg az előfizetésében, valamint a hitelesítés sikerességének fokát.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Runbook elindítása hibrid Runbook-feldolgozón

[Runbook elindítása Azure Automation](start-runbooks.md) ismerteti a runbook indításának különböző módszereit. A hibrid Runbook-feldolgozók runbook indítása olyan **futtatási** lehetőséget használ, amely lehetővé teszi a hibrid runbook-feldolgozói csoport nevének megadását. Ha meg van adva egy csoport, a csoport egyik feldolgozója kéri le és futtatja a runbook. Ha a runbook nem adja meg ezt a beállítást, Azure Automation a szokásos módon futtatja a runbook.

Amikor elindít egy runbook a Azure Portalban, megjelenik a **Futtatás** lehetőség, amelyen kiválaszthatja az **Azure** -t vagy a **hibrid feldolgozót**. Ha a **hibrid feldolgozót**választja, kiválaszthatja a hibrid Runbook Worker csoportot egy legördülő listából.

Használja a `RunOn` paramétert a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) parancsmaggal. Az alábbi példa a Windows PowerShell használatával indítja el a **test-runbook** nevű Runbook egy MyHybridGroup nevű hibrid runbook Worker csoporton.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Ha korábban már telepítve van, [töltse le a PowerShell legújabb verzióját](https://azure.microsoft.com/downloads/) . Csak akkor telepítse ezt a verziót azon a munkaállomáson, ahol a runbook a PowerShellből indítja el. Nem kell telepítenie a hibrid Runbook Worker számítógépre, ha nem kívánja elindítani a runbookok a számítógépről.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Aláírt runbookok használata Windows hibrid Runbook-feldolgozón

Egy Windows Hybrid Runbook Worker-feldolgozót úgy konfigurálhat, hogy csak az aláírt runbookok futtassa.

> [!IMPORTANT]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak az aláírt runbookok futtassa, a nem aláírt runbookok nem lesznek végrehajtva a munkavégzőn.

### <a name="create-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A következő példa létrehoz egy önaláírt tanúsítványt, amely a runbookok aláírására használható. Ez a kód létrehozza a tanúsítványt, és exportálja azt, hogy a hibrid Runbook-feldolgozó később is importálja. Az ujjlenyomatot is visszaadja a rendszer a tanúsítványra hivatkozó későbbi használatra.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Tanúsítvány importálása és a feldolgozók konfigurálása aláírás-ellenőrzéshez

Másolja a létrehozott tanúsítványt egy csoport minden hibrid Runbook-feldolgozója számára. Futtassa a következő szkriptet a tanúsítvány importálásához és a feldolgozók konfigurálásához az aláírás-érvényesítés használatára a runbookok-on.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Runbookok aláírása a tanúsítvány használatával

Ha a hibrid Runbook-feldolgozók csak aláírt runbookok használatára vannak konfigurálva, a hibrid Runbook-feldolgozón használni kívánt runbookok alá kell írniuk. A következő PowerShell-kód használatával írja alá ezeket a runbookok.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Egy runbook aláírása után importálnia kell az Automation-fiókjába, és közzé kell tennie az aláírási blokkban. A runbookok importálásával kapcsolatos további információkért lásd: [Runbook importálása](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Aláírt runbookok használata linuxos hibrid Runbook-feldolgozón

Ahhoz, hogy az aláírt runbookok működjön, egy linuxos hibrid Runbook-feldolgozónak rendelkeznie kell a [GPG](https://gnupg.org/index.html) végrehajtható fájllal a helyi gépen.

> [!IMPORTANT]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak az aláírt runbookok futtassa, a nem aláírt runbookok nem lesznek végrehajtva a munkavégzőn.

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG-kulcstartó és-kulcspár létrehozása

A GPG kulcstartó és a kulcspár létrehozásához használja a hibrid Runbook Worker **nxautomation** -fiókot.

1. A sudo alkalmazás használatával jelentkezzen be a **nxautomation** -fiókkal.

    ```bash
    sudo su – nxautomation
    ```

2. A **nxautomation**használata után létrehozza a GPG-kulcspárt. A GPG végigvezeti Önt a lépéseken. Meg kell adnia a nevet, az e-mail-címet, a lejárati időt és a hozzáférési kódot. Ezután várjon, amíg nincs elég entrópia a gépen a kulcs generálásához.

    ```bash
    sudo gpg --generate-key
    ```

3. Mivel a GPG-könyvtár a sudo-val lett létrehozva, a következő parancs használatával módosítania kell a tulajdonosát a **nxautomation** .

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>A kulcstartó elérhetővé tétele a hibrid Runbook Worker számára

Miután létrehozta a kulcstartót, tegye elérhetővé a hibrid Runbook Worker számára. Módosítsa a Settings (beállítások) fájl **/var/opt/Microsoft/omsagent/State/automationworker/DIY/Worker.conf** , hogy az a fájl szakaszban `[worker-optional]`szerepeljen a következő példában szereplő kód.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Ellenőrizze, hogy be van-e kapcsolva az aláírás ellenőrzése

Ha az aláírás-ellenőrzés le van tiltva a gépen, azt be kell kapcsolni a következő sudo parancs futtatásával. Cserélje `<LogAnalyticsworkspaceId>` le a helyére a munkaterület-azonosítót.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook aláírása

Miután konfigurálta az aláírás-ellenőrzést, a következő GPG-paranccsal írja alá a runbook.

```bash
gpg –-clear-sign <runbook name>
```

Az aláírt runbook neve ** <runbook name>. ASC**.

Most már feltöltheti az aláírt runbook Azure Automation, és végrehajthatja, mint egy normál runbook.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan használhatja a szöveges szerkesztőt a Azure Automation PowerShell-runbookok való együttműködésre, tekintse meg [a Runbook Azure Automation-ban való szerkesztését](automation-edit-textual-runbook.md)ismertető témakört.
* Ha a runbookok nem fejeződik be sikeresen, tekintse át a [runbook-végrehajtási hibák](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)hibaelhárítási útmutatóját.
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
