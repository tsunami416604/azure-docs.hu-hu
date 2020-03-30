---
title: Az Azure Automation kapcsolati adategységei
description: Az Azure Automation kapcsolati eszközei tartalmazzák a runbookból vagy DSC-konfigurációból egy külső szolgáltatáshoz vagy alkalmazáshoz való csatlakozáshoz szükséges információkat. Ez a cikk ismerteti a kapcsolatok részleteit, és hogyan működik velük mind szöveges, mind grafikus szerzői nyelven.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: b6276153921feb0e6f27194d36d1c32c1d0ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940818"
---
# <a name="connection-assets-in-azure-automation"></a>Az Azure Automation kapcsolati adategységei

Az Automation-kapcsolati eszköz tartalmazza a runbookvagy DSC-konfigurációból egy külső szolgáltatáshoz vagy alkalmazáshoz való csatlakozáshoz szükséges információkat. Ez magában foglalhatja a hitelesítéshez szükséges információkat, például a kapcsolati adatokon, például az URL-címen vagy a porton kívül a felhasználónevet és a jelszót. A kapcsolat értéke megtartja az összes tulajdonságot egy adott alkalmazáshoz való csatlakozáshoz egy eszközben, szemben a több változó létrehozásával. A felhasználó egy helyen szerkesztheti egy kapcsolat értékeit, és egyetlen paraméterben átadhatja a kapcsolat nevét egy runbook- vagy DSC-konfigurációhoz. A kapcsolatok tulajdonságai a **Get-AutomationConnection** tevékenységgel érhetők el a runbook vagy a DSC konfigurációjában.

Kapcsolat létrehozásakor meg kell adnia egy *kapcsolattípust.* A kapcsolat típusa egy olyan sablon, amely tulajdonságok készletét határozza meg. A kapcsolat a kapcsolat típusában meghatározott minden egyes tulajdonság értékeit határozza meg. A kapcsolattípusok az integrációs modulokban lévő Azure Automation-hez kerülnek, vagy az [Azure Automation API-val](/previous-versions/azure/reference/mt163818(v=azure.100)) jönnek létre, ha az integrációs modul kapcsolattípust tartalmaz, és importálja az Automation-fiókba. Ellenkező esetben létre kell hoznia egy metaadatfájlt az Automation-kapcsolat típusának megadásához. Ezzel kapcsolatban további információt az [Integrációs modulok című témakörben talál.](automation-integration-modules.md)

>[!NOTE]
>Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automationben egy egyedi kulcs használatával kerülnek tárolásra, amely minden automatizálási fiókhoz létrejön. Ez a kulcs egy rendszer által felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható. Ezt a folyamatot az Azure Automation kezeli.

## <a name="connection-types"></a>Kapcsolattípusok

Az Azure Automationben háromféle beépített kapcsolat érhető el:

* **Azure** – Ez a kapcsolat klasszikus erőforrások kezelésére használható.
* **AzureClassicCertificate** – Ezt a kapcsolatot az **AzureClassicRunAs-fiók** használja.
* **AzureServicePrincipal** – Ezt a kapcsolatot az **AzureRunAs-fiók** használja.

A legtöbb esetben nem kell kapcsolati erőforrást létrehoznia, mert az [RunAs-fiók](manage-runas-account.md)létrehozásakor jön létre.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok a Windows PowerShell automation-kapcsolatainak létrehozásához és kezeléséhez használatosak. Az [Azure PowerShell-modul](/powershell/azure/overview)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható.

