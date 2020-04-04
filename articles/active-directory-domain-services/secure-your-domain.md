---
title: Biztonságos Azure AD tartományi szolgáltatások | Microsoft dokumentumok
description: Megtudhatja, hogyan tilthatja le a gyenge titkosításokat, a régi protokollokat és az NTLM jelszókivonat-szinkronizálást egy Azure Active Directory tartományi szolgáltatások által kezelt tartományban.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 581963c94129c36acbd8761d93e369281797fa9f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654726"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Gyenge titkosítások és jelszókivonat-szinkronizálás letiltása az Azure AD tartományi szolgáltatások által kezelt tartomány biztonságossá tétele érdekében

Alapértelmezés szerint az Azure Active Directory tartományi szolgáltatások (Azure AD DS) lehetővé teszi a titkosítások, például az NTLM v1 és a TLS v1 használatát. Ezek a rejtjelek szükség lehet néhány régebbi alkalmazások, de gyengének minősülnek, és letiltható, ha nincs rájuk szükség. Ha az Azure AD Connect használatával helyszíni hibrid kapcsolattal rendelkezik, letilthatja az NTLM jelszókivétek szinkronizálását is.

Ez a cikk bemutatja, hogyan tilthatja le az NTLM v1 és TLS v1 titkosításokat, és hogyan tilthatja le az NTLM jelszókivonat-szinkronizálást.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Az Azure PowerShell telepítése és konfigurálása.
    * Szükség esetén kövesse az utasításokat [az Azure PowerShell-modul telepítéséhez és az Azure-előfizetéshez való csatlakozáshoz.](/powershell/azure/install-az-ps)
    * Győződjön meg arról, hogy a [Connect-AzAccount][Connect-AzAccount] parancsmag használatával jelentkezik be az Azure-előfizetésbe.
* Telepítse és konfigurálja az Azure AD PowerShellt.
    * Szükség esetén kövesse az utasításokat [az Azure AD PowerShell-modul telepítéséhez és az Azure AD-hez való csatlakozáshoz.](/powershell/azure/active-directory/install-adv2)
    * Győződjön meg arról, hogy a [Connect-AzureAD-parancsmag][Connect-AzureAD] használatával bejelentkezik az Azure AD-bérlőbe.

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Gyenge titkosítások és NTLM jelszókivonat-szinkronizálás letiltása

A gyenge titkosítási csomagok és az NTLM-hitelesítő adatok kivonatának szinkronizálásának letiltásához jelentkezzen be az Azure-fiókjába, majd a [Get-AzResource][Get-AzResource] parancsmag használatával vegye le az Azure AD DS-erőforrást:

> [!TIP]
> Ha hibaüzenetet kap a [Get-AzResource][Get-AzResource] paranccsal, amely szerint a *Microsoft.AAD/DomainServices* erőforrás nem létezik, [növelje a hozzáférést az összes Azure-előfizetés és felügyeleti csoport kezeléséhez.][global-admin]

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Ezután adja meg a *DomainSecuritySettings parancsot* a következő biztonsági beállítások konfigurálásához:

1. Tiltsa le az NTLM v1-es támogatását.
2. Tiltsa le az NTLM jelszókivétek szinkronizálását a helyszíni AD-ből.
3. Tiltsa le a TLS v1-et.

> [!IMPORTANT]
> A felhasználók és a szolgáltatásfiókok nem hajthatnak végre LDAP-alapú egyszerű kötéseket, ha letiltja az NTLM jelszókivonat-szinkronizálást az Azure AD DS felügyelt tartományában. Ha egyszerű LDAP-kötéseket kell végrehajtania, ne állítsa be a *"SyncNtlmPasswords"="Disabled";* biztonsági konfigurációs beállítást a következő parancsban.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Végül alkalmazza a meghatározott biztonsági beállításokat az Azure AD DS felügyelt tartománya a [Set-AzResource][Set-AzResource] parancsmag használatával. Adja meg az Azure AD DS-erőforrást az első lépésből, és az előző lépés biztonsági beállításait.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Néhány percet vesz igénybe, amíg a biztonsági beállításokat alkalmazni kell az Azure AD DS felügyelt tartományban.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a szinkronizálási folyamatról, olvassa el [az Objektumok és hitelesítő adatok szinkronizálásának módját az Azure AD DS által kezelt tartományban.][synchronization]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD