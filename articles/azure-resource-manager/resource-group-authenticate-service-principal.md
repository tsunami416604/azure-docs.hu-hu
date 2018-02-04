---
title: "Azure-alkalmazás identitását létrehozása a PowerShell használatával |} Microsoft Docs"
description: "Ismerteti, hogyan hozzon létre egy Azure Active Directory-alkalmazást és egy egyszerű szolgáltatást, és a szerepköralapú hozzáférés-vezérléssel erőforrásokhoz való hozzáférés engedélyezése az Azure PowerShell használatával. Azt mutatja, hogyan alkalmazás jelszóval vagy tanúsítvánnyal hitelesítheti."
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
ms.date: 12/28/2017
ms.author: tomfitz
ms.openlocfilehash: 8262162ce73176426057af4654f12614cac85472
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Szolgáltatásnév létrehozása erőforrások eléréséhez az Azure PowerShell használatával

Ha egy alkalmazás vagy parancsfájlt, amely erőforrások hozzáférésre van szüksége, állítsa be az alkalmazás identitást, és hitelesítsék az alkalmazást a saját hitelesítő adatokkal. Ezzel az identitással egyszerű szolgáltatás néven ismert. Ez a megközelítés lehetővé teszi:

* Engedélyek hozzárendelése saját engedélyek eltérő alkalmazás identitását. Ezek az engedélyek általában korlátozódik, hogy mit az alkalmazás kell tennie.
* A tanúsítvány használata a hitelesítéshez, egy felügyelet nélküli parancsfájl végrehajtása közben.

Ez a cikk bemutatja, hogyan használható [Azure PowerShell](/powershell/azure/overview) mindent, amire szüksége, az alkalmazás futtatásához a saját hitelesítő adatait és identitás beállítása.

