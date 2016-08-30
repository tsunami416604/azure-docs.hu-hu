<properties
    pageTitle="Azure-beli futtató fiók konfigurálása | Microsoft Azure"
    description="Az oktatóprogram végigvezeti az egyszerű biztonsági hitelesítés létrehozásán, tesztelésén és használatán az Azure Automationben."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="service principal name, setspn, azure authentication"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/07/2016"
    ms.author="magoedte"/>

# Forgatókönyvek hitelesítése Azure-beli futtató fiókkal
Ez a témakör bemutatja, hogyan lehet egy Automation-fiókot konfigurálni az Azure portálról az új futtató fiók funkció (más néven egyszerű szolgáltatás) használatával az előfizetésében lévő Azure Resource Manager-erőforrások eléréséhez az Automation forgatókönyvekkel.  Amikor létrehoz egy új Automation-fiókot az Azure portálon, automatikusan létrehoz egy új egyszerű szolgáltatást, alapértelmezés szerint hozzárendeli az előfizetés Közreműködő szerepköralapú hozzáférés-vezérlő (RBAC) szerepköréhez.  Ez leegyszerűsíti a folyamatot, és segít a tervezés gyors megkezdésében, és forgatókönyvek üzembe helyezésében az automatizálási szükségletek támogatására.      

Egy egyszerű szolgáltatás használatával a következőket teheti:

* Szabványosított hitelesítést használhat az Azure-hoz, amikor forgatókönyvekkel kezel Azure Resource Manager-erőforrásokat
* Automatizálhatja az Azure Alerts szolgáltatásban konfigurált globális forgatókönyvek használatát


>[AZURE.NOTE] Az Automation globális forgatókönyvekhez használható Azure [Alert integrációs funkció](../azure-portal/insights-receive-alert-notifications.md) egy egyszerű szolgáltatással konfigurált Automation-fiókot igényel. Kiválaszthat egy Automation-fiókot, amelyhez már meg van határozva egy egyszerű szolgáltatási felhasználó, vagy választhatja egy új létrehozását is.



Meg fogjuk mutatni, hogyan lehet létrehozni egy Automation-fiókot egyrészt az Azure portálról, másrészt az Azure PowerShell használatával frissíteni egy fiókot egy futtató fiókkal, valamint azt, hogyan lehet hitelesíteni azzal az egyszerű szolgáltatással a forgatókönyvekben.  

## Egy új Automation-fiók létrehozása az Azure portálról
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy új Azure Automation-fiók és egyszerű szolgáltatás létrehozásához az Azure portálon.

>[AZURE.NOTE] A lépéseket végrehajtó felhasználónak az Előfizetési rendszergazda szerepkörrel *kell* rendelkeznie.

