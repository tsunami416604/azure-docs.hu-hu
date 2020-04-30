---
title: Kapcsolatok kezelése Azure Automationban
description: A Azure Automation kapcsolati eszközei tartalmazzák azokat az információkat, amelyek a külső szolgáltatáshoz vagy alkalmazáshoz való kapcsolódáshoz szükségesek egy runbook vagy DSC-konfigurációból. Ez a cikk ismerteti a kapcsolatok részleteit, valamint azt, hogy miként dolgozhat velük a szöveges és a grafikus létrehozásban.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097115"
---
# <a name="manage-connections-in-azure-automation"></a>Kapcsolatok kezelése Azure Automationban

Az Automation-kapcsolati eszköz tartalmazza azokat az információkat, amelyek szükségesek egy külső szolgáltatáshoz vagy alkalmazáshoz való kapcsolódáshoz egy runbook vagy DSC-konfigurációból. Ez magában foglalhatja a hitelesítéshez szükséges adatokat, például a felhasználónevet és a jelszót, valamint a kapcsolódási adatokat, például egy URL-címet vagy egy portot. A kapcsolat értéke egy adott alkalmazáshoz való csatlakozáshoz szükséges összes tulajdonság megtartása egy adott eszközön, a több változó létrehozása helyett. A felhasználó egy helyen szerkesztheti a kapcsolatok értékeit, és egy runbook vagy DSC-konfigurációhoz is átadhatja a kapcsolatok nevét egyetlen paraméterben. A kapcsolat tulajdonságai a runbook vagy a `Get-AutomationConnection` DSC-konfigurációban a tevékenységgel érhetők el.

A kapcsolatok létrehozásakor meg kell adnia egy kapcsolattípus-típust. A kapcsolat típusa egy olyan sablon, amely a tulajdonságok készletét határozza meg. A kapcsolat meghatározza a kapcsolati típusában definiált egyes tulajdonságok értékeit. A kapcsolódási típusok hozzáadása a Azure Automation integrációs modulokban vagy a [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100)) -val együtt, ha az integrációs modul tartalmaz egy kapcsolattípust, és az Automation-fiókjába lett importálva. Ellenkező esetben létre kell hoznia egy metaadat-fájlt az Automation-kapcsolattípus megadásához. Ezzel kapcsolatban további információt az [integrációs modulok](automation-integration-modules.md)című témakörben talál.

>[!NOTE]
>Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a rendszer titkosítja és tárolja Azure Automation az egyes Automation-fiókokhoz generált egyedi kulcs használatával. Ezt a kulcsot a rendszer felügyelt Key Vault tárolja. A biztonságos eszköz tárolása előtt a kulcs betöltődik Key Vault, majd az eszköz titkosítására szolgál. Ezt a folyamatot a Azure Automation felügyeli.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="connection-types"></a>Kapcsolattípusok

A Azure Automationban háromféle beépített kapcsolat érhető el:

* **Azure** – ez a Kapcsolódás a klasszikus erőforrások kezelésére használható.
* **AzureClassicCertificate** – ezt a kapcsolatokat a **AzureClassicRunAs** -fiók használja.
* **AzureServicePrincipal** – ezt a kapcsolatokat a **AzureRunAs** -fiók használja.

A legtöbb esetben nem kell létrehoznia egy kapcsolódási erőforrást, mert a [futtató fiók](manage-runas-account.md)létrehozásakor jön létre.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagokkal automatizálási kapcsolatokat hozhat létre és kezelhet a Windows PowerShell használatával. A [Azure PowerShell modul](/powershell/azure/overview)részeként szállítják, amely az Automation runbookok és a DSC-konfigurációkhoz is használható.

