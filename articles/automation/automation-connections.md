---
title: Kapcsolati objektumok az Azure Automationben
description: Kapcsolati objektumok az Azure Automationben a runbookból vagy DSC-konfiguráció egy külső szolgáltatás vagy alkalmazás való kapcsolódáshoz szükséges adatokat tartalmazzák. Ez a cikk ismerteti a kapcsolatokat, és hogyan dolgozhatnak velük a szöveges és a grafikus szerzői részleteit.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1e9ca18d2075d40f6f55bc84723f79ae7e10850b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261213"
---
# <a name="connection-assets-in-azure-automation"></a>Kapcsolati objektumok az Azure Automationben

Automation szolgáltatásbeli kapcsolódási eszköz runbookból vagy DSC-konfiguráció egy külső szolgáltatás vagy alkalmazás való kapcsolódáshoz szükséges információkat tartalmazza. Ide tartozhatnak például a felhasználónév és jelszó a kapcsolati adatokat, például egy URL-cím és port mellett a hitelesítéshez szükséges adatokat. Az érték egy kapcsolat tulajdonságai között található egy eszköz több változó létrehozása helyett egy adott alkalmazás csatlakozik az összes viselkedéssel. A felhasználó szerkesztheti az értékeket egy kapcsolat egy helyen, és a egy runbook vagy DSC-konfiguráció egyetlen paramétert a átadható egy kapcsolat nevét. A kapcsolat tulajdonságainak érhetők el a runbook vagy DSC-konfiguráció a **Get-AutomationConnection** tevékenység. 

A kapcsolat létrehozásakor meg kell adnia egy *kapcsolattípus*. A kapcsolattípus tulajdonságait meghatározó sablon. A kapcsolat meghatározza a kapcsolattípus definiált tulajdonságok értékeit. Kapcsolattípusok integrációs modulok az Azure Automation hozzá vagy hoztak létre a [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163818.aspx) , ha az integrációs modul olyan kapcsolat típust tartalmaz, és importálja az Automation-fiók. Ellenkező esetben szüksége lesz egy metaadatait tartalmazó fájl megadásához egy Automation kapcsolattípust létrehozása.  Ezzel kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).  

>[!NOTE]
>Az Azure Automationben biztonságos eszközök tartalmazzák, hitelesítő adatok, tanúsítványok, kapcsolatok és a titkosított változókat. Ezek az eszközök titkosítottak és a létrehozott egyedi kulcs segítségével minden automation-fiókhoz tartozó Azure Automation tárolja. Ezt a kulcsot a Key Vaultban tárolt. A kulcs tárolása egy biztonságos objektumot, előtt betöltése a Key Vaultból és majd az eszköz titkosításához használt.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagok

A következő táblázat parancsmagjai segítségével a Windows PowerShell-lel Automation kapcsolatok létrehozása és kezelése. Részét képezi a [Azure PowerShell-modul](/powershell/azure/overview) elérhető a Automation-runbookok és a DSC-konfigurációkat használhatnak.

|Parancsmag|Leírás|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|A kapcsolat veszi át. Tartalmaz egy kivonattáblát a a kapcsolat a mezők értékeit.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Létrehoz egy új kapcsolatot.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Eltávolít egy létező kapcsolatot.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Beállítja egy létező kapcsolat adott mezőjének értékét.|

## <a name="activities"></a>Tevékenységek

Az alábbi táblázatban felsorolt tevékenységek a runbookból vagy DSC-konfigurációból található kapcsolatok elérésére használhatók.

|Tevékenységek|Leírás|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Lekérdezi egy kapcsolat használatára. A kapcsolat tulajdonságainak kivonat táblázatot ad vissza.|

>[!NOTE] 
>Kerülendő a változók használata a – Name paraméterében **Get-AutomationConnection** mivel ez megnehezítheti a runbook vagy DSC-konfigurációk és kapcsolati objektumok közötti függőségek a tervezés során.

 
## <a name="python2-functions"></a>Python2-funkciók 
A függvény a következő táblázat a Python2-forgatókönyvem található kapcsolatok elérésére szolgál. 

| Függvény | Leírás | 
|:---|:---| 
| automationassets.get_automation_connection | A kapcsolat veszi át. A kapcsolat tulajdonságait egy szótár adja vissza. | 

> [!NOTE] 
> Importálnia kell a "automationassets" modul a Python-alapú runbook tetején az eszközintelligencia-funkciók eléréséhez.

## <a name="creating-a-new-connection"></a>Az új kapcsolat létrehozása

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Új kapcsolat létrehozása az Azure portal használatával

