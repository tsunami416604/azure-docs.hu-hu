---
title: 'Azure Active Directory Domain Services: Csoportosan felügyelt szolgáltatásfiók létrehozása | Microsoft Docs'
description: Megtudhatja, hogyan hozhat létre Azure Active Directory Domain Services felügyelt tartományokkal használható csoportosan felügyelt szolgáltatásfiókot (gMSA)
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: 404160c895a8d0a72921fe202adba82c3d069aaf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234117"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Csoportosan felügyelt szolgáltatásfiók (gMSA) létrehozása Azure AD Domain Services felügyelt tartományon
Ez a cikk bemutatja, hogyan hozhat létre felügyelt szolgáltatásfiókok egy Azure AD Domain Services felügyelt tartományon.

## <a name="managed-service-accounts"></a>Felügyelt szolgáltatásfiókok
Az önálló felügyelt szolgáltatásfiók (önállóan felügyelt szolgáltatásfiókot) egy felügyelt tartományi fiók, amelynek jelszava automatikusan felügyelve van. Leegyszerűsíti az egyszerű szolgáltatásnév (SPN) kezelését, és engedélyezi a delegált felügyeletet más rendszergazdáknak. Ez a típusú felügyelt szolgáltatásfiók (MSA) a Windows Server 2008 R2 és a Windows 7 rendszerben jelent meg.

A csoportosan felügyelt szolgáltatásfiók (gMSA) ugyanazokat az előnyöket biztosítja a tartomány számos kiszolgálóján. Egy kiszolgálófarm által üzemeltetett szolgáltatás összes példányának ugyanazt a szolgáltatásnevet kell használnia a kölcsönös hitelesítési protokollok működéséhez. Ha gMSA használ, a Windows operációs rendszer a rendszergazda helyett a fiók jelszavát kezeli.

**További információ:**
- [Csoportosan felügyelt szolgáltatásfiókok – áttekintés](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [A csoportosan felügyelt szolgáltatásfiókok első lépései](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Szolgáltatásfiókok használata az Azure AD tartományi szolgáltatásokban
Azure AD Domain Services felügyelt tartományokat a Microsoft zárolja és felügyeli. Néhány fontos szempontot figyelembe kell venni, amikor a Azure AD Domain Serviceskal rendelkező szolgáltatásfiókok használata.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Szolgáltatásfiókok létrehozása a felügyelt tartományhoz tartozó egyéni szervezeti egységek (OU) alapján
Nem hozhat létre szolgáltatásfiókot a beépített "AADDC Users" vagy "AADDC Computers" szervezeti egységekben. [Hozzon létre egy egyéni szervezeti egységet](create-ou.md) a felügyelt tartományon, majd hozzon létre szolgáltatási fiókokat az adott egyéni szervezeti egységen belül.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>A Key Distribution Services (KDS) legfelső szintű kulcsa már előre létrehozva
A Key Distribution Services (KDS) legfelső szintű kulcsa előre létre van hozva egy Azure AD Domain Services felügyelt tartományon. Nem kell létrehoznia KDS, és nem rendelkezik jogosultságokkal. A KDS legfelső szintű kulcsát nem tekintheti meg a felügyelt tartományon.

## <a name="sample---create-a-gmsa-using-powershell"></a>Minta – gMSA létrehozása a PowerShell használatával
Az alábbi példa bemutatja, hogyan hozhat létre egyéni szervezeti egységet a PowerShell használatával. Ezután létrehozhat egy gMSA a szervezeti egységen belül a ```-Path``` (z) paraméter használatával a szervezeti egység megadásához.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell-parancsmag dokumentációja:**
- [New-ADOrganizationalUnit parancsmag](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [New-ADServiceAccount parancsmag](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>További lépések
- [Egyéni szervezeti egység létrehozása felügyelt tartományon](create-ou.md)
- [Csoportosan felügyelt szolgáltatásfiókok – áttekintés](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [A csoportosan felügyelt szolgáltatásfiókok első lépései](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
