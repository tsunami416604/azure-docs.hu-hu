---
title: Az Azure Automation kapcsolati adategységei
description: Az Azure Automation kapcsolati eszközei tartalmazzák a runbookból vagy DSC-konfigurációból egy külső szolgáltatáshoz vagy alkalmazáshoz való csatlakozáshoz szükséges információkat. Ez a cikk ismerteti a kapcsolatok részleteit, és hogyan működik velük mind szöveges, mind grafikus szerzői nyelven.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 7a6fc2bd5cb6f5c7ae5bef9e9741fae92518d885
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392396"
---
# <a name="connection-assets-in-azure-automation"></a>Az Azure Automation kapcsolati adategységei

Az Automation-kapcsolati eszköz tartalmazza a runbookvagy DSC-konfigurációból egy külső szolgáltatáshoz vagy alkalmazáshoz való csatlakozáshoz szükséges információkat. Ez magában foglalhatja a hitelesítéshez szükséges információkat, például a kapcsolati adatokon, például az URL-címen vagy a porton kívül a felhasználónevet és a jelszót. A kapcsolat értéke megtartja az összes tulajdonságot egy adott alkalmazáshoz való csatlakozáshoz egy eszközben, szemben a több változó létrehozásával. A felhasználó egy helyen szerkesztheti egy kapcsolat értékeit, és egyetlen paraméterben átadhatja a kapcsolat nevét egy runbook- vagy DSC-konfigurációhoz. A kapcsolatok tulajdonságai a runbook vagy a DSC konfigurációjában érhetők el a `Get-AutomationConnection` tevékenységgel.

Kapcsolat létrehozásakor meg kell adnia egy *kapcsolattípust.* A kapcsolat típusa egy olyan sablon, amely tulajdonságok készletét határozza meg. A kapcsolat a kapcsolat típusában meghatározott minden egyes tulajdonság értékeit határozza meg. A kapcsolattípusok az integrációs modulokban lévő Azure Automation-hez kerülnek, vagy az [Azure Automation API-val](/previous-versions/azure/reference/mt163818(v=azure.100)) jönnek létre, ha az integrációs modul kapcsolattípust tartalmaz, és importálja az Automation-fiókba. Ellenkező esetben létre kell hoznia egy metaadatfájlt az Automation-kapcsolat típusának megadásához. Ezzel kapcsolatban további információt az [Integrációs modulok című témakörben talál.](automation-integration-modules.md)

>[!NOTE]
>Az Azure Automation biztonságos eszközei közé tartoznak a hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók. Ezek az eszközök titkosítva vannak, és az Azure Automation-ben egy egyedi kulcs használatával, amely minden Automation-fiókhoz létrejön. Ez a kulcs egy rendszer által felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik a Key Vaultból, és ezután az eszköz titkosításához használható. Ezt a folyamatot az Azure Automation kezeli.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="connection-types"></a>Kapcsolattípusok

Az Azure Automationben háromféle beépített kapcsolat érhető el:

* **Azure** – Ez a kapcsolat klasszikus erőforrások kezelésére használható.
* **AzureClassicCertificate** – Ezt a kapcsolatot az **AzureClassicRunAs-fiók** használja.
* **AzureServicePrincipal** – Ezt a kapcsolatot az **AzureRunAs-fiók** használja.

A legtöbb esetben nem kell kapcsolati erőforrást létrehoznia, mert az [futtatási mint fiók](manage-runas-account.md)létrehozásakor jön létre.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok a Windows PowerShell automation-kapcsolatainak létrehozásához és kezeléséhez használatosak. Az [Azure PowerShell-modul](/powershell/azure/overview)részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációk ban használható.

