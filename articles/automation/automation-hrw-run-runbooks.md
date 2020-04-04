---
title: Runbookok futtatása az Azure Automation hibrid runbook-feldolgozóján
description: Ez a cikk a runbookok futtatásáról a helyi adatközpontban vagy a hibrid runbook-feldolgozó szerepkörrel rendelkező felhőszolgáltatóban futó ról nyújt tájékoztatást.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 902734ddc7195d643c3aedb4054f57723d1a51c2
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632139"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid runbook-feldolgozón

Runbookok, amelyek célja a hibrid Runbook-feldolgozó általában erőforrások at a helyi számítógépen vagy az erőforrások ellen a helyi környezetben, ahol a dolgozó telepítve van. Az Azure Automation ben futókönyvek általában az Azure-felhőben kezelik az erőforrásokat. Annak ellenére, hogy azokat eltérően használják, az Azure Automationben futó runbookok és a hibrid Runbook-feldolgozón futó runbookok szerkezetük azonos.

Amikor egy hibrid Runbook-feldolgozón futtatandó runbookot hoz létre, a runbookot a feldolgozót tartalmazó számítógépen kell szerkesztenie és tesztelnie kell. A gazdagép rendelkezik az összes PowerShell-modullal és hálózati hozzáféréssel a helyi erőforrások kezeléséhez és eléréséhez. Miután tesztelte a runbookot a hibrid Runbook-feldolgozó gépen, majd feltöltheti azt az Azure Automation-környezetbe, ahol futtatható a dolgozón. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-engedélyek hibrid Runbook-feldolgozószámára

A nem Azure-erőforrások elérése, a hibrid Runbook-feldolgozón futó runbookok nem használhatják az Azure-erőforrásokat hitelesítő runbookok által általában használt hitelesítési mechanizmust. A runbook oka vagy saját hitelesítést biztosít a helyi erőforrásoknak, vagy konfigurálja a hitelesítést [az Azure-erőforrások felügyelt identitásaival.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) Megadhatja a Futtatás másként fiókot is, hogy felhasználói környezetet biztosítson az összes runbookhoz.

### <a name="runbook-authentication"></a>Runbook-hitelesítés

Alapértelmezés szerint a runbookok a helyi számítógépen futnak. A Windows rendszerben a helyi rendszerfiók környezetében futnak. Linux esetén az **nxautomation**speciális felhasználói fiók kal összefüggésében futnak. Mindkét esetben a runbookok saját hitelesítést kell biztosítaniuk az általuk elért erőforrásokhoz.

