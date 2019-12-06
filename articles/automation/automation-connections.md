---
title: A Azure Automation lévő kapcsolatok eszközei
description: A Azure Automation kapcsolati eszközei tartalmazzák azokat az információkat, amelyek a külső szolgáltatáshoz vagy alkalmazáshoz való kapcsolódáshoz szükségesek egy runbook vagy DSC-konfigurációból. Ez a cikk ismerteti a kapcsolatok részleteit, valamint azt, hogy miként dolgozhat velük a szöveges és a grafikus létrehozásban.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 621441afaa9bef08a8ebf3b0af082c6a17c77b1b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850431"
---
# <a name="connection-assets-in-azure-automation"></a>A Azure Automation lévő kapcsolatok eszközei

Az Automation-kapcsolati eszköz tartalmazza azokat az információkat, amelyek szükségesek egy külső szolgáltatáshoz vagy alkalmazáshoz való kapcsolódáshoz egy runbook vagy DSC-konfigurációból. Ez magában foglalhatja a hitelesítéshez szükséges adatokat, például a felhasználónevet és a jelszót, valamint a kapcsolódási adatokat, például egy URL-címet vagy egy portot. A kapcsolat értéke egy adott alkalmazáshoz való csatlakozáshoz szükséges összes tulajdonság megtartása egy adott eszközön, a több változó létrehozása helyett. A felhasználó egy helyen szerkesztheti a kapcsolatok értékeit, és egy runbook vagy DSC-konfigurációhoz is átadhatja a kapcsolatok nevét egyetlen paraméterben. A kapcsolat tulajdonságai a **Get-AutomationConnection** tevékenységgel érhetők el a runbook vagy a DSC-konfigurációban.

A kapcsolatok létrehozásakor meg kell adnia egy kapcsolattípus- *típust*. A kapcsolat típusa egy olyan sablon, amely a tulajdonságok készletét határozza meg. A kapcsolat meghatározza a kapcsolati típusában definiált egyes tulajdonságok értékeit. A kapcsolódási típusok hozzáadása a Azure Automation integrációs modulokban vagy a [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) -val együtt, ha az integrációs modul tartalmaz egy kapcsolattípust, és az Automation-fiókjába lett importálva. Ellenkező esetben létre kell hoznia egy metaadat-fájlt az Automation-kapcsolattípus megadásához.  További információ erről: [integrációs modulok](automation-integration-modules.md).

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot a rendszer felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál. Ezt a folyamatot a Azure Automation felügyeli.

## <a name="connection-types"></a>Kapcsolattípusok

A Azure Automationban háromféle beépített kapcsolat érhető el:

* **Azure** – ez a Kapcsolódás a klasszikus erőforrások kezelésére használható.
* **AzureClassicCertificate** – ezt a kapcsolatokat a **AzureClassicRunAs** -fiók használja.
* **AzureServicePrincipal** – ezt a kapcsolatokat a **AzureRunAs** -fiók használja.

A legtöbb esetben nem kell létrehoznia egy kapcsolódási erőforrást, mert a futtató [fiók](manage-runas-account.md)létrehozásakor jön létre.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagokkal automatizálási kapcsolatokat hozhat létre és kezelhet a Windows PowerShell használatával. A [Azure PowerShell modul](/powershell/azure/overview) részeként szállítanak, amely az Automation runbookok és a DSC-konfigurációkhoz is használható.