|Parancsmag|Leírás|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Beolvassa a kapcsolatot. A kapcsolat mezőinek értékeit tartalmazó kivonattáblát tartalmaz.|
|[Új-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Új kapcsolatot hoz létre.|
|[Eltávolítás-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Eltávolít egy létező kapcsolatot.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Beállítja egy létező kapcsolat adott mezőjének az értékét.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek runbook- vagy DSC-konfigurációban lévő kapcsolatok elérésére szolgálnak.

|Tevékenységek|Leírás|
|---|---|
|Get-AutomationConnection | Kapcsolatot kap a használatához. A kapcsolat tulajdonságaival rendelkező kivonatoló táblát ad eredményül.|

>[!NOTE]
>Ne használjon változókat a **Get-AutomationConnection** –Name paraméterével, mivel ez megnehezítheti a runbookok vagy dsc-konfigurációk és a csatlakozási eszközök közötti függőségek felderítését a tervezés időpontjában.


## <a name="python2-functions"></a>Python2 függvények

Az alábbi táblázatban található függvény a Python2 runbookok kapcsolatainak eléréséhez használható.

| Függvény | Leírás |
|:---|:---|
| automationassets.get_automation_connection | Beolvassa a kapcsolatot. A kapcsolat tulajdonságaival rendelkező szótárat ad eredményül. |

> [!NOTE]
> Az eszközfüggvények eléréséhez importálnia kell a "automationassets" modult a Python runbook tetején.

## <a name="creating-a-new-connection"></a>Új kapcsolat létrehozása

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Új kapcsolat létrehozása az Azure Portalon

1. Az automatizálási fiókból kattintson az **Eszközök** részre az **Eszközök** panel megnyitásához.
2. Kattintson a **Kapcsolatok** **menügombra** a Kapcsolatok panel megnyitásához.
3. Kattintson a panel tetején a **Kapcsolat hozzáadása** elemre.
4. A **Típus** legördülő menüben válassza ki a létrehozni kívánt kapcsolat típusát. Az űrlap bemutatja az adott típus tulajdonságait.
5. Töltse ki az űrlapot, és kattintson a **Létrehozás** gombra az új kapcsolat mentéséhez.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Új kapcsolat létrehozása a Windows PowerShell lel

Hozzon létre egy új kapcsolatot a Windows PowerShell az [új-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) parancsmag használatával. Ennek a parancsmagnak van egy **ConnectionFieldValues** nevű paramétere, amely egy [kivonattáblát](https://technet.microsoft.com/library/hh847780.aspx) vár, amely a kapcsolattípus által meghatározott tulajdonságok mindegyikéhez értékeket határoz meg.

Ha ismeri az Automation [Run As fiók](automation-sec-configure-azure-runas-account.md) hitelesítéséhez runbookok a szolgáltatásnév használatával, a PowerShell-parancsfájl, amely alternatívát biztosít a Futtatás másként fiók a portálról, létrehoz egy új kapcsolati eszköz a következő mintaparancsok használatával.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

A parancsfájl használatával létrehozhatja a kapcsolati eszközt, mert az Automation-fiók létrehozásakor alapértelmezés szerint automatikusan több globális modult is tartalmaz az **AzureServicePrincipal** kapcsolattípussal együtt az **AzureRunAsConnection kapcsolati** eszköz létrehozásához. Ezt fontos szem előtt tartani, mert ha megpróbál létrehozni egy új kapcsolati eszközt, hogy egy másik hitelesítési módszerrel csatlakozzon egy szolgáltatáshoz vagy alkalmazáshoz, az sikertelen lesz, mert a kapcsolat típusa még nincs definiálva az Automation-fiókban. Ha többet szeretne tudni arról, hogy miként hozhat létre saját kapcsolattípust egyéni vagy moduljához a [PowerShell-galériából,](https://www.powershellgallery.com)olvassa el [az Integrációs modulok című témakört.](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Kapcsolat használata runbook- vagy DSC-konfigurációban

A **Get-AutomationConnection** parancsmagsegítségével egy runbook- vagy DSC-konfigurációban lekérheti a kapcsolatot. A [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) tevékenység nem használható. Ez a tevékenység lekéri a kapcsolat különböző mezőinek értékeit, és [kivonattáblaként](https://go.microsoft.com/fwlink/?LinkID=324844)adja vissza azokat, amelyek ezután a runbook vagy a DSC konfigurációjában a megfelelő parancsokkal használhatók.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

A következő mintaparancsok bemutatják, hogyan használhatja a Futtatás mint korábban említett fiókot az Azure Resource Manager erőforrásaival való hitelesítéshez a runbookban. A Futtatás másként fiókot képviselő csatlakozási eszközt használja, amely a tanúsítványalapú egyszerű szolgáltatásra hivatkozik, nem a hitelesítő adatokra.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Az Add-AzureRmAccount** mostantól a **Connect-AzureRMAccount aliasa.** Ha a tárelemekben való kereséssorán nem látja a **Connect-AzureRMAccount**elemet, használhatja **az Add-AzureRmAccount,** vagy frissítheti a modulokat az Automation-fiókban.

### <a name="graphical-runbook-samples"></a>Grafikus runbook-minták

**Get-AutomationConnection** tevékenységet adhat egy grafikus runbookhoz úgy, hogy a jobb gombbal a grafikus szerkesztő **Könyvtár** ablaktáblájában lévő kapcsolatra kattint, és a **Hozzáadás a vászonhoz parancsot**választja.

![hozzáadás vászonhoz](media/automation-connections/connection-add-canvas.png)

Az alábbi képen egy példa egy kapcsolat használata egy grafikus runbook. Ez ugyanaz a fenti példa a Futtatás másként fiók szöveges runbookkal való hitelesítéséhez. Ez a példa az **Állandó érték** adatkészletet használja a **Futtatási kapcsolat leválasztása** tevékenységhez, amely kapcsolatobjektumot használ a hitelesítéshez. A [folyamatkapcsolat](automation-graphical-authoring-intro.md#links-and-workflow) itt használatos, mivel a ServicePrincipalCertificate paraméterkészlet egyetlen objektumot vár.

![kapcsolatok bekapcsolása](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2-runbook-minta

Az alábbi minta bemutatja, hogyan hitelesíthető a Futtatás másként kapcsolat használatával egy Python2 runbookban.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>További lépések

- Tekintse át [a hivatkozásokat a grafikus szerzői,](automation-graphical-authoring-intro.md#links-and-workflow) hogy megértsék, hogyan irányíthatja és szabályozhatja a logikát a runbookok.

- Ha többet szeretne megtudni arról, hogy az Azure Automation hogyan használja a PowerShell-modulokat, és gyakorlati tanácsokat szeretne saját PowerShell-modulok létrehozásához, hogy integrációs modulként működjenek az Azure Automationben, olvassa el az Integrációs modulok című [témakört.](automation-integration-modules.md)