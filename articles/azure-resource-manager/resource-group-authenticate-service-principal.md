---
title: "Azure-alkalmazás identitását létrehozása a PowerShell használatával |} Microsoft Docs"
description: "Ismerteti, hogyan hozzon létre egy Azure Active Directory-alkalmazást és egy egyszerű szolgáltatást, és a szerepköralapú hozzáférés-vezérléssel erőforrásokhoz való hozzáférés engedélyezése az Azure PowerShell használatával. Azt mutatja, hogyan hitelesítheti az alkalmazás a tanúsítvánnyal."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: 175d95c16484b90b13936c3be39b67749f0c3238
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Hozzon létre egy egyszerű szolgáltatást egy tanúsítványt az Azure PowerShell használatával

Ha egy alkalmazás vagy parancsfájlt, amely erőforrások hozzáférésre van szüksége, állítsa be az alkalmazás identitást, és hitelesítsék az alkalmazást a saját hitelesítő adatokkal. Ezzel az identitással egyszerű szolgáltatás néven ismert. Ez a megközelítés lehetővé teszi:

* Engedélyek hozzárendelése saját engedélyek eltérő alkalmazás identitását. Ezek az engedélyek általában korlátozódik, hogy mit az alkalmazás kell tennie.
* A tanúsítvány használata a hitelesítéshez, egy felügyelet nélküli parancsfájl végrehajtása közben.

> [!IMPORTANT]
> Egy egyszerű szolgáltatás létrehozása, helyett érdemes az Azure AD felügyelete a Szolgáltatásidentitást az az alkalmazásidentitás. Az Azure AD MSI az Azure Active Directoryban, amely egyszerűbbé teszi a kód identitás létrehozása a nyilvános előzetes verziójú funkciók. Ha a kódot, amely támogatja az Azure AD MSI és fér hozzá az erőforrásokat, amelyek támogatják az Azure Active Directory hitelesítési szolgáltatás fut, Azure AD MSI beállítás jobban meg. További információt az Azure AD MSI, beleértve a szolgáltatások jelenleg támogatják a forgatókönyvet, tekintse meg a [Szolgáltatásidentitás felügyelt Azure-erőforrások](../active-directory/managed-service-identity/overview.md).

