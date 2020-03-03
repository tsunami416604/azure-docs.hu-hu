---
title: Runbookok futtatása Azure Automation hibrid Runbook-feldolgozón
description: Ez a cikk a hibrid Runbook-feldolgozói szerepkörrel rendelkező, a helyi adatközpontban vagy a felhőben működő gépek runbookok futtatásával kapcsolatos információkat tartalmaz.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: c67fff32770446cac3adef8af50c9e5733077bc7
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226280"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid Runbook-feldolgozón

A hibrid Runbook-feldolgozót célzó runbookok általában a helyi számítógépen vagy a munkavégző üzembe helyezett helyi környezetben lévő erőforrásokkal kezelik az erőforrásokat. A Azure Automation runbookok általában az Azure-felhőben lévő erőforrásokat kezelik. Annak ellenére, hogy eltérő módon használják őket, a runbookok, amely egy hibrid Runbook-feldolgozón futó Azure Automation és runbookok fut, azonos a struktúrában.

Ha hibrid Runbook-feldolgozón futtat egy runbook, akkor a runbook a munkavégzőt futtató gépen kell szerkesztenie és tesztelni. A gazdagépen minden PowerShell-modul és hálózati hozzáférés szükséges a helyi erőforrások kezeléséhez és eléréséhez. Ha teszteli a runbook a hibrid Runbook Worker gépen, feltöltheti azt a Azure Automation-környezetbe, ahol futtatható a feldolgozón. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook engedélyek a hibrid Runbook-feldolgozók számára

Mivel a nem Azure-beli erőforrásokhoz férnek hozzá, a hibrid Runbook-feldolgozón futó runbookok nem használhatja az Azure-erőforrásokhoz való runbookok-hitelesítéssel jellemzően használt hitelesítési mechanizmust. A runbook lehetővé teszi a saját hitelesítését a helyi erőforrásokhoz, vagy a hitelesítést [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)segítségével konfigurálja. Megadhat egy futtató fiókot is, amely felhasználói környezetet biztosít az összes runbookok számára.

### <a name="runbook-authentication"></a>Runbook-hitelesítés

Alapértelmezés szerint a runbookok a helyi számítógépen fut. Windows rendszeren a helyi rendszerfiók környezetében futnak. A Linux esetében a speciális felhasználói fiók **nxautomation**környezetében futnak. Mindkét esetben a runbookok meg kell adnia saját hitelesítését az általuk elért erőforrásokhoz.

A runbook olyan parancsmagokkal használhatja a [hitelesítő adatokat](automation-credentials.md) és [tanúsítványokat](automation-certificates.md) , amelyek lehetővé teszik a hitelesítő adatok megadását, hogy a runbook különböző erőforrásokhoz lehessen hitelesíteni. Az alábbi példa egy runbook egy részét mutatja be, amely újraindítja a számítógépet. Hitelesítő adatokat kér le a hitelesítő adatokból és a számítógép nevét egy változó objektumból, majd ezeket az értékeket használja a **restart-Computer** parancsmaggal.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript](automation-powershell-workflow.md#inlinescript) -tevékenységet is használhat. A InlineScript lehetővé teszi, hogy a [PSCredential Common paraméterében](/powershell/module/psworkflow/about/about_workflowcommonparameters)megadott hitelesítő adatokkal futtasson kódokat egy másik számítógépen.

### <a name="run-as-account"></a>Futtató fiók

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

### <a name="managed-identities-for-azure-resources"></a>Felügyelt identitások az Azure-erőforrásokhoz

Az Azure-beli virtuális gépek hibrid Runbook dolgozói felügyelt identitásokat használhatnak az Azure-erőforrásokhoz az Azure-erőforrásokhoz való hitelesítéshez. Az Azure-erőforrások felügyelt identitások használata a futtató fiókok helyett a következő előnyöket nyújtja:

* Exportálja a futtató tanúsítványt, majd importálja a hibrid Runbook-feldolgozóba
* A futtató fiók által használt tanúsítvány megújítása
* A futtató kapcsolatok objektum kezelése a runbook-kódban

A következő lépésekkel felügyelt identitást használhat egy hibrid Runbook-feldolgozón az Azure-erőforrásokhoz.

1. Hozzon létre egy Azure-beli virtuális gépet.
2. Felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuális gépen. Lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Adjon hozzáférést a virtuális géphez egy erőforráscsoporthoz a Resource Managerben. A [Resource Manager eléréséhez tekintse meg a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használatát](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)ismertető témakört.
4. Telepítse a hibrid Runbook-feldolgozót a virtuális gépre. Lásd: [Windows Hybrid Runbook Worker telepítése](automation-windows-hrw-install.md).
5. Frissítse a runbook, hogy a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) parancsmagot használja az *Identity* paraméterrel az Azure-erőforrásokban való hitelesítéshez. Ez a konfiguráció csökkenti a futtató fiók használatának és a társított Fiókkezelés elvégzésének szükségességét.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> a `Connect-AzAccount -Identity` a rendszer által hozzárendelt identitást és egyetlen felhasználó által hozzárendelt identitást használó hibrid Runbook-feldolgozók esetében működik. Ha több felhasználó által hozzárendelt identitást használ a hibrid Runbook-feldolgozón, a Runbook meg kell adnia a *AccountId* paramétert a **kapcsolódási AzAccount** , hogy kiválassza az adott felhasználó által hozzárendelt identitást.

