---
title: Identitás létrehozása Azure-alkalmazásokhoz a PowerShell-lel | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre az Azure PowerShell segítségével Azure Active Directory-alkalmazást és -szolgáltatásnevet, és miként adhat annak hozzáférést az erőforrásokhoz szerepköralapú hozzáférés-vezérléssel. Bemutatja az alkalmazás tanúsítvánnyal való hitelesítését.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: aec79655dde48a18891776f40c51aa5a5c859ef8
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577264"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Tanúsítvánnyal rendelkező szolgáltatásnév létrehozása az Azure PowerShell használatával

Ha olyan alkalmazása van, amelynek erőforrásokhoz kell hozzáférnie, létrehozhat egy identitást az alkalmazásnak, és hitelesítheti az alkalmazást annak saját hitelesítő adataival. Ezt az identitást szolgáltatásnévnek nevezzük. Ez a megközelítés lehetővé teszi az alábbiakat:

* A saját engedélyeitől eltérő engedélyek hozzárendelése az alkalmazásidentitáshoz. Ezek az engedélyek jellemzően csak azt engedélyezik, amire az alkalmazásnak szüksége van.
* Tanúsítvány használata hitelesítéshez szkriptek felügyelet nélküli futtatásakor.

> [!IMPORTANT]
> Szolgáltatásnév létrehozása helyett fontolja meg, hogy Azure AD Managed Service Identity-t használ az alkalmazásidentitásához. Az Azure AD MSI az Azure Active Directory egy nyilvános előzetes verziójú funkciója, mely leegyszerűsíti az identitások kód számára való létrehozását. Ha a kódja egy Azure AD MSI-t támogató szolgáltatásban fut, és Azure Active Directory-hitelesítést támogató erőforrásokhoz fér hozz, akkor az Azure AD MSI jobb megoldás Önnek. Ha szeretne többet megtudni az Azure AD MSI-ről, például hogy mely szolgáltatások támogatják jelenleg, olvassa el a [Managed Service Identity az Azure-erőforrásokhoz](../active-directory/managed-service-identity/overview.md) című cikket.

Ez a cikk bemutatja, hogy hogyan hozhat létre egy szolgáltatásnevet tanúsítvánnyal történő hitelesítéshez. A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).

A cikk lépéseinek követéséhez a PowerShell [legújabb verzióját](/powershell/azure/get-started-azureps) kell használnia.

## <a name="required-permissions"></a>Szükséges engedélyek

