---
title: "Azure Automation szolgáltatásbeli kapcsolódási eszközök |} Microsoft Docs"
description: "Kapcsolati objektumok Azure Automation DSC-konfiguráció vagy a runbook egy külső szolgáltatás vagy alkalmazás való kapcsolódáshoz szükséges adatokat tartalmaz. Ez a cikk ismerteti a kapcsolatok és a szöveges és a grafikus szerzői őket munkavégzés részleteit."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: f0239017-5c66-4165-8cca-5dcb249b8091
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/13/2017
ms.author: magoedte; bwren
ms.openlocfilehash: f1746f4f6706835d43edc171b03d4ececfa3560c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connection-assets-in-azure-automation"></a>Azure Automation szolgáltatásbeli kapcsolódási eszközök

Automation szolgáltatásbeli kapcsolódási eszköz csatlakozhat egy külső szolgáltatás vagy alkalmazás a runbookot vagy a DSC-konfiguráció szükséges információkat tartalmazza. Ide tartozhat például felhasználónév és jelszó kapcsolati információkat, például egy URL-cím és port mellett hitelesítéshez szükséges adatokat. A kapcsolat értékének megakadályozza a található összes tulajdonság egy eszköz több változók létrehozása figyelésekor az alkalmazáshoz való kapcsolódáshoz. A felhasználó szerkesztheti-e a kapcsolat egy helyen az értékeket, és a kapcsolat neve átadása egy runbookot vagy a DSC-konfiguráció egyetlen paramétert. A kapcsolat tulajdonságainak érhetők el a runbookot vagy a DSC-konfiguráció a **Get-AutomationConnection** tevékenység. 

