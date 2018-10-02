---
title: Az Azure Active Directory Domain Services felügyelt tartomány Secure |} A Microsoft Docs
description: A felügyelt tartomány Secure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 9dbdb5569a6b5718f01b82661f9323812ab16c27
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019825"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Az Azure AD Domain Services felügyelt tartomány Secure
Ez a cikk segítséget nyújt a felügyelt tartomány secure. Kapcsolja ki a gyenge titkosító csomag használatát, és NTLM hitelesítő adatok a Jelszókivonat-szinkronizálás letiltása.

## <a name="install-the-required-powershell-modules"></a>A szükséges PowerShell-modulok telepítése

### <a name="install-and-configure-azure-ad-powershell"></a>Az Azure AD PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [Azure AD PowerShell-modul telepítéséhez és az Azure AD connect](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [az Azure PowerShell-modul telepítése és csatlakozás az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>A gyenge titkosító csomagok és NTLM hitelesítő adatok a Jelszókivonat-szinkronizálás letiltása
A következő PowerShell-parancsprogram használata:
1. Tiltsa le az NTLM v1 támogatását a felügyelt tartományon.
2. A helyszíni NTLM-jelszókivonatok szinkronizálásának letiltása AD.
3. Tiltsa le a TLS v1 a felügyelt tartományon.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>További lépések
* [Szinkronizálás az Azure AD tartományi szolgáltatások ismertetése](active-directory-ds-synchronization.md)