1. Az automation-fiókjából, kattintson a **eszközök** való nyissa meg a **eszközök** panelen.
2. Kattintson a **kapcsolatok** való nyissa meg a **kapcsolatok** panelen.
3. Kattintson a **vegyen fel egy kapcsolatot** a panel tetején.
4. Az a **típus** legördülő menüben válassza ki a létrehozni kívánt kapcsolat típusát. Az űrlap jelent az, hogy adott típus tulajdonságait.
5. Töltse ki az űrlapot, és kattintson a **létrehozás** menteni az új kapcsolatot.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Új kapcsolat létrehozása a Windows PowerShell-lel

Új kapcsolat létrehozása a Windows PowerShell a [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) parancsmagot. Ez a parancsmag paramétereinek nevű **Kivonattáblájának** vár, amely egy [kivonattábla](https://technet.microsoft.com/library/hh847780.aspx) értékek meghatározása az egyes határozzák meg a kapcsolattípus tulajdonságait.

Ha ismeri az Automation [Futtatás mint fiók](automation-sec-configure-azure-runas-account.md) az egyszerű szolgáltatás használatával forgatókönyvek hitelesítéséhez, a PowerShell-parancsfájlt, a futtató fiók létrehozása a portálról, alternatív megoldásként a megadott új kapcsolat létrehozása az eszköz az alábbi Példaparancsok használatával.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

A kapcsolódási eszköz létrehozása, mert az Automation-fiók létrehozásakor automatikusan része a több globális modulok együtt a kapcsolattípus alapértelmezés szerint a parancsfájl segítségével tudja **AzureServicePrincipal** , Hozzon létre a **AzureRunAsConnection** kapcsolat típusú objektum.  Ez azért fontos, hogy ne feledje, ha tárolórétegeket próbál létrehozni egy új kapcsolatobjektum szeretne csatlakozni egy másik hitelesítési módszert a szolgáltatás vagy alkalmazás, akkor sikertelen lesz, mert a kapcsolat típusa nem meghatározott az Automation-fiókban.  Hogyan hozhat létre a saját típusát az egyéni vagy modulnak a kapcsolatban további információért a [PowerShell-galériából](https://www.powershellgallery.com), lásd: [integrációs modulok](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Kapcsolat egy runbook vagy DSC-konfiguráció használata

Egy kapcsolat egy runbookból vagy DSC-konfiguráció lekérése a **Get-AutomationConnection** parancsmagot.  Nem használhatja a [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) tevékenység.  Ez a tevékenység lekéri a kapcsolat különböző mezőinek értékét, és adja vissza őket egy [kivonattábla](https://go.microsoft.com/fwlink/?LinkID=324844) amelyek ezután felhasználhatók a runbook vagy DSC-konfiguráció megfelelő parancsaival.

### <a name="textual-runbook-sample"></a>Minta szöveges forgatókönyv

Az alábbi mintaparancsok bemutatják, hogyan használhatja a futtató fiókot, korábban említettük, a a runbookban Azure Resource Manager-erőforrásokkal történő hitelesítéshez.  Használja a kapcsolat adategység jelölő a futtató fiókot, amely hivatkozik, az egyszerű tanúsítvány-alapú szolgáltatás, nem hitelesítő adatokkal.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használhat **Add-AzureRmAccount**, vagy frissítheti az a modulokat az Automation-fiókban.

### <a name="graphical-runbook-samples"></a>Grafikus runbook-minták

Hozzáadhat egy **Get-AutomationConnection** tevékenységet, hogy a kapcsolat a grafikus szerkesztő a könyvtár ablaktáblán kattintson a jobb gombbal, majd válassza a grafikus runbookok **adja hozzá a vászonhoz**.

![](media/automation-connections/connection-add-canvas.png)

Az alábbi képen egy példa egy grafikus runbook-kapcsolattal.  Ez az ugyanebben a példában a futtató fiók használatával egy szöveges runbookkal hitelesítéséhez a fent látható.  Ez a példa a **konstans érték** meg a **futtató kapcsolat beszerzése** tevékenységgel, amely egy kapcsolat objektumot használja a hitelesítéshez.  A [folyamatkapcsolódása](automation-graphical-authoring-intro.md#links-and-workflow) itt szolgál, mivel a ServicePrincipalCertificate paraméterkészletet, a várt egyetlen objektumot.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook minta
A következő minta bemutatja, hogyan a Python2-forgatókönyvem futtató kapcsolat használatával történő hitelesítéshez.

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
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

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
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [hivatkozások a grafikus létrehozásról](automation-graphical-authoring-intro.md#links-and-workflow) való közvetlen és irányíthatja a runbookokban logikát.  

- Azure Automation PowerShell-modulok és ajánlott eljárások létrehozásához használt saját működjenek integrációs modulként az Azure Automation PowerShell-modulok kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).  
