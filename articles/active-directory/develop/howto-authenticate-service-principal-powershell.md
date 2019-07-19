---
title: Identitás létrehozása Azure-alkalmazásokhoz a PowerShell-lel | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre az Azure PowerShell segítségével Azure Active Directory-alkalmazást és -szolgáltatásnevet, és miként adhat annak hozzáférést az erőforrásokhoz szerepköralapú hozzáférés-vezérléssel. Bemutatja az alkalmazás tanúsítvánnyal való hitelesítését.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73033f91e9d20c56fedc6b4faf26dcf312fce1e1
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321113"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Útmutató: Tanúsítvánnyal rendelkező szolgáltatásnév létrehozása az Azure PowerShell használatával

Ha olyan alkalmazása van, amelynek erőforrásokhoz kell hozzáférnie, létrehozhat egy identitást az alkalmazásnak, és hitelesítheti az alkalmazást annak saját hitelesítő adataival. Ezt az identitást szolgáltatásnévnek nevezzük. Ez a megközelítés lehetővé teszi az alábbiakat:

* A saját engedélyeitől eltérő engedélyek hozzárendelése az alkalmazásidentitáshoz. Ezek az engedélyek jellemzően csak azt engedélyezik, amire az alkalmazásnak szüksége van.
* Tanúsítvány használata hitelesítéshez szkriptek felügyelet nélküli futtatásakor.

> [!IMPORTANT]
> Egyszerű szolgáltatásnév létrehozása helyett érdemes felügyelt identitásokat használni az Azure-erőforrásokhoz az alkalmazás identitásához. Ha a kód olyan szolgáltatáson fut, amely támogatja a felügyelt identitásokat, és a Azure Active Directory (Azure AD) hitelesítést támogató erőforrásokhoz fér hozzá, a felügyelt identitások jobb megoldást biztosítanak Önnek. Ha többet szeretne megtudni az Azure-erőforrások felügyelt identitásáról, beleértve a jelenleg támogatott szolgáltatásokat, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)című témakört.

Ez a cikk bemutatja, hogy hogyan hozhat létre egy szolgáltatásnevet tanúsítvánnyal történő hitelesítéshez. A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).

A cikk lépéseinek követéséhez a PowerShell [legújabb verzióját](/powershell/azure/install-az-ps) kell használnia.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Szükséges engedélyek

A cikk elvégzéséhez megfelelő engedélyekkel kell rendelkeznie az Azure AD-ben és az Azure-előfizetésben. Pontosabban létre kell hoznia egy alkalmazást az Azure AD-ben, és hozzá kell rendelnie a szolgáltatásnevet egy szerepkörhöz.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [Szükséges engedélyek ellenőrzése](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal

Az alábbi példa egy egyszerű forgatókönyvet követ. A [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) használatával létrehoz egy önaláírt tanúsítvánnyal rendelkező szolgáltatásnevet, és a [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) használatával rendeli hozzá a [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkört az egyszerű szolgáltatáshoz. A szerepkör-hozzárendelés hatóköre az aktuális Azure-előfizetés. Másik előfizetés kiválasztásához használja a [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

A példa 20 másodpercig alvó állapotba lép, hogy az új szolgáltatásnév az Azure AD-ben való propagálása némi időt hagyjon. Ha a parancsfájl nem vár elég sokáig, a következő hibaüzenet jelenik meg: "A (z) {ID} résztvevő nem létezik a (z) {DIR-ID} címtárban." A hiba megoldásához várjon egy percet, majd futtassa újra a **New-AzRoleAssignment** parancsot.

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

A szolgáltatásnévvel való bejelentkezéskor mindig meg kell adnia az AD-alkalmazás címtárának bérlőazonosítóját. A bérlő az Azure AD egy példánya.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
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

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Tanúsítvány biztosítása automatizált PowerShell-szkripttel
A szolgáltatásnévvel való bejelentkezéskor mindig meg kell adnia az AD-alkalmazás címtárának bérlőazonosítóját. A bérlő az Azure AD egy példánya.

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

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Az alkalmazásazonosító és a bérlőazonosító nem bizalmas adat, ezért beágyazhatja közvetlenül a szkriptjébe. A bérlőazonosító megállapításához használja a következőt:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Az alkalmazásazonosító megállapításához használja a következőt:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Hitelesítő adatok módosítása

Ha módosítani szeretné egy AD-alkalmazás hitelesítő adatait, vagy a biztonsági sérülés vagy a hitelesítő adatok lejárata miatt, használja a [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) és a [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) parancsmagokat.

Ha egy alkalmazás összes hitelesítő adatát szeretné eltávolítani, használja a következőt:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Ha tanúsítványértéket kíván hozzáadni, hozzon létre egy önaláírt tanúsítványt a cikkben ismertetett módon. Ezután használja a következőt:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Hibakeresés

A szolgáltatásnév létrehozásakor az alábbi hibákba ütközhet:

* **„Authentication_Unauthorized”** vagy **„No subscription found in the context.”** (Nem található előfizetés a környezetben.) – Ez a hiba akkor jelenik meg, ha a fiókja nem rendelkezik a [szükséges engedélyekkel](#required-permissions) az Azure ad-ben az alkalmazás regisztrálásához. Ez a hiba általában akkor jelenik meg, ha csak a Azure Active Directory rendszergazdai felhasználói regisztrálhatnak alkalmazásokat, és a fiókja nem rendszergazda. Kérje meg a rendszergazdáját, hogy rendeljen rendszergazdai szerepkört Önhöz, vagy hogy engedélyezze az alkalmazások regisztrálását a felhasználóknak.

* A (z) "nincs engedélye a (z **)" Microsoft. Authorization/roleAssignments/Write "művelet végrehajtására a (z)"/Subscriptions/{GUID} "hatókörben."** – ezt a hibát akkor látja, ha a fiók nem rendelkezik megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen hozzá identitás. Kérje meg az előfizetés-rendszergazdáját, hogy adja hozzá Önt a Felhasználói hozzáférés rendszergazdája szerepkörhöz.

## <a name="next-steps"></a>További lépések

* A jelszót használó szolgáltatásnevek beállításáról a következő témakörben tájékozódhat: [Azure-beli szolgáltatásnév létrehozása az Azure PowerShell használatával](/powershell/azure/create-azure-service-principal-azureps).
* Az erőforrásokat kezelő alkalmazások Azure-ba való integrálásáról részletes útmutatást az [Azure Resource Manager API-val végzett engedélyezés fejlesztői útmutatójában](../../azure-resource-manager/resource-manager-api-authentication.md) találhat.
* Az alkalmazásokról és a szolgáltatásnevekről bővebben az [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](app-objects-and-service-principals.md) című cikkben olvashat.
* Az Azure AD-hitelesítéssel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-hez](authentication-scenarios.md).