A [runbookban használhatja a Hitelesítő adatok](automation-credentials.md) és [a tanúsítvány](automation-certificates.md) eszközeit olyan parancsmagokkal, amelyek lehetővé teszik a hitelesítő adatok megadását, hogy a runbook hitelesíthesse magát a különböző erőforrások számára. A következő példa a runbook egy olyan részét mutatja be, amely újraindítja a számítógépet. Hitelesítő adatokat kér le egy hitelesítő egységből és a számítógép nevét egy `Restart-Computer` változó eszközből, majd ezeket az értékeket használja a parancsmaggal.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript-tevékenységet](automation-powershell-workflow.md#inlinescript) is használhat. `InlineScript`Lehetővé teszi, hogy egy másik számítógépen, a [PSCredential közös paraméter](/powershell/module/psworkflow/about/about_workflowcommonparameters)által megadott hitelesítő adatokkal rendelkező számítógépen futtasson kódblokkokat.

### <a name="run-as-account"></a>Futtató fiók

Ahelyett, hogy a runbook saját hitelesítést biztosít a helyi erőforrások, megadhatja a Futtatás másként fiók egy hibrid Runbook feldolgozó csoport. Ehhez meg kell határoznia egy [hitelesítő adatokat,](automation-credentials.md) amely hozzáfér a helyi erőforrásokhoz. Ezek az erőforrások közé tartozik a tanúsítvány tárolók és az összes runbookok futnak ezekkel a hitelesítő adatokkal a hibrid Runbook-feldolgozó a csoportban.

A hitelesítő adatok felhasználónevének a következő formátumok egyikében kell lennie:

* Tartomány\felhasználónév
* username@domain
* felhasználónév (a helyszíni számítógép helyi fiókjaihoz)

Az alábbi eljárással adja meg a Futtatás másként fiók egy hibrid Runbook feldolgozó csoport.

1. Hozzon létre egy [hitelesítő adatokat](automation-credentials.md) a helyi erőforrásokhoz való hozzáféréssel.
2. Nyissa meg az Automation-fiókot az Azure Portalon.
3. Jelölje ki a **Hibrid munkavégző csoportok** csempét, majd jelölje ki a csoportot.
4. Válassza a **Minden beállítás**lehetőséget, majd a **Hibrid munkavégző csoport beállításai lehetőséget.**
5. Módosítsa a **Futtatás alapértelmezettről** **egyénire**értékét. **Default**
6. Jelölje ki a hitelesítő adatokat, és kattintson a **Mentés gombra.**

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Felügyelt identitások az Azure-erőforrásokhoz

Az Azure-erőforrások hibrid Runbook-feldolgozói felügyelt identitások használatával hitelesítheti magát az Azure-erőforrásokban. A felügyelt identitások használata az Azure-erőforrások helyett a Futtatás másként fiókok előnyöket, mert nem kell:

* Exportálja a Futtatás másként tanúsítványt, majd importálja azt a hibrid runbook-feldolgozóba.
* A Futtatás másként fiók által használt tanúsítvány megújítása.
* A Run As connection objektum kezelése a runbook-kódban.

Kövesse a következő lépéseket az Azure-erőforrások felügyelt identitásának használatához egy hibrid Runbook-feldolgozón.

1. Hozzon létre egy Azure-beli virtuális gép.
2. Konfigurálja az Azure-erőforrások felügyelt identitásait a virtuális számítógépen. Lásd: [Felügyelt identitások konfigurálása az Azure-erőforrások hoz egy virtuális gép az Azure Portalon.](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. Adja meg a virtuális gép hozzáférést egy erőforráscsoport az erőforrás-kezelőben. Lásd: A Windows virtuális gép rendszerhez rendelt felügyelt identitás használata [az Erőforrás-kezelő eléréséhez.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. Telepítse a hibrid Runbook-feldolgozót a virtuális gépre. Lásd: [Windows hibrid runbook-feldolgozó telepítése.](automation-windows-hrw-install.md)
5. Frissítse a runbookot a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) parancsmag és a paraméter használatával az `Identity` Azure-erőforrások hitelesítéséhez. Ez a konfiguráció csökkenti a Futtatás másként fiók használatának és a társított fiókkezelés végrehajtásának szükségességét.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`Hibrid Runbook-feldolgozóhoz működik, rendszeráltal hozzárendelt identitást és egyetlen felhasználó által hozzárendelt identitást használva. Ha több felhasználó által hozzárendelt identitást használ a hibrid Runbook-feldolgozón, a `Connect-AzAccount` runbooknak meg kell adnia a *AccountId paramétert* egy adott felhasználó által hozzárendelt identitás kiválasztásához.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automatizálás futtatása fiókként

Az erőforrások Azure-beli üzembe helyezéséhez szükséges automatikus buildfolyamat részeként előfordulhat, hogy a helyszíni rendszerekhez való hozzáférésre van szüksége egy feladat vagy a központi telepítési sorrend ben leírt lépések készletének támogatásához. A Futtatás másként fiók használatával az Azure-ral való hitelesítés biztosításához telepítenie kell a Futtatás másként fiók tanúsítványt.

A következő PowerShell runbook, **az Export-RunAsCertificateToHybridWorker,** exportálja a Futtatás másként tanúsítványt az Azure Automation-fiókból. A runbook letölti és importálja a tanúsítványt a helyi számítógép tanúsítványtárolóba egy hibrid Runbook-feldolgozón, amely ugyanahhoz a fiókhoz csatlakozik. Miután befejezte ezt a lépést, a runbook ellenőrzi, hogy a dolgozó sikeresen hitelesíthető-e az Azure-ban a Futtatás másként fiók használatával.

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
>A PowerShell runbookok, `Add-AzAccount` és `Connect-AzAccount` `Add-AzureRMAccount` aliasok. Ha a könyvtárelemeiben keres, `Connect-AzAccount`ha nem `Add-AzAccount`látja a , használhatja a , vagy frissítheti a modulokat az Automation-fiókban.

A Futtatás másként fiók előkészítésének befejezése:

1. Mentse az **Export-RunAsCertificateToHybridWorker** runbookot a számítógépre **.ps1** kiterjesztéssel.
2. Importálja az Automation-fiókba.
3. Módosítsa a runbookot, és `Password` módosítsa a saját jelszavának o változó értékét. 
4. Tegye közzé a runbookot.
5. Futtassa a runbookot, amely a hibrid Runbook-feldolgozó csoportot célozza meg, amely fut, és hitelesíti a runbookokat a Futtatás másként fiók használatával. 
6. Vizsgálja meg a feladatstreamet, és nézze meg, hogy jelenti-e a tanúsítvány importálási kísérletét a helyi számítógéptárolóba, és több sort követ. Ez a viselkedés attól függ, hogy hány Automation-fiókot határoz meg az előfizetésben, és a hitelesítés sikerességi fokát.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Feladat viselkedése a hibrid Runbook-feldolgozókon

Az Azure Automation a hibrid runbook-feldolgozók feladatait némileg eltér az Azure sandboxokban futó feladatoktól. Az egyik legfontosabb különbség az, hogy nincs korlátozva a runbook-dolgozók feladat időtartama. Az Azure sandboxokban futó Runbookok a [méltányos részesedés](automation-runbook-execution.md#fair-share)miatt három órára korlátozódnak.

Egy hosszú ideig futó runbook, győződjön meg arról, hogy rugalmas a lehetséges újraindítás, például ha a dolgozót futtató gép újraindul. Ha a hibrid Runbook-feldolgozó gazdagép újraindul, minden futó runbook-feladat újraindul az elejétől, vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontjáról. A runbook-feladat több mint háromszoros újraindítása után a felfüggesztést a kettõsen felfüggeszti.

Ne feledje, hogy a hibrid runbook-feldolgozók feladatai a windowsos helyi rendszerfiók vagy a Linux **nxautomation-fiók** alatt futnak. Linux esetén győződjön meg arról, hogy az **nxautomation-fiók** hozzáfér a runbook-modulok tárolási helyéhez. A [Telepítésmodul](/powershell/module/powershellget/install-module) parancsmag használatakor mindenképpen adja meg az `Scope` Összes felhasználó értéket a paraméterhez, hogy az **nxautomation-fiók** hozzáférhessen. A Linuxos PowerShellről a [Nem Windows-platformokon a PowerShell ismert problémái](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)című témakörben talál további információt.

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Runbook indítása hibrid runbook-feldolgozón

[Egy runbook indítása az Azure Automation-ben](automation-starting-a-runbook.md) ismerteti a runbook indításának különböző módszereit. A hibrid Runbook-feldolgozó runbookjának indítása egy **Futtatás on** beállítást használ, amely lehetővé teszi egy hibrid Runbook-feldolgozó csoport nevének megadását. Ha egy csoport meg van adva, a csoport egyik dolgozója lekéri és futtatja a runbookot. Ha a runbook nem adja meg ezt a beállítást, az Azure Automation a szokásos módon futtatja a runbookot.

Amikor elindít egy runbookot az Azure Portalon, megjelenik a **Futtatás a futtatás a** beállítás, amelyhez kiválaszthatja az **Azure-t** vagy **a hibrid feldolgozót.** Ha a **Hibrid feldolgozó**lehetőséget választja, a hibrid Runbook-feldolgozó csoportot egy legördülő menüből választhatja ki.

Használja `RunOn` a paramétert a `Start-AzureAutomationRunbook` parancsmaggal. A következő példa a Windows PowerShell segítségével indítegy **Teszt-Runbook** nevű runbookot egy MyHybridGroup nevű hibrid Runbook-feldolgozó csoporton.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> A `RunOn` paraméter hozzá `Start-AzureAutomationRunbook` lett adva a Microsoft Azure PowerShell 0.9.1-es verziójához. Ha van egy korábbi telepítve, [töltse le a legújabb verziót.](https://azure.microsoft.com/downloads/) Csak telepítse ezt a verziót arra a munkaállomásra, ahol a runbookot a PowerShellből indítja. Nem kell telepítenie a hibrid Runbook feldolgozó számítógépre, hacsak nem kívánja elindítani a runbookokat erről a számítógépről.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Aláírt runbookok futtatása Windows hibrid runbook-feldolgozón

A Windows hibrid runbook-feldolgozót beállíthatja úgy, hogy csak aláírt runbookokat futtasson.

> [!IMPORTANT]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak aláírt runbookokat futtasson, a nem aláírt runbookok nem hajthatók végre a dolgozón.

### <a name="create-signing-certificate"></a>Aláíró tanúsítvány létrehozása

A következő példa létrehoz egy önaláírt tanúsítványt, amely a runbookok aláírásához használható. Ez a kód létrehozza a tanúsítványt, és exportálja azt, hogy a hibrid Runbook-feldolgozó később importálhassa. Az ujjlenyomat ot a rendszer a tanúsítványhivatkozáshoz való későbbi használatra is visszaadja.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Tanúsítvány importálása és dolgozók konfigurálása aláírás-ellenőrzéshez

Másolja a létrehozott tanúsítványt a csoport minden egyes hibrid Runbook-feldolgozójának. Futtassa a következő parancsfájlt a tanúsítvány importálásához, és konfigurálja a dolgozókat a runbookok aláírás-ellenőrzésének használatára.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>A runbookok aláírása a tanúsítvány használatával

A hibrid Runbook-feldolgozók konfigurálva csak aláírt runbookok, alá kell írnia a hibrid Runbook-feldolgozó használni a runbookok. Használja a következő minta PowerShell-kódot ezeket a runbookokat.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Ha egy runbook alá van írva, importálnia kell azt az Automation-fiókba, és közzé kell tennie az aláírási blokk. A runbookok importálásáról a [Runbookok importálása fájlból az Azure Automationbe című témakörben](manage-runbooks.md#importing-a-runbook)olvashat.

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Aláírt runbookok együttműködése Linux os hibrid runbook-feldolgozón

Ahhoz, hogy az aláírt runbookok, a Linux hibrid Runbook-feldolgozó nak rendelkeznie kell a [GPG](https://gnupg.org/index.html) futtatható a helyi gépen.

> [!IMPORTANT]
> Miután konfigurálta a hibrid Runbook-feldolgozót, hogy csak aláírt runbookokat futtasson, a nem aláírt runbookok nem hajthatók végre a dolgozón.

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG kulcstartó és kulcspár létrehozása

A GPG-kulcstartó és kulcspár létrehozásához használja a hibrid Runbook Worker **nxautomation-fiókot.**

1. A sudo alkalmazás segítségével jelentkezzen be **nxautomation** fiókként.

    ```bash
    sudo su – nxautomation
    ```

2. Az **nxautomation**használata után hozza létre a GPG kulcspárt. A GPG végigvezeti a lépéseken. Meg kell adnia a nevet, az e-mail címet, a lejárati időt és a jelszót. Ezután várjon, amíg elegendő entrópia van a gépen a kulcs létrehozásához.

    ```bash
    sudo gpg --generate-key
    ```

3. Mivel a GPG könyvtár sudo használatával lett létrehozva, a következő paranccsal meg kell változtatnia a tulajdonosát **nxautomation-re.**

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>A kulcstartó elérhetővé az elérhetővé a hibrid Runbook-feldolgozó számára

A kulcstartó létrehozása után tegye elérhetővé a hibrid Runbook-feldolgozó számára. Módosítsa a **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** beállításfájlt úgy, hogy a `[worker-optional]`fájlszakasz ban a következő példakód szerepeljen.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Annak ellenőrzése, hogy az aláírás érvényesítése be van-e kapcsolva

Ha az aláírás-ellenőrzés le van tiltva a számítógépen, a következő sudo parancs futtatásával kell bekapcsolnia. Cserélje `<LogAnalyticsworkspaceId>` le a munkaterület-azonosítóra.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook aláírása

Miután konfigurálta az aláírás-érvényesítést, a következő GPG-paranccsal írjon alá egy runbookot.

```bash
gpg –-clear-sign <runbook name>
```

Az aláírt runbook `<runbook name>.asc`neve .

Most már feltöltheti az aláírt runbookot az Azure Automationbe, és úgy hajthatja végre, mint egy normál runbookot.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne tudni a runbookok indításának módszereiről, [olvassa el a Runbook indítása az Azure Automationben című témakört.](automation-starting-a-runbook.md)
* Ha tudni szeretné, hogyan használhatja a szöveges szerkesztőt a PowerShell-runbookokkal való együttműködésre az Azure Automationben, olvassa el [a Runbook szerkesztése az Azure Automationben című témakört.](automation-edit-textual-runbook.md)
* Ha a runbookok nem futnak sikeresen, tekintse át a hibaelhárítási útmutatót a [Runbook végrehajtási hibák.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
