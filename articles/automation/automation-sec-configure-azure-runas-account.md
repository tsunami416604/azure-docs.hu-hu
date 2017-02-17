---
title: "Azure-beli futtató fiók konfigurálása | Microsoft Docs"
description: "Az oktatóprogram végigvezeti az egyszerű biztonsági hitelesítés létrehozásán, tesztelésén és használatán az Azure Automationben."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "egyszerű szolgáltatásnév, setspn, azure-hitelesítés"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 32afda1861c2c558f8bb9ffedf897cd8d1df0b5c


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Forgatókönyvek hitelesítése Azure-beli futtató fiókkal
Ez a témakör bemutatja, hogyan lehet Automation-fiókot konfigurálni az Azure Portal webhelyről az új futtató fiók funkció használatával, amely az Azure Resource Managerben vagy az Azure Service Managerben erőforrásokat kezelő forgatókönyvek hitelesítésére szolgál.

Amikor létrehoz egy új Automation-fiókot az Azure Portal webhelyen, a rendszer a következőket is létrehozza:

* Egy futtató fiókot, amely létrehoz egy új szolgáltatásnevet az Azure Active Directoryban, illetve egy tanúsítványt, illetve hozzárendeli a Közreműködő szerepkörön alapuló hozzáférés-vezérlést (RBAC). Ezzel a szerepkörrel felügyelhetők a Resource Manager-erőforrások forgatókönyvek segítségével.   
* Egy klasszikus futtató fiókot, úgy, hogy feltölt egy felügyeleti tanúsítványt. Ezzel felügyelhetők az Azure Service Management- és klasszikus erőforrások forgatókönyvek segítségével.  

Ez leegyszerűsíti a folyamatot, és segít a tervezés gyors megkezdésében, és forgatókönyvek üzembe helyezésében az automatizálási szükségletek támogatására.      

A futtató és klasszikus futtató fiókok segítségével a következőket teheti meg:

* Szabványos módot biztosíthat az Azure-hitelesítésre abban az esetben, ha Azure Resource Manager- vagy Azure Service Management-erőforrásokat felügyel az Azure Portal webhelyen, forgatókönyvek segítségével.  
* Automatizálhatja az Azure Alerts szolgáltatásban konfigurált globális forgatókönyvek használatát.

> [!NOTE]
> Az Automation globális forgatókönyvekhez készült Azure [Alert integrációs funkció](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) használatához szükség van egy futtató és klasszikus futtató fiókokkal konfigurált Automation-fiókra. Válasszon egy Automation-fiókot, amelyhez már tartozik futtató és klasszikus futtató fiók, vagy hozzon létre egy új fiókot.
> 
> 

A cikkben bemutatjuk, hogyan hozhatja létre az Automation-fiókot az Azure Portal webhelyen, hogyan frissítheti Automation-fiókját a PowerShell segítségével, illetve, hogy hogyan végezhet hitelesítést a forgatókönyvekkel.

Mielőtt azonban továbblépnénk, fontos, hogy néhány dolgot tisztázzunk.

1. Az itt leírtak nincsenek hatással a korábban a klasszikus vagy a Resource Manager üzemi modell keretében már létrehozott Automation-fiókokra.  
2. Ezek a lépések kizárólag az Azure Portal webhelyen létrehozott Automation-fiókokon végezhetők el.  Ha a klasszikus portálról hoz létre fiókot, a rendszer nem fogja replikálni a futtató fiók konfigurációját.
3. Ha jelenleg rendelkezik olyan forgatókönyvekkel és objektumokkal (azaz ütemezésekkel, változókkal stb.), amelyeket klasszikus erőforrások felügyeletére hozott létre, és szeretné, hogy ezek a forgatókönyvek végezzék el az új klasszikus futtató fiók hitelesítését, át kell helyeznie ezeket az új Automation-fiókba, vagy frissítenie kell a meglévő fiókot az alább található PowerShell-parancsprogrammal.  
4. Ha az új futtató fiókkal és a klasszikus futtató Automation-fiókkal szeretne hitelesítést végezni, az alább található példakód segítségével módosítania kell meglévő forgatókönyveit.  **Vegye figyelembe**, hogy a futtató fiók Resource Manager-erőforrásokon, tanúsítványalapú szolgáltatásnévvel történő hitelesítésre, míg a klasszikus futtató fiók Service Manager-erőforrásokon, felügyeleti tanúsítvánnyal történő hitelesítésére szolgál.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Egy új Automation-fiók létrehozása az Azure portálról
Az ebben a fejezetben olvasható lépések végrehajtásával létrehozhatja az új Azure Automation-fiókját az Azure Portal webhelyen.  Ezzel mind a futtató fiókot, mind a klasszikus futtató fiókot létrehozza.  

