---
title: A Azure Active Directory Domain Services felügyelt tartomány biztonságossá tétele | Microsoft Docs
description: Felügyelt tartomány védelme
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879151"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services felügyelt tartomány biztonságossá tétele
Ez a cikk segítséget nyújt a felügyelt tartomány biztonságossá tételében. Kikapcsolhatja a gyenge titkosítási csomagok használatát, és letilthatja az NTLM hitelesítő adatok kivonatának szinkronizálását.

## <a name="install-the-required-powershell-modules"></a>A szükséges PowerShell-modulok telepítése

### <a name="install-and-configure-azure-ad-powershell"></a>Az Azure AD PowerShell telepítése és konfigurálása
A cikk utasításait követve [telepítse az Azure ad PowerShell-modult, és kapcsolódjon az Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)-hez.

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
A cikk utasításait követve [telepítse a Azure PowerShell modult, és kapcsolódjon az Azure-](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)előfizetéséhez.


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>A gyenge titkosítási csomagok és az NTLM hitelesítő adatok kivonatának szinkronizálásának letiltása
Használja az alábbi PowerShell-szkriptet a következőhöz:

1. Tiltsa le a NTLM v1-támogatást a felügyelt tartományban.
2. Tiltsa le az NTLM-jelszó kivonatok szinkronizálását a helyszíni AD-ből.
3. Tiltsa le a TLS v1-et a felügyelt tartományban.

Ha hibaüzenet jelenik meg azzal `Get-AzResource` a paranccsal, hogy a *Microsoft. HRE/DomainServices* erőforrás nem létezik, [emelje ki a hozzáférést az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../role-based-access-control/elevate-access-global-admin.md).

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> A felhasználók (és a szolgáltatásfiókok) nem hajthatnak végre LDAP egyszerű kötéseket, ha letiltotta az NTLM-jelszó kivonatának szinkronizálását a Azure AD Domain Services-példányon.  Az NTLM jelszó-kivonatok szinkronizálásának letiltásával kapcsolatos további információkért olvassa el [Az Azure ad tartományi szolgáltatások által felügyelt tartomány biztonságossá tétele](secure-your-domain.md)című témakört.
>
>

## <a name="next-steps"></a>További lépések
* [A Azure AD Domain Services szinkronizálásának megismerése](synchronization.md)
