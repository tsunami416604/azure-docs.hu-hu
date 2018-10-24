---
title: Identitás létrehozása Azure-alkalmazásokhoz a PowerShell-lel | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre az Azure PowerShell segítségével Azure Active Directory-alkalmazást és -szolgáltatásnevet, és miként adhat annak hozzáférést az erőforrásokhoz szerepköralapú hozzáférés-vezérléssel. Bemutatja az alkalmazás tanúsítvánnyal való hitelesítését.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: tomfitz
ms.openlocfilehash: e00dcd90db4d7d7d67273da4840db6a784a5d86c
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960391"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Útmutató: egyszerű szolgáltatás létrehozása a tanúsítvány az Azure PowerShell használatával

Ha olyan alkalmazása van, amelynek erőforrásokhoz kell hozzáférnie, létrehozhat egy identitást az alkalmazásnak, és hitelesítheti az alkalmazást annak saját hitelesítő adataival. Ezt az identitást szolgáltatásnévnek nevezzük. Ez a megközelítés lehetővé teszi az alábbiakat:

* A saját engedélyeitől eltérő engedélyek hozzárendelése az alkalmazásidentitáshoz. Ezek az engedélyek jellemzően csak azt engedélyezik, amire az alkalmazásnak szüksége van.
* Tanúsítvány használata hitelesítéshez szkriptek felügyelet nélküli futtatásakor.

> [!IMPORTANT]
> Egyszerű szolgáltatás létrehozása helyett fontolja meg a felügyelt identitások használatával az Azure-erőforrások esetében az alkalmazás azonosítóját. A kódot, amely támogatja a felügyelt identitások és hozzáférések erőforrások, amelyek támogatják az Azure Active Directory (Azure AD) hitelesítési szolgáltatás fut, ha a felügyelt identitásokból, jobb megoldás. További információ az Azure-erőforrásokhoz, mely szolgáltatások jelenleg támogatja, beleértve a felügyelt identitásokból [Mi az Azure-erőforrások felügyelt identitások?](../managed-identities-azure-resources/overview.md).

Ez a cikk bemutatja, hogy hogyan hozhat létre egy szolgáltatásnevet tanúsítvánnyal történő hitelesítéshez. A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).

A cikk lépéseinek követéséhez a PowerShell [legújabb verzióját](/powershell/azure/get-started-azureps) kell használnia.

## <a name="required-permissions"></a>Szükséges engedélyek

Ez a cikk végrehajtásához az Azure ad megfelelő engedélyekkel kell rendelkeznie, és Azure-előfizetést. Pontosabban kell lennie az Azure AD-ben hozzon létre egy alkalmazást, és az egyszerű szolgáltatás hozzárendelése szerepkörhöz.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [Szükséges engedélyek ellenőrzése](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal

Az alábbi példa egy egyszerű forgatókönyvet követ. A [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) paranccsal létrehoz egy önaláírt tanúsítványt használó szolgáltatásnevet, majd a [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) paranccsal hozzárendeli a [Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkört a szolgáltatásnévhez. A szerepkör-hozzárendelés hatóköre az aktuális Azure-előfizetés. Másik előfizetés választásához használja a [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) parancsot.

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

A példában alvó állapotban van, várja meg, hogy az új szolgáltatás egyszerű, az Azure AD egészében propagálása 20 másodpercet. Ha a szkriptje nem vár eleget, akkor a következő hibát fogja látni: „A(z) {ID} résztvevő nem létezik a(z) {DIR-ID} címtárban”. A hiba megoldásához várjon egy percet, és futtassa ismét az **New-AzureRmRoleAssignment** parancsot.

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

A szolgáltatásnévvel való bejelentkezéskor mindig meg kell adnia az AD-alkalmazás címtárának bérlőazonosítóját. A bérlő az Azure AD-példányba.

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

Az alábbi példa egy hitelesítésszolgáltatótól származó tanúsítvánnyal hoz létre egy szolgáltatásnevet. A hozzárendelés hatóköre a megadott Azure-előfizetés. A szolgáltatásnevet a [Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz társítja. Ha hiba történik a szerepkör-hozzárendelés során, a szkript megismétli a hozzárendelést.

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
A szolgáltatásnévvel való bejelentkezéskor mindig meg kell adnia az AD-alkalmazás címtárának bérlőazonosítóját. A bérlő az Azure AD-példányba.

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

Az AD-alkalmazás hitelesítő adatainak módosításához (akár biztonsági probléma, akár lejárat miatt) használja a [Remove-AzureRmADAppCredential](/powershell/module/azurerm.resources/remove-azurermadappcredential) és a [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) parancsmagot.

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

* **„Authentication_Unauthorized”** vagy **„No subscription found in the context.”** (Nem található előfizetés a környezetben.) – Ezt a hibaüzenetet, ha a fiókja nem rendelkezik a [szükséges engedélyek](#required-permissions) az alkalmazás regisztrálása az Azure ad-ben. Általában ezt a hibaüzenetet, ha az Azure Active Directoryban csak rendszergazda felhasználók regisztrálhatnak alkalmazásokat, és a fiók nem rendszergazda. Kérje meg a rendszergazdáját, hogy rendeljen rendszergazdai szerepkört Önhöz, vagy hogy engedélyezze az alkalmazások regisztrálását a felhasználóknak.

* A fiók **"Nincs engedélyezés műveletre 'Microsoft.Authorization/roleAssignments/write' over scope"/ subscriptions / {guid}"."**  – Ezt a hibát látja, ha a fiókja nem rendelkezik megfelelő engedélyekkel szerepkörök hozzárendeléséhez, az identitás. Kérje meg az előfizetés-rendszergazdáját, hogy adja hozzá Önt a Felhasználói hozzáférés rendszergazdája szerepkörhöz.

## <a name="next-steps"></a>További lépések

* A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).
* Az erőforrásokat kezelő alkalmazások Azure-ba való integrálásáról részletes útmutatást az [Azure Resource Manager API-val végzett engedélyezés fejlesztői útmutatójában](../../azure-resource-manager/resource-manager-api-authentication.md) találhat.
* Az alkalmazásokról és a szolgáltatásnevekről bővebben az [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](app-objects-and-service-principals.md) című cikkben olvashat.
* Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md).
