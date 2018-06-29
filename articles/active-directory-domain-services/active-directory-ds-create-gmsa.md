---
title: 'Az Azure Active Directory tartományi szolgáltatások: A csoportosan felügyelt szolgáltatásfiók létrehozása |} Microsoft Docs'
description: Azure Active Directory tartományi szolgáltatások felügyelt tartományok felügyelete
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
ms.topic: article
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 4b4ce876760d024170020ae3faf618c7e9e76773
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036064"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>A csoportosan felügyelt szolgáltatásfiókjait (gMSA) létrehozni az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan lehet létrehozni a felügyelt szolgáltatásfiókok az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

## <a name="managed-service-accounts"></a>Felügyelt szolgáltatásfiókok
A önálló felügyelt szolgáltatásfiókok (önállóan felügyelt szolgáltatásfiókot) egy felügyelt tartományi fiók, amelynek jelszó automatikusan kezeli. Egyszerűbbé teszi az egyszerű szolgáltatásnév (SPN) kezelő, és lehetővé teszi, hogy delegált felügyeleti más rendszergazdák számára. Felügyelt szolgáltatásfiókok (msa-t) az ilyen típusú bevezetett Windows 7 és Windows Server 2008 R2.

A csoport által felügyelt szolgáltatásfiókjait (gMSA) az előnyei ugyanabban a tartományban, sok kiszolgálón. Egy kiszolgálófarmon üzemeltetett szolgáltatás minden példányának kell használnia az azonos egyszerű kölcsönös hitelesítési protokollok működéséhez. Ha a csoportosan felügyelt szolgáltatásfiók egyszerű szolgáltatás, a Windows operációs rendszer kezeli a fiók jelszavát ahelyett, hogy a rendszergazda.

**További információ:**
- [Csoportosan felügyelt szolgáltatásfiókok – áttekintés](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Ismerkedés a csoportosan felügyelt szolgáltatásfiókok](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatásokban a szolgáltatásfiókok
Az Azure AD tartományi szolgáltatások felügyelt tartományok zárolva, és a Microsoft kezeli. Nincsenek néhány fő szempontjait a szolgáltatásfiókok az Azure AD tartományi szolgáltatásokkal.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>A felügyelt tartományra egyéni szervezeti egységekhez (OU) belül szolgáltatásfiókok létrehozása
A beépített "AADDC felhasználók" vagy "AADDC számítógépek" szervezeti egység nem hozható létre a szolgáltatás-fiók. [Hozzon létre egy egyéni szervezeti](active-directory-ds-admin-guide-create-ou.md) felügyelt tartományra, majd hozza létre a szolgáltatásfiókok belül egyéni szervezeti egységre.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>A terjesztési Kulcsszolgáltatások (KDS) legfelső szintű kulcs már előre létrehozott
A terjesztési Kulcsszolgáltatások (KDS) legfelső szintű kulcs előre létrehozott megtalálható az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Nem kell létrehozni a KDS legfelső szintű kulcsot, és nem rendelkezik a szükséges engedélyekkel, vagy tegye. KDS-gyökérkulcs vagy nem tekintheti meg a felügyelt tartományra.

## <a name="sample---create-a-gmsa-using-powershell"></a>Minta - a PowerShell használatával csoportosan felügyelt szolgáltatásfiók létrehozása
A következő példa bemutatja, hogyan hozzon létre egy egyéni szervezeti PowerShell használatával. Ezután létrehozhat belüli szervezeti egységre csoportosan felügyelt szolgáltatásfiók használatával a ```-Path``` paraméterrel adhatja meg a szervezeti Egységhez.

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

**PowerShell-parancsmagok dokumentációira:**
- [Új ADOrganizationalUnit parancsmag](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Új ADServiceAccount parancsmaggal](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>További lépések
- [Hozzon létre egyéni szervezeti olyan felügyelt tartományhoz](active-directory-ds-admin-guide-create-ou.md)
- [Csoportosan felügyelt szolgáltatásfiókok – áttekintés](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Ismerkedés a csoportosan felügyelt szolgáltatásfiókok](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