### <a name="runas-script"></a>Automation futtató fiók

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
>PowerShell-runbookok esetében a **Add-AzAccount** és a **Add-AzureRMAccount** aliasok a **csatlakozási-AzAccount**. Ha nem látja a **AzAccount**, használhatja a **AzAccount**, vagy frissítheti a modulokat az Automation-fiókban.

A futtató fiók előkészítésének befejezése:

1. Mentse az **export-RunAsCertificateToHybridWorker** runbook a számítógépre **. ps1** kiterjesztéssel.
2. Importálja az Automation-fiókjába.
3. Szerkessze a runbook, és módosítsa a *jelszó* változó értékét a saját jelszavával. 
4. Tegye közzé a runbook.
5. Futtassa a runbook, amely a futtató fiók használatával futtatja és hitelesíti a runbookok a hibrid Runbook-munkavégző csoportot. 
6. Vizsgálja meg a folyamat adatfolyamát, és figyelje meg, hogy a tanúsítvány importálására tett kísérlet a helyi számítógép tárolójába történik, és több sorral is elvégezhető. Ez a viselkedés attól függ, hogy hány Automation-fiókot határoz meg az előfizetésében, valamint a hitelesítés sikerességének fokát.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Viselkedés a hibrid Runbook-feldolgozók esetében

