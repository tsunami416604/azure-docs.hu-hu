---
title: Kerberos korlátozott delegálás az Azure AD tartományi szolgáltatások | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti az erőforrás-alapú Kerberos korlátozott delegálást (KCD) egy Azure Active Directory tartományi szolgáltatások által felügyelt tartományban.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 0d7c9319097d81f4db2e2c5ecfc692269d27a4db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476090"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Kerberos korlátozott delegálás (KCD) konfigurálása az Azure Active Directory tartományi szolgáltatásokban

Az alkalmazások futtatásakor előfordulhat, hogy ezek az alkalmazások egy másik felhasználó környezetében férnek hozzá az erőforrásokhoz. Az Active Directory tartományi szolgáltatások (AD DS) támogatja a *Kerberos delegálás* nevű mechanizmust, amely lehetővé teszi a használati esetet. A Kerberos *korlátozott* delegálása (KCD) ezután erre a mechanizmusra épül, és meghatározza a felhasználó környezetében elérhető konkrét erőforrásokat. Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) által felügyelt tartományok biztonságosabban vannak lezárva, mint a hagyományos helyszíni Active DD DS-környezetek, ezért használjon biztonságosabb *erőforrás-alapú KCD-t.*

Ez a cikk bemutatja, hogyan konfigurálhatja az erőforrás-alapú Kerberos korlátozott delegálás egy Azure AD DS felügyelt tartományban.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
    * Szükség esetén végezze el az oktatóanyagot [a Windows Server virtuális gép létrehozásához, majd csatlakozzon egy felügyelt tartományhoz,][create-join-windows-vm] majd telepítse az [AD DS felügyeleti eszközeit.][tutorial-create-management-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos korlátozott delegálás – áttekintés

A Kerberos delegálás lehetővé teszi, hogy az egyik fiók megszemélyesítsen egy másik fiókot az erőforrások eléréséhez. Például egy olyan webalkalmazás, amely hozzáfér egy háttérwebes összetevőhöz, más felhasználói fiókként is megszemélyesítheti magát, amikor a háttérkapcsolat létrejöttét végzi. A Kerberos delegálása nem biztonságos, mivel nem korlátozza, hogy a megszemélyesítő fiók milyen erőforrásokhoz férhet hozzá.

A Kerberos korlátozott delegálása (KCD) korlátozza azokat a szolgáltatásokat vagy erőforrásokat, amelyekhez egy adott kiszolgáló vagy alkalmazás csatlakozhat, ha egy másik identitást megszemélyesít. A hagyományos KCD tartományi rendszergazdai jogosultságokat igényel egy szolgáltatás tartományi fiókjának konfigurálásához, és korlátozza a fiók egyetlen tartományon való futtatását.

Hagyományos KCD is van néhány kérdés. A korábbi operációs rendszerekben például a szolgáltatás rendszergazdájának nem volt hasznos módja annak, hogy megtudja, mely előtér-szolgáltatások delegáltak a tulajdonukban lévő erőforrás-szolgáltatásokra. Minden olyan előtér-szolgáltatás, amely képes delegálni egy erőforrás-szolgáltatás egy potenciális támadási pont. Ha egy olyan kiszolgáló, amely az erőforrás-szolgáltatások delegálására konfigurált előtér-szolgáltatást üzemeltet, sérülhet, az erőforrás-szolgáltatások is sérülhetnek.

Egy Azure AD DS felügyelt tartományban nem rendelkezik tartományi rendszergazdai jogosultságokkal. Ennek eredményeképpen a hagyományos fiókalapú KCD nem konfigurálható egy Azure AD DS felügyelt tartományban. Az erőforrás-alapú KCD ehelyett használható, ami szintén biztonságosabb.

### <a name="resource-based-kcd"></a>Erőforrás-alapú KCD

A Windows Server 2012 és újabb verziók lehetővé teszik a szolgáltatás-rendszergazdák számára a korlátozott delegálás konfigurálását a szolgáltatáshoz. Ezt a modellt erőforrás-alapú KCD-nek nevezzük. Ezzel a megközelítéssel a háttér-szolgáltatás rendszergazdája engedélyezheti vagy megtagadhatja az adott előtér-szolgáltatások kcd használatát.

Az erőforrás-alapú KCD a PowerShell használatával van konfigurálva. [A Set-ADComputer][Set-ADComputer] vagy [a Set-ADUser][Set-ADUser] parancsmagokat attól függően használhatja, hogy a megszemélyesítő fiók számítógépfiók vagy felhasználói fiók/szolgáltatásfiók.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Erőforrásalapú KCD konfigurálása számítógépfiókhoz

Ebben az esetben tegyük fel, hogy van egy webalkalmazás, amely fut a számítógépen nevű *contoso-webapp.aaddscontoso.com.* A webalkalmazásnak hozzá kell férnie egy webes API-hoz, amely a *contoso-api.aaddscontoso.com* nevű számítógépen fut a tartományi felhasználók környezetében. A forgatókönyv konfigurálásához hajtsa végre az alábbi lépéseket:

1. [Hozzon létre egyéni szervezeti egységet](create-ou.md). Az azure AD DS felügyelt tartományon belüli felhasználóknak az egyéni szervezeti egység kezeléséhez szükséges engedélyeket delegálhatja.
1. [Tartomány-csatlakozzon a virtuális gépek][create-join-windows-vm], mind az egyik, amely futtatja a webalkalmazást, és az egyik, hogy fut a webes API-t, az Azure AD DS felügyelt tartományban. Ezeket a számítógépfiókokat az előző lépésből hozza létre az egyéni szervezeti egységben.

    > [!NOTE]
    > A webalkalmazás és a webes API számítógép-fiókjainak egy egyéni szervezeti egységben kell lenniük, ahol rendelkezik az erőforrás-alapú KCD konfigurálásához szükséges engedélyekkel. A beépített *AAD DC-számítógépek* tárolóban nem konfigurálható erőforrásalapú KCD egy számítógépfiókhoz.

1. Végül konfigurálja az erőforrás-alapú KCD-t a [Set-ADComputer][Set-ADComputer] PowerShell parancsmag használatával. A tartományhoz csatlakozott felügyeleti virtuális gép, és bejelentkezett, mint az *Azure AD DC rendszergazdák* csoportjának tagjaként bejelentkezett, futtassa a következő parancsmagokat. Adja meg saját számítógépnevét, ha szükséges:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Erőforrásalapú KCD konfigurálása felhasználói fiókhoz

Ebben az esetben tegyük fel, hogy van egy webalkalmazás, amely *egy appsvc*nevű szolgáltatásfiókként fut. A webalkalmazásnak hozzá kell férnie egy webes API-hoz, amely a tartományi felhasználók környezetében *háttérrendszer* nevű szolgáltatásfiókként fut. A forgatókönyv konfigurálásához hajtsa végre az alábbi lépéseket:

1. [Hozzon létre egyéni szervezeti egységet](create-ou.md). Az azure AD DS felügyelt tartományon belüli felhasználóknak az egyéni szervezeti egység kezeléséhez szükséges engedélyeket delegálhatja.
1. [Tartomány-csatlakozzon a virtuális gépek,][create-join-windows-vm] amelyek a háttérwebes API/erőforrás fut az Azure AD DS felügyelt tartományban. Hozza létre számítógépfiókját az egyéni szervezeti egységen belül.
1. Hozza létre a szolgáltatásfiókot (például "appsvc") a webalkalmazás futtatásához az egyéni szervezeti egységen belül.

    > [!NOTE]
    > Ismét a web API virtuális gép számítógép-fiók, és a szolgáltatásfiók a webalkalmazás, kell lennie egy egyéni szervezeti egység, ahol rendelkezik engedélyekkel az erőforrás-alapú KCD konfigurálásához. A beépített *AAD DC-számítógépek* vagy az *AAD DC-felhasználók* tárolókban lévő fiókokhoz nem konfigurálható erőforrás-alapú KCD. Ez azt is jelenti, hogy nem használhatja az Azure AD-ből szinkronizált felhasználói fiókokat az erőforrás-alapú KCD beállításához. Létre kell hoznia és használnia kell a kifejezetten az Azure AD DS-ben létrehozott szolgáltatásfiókokat.

1. Végül konfigurálja az erőforrás-alapú KCD-t a [Set-ADUser][Set-ADUser] PowerShell parancsmag használatával. A tartományhoz csatlakozott felügyeleti virtuális gép, és bejelentkezett, mint az *Azure AD DC rendszergazdák* csoportjának tagjaként bejelentkezett, futtassa a következő parancsmagokat. Adja meg saját szolgáltatásnevét, ha szükséges:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni arról, hogyan működik a delegálás az Active Directory tartományi szolgáltatásokban, olvassa el a [Kerberos korlátozott delegálás – áttekintés című témakört.][kcd-technet]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
