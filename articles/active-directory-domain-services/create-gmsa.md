---
title: Csoport kezelt szolgáltatásfiókok az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Megtudhatja, hogy miként hozhat létre csoportkezelt szolgáltatásfiókot (gMSA) az Azure Active Directory tartományi szolgáltatások által felügyelt tartományokhoz való használatra
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 95269fbe38ae21d0b9761f67adee681ae896a8ab
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476340"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Csoportkezelt szolgáltatásfiók (gMSA) létrehozása az Azure AD tartományi szolgáltatásokban

Az alkalmazásoknak és szolgáltatásoknak gyakran identitásra van szükségük ahhoz, hogy más erőforrásokkal hitelesítsék magukat. Előfordulhat például, hogy egy webszolgáltatásnak hitelesítésre van szüksége egy adatbázis-szolgáltatással. Ha egy alkalmazás vagy szolgáltatás több példányt, például egy webkiszolgáló-farmot, manuálisan létre, és konfigurálja az identitások az erőforrások időigényes lesz.

Ehelyett egy csoport felügyelt szolgáltatásfiók (gMSA) hozható létre az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományban. A Windows operációs rendszer automatikusan kezeli a gMSA hitelesítő adatait, ami leegyszerűsíti a nagy erőforráscsoportok kezelését.

Ez a cikk bemutatja, hogyan hozhat létre gMSA-t egy Azure AD DS felügyelt tartományban az Azure PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
    * Szükség esetén fejezze be az [oktatóanyagot egy felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]

## <a name="managed-service-accounts-overview"></a>Kezelt szolgáltatásfiókok – áttekintés

Az önálló felügyelt szolgáltatásfiók (sMSA) olyan tartományi fiók, amelynek jelszavát a rendszer automatikusan kezeli. Ez a megközelítés leegyszerűsíti az egyszerű szolgáltatásnév (SPN) kezelését, és lehetővé teszi a delegált felügyeletet más rendszergazdák számára. Nem kell manuálisan létrehoznia és elforgatnia a fiók hitelesítő adatait.

A csoport által kezelt szolgáltatásfiók (gMSA) ugyanazt a felügyeleti egyszerűsítést biztosítja, de a tartomány több kiszolgálója számára. A gMSA lehetővé teszi, hogy a kiszolgálófarmon üzemeltetett szolgáltatás minden példánya ugyanazt a szolgáltatásnévhasználatát használja a kölcsönös hitelesítési protokollok működéséhez. Ha a gMSA szolgáltatásnévként van használva, a Windows operációs rendszer ismét kezeli a fiók jelszavát ahelyett, hogy a rendszergazdára támaszkodna.

További információt a [csoportkezelt szolgáltatásfiókok (gMSA) – áttekintés című témakörben talál.][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Szolgáltatásfiókok használata az Azure AD DS-ben

Mivel az Azure AD DS által felügyelt tartományokat zárolta és a Microsoft kezeli, a szolgáltatásfiókok használata során néhány szempontot figyelembe kell venni:

* Szolgáltatási fiókok létrehozása egyéni szervezeti egységekben a felügyelt tartományban.
    * A beépített *AADDC-felhasználók* vagy az *AADDC-számítógépek* felhasználói között nem hozhat létre szolgáltatásfiókot.
    * Ehelyett [hozzon létre egy egyéni szervezeti egységet][create-custom-ou] az Azure AD DS felügyelt tartományban, majd hozzon létre szolgáltatásfiókokat az adott egyéni szervezeti egységben.
* A kulcsterjesztési szolgáltatások (KDS) gyökérkulcsa előre létre van hozva.
    * A KDS gyökérkulcs a gMSA-k jelszavainak létrehozására és beolvasására szolgál. Az Azure AD DS-ben a KDS-gyökér jön létre az Ön számára.
    * Nincs jogosultsága egy másik kds gyökérkulcs létrehozására vagy megtekintésére.

## <a name="create-a-gmsa"></a>GMSA létrehozása

Először hozzon létre egy egyéni szervezeti egységet a [New-ADOrganizationalUnit][New-AdOrganizationalUnit] parancsmag használatával. Az egyéni ousok létrehozásáról és kezeléséről az [Egyéni adottségi szerepkörök az Azure AD DS-ben][create-custom-ou]című témakörben talál további információt.

> [!TIP]
> A gMSA létrehozásához ezeket a lépéseket a [felügyeleti virtuális gép használatával hajtsa végre.][tutorial-create-management-vm] Ez a felügyeleti virtuális gép már rendelkezik a szükséges AD PowerShell-parancsmagokkal és a felügyelt tartománnyal való kapcsolattal.

A következő példa létrehoz egy egyéni ou nevű *myNewOU* az Azure AD DS felügyelt tartomány nevű *aaddscontoso.com.* Saját szervezeti egység és felügyelt tartománynév használata:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Most hozzon létre egy gMSA-t a [New-ADServiceAccount][New-ADServiceAccount] parancsmag használatával. A következő példaparaméterek vannak definiálva:

* **-A név** *beállítása WebFarmSvc*
* **-Az elérési út** paraméter az előző lépésben létrehozott gMSA egyéni szervezeti egységét adja meg.
* A DNS-bejegyzések és az egyszerű szolgáltatásnevek *WebFarmSvc.aaddscontoso.com*
* Az *AADDSCONTOSO-SERVER$* rendszerben lévő rendszertagok lekérhetik a jelszót az identitás használatával.

Adja meg saját nevét és tartománynevét.

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

Az alkalmazások és szolgáltatások mostantól beállíthatók a gMSA szükség szerint történő használatára.

## <a name="next-steps"></a>További lépések

A gMSA-król további információt a [Csoportkezelt szolgáltatásfiókok – első lépések című témakörben talál.][gmsa-start]

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