|Parancsmag|Leírás|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Beolvassa a kapcsolatot. Tartalmaz egy kivonattáblát a kapcsolatmezők értékeivel.|
|[Új-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Új kapcsolatot hoz létre.|
|[Eltávolítás-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Meglévő kapcsolat eltávolítása.|
|[Set-AzAutomationConnectionFieldValue érték](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Beállítja egy létező kapcsolat adott mezőjének az értékét.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek runbook- vagy DSC-konfigurációban lévő kapcsolatok elérésére szolgálnak.

|Tevékenységek|Leírás|
|---|---|
|`Get-AutomationConnection` | Kapcsolatot kap a használatához. A kapcsolat tulajdonságaival rendelkező kivonatot ad eredményül.|

>[!NOTE]
>Kerülje a változók `Name` használatát `Get-AutomationConnection`a paraméterrel. Ennek a paraméternek a használata megnehezítheti a runbookok vagy dsc-konfigurációk és a csatlakozási eszközök közötti függőségek felderítését a tervezés időpontjában.

## <a name="python-2-functions"></a>Python 2 függvények

Az alábbi táblázatban található függvény a Python 2 runbookok kapcsolatainak elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_connection` | Beolvassa a kapcsolatot. A kapcsolat tulajdonságaival rendelkező szótárat ad eredményül. |

> [!NOTE]
> Az eszközfüggvények eléréséhez importálnia kell a `automationassets` modult a Python runbook tetején.

## <a name="creating-a-new-connection"></a>Új kapcsolat létrehozása

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Új kapcsolat létrehozása az Azure Portalon

1. Az Automation-fiókból kattintson az **Eszközök** részre az **Eszközök** panel megnyitásához.
2. Kattintson a **Kapcsolatok** **menügombra** a Kapcsolatok panel megnyitásához.
3. Kattintson a panel tetején a **Kapcsolat hozzáadása** elemre.
4. A **Típus** legördülő menüben válassza ki a létrehozni kívánt kapcsolat típusát. Az űrlap bemutatja az adott típus tulajdonságait.
5. Töltse ki az űrlapot, és kattintson a **Létrehozás** gombra az új kapcsolat mentéséhez.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Új kapcsolat létrehozása a Windows PowerShell lel

Hozzon létre egy új kapcsolatot `New-AzAutomationConnection` a Windows PowerShell a parancsmag használatával. Ennek a parancsmagnak `ConnectionFieldValues` van egy nevű paramétere, amely [kivonatoló](https://technet.microsoft.com/library/hh847780.aspx) értékeket vár a kapcsolattípus által meghatározott tulajdonságok mindegyikéhez.

A következő példaparancsokat használhatja a Futtatás másként fiók portálról történő létrehozásának alternatívájaként egy új kapcsolati eszköz létrehozásához.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

A parancsfájl segítségével létrehozhatja a kapcsolati eszközt, mert az Automation-fiók létrehozásakor alapértelmezés szerint automatikusan `AzureServicePrincipal` több `AzureRunAsConnection` globális modult is tartalmaz a kapcsolattípussal együtt a kapcsolati eszköz létrehozásához. Ezt fontos szem előtt tartani, mert ha megpróbál létrehozni egy új kapcsolati eszközt, hogy egy másik hitelesítési módszerrel csatlakozzon egy szolgáltatáshoz vagy alkalmazáshoz, az sikertelen lesz, mert a kapcsolat típusa még nincs definiálva az Automation-fiókban. Ha többet szeretne tudni arról, hogy miként hozhat létre saját kapcsolattípust egyéni vagy modulhoz a [PowerShell-galériából,](https://www.powershellgallery.com)olvassa el az Integrációs modulok című [témakört.](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Kapcsolat használata runbook- vagy DSC-konfigurációban

Kapcsolat lekérése egy runbook vagy DSC konfigurációban a `Get-AutomationConnection` parancsmaggal. A `Get-AzAutomationConnection` tevékenység nem használható. Ez a tevékenység beolvassa a kapcsolat különböző mezőinek értékeit, és kivonattáblázatként adja vissza [azokat.](https://go.microsoft.com/fwlink/?LinkID=324844) Ez a kivonatolás ezután használható a megfelelő parancsokkal a runbook vagy a DSC konfigurációjában.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

A következő mintaparancsok bemutatják, hogyan használhatja a Futtatás mint korábban említett fiókot az Azure Resource Manager erőforrásaival való hitelesítéshez a runbookban. A Futtatás másként fiókot képviselő csatlakozási eszközt használja, amely a tanúsítványalapú egyszerű szolgáltatásra hivatkozik, nem a hitelesítő adatokra.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Nem grafikus PowerShell-runbookok esetén, `Add-AzAccount` és `Add-AzureRMAccount` a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.

### <a name="graphical-runbook-samples"></a>Grafikus runbook-minták

Tevékenység `Get-AutomationConnection` hozzáadása grafikus runbookhoz úgy, hogy a jobb gombbal a grafikus szerkesztő Könyvtár ablaktáblájában a kapcsolatra kattint, és a **Hozzáadás a vászonhoz parancsot**választja.

![hozzáadás vászonhoz](media/automation-connections/connection-add-canvas.png)

Az alábbi képen egy példa egy kapcsolat használata egy grafikus runbook. Ez ugyanaz a példa, mint a fent látható a Futtatás másként fiók szöveges runbookhasználatával történő hitelesítéséhez. Ez a `Constant value` példa a `Get RunAs Connection` kapcsolatobjektumot hitelesítéshez használó tevékenység adatkészletét használja. A [folyamatkapcsolat](automation-graphical-authoring-intro.md#links-and-workflow) itt használatos, mivel a `ServicePrincipalCertificate` paraméterkészlet egyetlen objektumot vár.

![kapcsolatok bekapcsolása](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 runbook minta

Az alábbi minta bemutatja, hogyan hitelesíthető a Futtatás másként kapcsolat használatával egy Python 2 runbookban.

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
* A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Ha többet szeretne megtudni arról, hogy az Azure Automation hogyan használja a PowerShell-modulokat, és gyakorlati tanácsokat szeretne saját PowerShell-modulok létrehozásához, hogy integrációs modulként működjenek az Azure Automationben, olvassa el az Integrációs modulok című [témakört.](automation-integration-modules.md)