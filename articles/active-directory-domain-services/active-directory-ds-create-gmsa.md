---
title: 'Az Azure Active Directory Domain Services: Hozzon létre egy csoportosan felügyelt szolgáltatásfiókot |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services felügyelt tartomány felügyeletéhez
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 9bfd38b2eba3cab5012e5715ad283b9cb7b84a9b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505892"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Csoportosan felügyelt szolgáltatásfiók (gMSA) létrehozása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan lehet létrehozni a felügyelt szolgáltatásfiókok az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

## <a name="managed-service-accounts"></a>Felügyelt szolgáltatásfiókok
Önálló felügyelt szolgáltatásfiókok (önállóan felügyelt szolgáltatásfiókot) egy felügyelt tartományi fiók, amelynek a jelszavát automatikusan kezeli. Egyszerűbbé téve a szolgáltatásfelügyelet egyszerű szolgáltatásnevet (SPN), és lehetővé teszi, hogy delegált felügyeleti más rendszergazdák számára. Az ilyen típusú felügyelt szolgáltatásfiókok (MSA) a Windows Server 2008 R2 és Windows 7 jelent meg.

A csoportosan felügyelt szolgáltatásfiók (gMSA) ugyanazokat az előnyöket kínál a tartományban lévő kiszolgálók számát. Egy kiszolgálóból álló farmra üzemeltetett szolgáltatás minden példányát kell használnia a kölcsönös hitelesítési protokoll azonos szolgáltatásnév működjön. Csoportosan felügyelt szolgáltatásfiók egyszerű szolgáltatásnevének használatos, ha a Windows operációs rendszer kezeli a fiók jelszava helyett a rendszergazda.

**További információ:**
- [Csoport felügyelt szolgáltatásfiókok – áttekintés](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Csoportosan felügyelt szolgáltatásfiókok – első lépések](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatásokban használnak
Az Azure AD Domain Services felügyelt tartomány zárolását, és a Microsoft által felügyelt. Van néhány fő szempontot szolgáltatásfiókok használata az Azure AD tartományi szolgáltatásokkal.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Egyéni szervezeti egység (OU) belül szolgáltatásfiókok létrehozása a felügyelt tartományon
A beépített "AADDC felhasználók" vagy "AADDC számítógépek" szervezeti egységet a szolgáltatásfiók nem hozható létre. [Hozzon létre egy egyéni szervezeti](active-directory-ds-admin-guide-create-ou.md) el a felügyelt tartományhoz, majd hozza létre az egyéni szervezeti egységre belül szolgáltatásfiókok.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>A kulcs-telepítési szolgáltatások (KDS) legfelső szintű kulcs már előre létrehozott
A kulcs-telepítési szolgáltatások (KDS) legfelső szintű kulcs előre létrehozott egy Azure AD tartományi szolgáltatásokkal felügyelt tartományban található. Nem kell létrehozni a KDS legfelső szintű kulcs, és nem rendelkezik jogosultságokkal teheti így. KDS-gyökérkulcs vagy nem tekintheti meg a felügyelt tartományhoz.

## <a name="sample---create-a-gmsa-using-powershell"></a>– PowerShell-lel csoportosan felügyelt szolgáltatásfiók létrehozása
A következő minta bemutatja, hogyan hozhat létre egy egyéni szervezeti PowerShell használatával. Ezután létrehozhat csoportosan felügyelt szolgáltatásfiók belül, amelynek használatával a ```-Path``` paraméterrel adja meg a szervezeti Egységhez.

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

**PowerShell-parancsmag dokumentáció:**
- [Új ADOrganizationalUnit parancsmag](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Új ADServiceAccount parancsmaggal](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>További lépések
- [Egy egyéni szervezeti egység létrehozása egy felügyelt tartományon](active-directory-ds-admin-guide-create-ou.md)
- [Csoport felügyelt szolgáltatásfiókok – áttekintés](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Csoportosan felügyelt szolgáltatásfiókok – első lépések](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