Ez a cikk bemutatja, hogyan hozzon létre egy egyszerű, amely hitelesíti a tanúsítvánnyal. A szolgáltatás egyszerű jelszó beállításához tekintse meg a [hozzon létre egy Azure szolgáltatás egyszerű az Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

## <a name="required-permissions"></a>Szükséges engedélyek

Ez a cikk befejeződik, az Azure Active Directory és az Azure-előfizetés a megfelelő engedélyekkel kell rendelkeznie. Pontosabban kell lennie az Azure Active Directory-alkalmazás létrehozása, és a szolgáltatás egyszerű hozzárendelése egy szerepkörhöz.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [szükséges engedély ellenőrzése](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Egyszerű szolgáltatásnév létrehozása önaláírt tanúsítvánnyal

A következő példa egy olyan egyszerű forgatókönyvet ismertet. Használja [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) szolgáltatásnevet létrehozni az önaláírt tanúsítványt, és használja a [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) hozzárendelni a [közreműködő](../active-directory/role-based-access-built-in-roles.md#contributor)a szolgáltatás egyszerű szerepkört. A szerepkör-hozzárendelés hatókörét az aktuálisan kijelölt Azure-előfizetéshez. Válasszon másik előfizetést, használja a [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

A példa alvó állapotban van, várja meg, hogy az új szolgáltatáshoz Azure Active Directory teljes propagálása egyszerű 20 másodpercig. Ha a parancsfájl nem elég hosszú várja meg, láthatja, egy üzenet szerint: "{} Azonosítójú rendszerbiztonsági tag nem létezik a következő könyvtárban: {DIR-azonosító}." Ez a hiba megoldásához Várjon egy kicsit, majd futtassa a **New-AzureRmRoleAssignment** újra a parancsot.

A következő példában bonyolultabb, mivel lehetővé teszi a szerepkör-hozzárendelés, amely eltér a jelenlegi Azure-előfizetés hatókörének beállításához. Itt adhatja meg, a ResourceGroup paraméter csak korlátozhatja azon szerepkör-hozzárendelés erőforráscsoporthoz. Ha a szerepkör-hozzárendelés során hiba lép fel, a hozzárendelés újrapróbálkozik. Azure PowerShell 2.0 a Windows 10 vagy Windows Server 2016 kell rendelkeznie.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -Subscription $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Ha Ön **nem rendelkeznek Windows 10 vagy Windows Server 2016**, le kell töltenie a [önaláírt tanúsítvány generátor](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) Microsoft Script Center. Bontsa ki a tartalmát, és importálni kell a parancsmagot.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

A parancsfájl helyettesítse be a tanúsítvány előállításához a következő két sorral.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -StoreName My `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Adja meg a tanúsítvány automatikus PowerShell-parancsfájl segítségével

Amikor jelentkezik be, és egy egyszerű szolgáltatást, meg kell adnia annak a könyvtárnak a bérlő azonosítója az AD-alkalmazás. A bérlő az Azure Active Directory példánya.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Az Alkalmazásazonosító és a bérlő azonosítója nem különbözőnek számítanak, így közvetlenül a parancsfájlban beágyazhatók. Ha szeretné beolvasni a bérlő Azonosítóját, használja:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Ha szeretné beolvasni az alkalmazás Azonosítóját, használja:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Egyszerű szolgáltatásnév létrehozása hitelesítésszolgáltatótól származó tanúsítvánnyal

Az alábbi példában egy hitelesítésszolgáltató által kiállított tanúsítványt egyszerű szolgáltatásnév létrehozása. A megadott Azure-előfizetés a hozzárendelés hatókörét. A szolgáltatás egyszerű, hogy hozzáadja a [közreműködő](../active-directory/role-based-access-built-in-roles.md#contributor) szerepkör. Ha a szerepkör-hozzárendelés során hiba lép fel, a hozzárendelés újrapróbálkozik.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Adja meg a tanúsítvány automatikus PowerShell-parancsfájl segítségével
Amikor jelentkezik be, és egy egyszerű szolgáltatást, meg kell adnia annak a könyvtárnak a bérlő azonosítója az AD-alkalmazás. A bérlő az Azure Active Directory példánya.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Az Alkalmazásazonosító és a bérlő azonosítója nem különbözőnek számítanak, így közvetlenül a parancsfájlban beágyazhatók. Ha szeretné beolvasni a bérlő Azonosítóját, használja:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Ha szeretné beolvasni az alkalmazás Azonosítóját, használja:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Hitelesítő adatok módosítása

Az AD-alkalmazás, vagy a biztonsági sérülés vagy a hitelesítő adatok érvényessége miatt a hitelesítő adatok módosításához használja a [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) és [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) parancsmagok.

Az alkalmazás hitelesítő adatok eltávolításához használja:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Tanúsítvány érték hozzáadása, hozzon létre egy önaláírt tanúsítványt, ebben a cikkben ismertetett módon. Ezután használja:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Hibakeresés

Előfordulhat, hogy hibaüzenet a következő egyszerű szolgáltatás létrehozása során:

* **"Authentication_Unauthorized"** vagy **"előfizetést az adott környezetben található."** – Ezt a hibaüzenetet látja, ha a fiók nem rendelkezik a [szükséges engedélyek](#required-permissions) az Azure Active Directory regisztrálnia az alkalmazást. Általában ezt a hibát látva az Azure Active Directoryban csak rendszergazda felhasználók regisztrálhatják az alkalmazásokat, és a fiók nincs a rendszergazda segítségét. Kérje a rendszergazdától, vagy rendeljen Önhöz egy rendszergazdai szerepkört, vagy lehetővé teszi a felhasználók alkalmazásokat regisztrálni.

* A fiók **"nem jogosult a műveletre"Microsoft.Authorization/roleAssignments/write"hatókörben"/Subscriptions/ {guid}"."**  -Ezt a hibaüzenetet látja, ha a fiók nem rendelkezik megfelelő engedélyekkel a szerepkör hozzárendelése identitást. Kérje meg a előfizetési rendszergazda, akkor a felhasználói hozzáférés adminisztrátora szerepkörbe való felvételre.

## <a name="next-steps"></a>További lépések
* A szolgáltatás egyszerű jelszó beállításához tekintse meg a [hozzon létre egy Azure szolgáltatás egyszerű az Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Az alkalmazás integrálása az Azure erőforrások kezeléséhez részletes lépéseiért lásd: [fejlesztői útmutató az Azure Resource Manager API-val engedélyezési](resource-manager-api-authentication.md).
* Egy részletes ismertetése az alkalmazások és szolgáltatásnevekről [alkalmazás és szolgáltatás egyszerű objektumok](../active-directory/active-directory-application-objects.md). 
* Azure Active Directory-hitelesítéssel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure AD](../active-directory/active-directory-authentication-scenarios.md).