1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza az **Automation-fiókok** elemet.
3. Az Automation-fiókok panelen kattintson a **Hozzáadás** elemre.<br>![Automation-fiók hozzáadása](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha egynél több előfizetéssel rendelkezik, adja meg az új fiókhoz használni kívántat, valamint egy új vagy meglévő **Erőforráscsoport** és egy Azure adatközpont **Hely** elemet.
6. Ellenőrizze, hogy az **Igen** érték van kiválasztva az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  

    ![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen.  Miközben a fiók létrejön, és csatolva lesz az előfizetés **Közreműködő** szerepköréhez, nem fog hozzá tartozó hitelesítési identitással rendelkezni az előfizetések könyvtáron belül, és így nem fog hozzáférni erőforrásokhoz az előfizetésben.  Ez megakadályozza a fiókra hivatkozó forgatókönyveket abban, hogy hitelesítsenek és feladatokat végezzenek el az Azure Resource Manager-erőforrásokon.

    ![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Ha az engedély megtagadását jelző hibaüzenettel találkozik a **Létrehozás** gombra kattintás után, ez azért van, mert a fiókja nem tartozik az Előfizetési rendszergazda szerepkörbe.  

7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

### Érintett erőforrások
Amikor az Automation-fiók létrehozása befejeződött, számos erőforrás automatikusan létrejön. Ezeket a következő táblázat foglalja össze.

Erőforrás|Leírás 
----|----
AzureAutomationTutorial forgatókönyv|Egy példa forgatókönyv, amely megmutatja, hogyan kell a futtató fiók használatával hitelesíteni, és megjeleníti az első 10 Azure virtuális gépet az előfizetésében.
AzureRunAsCertificate|Egy tanúsítványeszköz, amely vagy az Automation-fiók létrehozása közben kiválasztott, egy futtató fiókot létrehozó beállítás, vagy az alábbi PowerShell-parancsprogram egy meglévő fiókon történt használata miatt jött létre.  Ennek a tanúsítványnak egy éves időtartama van. 
AzureRunAsConnection|Egy kapcsolati eszköz, amely vagy az Automation-fiók létrehozása közben kiválasztott, egy futtató fiókot létrehozó beállítás, vagy az alábbi PowerShell-parancsprogram egy meglévő fiókon történt használata miatt jött létre.
Modulok|15 modul az Azure, a PowerShell és az Automation szolgáltatásokhoz való parancsmagokkal, amelyeket azonnal el lehet kezdeni használni a forgatókönyvekben.  

## Egy Automation-fiók frissítése a PowerShell használatával
Az alábbi eljárás frissít egy meglévő Automation-fiókot, és létrehozza az egyszerű szolgáltatást a PowerShell használatával.  Az eljárás akkor szükséges, ha létrehozott egy fiókot, de nem hozta létre a futtató fiókot.

A folytatás előtt ellenőrizze az alábbiakat:

1. Windows 7 futtatása esetén letöltötte és telepítette a [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) verzióját.   
    Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 8.1 és Windows 7 SP1 futtatása esetén a [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) verziója elérhető telepítésre.
2. Azure PowerShell 1.0. Ezen kiadásról és annak telepítéséről további információkért lásd: [Az Azure PowerShell telepítése és konfigurálása](../powershell-install-configure.md). 
3. Létrehozott egy Automation-fiókot.  A fiókra az –AutomationAccountName és -ApplicationDisplayName paraméterek értékeként hivatkozik az alábbi parancsprogram.


A PowerShell-parancsprogram konfigurálja az alábbiakat:

* Egy Azure AD alkalmazást, amelyet az önaláírt tanúsítvány hitelesít. Hozzon létre egy egyszerű szolgáltatásfiókot ehhez az alkalmazáshoz az Azure AD-ben, és rendelje hozzá a Közreműködő szerepkört (ezt módosíthatja Tulajdonosra vagy bármelyik másik szerepkörre) ehhez a fiókhoz a jelenlegi előfizetésében.  További információkért tekintse át [Az Azure Automation szerepköralapú hozzáférés-vezérlése](../automation/automation-role-based-access-control.md) című cikket.  
* Egy **AzureRunAsCertificate** nevű, a megadott Automation-fiókban található Automation tanúsítványeszközt, amely magában foglalja az egyszerű szolgáltatásban használt tanúsítványt.
* Egy **AzureRunAsConnection** nevű, a megadott Automation-fiókban található Automation kapcsolati eszközt, amely magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.  


### A PowerShell-parancsprogram futtatása

1. Mentse el a következő parancsprogramot a számítógépén.  Ebben a példában mentse el a következő fájlnéven: **New-AzureServicePrincipal.ps1**.  

    ```
    #Requires -RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory=$true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount
    Import-Module AzureRM.Resources
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
3. Az emelt szintű PowerShell parancssori rendszerhéjból lépjen a mappába, amely az 1. lépésben létrehozott parancsprogramot tartalmazza, és futtassa azt a *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* és *-CertPlainPassword* paraméterek értékeinek módosításával.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -SubscriptionId <SubscriptionId> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] A rendszer az Azure rendszerrel történő hitelesítést fogja kérni a parancsprogram futtatása után.  Egy olyan fiókkal *kell* bejelentkeznie, amely Szolgáltatás-rendszergazda az előfizetésben.  
<br>
4. Miután a parancsprogram sikeresen befejeződik, folytassa a következő szakasszal az új hitelesítési konfiguráció teszteléséhez és ellenőrzéséhez.

### Hitelesítés ellenőrzése
A következőkben egy rövid tesztet fogunk elvégezni annak megerősítéséhez, hogy képes sikeresen hitelesíteni az új egyszerű szolgáltatás használatával. Ha nem tud sikeresen hitelesíteni, lépjen vissza az 1. lépéshez és ellenőrizze az összes korábbi lépés elvégzését.    

1. Az Azure portálon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Hozzon létre egy új forgatókönyvet a **Forgatókönyv hozzáadása** gombra kattintva, majd a **Forgatókönyv hozzáadása** panelen válassza az **Új forgatókönyv hozzáadása** elemet.
4. Adja a forgatókönyvnek a *Test-SecPrin-Runbook* nevet, és válassza a PowerShell lehetőséget a **Forgatókönyv típusa** beállításnál.  A forgatókönyv létrehozásához kattintson a **Létrehozás** gombra.
5. A **PowerShell forgatókönyv szerkesztése** panelen másolja be a következő kódot a vászonra:<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```  
<br>
6. A **Mentés** gombra kattintva mentse el a forgatókönyvet.
7. Kattintson a **Teszt panel** elemre a **Teszt** panel megnyitásához.
8. Kattintson az **Indítás** gombra a teszt elindításához.
9. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), az állapota pedig megjelenik a panelen.  
10. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
11. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. A mi esetünkben **Befejezve** állapotot kell látnunk.<br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. Zárja be a **Teszt** panelt, és térjen vissza a vászonra.
13. Zárja be a **PowerShell-forgatókönyv szerkesztése** panelt.
14. Zárja be a **Test-SecPrin-Runbook** panelt.

## Mintakód a Resource Manager-erőforrásokkal történő hitelesítéshez

Használhatja az alábbi, az AzureAutomationTutorial mintaforgatókönyvből származó frissített mintakódot a futtató fiók használatával történő hitelesítéshez, hogy kezelje a Resource Manager-erőforrásokat a forgatókönyvekkel. 

   ```
   $connectionName = "AzureRunAsConnection"
   $SubId = Get-AutomationVariable -Name 'SubscriptionId'
   try
   {
      # Get the connection "AzureRunAsConnection "
      $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

      "Logging in to Azure..."
      Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
      "Setting context to a specific subscription"   
      Set-AzureRmContext -SubscriptionId $SubId          
   }
   catch {
       if (!$servicePrincipalConnection)
       {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
       } else{
           Write-Error -Message $_.Exception
           throw $_.Exception
       }
   } 
   ```

A parancsprogram tartalmaz két további kódsort az előfizetési környezet hivatkozásának támogatására, így könnyedén tud több előfizetéssel is dolgozni. Egy SubscriptionId nevű változó objektum tartalmazza az előfizetés azonosítóját, és az Add-AzureRmAccount parancsmag nyilatkozata után a [Set-AzureRmContext parancsmag](https://msdn.microsoft.com/library/mt619263.aspx) a *-SubscriptionId* paraméterkészlettel nyilatkozik. Ha a változó neve túl általános, módosíthatja a változó nevét, hogy tartalmazzon egy előtagot vagy igazodjon más elnevezési konvenciókhoz, és könnyebbé tegye az azonosítását. Alternatív megoldásként a -SubscriptionId helyett használhatja a -SubscriptionName paraméterkészletet egy megfelelő változó objektummal.  

## Következő lépések
- Az Egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](../active-directory/active-directory-application-objects.md).
- Az Azure Automation szerepköralapú hozzáférés-vezérlési funkciójáról további információkért lásd: [Az Azure Automation szerepköralapú hozzáférés-vezérlése](../automation/automation-role-based-access-control.md).



<!--HONumber=jun16_HO2-->


