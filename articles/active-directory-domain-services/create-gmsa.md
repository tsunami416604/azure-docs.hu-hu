---
title: Csoportosan felügyelt szolgáltatásfiókok a Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Active Directory Domain Services felügyelt tartományokkal használható csoportosan felügyelt szolgáltatásfiókot (gMSA)
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 58749e4518f6fa73c8641ce38483c101576047aa
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614078"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Csoportosan felügyelt szolgáltatásfiók (gMSA) létrehozása Azure AD Domain Services

Az alkalmazásoknak és szolgáltatásoknak gyakran identitásra van szükségük a más erőforrásokkal való hitelesítéshez. Előfordulhat például, hogy egy webszolgáltatásnak hitelesítenie kell magát egy adatbázis-szolgáltatásban. Ha egy alkalmazásnak vagy szolgáltatásnak több példánya van, például egy webkiszolgáló-Farm, akkor manuálisan hozza létre és konfigurálja az adott erőforrásokhoz tartozó identitásokat.

Ehelyett egy csoportosan felügyelt szolgáltatásfiók (gMSA) hozható létre a Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományban. A Windows operációs rendszer automatikusan kezeli a gMSA hitelesítő adatait, ami leegyszerűsíti a nagyméretű erőforrások kezelését.

Ez a cikk bemutatja, hogyan hozhat létre gMSA egy Azure AD DS felügyelt tartományban Azure PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>A felügyelt szolgáltatásfiókok áttekintése

A különálló felügyelt szolgáltatásfiók (önállóan felügyelt szolgáltatásfiókot) egy olyan tartományi fiók, amelynek a jelszava automatikusan felügyelve van. Ez a megközelítés leegyszerűsíti az egyszerű szolgáltatásnév (SPN) felügyeletét, és engedélyezi a delegált felügyeletet más rendszergazdáknak. Nem kell manuálisan létrehoznia és elforgatnia a fiók hitelesítő adatait.

A csoportosan felügyelt szolgáltatásfiókok (gMSA) ugyanazt a felügyeleti egyszerűsítést biztosítják, de a tartományban több kiszolgáló esetében is. A gMSA lehetővé teszi, hogy egy kiszolgálófarm által üzemeltetett szolgáltatás minden példánya ugyanazt a szolgáltatásnevet használja, mint a kölcsönös hitelesítési protokollok működéséhez. Ha gMSA használ, a Windows operációs rendszer ismét a rendszergazda helyett a fiók jelszavát kezeli.

További információ: [csoportosan felügyelt szolgáltatásfiókok (gMSA) áttekintése][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Szolgáltatásfiókok használata az Azure AD DS

Mivel az Azure AD DS felügyelt tartományokat a Microsoft zárolja és kezeli, néhány szempontot figyelembe kell venni a szolgáltatásfiókok használatakor:

* Szolgáltatásfiókok létrehozása a felügyelt tartományhoz tartozó egyéni szervezeti egységekben (OU).
    * Nem hozhat létre szolgáltatásfiókot a beépített *AADDC-felhasználók* vagy *AADDC számítógépek* szervezeti egységekben.
    * Ehelyett [hozzon létre egy egyéni szervezeti egységet][create-custom-ou] az Azure AD DS felügyelt tartományban, majd hozzon létre szolgáltatásfiókot az adott egyéni szervezeti egységben.
* A Key Distribution Services (KDS) legfelső szintű kulcsa előre létre van hozva.
    * A KDS legfelső szintű kulcsa a csoportosan felügyelt szolgáltatásfiókokat jelszavának előállítására és lekérésére szolgál. Az Azure AD DS az KDS gyökerét hozza létre a rendszer.
    * Nem rendelkezik jogosultsággal egy másik létrehozásához, vagy megtekintheti az alapértelmezett, KDS legfelső szintű kulcsot.

## <a name="create-a-gmsa"></a>GMSA létrehozása

Először hozzon létre egy egyéni szervezeti egységet a [New-ADOrganizationalUnit][New-AdOrganizationalUnit] parancsmag használatával. Az egyéni szervezeti egységek létrehozásával és kezelésével kapcsolatos további információkért lásd: [Egyéni szervezeti egységek az Azure ad DSban][create-custom-ou].

> [!TIP]
> A lépések gMSA létrehozásához [használja a felügyeleti virtuális gépet][tutorial-create-management-vm]. Ennek a felügyeleti virtuális gépnek már rendelkeznie kell a szükséges AD PowerShell-parancsmagokkal és a felügyelt tartományhoz való kapcsolódással.

A következő példa létrehoz egy *myNewOU* nevű egyéni szervezeti egységet az Azure AD DS felügyelt tartomány *aaddscontoso.com*nevű felügyelt tartományában. Saját szervezeti egység és felügyelt tartománynév használata:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Most hozzon létre egy gMSA a [New-ADServiceAccount][New-ADServiceAccount] parancsmag használatával. A következő példa paraméterek vannak meghatározva:

* **-A name** értéke *WebFarmSvc*
* **-Path** paraméter adja meg az előző lépésben létrehozott gMSA tartozó egyéni szervezeti egységet.
* A DNS-bejegyzések és egyszerű szolgáltatásnév *WebFarmSvc.aaddscontoso.com* vannak beállítva
* A *AADDSCONTOSO-Server $* rendszerbiztonsági tag jogosult a jelszó beolvasására az identitás használatát.

Adja meg a saját nevét és tartományneveit.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Az alkalmazások és a szolgáltatások mostantól úgy konfigurálhatók, hogy igény szerint használják a gMSA.

## <a name="next-steps"></a>Következő lépések

A csoportosan felügyelt szolgáltatásfiókokat kapcsolatos további információkért lásd: [Bevezetés a csoportosan felügyelt szolgáltatásfiókok][gmsa-start]használatába.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