A cikk lépéseinek végrehajtásához megfelelő engedélyekkel kell rendelkeznie az Azure Active Directoryhoz és az Azure-előfizetéshez. Konkrétan létre kell tudnia hozni alkalmazást az Active Directoryban, és ki kell tudnia osztani szerepköröket a szolgáltatásnévnek.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [Szükséges engedélyek ellenőrzése](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal

Az alábbi példa egy egyszerű forgatókönyvet követ. A [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) paranccsal létrehoz egy önaláírt tanúsítványt használó szolgáltatásnevet, majd a [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) paranccsal hozzárendeli a [Közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkört a szolgáltatásnévhez. A szerepkör-hozzárendelés hatóköre az aktuális Azure-előfizetés. Másik előfizetés választásához használja a [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) parancsot.

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

A példa tartalmaz egy 20 másodperces várakozást, mely időt ad az új szolgáltatásnévnek arra, hogy propagálódjon a teljes Azure Active Directoryban. Ha a szkriptje nem vár eleget, akkor a következő hibát fogja látni: „A(z) {ID} résztvevő nem létezik a(z) {DIR-ID} címtárban”. A hiba megoldásához várjon egy percet, és futtassa ismét az **New-AzureRmRoleAssignment** parancsot.

A szerepkör-hozzárendelés hatókörét beállíthatja egy adott erőforráscsoportra a **ResourceGroupName** paraméter használatával. Egy adott erőforrásra is beállíthatja a hatókört a **ResourceType** és a **ResourceName** paraméter együttes használatával. 

Ha **nem rendelkezik Windows 10 vagy Windows Server 2016 rendszerrel**, le kell töltenie az [önaláírt tanúsítványok generátorát](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) a Microsoft Script Center webhelyről. Bontsa ki a fájl tartalmát, és importálja a szükséges parancsmagot.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

A szkriptben helyettesítse be az alábbi két sort a tanúsítvány generálásához.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Tanúsítvány biztosítása automatizált PowerShell-szkripttel

A szolgáltatásnévvel való bejelentkezéskor mindig meg kell adnia az AD-alkalmazás címtárának bérlőazonosítóját. A bérlő az Azure Active Directory egy példánya.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Szolgáltatásnév létrehozása hitelesítésszolgáltatótól származó tanúsítvánnyal

Az alábbi példa egy hitelesítésszolgáltatótól származó tanúsítvánnyal hoz létre egy szolgáltatásnevet. A hozzárendelés hatóköre a megadott Azure-előfizetés. A szolgáltatásnevet a [Közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz társítja. Ha hiba történik a szerepkör-hozzárendelés során, a szkript megismétli a hozzárendelést.

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

 Connect-AzureRmAccount
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

### <a name="provide-certificate-through-automated-powershell-script"></a>Tanúsítvány biztosítása automatizált PowerShell-szkripttel
A szolgáltatásnévvel való bejelentkezéskor mindig meg kell adnia az AD-alkalmazás címtárának bérlőazonosítóját. A bérlő az Azure Active Directory egy példánya.

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

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Az alkalmazásazonosító és a bérlőazonosító nem bizalmas adat, ezért beágyazhatja közvetlenül a szkriptjébe. A bérlőazonosító megállapításához használja a következőt:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Az alkalmazásazonosító megállapításához használja a következőt:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Hitelesítő adatok módosítása

Az AD-alkalmazás hitelesítő adatainak módosításához (akár biztonsági probléma, akár lejárat miatt) használja a [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) és a [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) parancsmagot.

Ha egy alkalmazás összes hitelesítő adatát szeretné eltávolítani, használja a következőt:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Ha tanúsítványértéket kíván hozzáadni, hozzon létre egy önaláírt tanúsítványt a cikkben ismertetett módon. Ezután használja a következőt:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Hibakeresés

A szolgáltatásnév létrehozásakor az alábbi hibákba ütközhet:

* **„Authentication_Unauthorized”** vagy **„No subscription found in the context.”** (Nem található előfizetés a környezetben.) – Ez a hiba akkor jelenik meg, ha a fiókja nem rendelkezik a [szükséges engedélyekkel](#required-permissions) az Azure Active Directoryban alkalmazások regisztrálásához. Jellemzően akkor fordul elő, ha az Azure Active Directory-példányában csak rendszergazdák regisztrálhatnak alkalmazásokat, és az Ön fiókja nem rendszergazdai fiók. Kérje meg a rendszergazdáját, hogy rendeljen rendszergazdai szerepkört Önhöz, vagy hogy engedélyezze az alkalmazások regisztrálását a felhasználóknak.

* A fiókja **„nem jogosult elvégezni a(z) Microsoft.Authorization/roleAssignments/write műveletet a(z) /subscriptions/{guid} hatókörben”** – Ez a hiba akkor jelenik meg, ha a fiókja nem rendelkezik megfelelő engedélyekkel a szerepkörök identitásokhoz való hozzárendeléséhez. Kérje meg az előfizetés-rendszergazdáját, hogy adja hozzá Önt a Felhasználói hozzáférés rendszergazdája szerepkörhöz.

## <a name="next-steps"></a>További lépések
* A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).
* Az erőforrásokat kezelő alkalmazások Azure-ba való integrálásáról részletes útmutatást az [Azure Resource Manager API-val végzett engedélyezés fejlesztői útmutatójában](resource-manager-api-authentication.md) találhat.
* Az alkalmazásokról és a szolgáltatásnevekről bővebben az [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](../active-directory/develop/app-objects-and-service-principals.md) című cikkben olvashat. 
* Az Azure Active Directory-hitelesítésről további információt a [Hitelesítési forgatókönyvek az Azure AD-hez](../active-directory/develop/authentication-scenarios.md) című cikkben találhat.