|Parancsmag|Leírás|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Egy-egy kapcsolatok beolvasása. Egy kivonatoló táblát tartalmaz a kapcsolatok mezőinek értékeivel.|
|[Új – AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Új kapcsolatot hoz létre.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Eltávolít egy létező kapcsolatot.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Beállítja egy létező kapcsolat adott mezőjének az értékét.|

## <a name="activities"></a>Activities (Tevékenységek)

Az alábbi táblázatban szereplő tevékenységek a runbook vagy DSC-konfigurációban lévő kapcsolatok elérésére szolgálnak.

|Activities (Tevékenységek)|Leírás|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Lekéri a használni kívánt kapcsolódási lehetőséget. Egy olyan kivonatoló táblát ad vissza, amely a kapcsolatok tulajdonságaival rendelkezik.|

>[!NOTE]
>Kerülje a **Get-AutomationConnection** -Name paraméterrel rendelkező változók használatát, mivel ez megnehezítheti a runbookok-vagy DSC-konfigurációk közötti függőségek észlelését, valamint a kapcsolati adategységeket a tervezéskor.


## <a name="python2-functions"></a>Python2 függvények
A következő táblázatban szereplő függvény a Python2-runbook lévő kapcsolatok elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| automationassets. get_automation_connection | Egy-egy kapcsolatok beolvasása. Egy olyan szótárt ad vissza, amely a kapcsolatok tulajdonságaival rendelkezik. |

> [!NOTE]
> Az Asset functions eléréséhez importálnia kell a "automationassets" modult a Python-runbook tetején.

## <a name="creating-a-new-connection"></a>Új kapcsolat létrehozása

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Új kapcsolódás létrehozása a Azure Portal

1. Az Automation-fiókban kattintson az **assets** (eszközök) elemre az **eszközök** panel megnyitásához.
2. Kattintson a **kapcsolatok** elemre a **kapcsolatok** panel megnyitásához.
3. Kattintson a panel tetején a **kapcsolatok hozzáadása** lehetőségre.
4. A **típus** legördülő menüben válassza ki a létrehozni kívánt kapcsolódási típust. Az űrlap az adott típus tulajdonságait fogja bemutatni.
5. Töltse ki az űrlapot, és kattintson a **Létrehozás** gombra az új kapcsolódás mentéséhez.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Új kapcsolat létrehozása a Windows PowerShell segítségével

Hozzon létre egy új kapcsolatokat a Windows PowerShell használatával a [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) parancsmaggal. Ez a parancsmag egy **connectionfieldvalues paraméternek** nevű paraméterrel rendelkezik, amely egy [kivonatoló táblázatot](https://technet.microsoft.com/library/hh847780.aspx) vár, amely meghatározza a kapcsolattípus által meghatározott egyes tulajdonságok értékeit.

Ha már ismeri az Automation [futtató fiókot](automation-sec-configure-azure-runas-account.md) a runbookok az egyszerű szolgáltatással történő hitelesítéséhez, a PowerShell-parancsfájl, amely a futtató fióknak a portálon való létrehozásához használható, a létrehoz egy új kapcsolódási eszközt a következő minta-parancsok használatával.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Használhatja a parancsfájlt a kapcsolódási eszköz létrehozásához, mert az Automation-fiók létrehozásakor az automatikusan több globális modult is tartalmaz alapértelmezés szerint, valamint a **azurerunasconnection elemet** kapcsolódási eszköz létrehozásához használt **AzureServicePrincipal** .  Ez azért fontos, mert ha új kapcsolati eszközt próbál létrehozni egy másik hitelesítési módszerrel rendelkező szolgáltatáshoz vagy alkalmazáshoz való kapcsolódáshoz, akkor az sikertelen lesz, mert a kapcsolat típusa még nincs definiálva az Automation-fiókban.  További információ arról, hogyan hozható létre saját kapcsolattípus a [PowerShell-Galéria](https://www.powershellgallery.com)egyéni vagy moduljának létrehozásához: [integrációs modulok](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook vagy DSC-konfigurációban létesített kapcsolatok használata

A **Get-AutomationConnection** parancsmaggal egy RUNBOOK vagy DSC-konfigurációban lévő kapcsolatokat kell lekérnie.  A [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) tevékenység nem használható.  Ez a tevékenység lekéri a kapcsolatok különböző mezőinek értékeit, és egy [kivonatoló táblázatként](https://go.microsoft.com/fwlink/?LinkID=324844) adja vissza őket, amelyet aztán a RUNBOOK vagy DSC-konfiguráció megfelelő parancsaival használhat.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

Az alábbi példák azt mutatják be, hogyan használható a korábban említett futtató fiók a runbook Azure Resource Manager erőforrásaival történő hitelesítéshez.  A futtató fiókot jelképező, a tanúsítványon alapuló egyszerű szolgáltatásnév, a hitelesítő adatokkal nem rendelkező eszközre hivatkozik.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> A **Add-AzureRmAccount** mostantól egy alias a **kapcsolat-AzureRmAccount**. Ha nem látja a **AzureRMAccount**, használhatja a **AzureRMAccount**, vagy frissítheti a modulokat az Automation-fiókban.

### <a name="graphical-runbook-samples"></a>Grafikus runbook minták

A **Get-AutomationConnection** tevékenység grafikus runbook való hozzáadásához kattintson a jobb gombbal a kapcsolódásra a grafikus szerkesztő könyvtár paneljén, és válassza a **Hozzáadás a vászonhoz**lehetőséget.

![Hozzáadás a vászonhoz](media/automation-connections/connection-add-canvas.png)

Az alábbi ábrán egy példa látható a kapcsolatok grafikus runbook való használatára.  Ez ugyanaz a példa, amely a futtató fiók és a szöveges runbook használatával történő hitelesítésre szolgál.  Ez a példa az **állandó érték** adatkészletet használja a következőhöz: a **runas-kapcsolatok beolvasása** tevékenység, amely a hitelesítéshez egy kapcsolatok objektumot használ.  Itt szerepel egy [folyamat hivatkozása](automation-graphical-authoring-intro.md#links-and-workflow) , mert a serviceprincipalcertificate paraméterkészletet paraméter beállítása egyetlen objektumra vár.

![kapcsolatok beolvasása](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook minta
A következő minta azt mutatja be, hogyan lehet hitelesítést végezni a Python2-runbook futtató kapcsolódási funkciójával.

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

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [grafikus authoring-hivatkozásokat](automation-graphical-authoring-intro.md#links-and-workflow) , hogy megtudja, hogyan irányíthatja és irányíthatja a logikát a runbookok.

- Ha többet szeretne megtudni a PowerShell-modulok Azure Automation használatáról, valamint a saját PowerShell-modulok létrehozásával kapcsolatos ajánlott eljárásokat a Azure Automationon belüli integrációs modulokként való munkához, tekintse meg az [integrációs modulokat](automation-integration-modules.md).