> [!NOTE]
> Ahhoz, hogy a felhasználó elvégezhesse ezeket a lépéseket, *rendelkeznie kell* az Előfizetés-adminisztrátorok szerepkörrel, valamint annak az előfizetésnek a társadminisztrátori szerepkörével, amely hozzáférést biztosít az előfizetéshez a felhasználónak.  Ezenfelül a felhasználót fel kell venni az előfizetés alapértelmezett Active Directoryjának felhasználói közé. A fiókhoz nem szükséges kiemelt szerepkört rendelni.
> 
> 

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Válassza az **Automation-fiókok** elemet.
3. Az Automation-fiókok panelen kattintson a **Hozzáadás** elemre.<br>![Automation-fiók hozzáadása](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Ha az **Automation-fiók hozzáadása** panelen a következő figyelmeztetés jelenik meg, annak az az oka, hogy a fiók nem tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, illetve nem az előfizetés társadminisztrátora.<br>![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha egynél több előfizetéssel rendelkezik, adja meg, hogy melyiket kívánja használni az új fiókhoz. Ezenfelül az **Erőforráscsoport** beállításnál adjon meg egy új vagy meglévő erőforráscsoportot, a **Hely** mezőben pedig válassza ki az Azure-adatközpont helyét.
6. Ellenőrizze, hogy az **Igen** érték van kiválasztva az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  
   
   > [!NOTE]
   > Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen.  Ha a fiókot az Azure Portalon hozza létre, az nem kap hitelesítési identitást a klasszikus vagy Resource Manager-előfizetés címtárszolgáltatásában, így az előfizetéshez tartozó erőforrásokhoz sem fér hozzá.  Ez megakadályozza, hogy az erre a fiókra hivatkozó forgatókönyvek hitelesítést végezhessenek, illetve feladatokat futtathassanak az ezekben az üzemi modellekben található erőforrások alapján.
   > 
   > ![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Ha nem hozza létre a szolgáltatásnevet, a rendszer nem osztja ki a Közreműködő szerepkört.
   > 
   > 
7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="resources-included"></a>Érintett erőforrások
Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön.  Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.<br>

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |PowerShell-mintaforgatókönyv, amely bemutatja, hogyan kell a futtató fiók használatával hitelesíteni, és megjeleníti az összes Resource Manager-erőforrást. |
| AzureAutomationTutorialScript forgatókönyv |PowerShell-mintaforgatókönyv, amely bemutatja, hogyan kell a futtató fiók használatával hitelesíteni, és megjeleníti az összes Resource Manager-erőforrást. |
| AzureRunAsCertificate |Tanúsítványobjektum, amely automatikusan létrejön, amikor Automation-fiókot hoz létre, vagy lefuttatja egy meglévő fiókon az alább található PowerShell-parancsprogramot.  Lehetővé teszi az Azure-hitelesítést, így a forgatókönyvekből is kezelheti az Azure Resource Manager-erőforrásokat.  Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |Kapcsolatobjektum, amely automatikusan létrejön, amikor Automation-fiókot hoz létre, vagy lefuttatja egy meglévő fiókon az alább található PowerShell-parancsprogramot. |

Az alábbi táblázat a klasszikus futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.<br>

| Erőforrás | Leírás |
| --- | --- |
| AzureClassicAutomationTutorial forgatókönyv |Mintaforgatókönyv, amely a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv |Mintaforgatókönyv, amely a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicRunAsCertificate |Automatikusan létrejövő tanúsítványobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a forgatókönyvekből.  Ennek a tanúsítványnak egy éves időtartama van. |
| AzureClassicRunAsConnection |Automatikusan létrejövő kapcsolatobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a forgatókönyvekből. |

## <a name="verify-run-as-authentication"></a>A futtató fiókos hitelesítés ellenőrzése
A következőkben egy rövid tesztet fogunk végrehajtani, amellyel megállapítjuk, hogy képes-e sikeres hitelesítést végezni az új futtató fiók használatával.     

1. Az Azure portálon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Válassza ki az **AzureAutomationTutorialScript** forgatókönyvet, majd a forgatókönyv elindításához kattintson az **Indítás** gombra.  A rendszer felkéri, hogy erősítse meg, valóban el szeretné indítani a forgatókönyvet.
4. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), megjelenik a Feladat panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.  
5. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
6. Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.<br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.
8. A **Kimenet** panelen azt kell látnia, hogy a forgatókönyv sikeresen elvégezte a hitelesítést, és visszaadta az erőforráscsoportban elérhető összes erőforrás listáját.
9. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.
10. Zárja be a **Feladat összegzése** panelt, és a megfelelő **AzureAutomationTutorialScript** forgatókönyv paneljét.

## <a name="verify-classic-run-as-authentication"></a>A klasszikus futtató fiókos hitelesítés ellenőrzése
A következőkben egy rövid tesztet fogunk végrehajtani, amellyel megállapítjuk, hogy képes-e sikeres hitelesítést végezni az új klasszikus futtató fiók használatával.     

1. Az Azure portálon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Válassza ki az **AzureClassicAutomationTutorialScript** forgatókönyvet, majd a forgatókönyv elindításához kattintson az **Indítás** gombra.  A rendszer felkéri, hogy erősítse meg, valóban el szeretné indítani a forgatókönyvet.
4. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), megjelenik a Feladat panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.  
5. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
6. Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.<br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.
8. A **Kimenet** panelen azt kell látnia, hogy a forgatókönyv sikeresen elvégezte a hitelesítést, és visszaadta az előfizetésben szereplő összes klasszikus virtuális gép listáját.
9. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.
10. Zárja be a **Feladat összegzése** panelt, és a megfelelő **AzureClassicAutomationTutorialScript** forgatókönyv paneljét.

## <a name="update-an-automation-account-using-powershell"></a>Egy Automation-fiók frissítése a PowerShell használatával
Ebben a részben leírjuk, hogyan frissítheti meglévő Automation-fiókját a PowerShell segítségével. Erre a következő esetekben lehet szükség:

1. Létrehozott egy Automation-fiókot, de futtató fiókot nem.
2. Már rendelkezik egy, a Resource Manager-erőforrások felügyeletére szolgáló Automation-fiókkal, de szeretné azt frissíteni, hogy forgatókönyves hitelesítést lehetővé tevő futtató fiókot is tartalmazzon.
3. Már rendelkezik egy, a klasszikus erőforrások felügyeletére szolgáló Automation-fiókkal, de szeretné azt frissíteni, és klasszikus futtató fiókként használni, hogy megtakarítsa az új fiók létrehozásához és a forgatókönyvek és objektumok áthelyezéséhez szükséges időt.   

A folytatás előtt ellenőrizze az alábbiakat:

1. Windows 7 futtatása esetén letöltötte és telepítette a [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) verzióját.   
    Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 8.1 és Windows 7 SP1 futtatása esetén a [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) verziója elérhető telepítésre.
2. Azure PowerShell 1.0. Ezen kiadásról és annak telepítéséről további információkért lásd: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
3. Létrehozott egy Automation-fiókot.  A fiókra az –AutomationAccountName és -ApplicationDisplayName paraméterek értékeként hivatkozik az alábbi parancsprogram.

A parancsprogram végrehajtásához feltétlenül szükséges *SubscriptionID*, *ResourceGroup* és *AutomationAccountName* paraméterek értékének beszerzéséhez lépjen be az Azure Portalra, válassza ki Automation-fiókját az **Automation-fiók** panelen, majd válassza a **Minden beállítás** elemet.  A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget.  A **Tulajdonságok** panelen megtalálja a keresett értékeket.<br> ![Automation-fiók tulajdonságai](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Futtató fiókhoz használható PowerShell-parancsprogram létrehozása
Az alábbi PowerShell-parancsprogram a következők konfigurálására alkalmas:

* Egy Azure AD alkalmazást, amelyet az önaláírt tanúsítvány hitelesít. Hozzon létre egy egyszerű szolgáltatásfiókot ehhez az alkalmazáshoz az Azure AD-ben, és rendelje hozzá a Közreműködő szerepkört (ezt módosíthatja Tulajdonosra vagy bármelyik másik szerepkörre) ehhez a fiókhoz a jelenlegi előfizetésében.  További információkért tekintse át [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.
* Egy **AzureRunAsCertificate** nevű Automation-tanúsítványobjektum a megadott Automation-fiókban. Ez tartalmazza a szolgáltatásnév által használt tanúsítványt.
* Egy **AzureRunAsConnection** nevű, a megadott Automation-fiókban található Automation kapcsolati eszközt, amely magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.    

Az alábbi lépésekből megtudhatja, hogyan hajtsa végre a parancsprogramot.

1. Mentse el a következő parancsprogramot a számítógépén.  Ebben a példában mentse el a következő fájlnéven: **New-AzureServicePrincipal.ps1**.  
   
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
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
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
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
2. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
3. Az emelt szintű PowerShell parancssori rendszerhéjból lépjen a mappába, amely az 1. lépésben létrehozott parancsprogramot tartalmazza, és futtassa azt a *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* és *-CertPlainPassword* paraméterek értékeinek módosításával.<br>
   
   > [!NOTE]
   > A rendszer az Azure rendszerrel történő hitelesítést fogja kérni a parancsprogram futtatása után. Olyan fiókkal kell bejelentkeznie, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, illetve az előfizetés társadminisztrátorai közé tartozik.
   > 
   > 
   
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
   <br>

Ha a parancsprogram sikeresen lefutott, az alább látható [kódminta](#sample-code-to-authenticate-with-resource-manager-resources) segítségével állítsa be a hitelesítést a Resource Manager-erőforrásokkal, valamint ellenőrizze a hitelesítő adatok konfigurációját.

### <a name="create-classic-run-as-account-powershell-script"></a>Klasszikus futtató fiókhoz használható PowerShell-parancsprogram létrehozása
Az alábbi PowerShell-parancsprogram a következők konfigurálására alkalmas:

* Egy **AzureClassicRunAsCertificate** nevű Automation-tanúsítványobjektum a megadott Automation-fiókban. Ez tartalmazza a forgatókönyvek hitelesítéséhez szükséges tanúsítványt.
* Egy **AzureClassicRunAsConnection** nevű Automation-kapcsolatobjektum a megadott Automation-fiókban. Ez tartalmazza az előfizetés nevét, a subscriptionId paramétert, valamint a tanúsítványobjektum nevét.

A parancsprogram létrehoz egy önaláírt felügyeleti tanúsítványt, majd menti azt a PowerShell-munkamenet végrehajtásához használt felhasználói profilhoz tartozó ideiglenes mappába: *%USERPROFILE%\AppData\Local\Temp*.  A parancsprogram végrehajtását követően fel kell töltenie az Azure felügyeleti tanúsítványt annak az előfizetésnek a felügyeleti tárába, amelyben az Automation-fiókot létrehozták.  Az alábbi lépésekből megtudhatja, hogyan hajtsa végre a parancsprogramot, és hogyan töltse fel a tanúsítványt.  

1. Mentse el a következő parancsprogramot a számítógépén.  Ebben a példában mentse a következő fájlnéven: **New-AzureClassicRunAsAccount.ps1**.
   
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
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose
   
        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues
   
        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"
2. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.  
3. Az emelt szintű PowerShell parancssori rendszerhéjból lépjen a mappába, amely az 1. lépésben létrehozott parancsprogramot tartalmazza, és futtassa azt a *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* és *-CertPlainPassword* paraméterek értékeinek módosításával.<br>
   
   > [!NOTE]
   > A rendszer az Azure rendszerrel történő hitelesítést fogja kérni a parancsprogram futtatása után. Olyan fiókkal kell bejelentkeznie, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, illetve az előfizetés társadminisztrátorai közé tartozik.
   > 
   > 
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

Miután a parancsprogram sikeresen lefutott, szüksége lesz a tanúsítvány másolatára, amely a felhasználói profilhoz tartozó **Temp** mappában jött létre.  Kövesse a lépéseket, amelyek bemutatják, hogy hogyan [tölthet fel egy felügyeleti API-tanúsítványt](../azure-api-management-certs.md) a klasszikus Azure-portálra, majd a [mintakód](#sample-code-to-authenticate-with-service-management-resources) segítségével ellenőrizze a hitelesítő adatok konfigurációját a Service Management-erőforrásokkal.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Mintakód a Resource Manager-erőforrásokkal történő hitelesítéshez
A Resource Manager-erőforrások forgatókönyvekkel való kezeléséhez használhatja az alábbi, az **AzureAutomationTutorialScript** mintaforgatókönyvből származó frissített mintakódot a futtató fiók használatával történő hitelesítéshez.   

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


A parancsprogram tartalmaz két további kódsort az előfizetési környezet hivatkozásának támogatására, így könnyedén tud több előfizetéssel is dolgozni. Egy SubscriptionId nevű változó objektum tartalmazza az előfizetés azonosítóját, és az Add-AzureRmAccount parancsmag nyilatkozata után a [Set-AzureRmContext parancsmag](https://msdn.microsoft.com/library/mt619263.aspx) a *-SubscriptionId* paraméterkészlettel nyilatkozik. Ha a változó neve túl általános, módosíthatja a változó nevét, hogy tartalmazzon egy előtagot vagy igazodjon más elnevezési konvenciókhoz, és könnyebbé tegye az azonosítását. Alternatív megoldásként a -SubscriptionId helyett használhatja a -SubscriptionName paraméterkészletet egy megfelelő változó objektummal.  

Figyelje meg, hogy a forgatókönyvben való hitelesítést biztosító parancsmag, (**Add-AzureRmAccount**) a *ServicePrincipalCertificate* paraméterkészletet használja.  Ez a szolgáltatásnév segítségével, és nem hitelesítő adatokkal végzi el a hitelesítést.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Mintakód a Service Manager-erőforrásokkal történő hitelesítéshez
Ha a klasszikus futtató fiókkal szeretne hitelesítést végezni a klasszikus erőforrások forgatókönyvekkel való felügyeletéhez, használhatja az alábbi, az **AzureClassicAutomationTutorialScript** mintaforgatókönyvből származó mintakódot is.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## <a name="next-steps"></a>Következő lépések
* Az Egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](../active-directory/active-directory-application-objects.md).
* Az Azure Automation szerepköralapú hozzáférés-vezérlési funkciójáról további információkért lásd: [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md).
* A tanúsítványokkal és az Azure-szolgáltatásokkal kapcsolatos részletes információkért lásd: [Certificates overview for Azure Cloud Services](../cloud-services/cloud-services-certs-create.md) (Tanúsítványok áttekintése az Azure Cloud Servicesben).




<!--HONumber=Dec16_HO1-->