|Parancsmag|Leírás|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Egy-egy kapcsolatok beolvasása. A szórótábla tartalmazza a kapcsolatok mezőinek értékeit.|
|[Új – AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Új kapcsolatot hoz létre.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Eltávolítja a meglévő kapcsolatokat.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Beállítja egy létező kapcsolat adott mezőjének az értékét.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban szereplő tevékenységek a runbook vagy DSC-konfigurációban lévő kapcsolatok elérésére szolgálnak.

|Tevékenységek|Leírás|
|---|---|
|`Get-AutomationConnection` | Lekéri a használni kívánt kapcsolódási lehetőséget. Egy olyan szórótábla ad vissza, amely a kapcsolatok tulajdonságaival rendelkezik.|

>[!NOTE]
>Ne használjon változókat a `Name` paraméterrel `Get-AutomationConnection`. Ennek a paraméternek a használata megnehezítheti a runbookok vagy DSC-konfigurációk közötti függőségek felderítését, valamint a kapcsolati adategységeket a tervezéskor.

## <a name="python-2-functions"></a>Python 2 függvények

A következő táblázatban szereplő függvény a kapcsolatoknak a Python 2 runbook való elérésére szolgál.

| Függvény | Leírás |
|:---|:---|
| `automationassets.get_automation_connection` | Egy-egy kapcsolatok beolvasása. Egy olyan szótárt ad vissza, amely a kapcsolatok tulajdonságaival rendelkezik. |

> [!NOTE]
> Az Asset functions `automationassets` eléréséhez importálnia kell a modult a Python-runbook tetején.

## <a name="creating-a-new-connection"></a>Új kapcsolatok létrehozása

### <a name="create-a-new-connection-with-the-azure-portal"></a>Új kapcsolatok létrehozása a Azure Portal

1. Az Automation-fiókban kattintson az **assets** (eszközök) elemre az **eszközök** panel megnyitásához.
2. Kattintson a **kapcsolatok** elemre a **kapcsolatok** panel megnyitásához.
3. Kattintson a panel tetején a **kapcsolatok hozzáadása** lehetőségre.
4. A **típus** legördülő menüben válassza ki a létrehozni kívánt kapcsolódási típust. Az űrlap az adott típus tulajdonságait fogja bemutatni.
5. Töltse ki az űrlapot, és kattintson a **Létrehozás** gombra az új kapcsolódás mentéséhez.

### <a name="create-a-new-connection-with-windows-powershell"></a>Új Windows PowerShell-kapcsolatok létrehozása

Hozzon létre egy új Windows PowerShell-kapcsolatokat `New-AzAutomationConnection` a parancsmag használatával. Ehhez a parancsmaghoz tartozik egy `ConnectionFieldValues` nevű paraméter, amely egy [szórótábla](https://technet.microsoft.com/library/hh847780.aspx) határozza meg a kapcsolattípus által meghatározott egyes tulajdonságok értékeit.

Az alábbi parancsokkal Alternatív megoldásként is létrehozhatja a futtató fiókot a portálon egy új kapcsolódási eszköz létrehozásához.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Az Automation-fiók létrehozásakor a szolgáltatás alapértelmezés szerint több globális modult is tartalmaz, valamint a kapcsolódási `AzureServicePrincipal` típust `AzureRunAsConnection` a kapcsolódási eszköz létrehozásához. Ha egy másik hitelesítési módszerrel rendelkező szolgáltatáshoz vagy alkalmazáshoz való csatlakozáshoz próbál új kapcsolati objektumot létrehozni, a művelet meghiúsul, mert a kapcsolat típusa még nincs definiálva az Automation-fiókban. Az egyéni [PowerShell-Galéria](https://www.powershellgallery.com) modulhoz tartozó saját kapcsolattípus létrehozásával kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook vagy DSC-konfigurációban létesített kapcsolatok használata

Runbook vagy DSC-konfigurációval rendelkező kapcsolatok lekérése `Get-AutomationConnection` a parancsmaggal. A `Get-AzAutomationConnection` tevékenység nem használható. Ez a tevékenység lekéri a kapcsolatok különböző mezőinek értékeit, és visszaadja őket [szórótábla](https://go.microsoft.com/fwlink/?LinkID=324844). Ezt a szórótábla ezután a runbook vagy a DSC-konfiguráció megfelelő parancsaival használhatja.

### <a name="textual-runbook-sample"></a>Szöveges runbook minta

Az alábbi példák azt mutatják be, hogyan használható a korábban említett futtató fiók a runbook Azure Resource Manager erőforrásaival történő hitelesítéshez. A futtató fiókot jelképező, a tanúsítványon alapuló egyszerű szolgáltatásnév, a hitelesítő adatokkal nem rendelkező eszközre hivatkozik.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> A nem grafikus PowerShell-runbookok, `Add-AzAccount` valamint `Add-AzureRMAccount` a [csatlakozási-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

### <a name="graphical-runbook-samples"></a>Grafikus runbook minták

A `Get-AutomationConnection` tevékenységek grafikus runbook való hozzáadásához kattintson a jobb gombbal a kapcsolódásra a grafikus szerkesztő könyvtár paneljén, és válassza a **Hozzáadás a vászonhoz**lehetőséget.

![Hozzáadás a vászonhoz](media/automation-connections/connection-add-canvas.png)

Az alábbi ábrán egy példa látható a kapcsolatok grafikus runbook való használatára. Ez ugyanaz a példa, ahogy a fentiekben is látható, a futtató fiókkal a szöveges runbook való hitelesítéshez. Ez a példa az `Constant value` adatkészletet használja `Get RunAs Connection` arra a tevékenységre, amely a hitelesítéshez egy kapcsolatok objektumot használ. Itt szerepel egy [folyamat hivatkozása](automation-graphical-authoring-intro.md#links-and-workflow) , mert `ServicePrincipalCertificate` a paraméter-készlet egyetlen objektumot vár.

![kapcsolatok beolvasása](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 runbook minta

A következő minta azt mutatja be, hogyan lehet hitelesíteni a hitelesítést a Python 2 runbook futtató kapcsolódási funkciójával.

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

- Tekintse át a [grafikus authoring-hivatkozásokat](automation-graphical-authoring-intro.md#links-and-workflow) , hogy megtudja, hogyan irányíthatja és irányíthatja a logikát a runbookok.
* A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Ha többet szeretne megtudni a PowerShell-modulok Azure Automation használatáról, valamint a saját PowerShell-modulok létrehozásával kapcsolatos ajánlott eljárásokat a Azure Automationon belüli integrációs modulokként való munkához, tekintse meg az [integrációs modulokat](automation-integration-modules.md).