## <a name="required-permissions"></a>Szükséges engedélyek
Ez a cikk befejezéséhez megfelelő engedélyekkel kell rendelkeznie az Azure Active Directory és az Azure-előfizetésében is. Pontosabban kell lennie az Azure Active Directory-alkalmazás létrehozása, és a szolgáltatás egyszerű hozzárendelése egy szerepkörhöz. 

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [szükséges engedély ellenőrzése](resource-group-create-service-principal-portal.md#required-permissions).

Most folytassa a szakasz a hitelesítéséhez:

* [jelszó](#create-service-principal-with-password)
* [önaláírt tanúsítvány](#create-service-principal-with-self-signed-certificate)
* [a hitelesítésszolgáltató tanúsítványa](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>PowerShell-parancsok

Egy egyszerű beállításához használhatja:

| Parancs | Leírás |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Létrehoz egy Azure Active Directory szolgáltatás egyszerű |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | A megadott RBAC szerepkört rendel hozzá a megadott rendszerbiztonsági tag, a megadott hatókörben. |


## <a name="create-service-principal-with-password"></a>Egyszerű szolgáltatásnév létrehozása jelszóval

Hozzon létre egy egyszerű szolgáltatást a közreműködő szerepkört az előfizetés, használja: 

```powershell
Login-AzureRmAccount
$password = convertto-securestring {provide-password} -asplaintext -force
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password $password
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

A példa alvó állapotban van, várja meg, hogy az új szolgáltatáshoz Azure Active Directory teljes propagálása egyszerű 20 másodpercig. Ha a parancsfájl nem elegendő ideig kell várni, egy üzenet szerint láthatja: "{} Azonosítójú rendszerbiztonsági tag nem létezik a következő könyvtárban: {DIR-azonosító}."

A következő parancsfájl lehetővé teszi az alapértelmezett előfizetés nem határoz meg, és a szerepkör-hozzárendelés újrapróbálkozik, ha a hiba akkor fordul elő:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $SecurePassword = convertto-securestring $Password -asplaintext -force
 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $SecurePassword
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

Néhány elemet a parancsfájllal kapcsolatos figyelembe venni:

* Az identitás hozzáférést biztosít az alapértelmezett előfizetés, nem kell megadnia a ResourceGroup vagy a SubscriptionId paraméterek.
* Itt adhatja meg, a ResourceGroup paraméter csak korlátozhatja azon szerepkör-hozzárendelés erőforráscsoporthoz.
*  Ebben a példában hozzáadja a közreműködő szerepkört az egyszerű szolgáltatás. Más szerepköreivel kapcsolatban, tekintse meg a [RBAC: beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md).
* A parancsfájl alvó állapotban van, várja meg, hogy az új szolgáltatáshoz Azure Active Directory teljes propagálása egyszerű 15 másodpercig. Ha a parancsfájl nem elegendő ideig kell várni, egy üzenet szerint láthatja: "{} Azonosítójú rendszerbiztonsági tag nem létezik a következő könyvtárban: {DIR-azonosító}."
* Ha a szolgáltatás egyszerű hozzáférést biztosít a további előfizetések vagy erőforráscsoportok kell futtatni a `New-AzureRMRoleAssignment` parancsmagot újra különböző hatóköröket.


### <a name="provide-credentials-through-powershell"></a>Adjon meg hitelesítő adatokat a PowerShell segítségével
Most kell jelentkezzen be az alkalmazás műveletek végrehajtásához. A felhasználó nevét, használja a `ApplicationId` , amelyet az alkalmazás hozott létre. A jelszót a fiók létrehozásakor megadott használja. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

A bérlő azonosítója nincs különbözőnek számítanak, így közvetlenül a parancsfájlban beágyazása. Ha szeretné beolvasni a bérlő Azonosítóját, használja:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Egyszerű szolgáltatásnév létrehozása önaláírt tanúsítvánnyal

Egy szolgáltatásnevet létrehozni az önaláírt tanúsítványt és a közreműködő szerepkört az előfizetés, használja: 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

A példa alvó állapotban van, várja meg, hogy az új szolgáltatáshoz Azure Active Directory teljes propagálása egyszerű 20 másodpercig. Ha a parancsfájl nem elegendő ideig kell várni, egy üzenet szerint láthatja: "{} Azonosítójú rendszerbiztonsági tag nem létezik a következő könyvtárban: {DIR-azonosító}."

A következő parancsfájl lehetővé teszi az alapértelmezett előfizetés nem határoz meg, és a szerepkör-hozzárendelés újrapróbálkozik, ha a hiba akkor fordul elő. Azure PowerShell 2.0 a Windows 10 vagy Windows Server 2016 kell rendelkeznie.

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
    Set-AzureRmContext -SubscriptionId $SubscriptionId
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

Néhány elemet a parancsfájllal kapcsolatos figyelembe venni:

* Az identitás hozzáférést biztosít az alapértelmezett előfizetés, nem kell megadnia a ResourceGroup vagy a SubscriptionId paraméterek.
* Itt adhatja meg, a ResourceGroup paraméter csak korlátozhatja azon szerepkör-hozzárendelés erőforráscsoporthoz.
* Ebben a példában hozzáadja a közreműködő szerepkört az egyszerű szolgáltatás. Más szerepköreivel kapcsolatban, tekintse meg a [RBAC: beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md).
* A parancsfájl alvó állapotban van, várja meg, hogy az új szolgáltatáshoz Azure Active Directory teljes propagálása egyszerű 15 másodpercig. Ha a parancsfájl nem elegendő ideig kell várni, egy üzenet szerint láthatja: "{} Azonosítójú rendszerbiztonsági tag nem létezik a következő könyvtárban: {DIR-azonosító}."
* Ha a szolgáltatás egyszerű hozzáférést biztosít a további előfizetések vagy erőforráscsoportok kell futtatni a `New-AzureRMRoleAssignment` parancsmagot újra különböző hatóköröket.

Ha Ön **nem rendelkeznek Windows 10 vagy Windows Server 2016 Technical Preview**, le kell töltenie a [önaláírt tanúsítvány generátor](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) Microsoft Script Center. Bontsa ki a tartalmát, és importálni kell a parancsmagot.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
A parancsfájl helyettesítse be a tanúsítvány előállításához a következő két sorral.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Adja meg a tanúsítvány automatikus PowerShell-parancsfájl segítségével
Amikor jelentkezik be, és egy egyszerű szolgáltatást, meg kell adnia annak a könyvtárnak a bérlő azonosítója az AD-alkalmazás. A bérlő az Azure Active Directory példánya. Ha több előfizetéssel rendelkezik, akkor használhatja:

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
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Az alkalmazás azonosítója és a bérlő azonosítója nem különbség, így közvetlenül a parancsfájlban beágyazhatók. Ha szeretné beolvasni a bérlő Azonosítóját, használja:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Ha szeretné beolvasni az alkalmazás Azonosítóját, használja:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Egyszerű szolgáltatásnév létrehozása hitelesítésszolgáltatótól származó tanúsítvánnyal
Egy hitelesítésszolgáltató által kiadott tanúsítvánnyal történő egyszerű szolgáltatásnév létrehozása, használja a következő parancsfájlt:

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
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
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

Néhány elemet a parancsfájllal kapcsolatos figyelembe venni:

* Az előfizetés hozzáférés hatókörét.
* Ebben a példában hozzáadja a közreműködő szerepkört az egyszerű szolgáltatás. Más szerepköreivel kapcsolatban, tekintse meg a [RBAC: beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md).
* A parancsfájl alvó állapotban van, várja meg, hogy az új szolgáltatáshoz Azure Active Directory teljes propagálása egyszerű 15 másodpercig. Ha a parancsfájl nem elegendő ideig kell várni, egy üzenet szerint láthatja: "{} Azonosítójú rendszerbiztonsági tag nem létezik a következő könyvtárban: {DIR-azonosító}."
* Ha a szolgáltatás egyszerű hozzáférést biztosít a további előfizetések vagy erőforráscsoportok kell futtatni a `New-AzureRMRoleAssignment` parancsmagot újra különböző hatóköröket.

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
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Az alkalmazás azonosítója és a bérlő azonosítója nem különbség, így közvetlenül a parancsfájlban beágyazhatók. Ha szeretné beolvasni a bérlő Azonosítóját, használja:

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

A jelszó hozzáadásához használja:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Tanúsítvány érték hozzáadása, hozzon létre egy önaláírt tanúsítványt, ebben a cikkben ismertetett módon. Ezután használja:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Egyszerűbbé teheti a bejelentkezést a hozzáférési token mentése
Adja meg a szolgáltatás egyszerű hitelesítő adatokat, minden alkalommal, amikor jogcímadatokat kell-e jelentkezni elkerüléséhez mentheti a hozzáférési jogkivonat.

Az aktuális jogkivonat használni egy későbbi munkamenetben, mentse a profilt.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Nyissa meg a profilt, és vizsgálja meg annak tartalmát. Figyelje meg, hogy az tartalmazza-e olyan hozzáférési jogkivonatot. Helyett manuálisan a bejelentkezés ismét betölteni a profilt.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> A hozzáférési jogkivonat lejár, így csak a működik a mentett profil használatával mindaddig, amíg a lexikális elem érvénytelen.
>  

Azt is megteheti hívhat meg REST műveleteinek-e jelentkezni a powershellből. A hitelesítési válaszra kérheti le a hozzáférési jogkivonat más műveletek való használatra. Lásd a példát a hozzáférési jogkivonat beolvasása REST műveleteinek figyelőn, [generálása egy hozzáférési jogkivonat](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Hibakeresés

Egy egyszerű szolgáltatás létrehozása során felmerülő hibák a következők:

* **"Authentication_Unauthorized"** vagy **"előfizetést az adott környezetben található."** – Ezt a hibaüzenetet látja, ha a fiók nem rendelkezik a [szükséges engedélyek](#required-permissions) az Azure Active Directory regisztrálnia az alkalmazást. Általában ezt a hibát látva az Azure Active Directoryban csak rendszergazda felhasználók regisztrálhatják az alkalmazásokat, és a fiók nincs a rendszergazda segítségét. Kérje a rendszergazdától, vagy rendeljen Önhöz egy rendszergazdai szerepkört, vagy lehetővé teszi a felhasználók alkalmazásokat regisztrálni.

* A fiók **"nem jogosult a műveletre"Microsoft.Authorization/roleAssignments/write"hatókörben"/Subscriptions/ {guid}"."**  -Ezt a hibaüzenetet látja, ha a fiók nem rendelkezik megfelelő engedélyekkel a szerepkör hozzárendelése identitást. Kérje meg a előfizetési rendszergazda, akkor a felhasználói hozzáférés adminisztrátora szerepkörbe való felvételre.

## <a name="sample-applications"></a>Mintaalkalmazások
Az alkalmazás a különböző platformokat, a bejelentkezés kapcsolatos információkért lásd:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>További lépések
* Az alkalmazás integrálása az Azure erőforrások kezeléséhez részletes lépéseiért lásd: [fejlesztői útmutató az Azure Resource Manager API-val engedélyezési](resource-manager-api-authentication.md).
* Egy részletes ismertetése az alkalmazások és szolgáltatásnevekről [alkalmazás és szolgáltatás egyszerű objektumok](../active-directory/active-directory-application-objects.md). 
* Azure Active Directory-hitelesítéssel kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure AD](../active-directory/active-directory-authentication-scenarios.md).
* Elérhető műveleteket, lehet megadott vagy megtagadta a felhasználók listáját lásd: [Azure Resource Manager erőforrás-szolgáltató műveletek](../active-directory/role-based-access-control-resource-provider-operations.md).