Amikor kapcsolatot hoz létre, meg kell adnia egy *kapcsolattípus*. A kapcsolat típusa a sablont, amely tulajdonságait határozza meg. A kapcsolat határozza meg a kapcsolódási típus definiált tulajdonságok értékeit. Kapcsolattípusok integrációs modulok az Azure Automation hozzáadásakor vagy létrehozni a [Azure Automation API](http://msdn.microsoft.com/library/azure/mt163818.aspx) Ha az integrációs modul kapcsolattípus tartalmaz, és importálja az Automation-fiók. Ellenkező esetben szüksége lesz hozzon létre egy metaadatfájl automatizálási kapcsolat típusának megadását.  Ezzel kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).  

>[!NOTE] 
>Az Azure Automationben biztonságos eszközök közé tartozik a hitelesítő adatokat, a tanúsítványokat, a kapcsolatok és a titkosított változók. Ezek az eszközök titkosítva, és tárolja az Azure Automation létrehozott egyedi kulcs segítségével minden egyes automation-fiókhoz. Ezt a kulcsot egy mestertanúsítvány titkosítja és az Azure Automationben tárolja. Előtt tárolása biztonságos eszköz, az automatizálási fiók kulcs visszafejtése a mestertanúsítvány, és majd az eszköz titkosításához használt.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagjai

A következő táblázatban található parancsmagokkal létrehozása és kezelése az Automation-kapcsolatok a Windows PowerShell segítségével. Részét képezi a [Azure PowerShell modul](/powershell/azure/overview) elérhető Automation-forgatókönyveket és a DSC-konfigurációk.

|Parancsmag|Leírás|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Lekéri a kapcsolat. Tartalmaz egy kivonattáblát a kapcsolat mezők értékekkel.|
|[Új AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Létrehoz egy új kapcsolatot.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Eltávolít egy létező kapcsolatot.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Beállítja egy létező kapcsolat adott mezőjének értékét.|

## <a name="activities"></a>Tevékenységek

A tevékenységek az alábbi táblázatban a runbookot vagy a DSC-konfiguráció található kapcsolatok elérésére használhatók.

|Tevékenységek|Leírás|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Lekérdezi a kapcsolat használjon. A kapcsolat tulajdonságait kivonatoló tábla visszaadása.|

>[!NOTE] 
>Kerülendő a változók használata a – Name paraméterében **Get - AutomationConnection** óta ez megnehezítheti a runbookot vagy a DSC-konfigurációk és kapcsolati objektumok közti függőségek tervezési időben.

 
## <a name="python2-functions"></a>Python2 funkciók 
A függvény a következő táblázat a Python2 runbookban található kapcsolatok elérésére szolgál. 

| Függvény | Leírás | 
|:---|:---| 
| automationassets.get_automation_connection | Lekéri a kapcsolat. A kapcsolat tulajdonságait a szótár adja vissza. | 

> [!NOTE] 
> A Python runbook tetején a "automationassets" modul kell importálnia az eszköz funkciók eléréséhez.

## <a name="creating-a-new-connection"></a>Az új kapcsolat létrehozása

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Új kapcsolat létrehozása az Azure portállal

1. Az automation-fiók, kattintson a **eszközök** nyissa meg a kijelző a **eszközök** panelen.
2. Kattintson a **kapcsolatok** nyissa meg a kijelző a **kapcsolatok** panelen.
3. Kattintson a **kapcsolatot** a panel tetején.
4. Az a **típus** legördülő menüben válassza ki a létrehozni kívánt kapcsolat típusát. A képernyőn megjelennek az adott típusú tulajdonságok.
5. Töltse ki az űrlapot, és kattintson a **létrehozása** menti az új kapcsolatot.

### <a name="to-create-a-new-connection-with-the-azure-classic-portal"></a>Egy új kapcsolat létrehozása a klasszikus Azure portálon

1. Az automation-fiók kattintson **eszközök** az ablak tetején.
2. Az ablak alján kattintson **beállítás hozzáadása**.
3. Kattintson a **kapcsolat hozzáadása a**.
4. Az a **kapcsolattípus** legördülő menüben válassza ki a létrehozni kívánt kapcsolat típusát.  A varázsló az adott típusú tulajdonságok jelent.
5. Fejezze be a varázslót, és kattintson a jelölőnégyzetbe, az új kapcsolat mentéséhez.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Új kapcsolat létrehozása a Windows PowerShell használatával

Új kapcsolat létrehozása a Windows PowerShell használatával a [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) parancsmag. Ez a parancsmag nevű paraméterrel rendelkezik **ConnectionFieldValues** vár, amely egy [kivonattábla](http://technet.microsoft.com/library/hh847780.aspx) értékek meghatározása az egyes határozzák meg a kapcsolattípus tulajdonságait.

Ha ismeri az automatizálás [Futtatás mint fiók](automation-sec-configure-azure-runas-account.md) runbookok a szolgáltatás egyszerű hitelesítést végezni, a PowerShell-parancsfájl, a megadott helyett a futtató fiók létrehozása a portálon, az új kapcsolat létrehozása az alábbi Példaparancsok eszköz.  

    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 

A kapcsolódási eszköz létrehozása, mert az Automation-fiók létrehozásakor automatikusan tartalmaz több globális modulok együtt a kapcsolat típusa alapértelmezés szerint a parancsfájl segítségével lehet **AzurServicePrincipal** létrehozásához a **AzureRunAsConnection** kapcsolódási eszköz.  Ez fontos tartsa szem előtt, mert ha próbál kapcsolódni egy szolgáltatás vagy alkalmazás egy másik hitelesítési módszer új kapcsolódási eszköz létrehozása, mert a kapcsolat típusa nem már definiálva van az Automation-fiók meghiúsul.  További információt az egyéni vagy a modulnak a saját kapcsolattípus létrehozásával a [PowerShell-galériában](https://www.powershellgallery.com), lásd: [integrációs modulok](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>A runbookot vagy a DSC-konfiguráció kapcsolat használatával

Egy kapcsolat a runbookot vagy a DSC-konfiguráció lekérése a **Get-AutomationConnection** parancsmag.  Nem használhatja a [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn) tevékenység.  Ez a tevékenység lekéri a kapcsolat különböző mezőinek értékeit, és adja vissza őket egy [kivonattábla](http://go.microsoft.com/fwlink/?LinkID=324844) amelyek ezután felhasználhatók a DSC-konfiguráció vagy a runbook megfelelő parancsaival.

### <a name="textual-runbook-sample"></a>Szöveges forgatókönyvként minta

A következő mintaparancsok bemutatják, hogyan használható a futtató fiók, már említettük, az Azure Resource Manager erőforrást a runbookban való hitelesítéshez szükséges.  A kapcsolódási eszköz képviselő a Futtatás mint fiókot, amely hivatkozik a egyszerű tanúsítvány-alapú szolgáltatás, nem a hitelesítő adatokat használ.  

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 

### <a name="graphical-runbook-samples"></a>Grafikus forgatókönyv minták

Hozzáadhat egy **Get-AutomationConnection** tevékenység és a kapcsolat a könyvtár ablaktáblán grafikus szerkesztő csomagot jobb gombbal, majd válassza a grafikus forgatókönyvnek **vászonra Hozzáadás**.

![](media/automation-connections/connection-add-canvas.png)

Az alábbi ábrán egy példa egy grafikus forgatókönyvnek kapcsolat használatával.  Ez az ugyanebben a példában a futtató fiók használata a szöveges forgatókönyvként hitelesítéséhez fent látható.  Ez a példa a **konstans** meg van adva a **RunAs kapcsolat beolvasása** tevékenység egy olyan kapcsolatobjektum használ.  A [folyamatkapcsolódása](automation-graphical-authoring-intro.md#links-and-workflow) itt szolgál, mivel a ServicePrincipalCertificate paraméterhalmaz vár egy adott objektum.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook minta
A következő példa bemutatja, hogyan hitelesítést a Futtatás mint kapcsolat Python2 runbookban.

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

## <a name="next-steps"></a>Következő lépések

- Felülvizsgálati [hivatkozások grafikus szerzői](automation-graphical-authoring-intro.md#links-and-workflow) megértéséhez logikai a runbookok áramló irányítása.  

- Azure Automation PowerShell-modulok és használata ajánlott eljárások létrehozásához a saját működjön, integrációs modulok belül Azure Automation PowerShell-modulok kapcsolatos további információkért lásd: [integrációs modulok](automation-integration-modules.md).  
