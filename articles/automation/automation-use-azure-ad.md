---
title: Az Azure Automation Azure AD-jének használata az Azure-ban való hitelesítéshez
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Automationben az Azure-hitelesítés szolgáltatójaként.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 77476c67761a950430b39d5baddf2c6efd77f1a2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479447"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Az Azure Automation Azure AD-jének használata az Azure-ban való hitelesítéshez

Az [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) szolgáltatás számos felügyeleti feladatot, például a felhasználókezelés, a tartománykezelés és az egyszeri bejelentkezési konfiguráció t tesz lehetővé. Ez a cikk ismerteti, hogyan használhatja az Azure AD-ben az Azure Automation, mint a szolgáltató az Azure-hitelesítéshez. 

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="installing-azure-ad-modules"></a>Az Azure AD-modulok telepítése

Az Azure AD-t a következő PowerShell-modulokon keresztül engedélyezheti:

* Azure Active Directory PowerShell graph (AzureRM és Az modulok). Az Azure Automation az AzureRM-modullal és annak legutóbbi frissítésével, az Az modullal szállít. A funkciók közé tartozik a nem interaktív hitelesítés az Azure-ba az Azure AD-felhasználó (OrgId) hitelesítő adatokon alapuló hitelesítés használatával. Lásd: [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory for Windows PowerShell (MSOnline modul). Ez a modul lehetővé teszi a Microsoft Online-nal, köztük az Office 365-tel való interakciót.

>[!NOTE]
>A PowerShell Core nem támogatja az MSOnline modult. A modulparancsmagok használatához futtatnia kell őket a Windows PowerShellből. Javasoljuk, hogy az újabb Azure Active Directory PowerShell graph modulok helyett az MSOnline modul használatát használja. 

### <a name="preinstallation"></a>Előtelepítési

Az Azure AD-modulok telepítése előtt a számítógépre:

* Távolítsa el az AzureRM/Az modul és az MSOnline modul korábbi verzióit. 

* Távolítsa el a Microsoft Online Services bejelentkezési segédjét az új PowerShell-modulok megfelelő működésének biztosítása érdekében.  

### <a name="install-the-azurerm-and-az-modules"></a>Az AzureRM- és Az-modulok telepítése

>[!NOTE]
>Ezekhez a modulokhoz a PowerShell 5.1-es vagy újabb verzióját kell használnia a Windows 64 bites verziójával. 

1. Telepítse a Windows Management Framework (WMF) 5.1-es telepítését. Lásd: [A WMF 5.1 telepítése és konfigurálása.](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)

2. Telepítse az AzureRM-t és/vagy az Az-t az [Azure PowerShell telepítése Windowsra a PowerShellGet szolgáltatással](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)című témakör utasításainak megfelelően.

### <a name="install-the-msonline-module"></a>Az MSOnline modul telepítése

>[!NOTE]
>Az MSOnline modul telepítéséhez egy Office 365 felügyeleti szerepkör tagjának kell lennie. Lásd: [Rendszergazdai szerepkörök – betekintés.](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)

1. Győződjön meg arról, hogy a Microsoft . Valószínű, hogy a számítógépen telepítve van egy újabb verzió, de a .NET framework régebbi verzióival való visszamenőleges kompatibilitás engedélyezhető vagy letiltható. 

2. Telepítse a [Microsoft Online Services bejelentkezési segéd](https://www.microsoft.com/download/details.aspx?id=41950)64 bites verzióját.

3. Futtassa a Windows PowerShellt rendszergazdaként emelt szintű Windows PowerShell parancssor létrehozásához.

4. Az Azure Active Directory telepítése az [MSOnline 1.0-s rendszerből.](http://www.powershellgallery.com/packages/MSOnline/1.0)

5. Ha a rendszer a NuGet szolgáltató telepítését kéri, írja be az Y értéket, és nyomja le az ENTER billentyűt.

6. Ha a rendszer kéri a modul [psgallery-ből](https://www.powershellgallery.com/)történő telepítését, írja be az Y értéket, és nyomja le az ENTER billentyűt.

### <a name="install-support-for-pscredential"></a>A PSCredential támogatásának telepítése

Az Azure Automation a [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) osztály használatával képviseli a hitelesítő adatokat. A parancsfájlok `PSCredential` a `Get-AutomationPSCredential` parancsmag használatával olvassa le az objektumokat. További információ: [Credential assets in Azure Automation.](shared-resources/credentials.md)

## <a name="assigning-a-subscription-administrator"></a>Előfizetés-rendszergazda hozzárendelése

Az Azure-előfizetéshez rendszergazda szükséges. Ez a személy az előfizetés hatókörének tulajdonosi szerepkörrel rendelkezik. Lásd: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben.](automation-role-based-access-control.md) 

## <a name="changing-the-azure-ad-users-password"></a>Az Azure AD-felhasználó jelszavának módosítása

Az Azure AD-felhasználó jelszavának módosítása:

1. Jelentkezzen ki az Azure-ból.

2. A rendszergazda jelentkezzen be az Azure-felhasználó az Azure AD-felhasználó csak létre, a teljes felhasználónév (beleértve a tartományt) és egy ideiglenes jelszót. 

3. Kérje meg a rendszergazdát, hogy amikor a rendszer kéri, változtassa meg a jelszót.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Az Azure Automation konfigurálása az Azure AD-felhasználó használatára az Azure-előfizetés kezeléséhez

Ahhoz, hogy az Azure Automation kommunikáljon az Azure AD-vel, le kell kérnie az Azure-kapcsolathoz tartozó hitelesítő adatokat az Azure AD-vel. Ilyen hitelesítő adatok például a bérlőazonosító, az előfizetés-azonosító és hasonlók. Az Azure és az Azure AD közötti kapcsolatról a [Szervezet csatlakoztatása az Azure Active Directoryhoz](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)című témakörben tud többet.

## <a name="creating-a-credential-asset"></a>Hitelesítő eszköz létrehozása

Az Azure AD-hez elérhető Azure-hitelesítő adatokkal itt az ideje, hogy hozzon létre egy Azure Automation-hitelesítő adatokat az Azure AD-hitelesítő adatok biztonságos tárolásához, hogy a runbookok és a Desire State Configuration (DSC) parancsfájlok elérhessék őket. Ezt az Azure Portalon vagy a PowerShell-parancsmagok használatával teheti meg.

### <a name="create-the-credential-asset-in-azure-portal"></a>A hitelesítő adatok létrehozása az Azure Portalon

Az Azure Portal használatával létrehozhatja a hitelesítő adatokat. Ezt a műveletet az Automation-fiókból végezze el a Megosztott erőforrások **csoport hitelesítő adatai** **nak**használatával. Lásd: [Hitelesítő adatok az Azure Automationben.](shared-resources/credentials.md)

### <a name="create-the-credential-asset-with-windows-powershell"></a>A hitelesítő adatok létrehozása a Windows PowerShell használatával

Új hitelesítő adatok előkészítéséhez a Windows PowerShellben `PSCredential` a parancsfájl először létrehoz egy objektumot a hozzárendelt felhasználónév és jelszó használatával. A parancsfájl ezután ezt az objektumot használja az eszköz létrehozásához a [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) parancsmag hívásával. Másik lehetőségként a parancsfájl meghívhatja a [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) parancsmabot, hogy kérje a felhasználót, hogy írja be a nevet és a jelszót. Lásd: [Hitelesítő adatok az Azure Automationben.](shared-resources/credentials.md) 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Azure-erőforrások kezelése azure-automatizálási runbookból

Az Azure Automation-runbookok ból azure-erőforrásokat a hitelesítő adatok használatával kezelheti. Az alábbiakban egy példa PowerShell runbook, amely összegyűjti a hitelesítő adatokat használni a virtuális gépek leállítása és indítása egy Azure-előfizetésben. Ez a runbook először használja `Get-AutomationPSCredential` a hitelesítő adatok lekéréséhez az Azure-ban való hitelesítéshez. Ezután meghívja a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) parancsmag az Azure-hoz való csatlakozáshoz a hitelesítő adatok használatával. A parancsfájl a [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) parancsmag segítségével válassza ki az előfizetést, amivel dolgozni szeretne. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>További lépések

* Az Automation hitelesítő adatokkal kapcsolatos eszközökkel kapcsolatos információkat az [Azure Automation hitelesítő adatokban](shared-resources/credentials.md)találja.
* Az [Azure Automation moduljainak kezelése az Automation-modulok](shared-resources/modules.md) kezeléséről.
* Ha többet szeretne megtudni a runbook okádékának az Azure Automationben történő indításához használható módszerekről, [olvassa el a Runbook indítása az Azure Automationben című témakört.](automation-starting-a-runbook.md)
* A PowerShellről, beleértve a nyelvi referencia- és tanulási modulokat, a [PowerShell-dokumentumokban](https://docs.microsoft.com/powershell/scripting/overview)talál további információt.