A Azure Automation a hibrid Runbook-feldolgozók feladatait némileg eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Az egyik legfontosabb különbség, hogy a runbook-feldolgozók nem korlátozzák a feladatok időtartamát. A runbookok Azure-beli munkaterületeken való futtatása a [méltányos megosztás](automation-runbook-execution.md#fair-share)miatt legfeljebb három órát vesz igénybe.

Hosszan futó runbook esetén győződjön meg arról, hogy az esetleges újraindításra képes, például ha a munkavégző gépet futtató gép újraindul. Ha a hibrid Runbook Worker gazdagépe újraindul, a futó Runbook-feladatok a kezdetektől vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontján indulnak újra. A runbook-feladatok többszöri újraindítása után a rendszer felfüggeszti a műveletet.

Ne feledje, hogy a hibrid Runbook-feldolgozókhoz tartozó feladatok a helyi rendszerfiók alatt futnak a Windows rendszeren vagy a Linuxon futó **nxautomation** -fiókban. A Linux esetében gondoskodnia kell arról, hogy a **nxautomation** -fiók hozzáférjen a runbook-modulok tárolására szolgáló helyhez. Ha az [install-Module](/powershell/module/powershellget/install-module) parancsmagot használja, ügyeljen arra, hogy a *hatókör* -paraméterhez **AllUsers** ad meg, hogy a **nxautomation** -fiók hozzáférhessen. A Linux PowerShell-lel kapcsolatos további információkért lásd: [ismert problémák a PowerShell számára a nem Windows platformokon](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Runbook indítása hibrid Runbook-feldolgozón

A [Runbook indítása Azure Automation](automation-starting-a-runbook.md) ismerteti a runbook indításának különböző módszereit. A hibrid Runbook-feldolgozók runbook indítása olyan **futtatási** lehetőséget használ, amely lehetővé teszi a hibrid runbook-feldolgozói csoport nevének megadását. Ha meg van adva egy csoport, a csoport egyik feldolgozója kéri le és futtatja a runbook. Ha a runbook nem adja meg ezt a beállítást, Azure Automation a szokásos módon futtatja a runbook.

Amikor elindít egy runbook a Azure Portalban, megjelenik a **Futtatás** lehetőség, amelyen kiválaszthatja az **Azure** -t vagy a **hibrid feldolgozót**. Ha a **hibrid feldolgozót**választja, kiválaszthatja a hibrid Runbook Worker csoportot egy legördülő listából.

Használja a *RunOn* paramétert a **Start-AzureAutomationRunbook** parancsmaggal. Az alábbi példa a Windows PowerShell használatával indítja el a **test-runbook** nevű Runbook egy MyHybridGroup nevű hibrid runbook Worker csoporton.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> A *RunOn* paramétert a **Start-AzureAutomationRunbook** értékhez adta hozzá a (z) Microsoft Azure PowerShell verziójának 0.9.1. Ha korábban már telepítve van, [töltse le a legújabb verziót](https://azure.microsoft.com/downloads/) . Csak akkor telepítse ezt a verziót azon a munkaállomáson, ahol a runbook a PowerShellből indítja el. Nem kell telepítenie a hibrid Runbook Worker számítógépre, ha nem kívánja elindítani a runbookok a számítógépről.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Aláírt runbookok használata Windows hibrid Runbook-feldolgozón

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

Egy runbook aláírása után importálnia kell az Automation-fiókjába, és közzé kell tennie az aláírási blokkban. A runbookok importálásával kapcsolatos további információkért lásd: [Runbook importálása fájlból Azure Automationba](manage-runbooks.md#import-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Aláírt runbookok használata linuxos hibrid Runbook-feldolgozón

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

Miután létrehozta a kulcstartót, tegye elérhetővé a hibrid Runbook Worker számára. Módosítsa a Settings (beállítások) `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf`, hogy a következő mintakód szerepeljen a fájl szakaszban: **[Worker-optional]** .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Ellenőrizze, hogy be van-e kapcsolva az aláírás ellenőrzése

Ha az aláírás-ellenőrzés le van tiltva a gépen, azt be kell kapcsolni a következő sudo parancs futtatásával. Cserélje le a `<LogAnalyticsworkspaceId>`t a munkaterület-azonosítójával.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook aláírása

Miután konfigurálta az aláírás-ellenőrzést, a következő GPG-paranccsal írja alá a runbook.

```bash
gpg –-clear-sign <runbook name>
```

Az aláírt runbook neve `<runbook name>.asc`.

Most már feltöltheti az aláírt runbook Azure Automation, és végrehajthatja, mint egy normál runbook.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a runbook elindításának módszereiről, tekintse meg a [Runbook elindítása a Azure Automationban](automation-starting-a-runbook.md)című témakört.
* Ha szeretné megtudni, hogyan használhatja a szöveges szerkesztőt a Azure Automation PowerShell-runbookok való együttműködésre, tekintse meg [a Runbook Azure Automation-ban való szerkesztését](automation-edit-textual-runbook.md)ismertető témakört.
* Ha a runbookok nem fejeződik be sikeresen, tekintse át a [runbook-végrehajtási hibák](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)hibaelhárítási útmutatóját.
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).
