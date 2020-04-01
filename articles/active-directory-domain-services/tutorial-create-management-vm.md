---
title: Oktatóanyag – Felügyeleti virtuális gép létrehozása az Azure Active Directory tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre és konfigurálhat egy Windows-virtuális gépet, amelyet az Azure Active Directory tartományi szolgáltatások példányának felügyeletére használ.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 09fcf88c6dfe90380f387c6d72c751634f5b1606
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475744"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Oktatóanyag: Felügyeleti virtuális gép létrehozása az Azure Active Directory tartományi szolgáltatások felügyelt tartományának konfigurálásához és felügyeletéhez

Az Azure Active Directory tartományi szolgáltatások (AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP és a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directoryval. Ezt a felügyelt tartományt ugyanazon a távoli kiszolgálófelügyeleti eszközökkel (RSAT) felügyeli, mint egy helyszíni Active Directory tartományi szolgáltatások tartományában. Mivel az Azure AD DS egy felügyelt szolgáltatás, vannak olyan felügyeleti feladatok, amelyeket nem hajthat végre, például távoli asztali protokoll (RDP) használatával a tartományvezérlőkhöz való csatlakozáshoz.

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Windows Server virtuális gép az Azure-ban, és telepítse a szükséges eszközöket egy Azure AD DS felügyelt tartomány felügyeletéhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure AD DS felügyelt tartományában rendelkezésre álló felügyeleti feladatok megismerése
> * Az Active Directory felügyeleti eszközeinek telepítése Windows Server virtuális gépre
> * Gyakori feladatok végrehajtása az Active Directory felügyeleti központ használatával

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén tekintse meg az Első oktatóanyagot az [Azure Active Directory tartományi szolgáltatások példányának létrehozásáról és konfigurálásakor.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server virtuális gép.
    * Szükség esetén tekintse meg az előző oktatóanyagban [a Windows Server virtuális gép létrehozásáról és felügyelt tartományhoz való csatlakozásról.][create-join-windows-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.
* Az Azure AD DS virtuális hálózatában üzembe helyezett Azure Bastion gazdagép.
    * Szükség esetén [hozzon létre egy Azure-bástya-gazdat.][azure-bastion]

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban hozzon létre és konfiguráljon egy felügyeleti virtuális gép az Azure Portalhasználatával. Első lépésekhez először jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Elérhető felügyeleti feladatok az Azure AD DS-ben

Az Azure AD DS felügyelt tartományt biztosít a felhasználók, alkalmazások és szolgáltatások számára. Ez a megközelítés módosítja a rendelkezésre álló felügyeleti feladatok at, és milyen jogosultságokkal rendelkezik a felügyelt tartományban. Ezek a feladatok és engedélyek eltérhetnek attól, amit a szokásos helyszíni Active Directory tartományi szolgáltatások környezetében tapasztal. Az Azure AD DS által felügyelt tartománytartományvezérlőktartományvezérlőihez nem tud csatlakozni a Távoli asztal használatával.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Az Azure AD DS által felügyelt tartományban végrehajtható felügyeleti feladatok

Az *AAD DC rendszergazdák* csoport tagjai az Azure AD DS felügyelt tartományában kapnak jogosultságokat, amelyek lehetővé teszik számukra az olyan feladatok elvégzését, mint például:

* Konfigurálja a felügyelt tartomány *AADDC-számítógépek* és *AADDC-felhasználók* tárolóinak beépített csoportházirend-objektumát.Configure the built-in group policy object (GPO) for the AADDC Computers and AADDC Users containers in the managed domain.
* DNS-kezelés a felügyelt tartományon.
* Egyéni szervezeti egységek (OUs) létrehozása és felügyelete a felügyelt tartományban.
* Felügyeleti hozzáférés szerzése a felügyelt tartományhoz csatlakoztatott számítógépekhez.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Az Azure AD DS által felügyelt tartományban nem rendelkező rendszergazdai jogosultságok

Az Azure AD DS felügyelt tartomány zárolva van, így nem rendelkezik jogosultságokkal bizonyos felügyeleti feladatok elvégzéséhez a tartományban. Az alábbi példák olyan feladatok, amelyeket nem tehet meg:

* Bővítse ki a felügyelt tartomány sémáját.
* Csatlakozás a felügyelt tartomány tartományvezérlőihez a Távoli asztal használatával.
* Tartományvezérlők hozzáadása a felügyelt tartományhoz.
* Nem rendelkezik *tartományi vagy* *vállalati rendszergazdai* jogosultságokkal a felügyelt tartományhoz.

## <a name="sign-in-to-the-windows-server-vm"></a>Bejelentkezés a Windows Server virtuális gépbe

Az előző oktatóanyagban létrejött egy Windows Server virtuális gép, és csatlakozott az Azure AD DS felügyelt tartományához. Használjuk a virtuális gép a felügyeleti eszközök telepítéséhez. Szükség esetén [kövesse az oktatóanyag lépéseit a Windows Server virtuális gép felügyelt tartományhoz való létrehozásához és csatlakozásához.][create-join-windows-vm]

> [!NOTE]
> Ebben az oktatóanyagban egy Windows Server virtuális gép az Azure-ban, amely csatlakozik az Azure AD DS felügyelt tartományhoz. A felügyelt tartományhoz csatlakozó Windows-ügyfelet, például a Windows 10-et is használhatja.
>
> A felügyeleti eszközök Windows-ügyfélre történő telepítéséről a [Távoli kiszolgálófelügyeleti eszközök (RSAT) telepítése című](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) témakörben talál további információt.

Első lépésekhez csatlakozzon a Windows Server virtuális géphez az alábbiak szerint:

1. Az Azure Portalon válassza **erőforráscsoportok** a bal oldalon. Válassza ki azt az erőforráscsoportot, amelyben a virtuális gép létrejött, például *a myResourceGroup*, majd válassza ki a virtuális gép, például *a myVM.*
1. A virtuális **gép Áttekintő** ablaktábláján válassza a **Csatlakozás**lehetőséget, majd **a Bástya**lehetőséget.

    ![Csatlakozás Windows virtuális géphez az Azure Portalon található Bastion használatával](./media/join-windows-vm/connect-to-vm.png)

1. Adja meg a virtuális gép hitelesítő adatait, majd válassza a **Csatlakozás lehetőséget.**

   ![Csatlakozás a Bástyagazdagépen keresztül az Azure Portalon](./media/join-windows-vm/connect-to-bastion.png)

Szükség esetén engedélyezze a webböngésző számára, hogy előugró ablakokat nyisson meg a Bástya-kapcsolat megjelenítéséhez. Néhány másodpercet vesz igénybe, hogy a kapcsolatot a virtuális gép.

## <a name="install-active-directory-administrative-tools"></a>Az Active Directory felügyeleti eszközeinek telepítése

Az Azure AD DS által felügyelt tartományok kezelése ugyanazokat a felügyeleti eszközöket, mint a helyszíni Active Directory tartományi szolgáltatások környezetek, például az Active Directory felügyeleti központ (ADAC) vagy az AD PowerShell. Ezek az eszközök a Távoli kiszolgálófelügyeleti eszközök (RSAT) szolgáltatás részeként telepíthetők Windows Server és ügyfélszámítógépeken. Az *AAD DC rendszergazdák* csoport tagjai ezután felügyelhetik az Azure AD DS felügyelt tartományokat távolról ezekkel az AD felügyeleti eszközökkel egy olyan számítógépről, amely csatlakozik a felügyelt tartományhoz.

Ha az Active Directory felügyeleti eszközeit tartományhoz csatlakozó virtuális gépre szeretné telepíteni, hajtsa végre az alábbi lépéseket:

1. Ha **a Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő parancsot.**
1. A **Kiszolgálókezelő** ablak *Irányítópult* ablakában válassza a **Szerepkörök és szolgáltatások hozzáadása**lehetőséget.
1. A *Szerepkörök és szolgáltatások hozzáadása varázsló*Kezdés **előtt** lapján válassza a **Tovább**lehetőséget.
1. A *Telepítés típusmezőben*hagyja bejelölve a **szerepkör- vagy szolgáltatásalapú telepítési** beállítást, és válassza a **Tovább**gombot.
1. A **Kiszolgálókiválasztása** lapon válassza ki az aktuális virtuális gépelemet a kiszolgálókészletből, például *myvm.aaddscontoso.com*, majd válassza a **Tovább**gombot.
1. A **Kiszolgálói szerepkörök** lapon kattintson a **Tovább gombra.**
1. A **Szolgáltatások** lapon bontsa ki a **Távoli kiszolgálófelügyeleti eszközök** csomópontot, majd bontsa ki a **Szerepkör-felügyeleti eszközök** csomópontot.

    Válassza az **AD DS és az AD LDS Tools** szolgáltatást a szerepkör-felügyeleti eszközök listájából, majd válassza a **Tovább**gombot.

    ![Telepítse az "AD DS és AD LDS tools" eszközt a Szolgáltatások lapról](./media/tutorial-create-management-vm/install-features.png)

1. A **Megerősítés** lapon válassza a **Telepítés**lehetőséget. A felügyeleti eszközök telepítése eltarthat egy-két percig.
1. Ha a szolgáltatás telepítése befejeződött, válassza a **Bezárás** lehetőséget a **Szerepkörök és szolgáltatások hozzáadása** varázslóból való kilépéshez.

## <a name="use-active-directory-administrative-tools"></a>Az Active Directory felügyeleti eszközeinek használata

A felügyeleti eszközök telepítése, nézzük meg, hogyan használhatja őket az Azure AD DS felügyelt tartomány felügyeletéhez. Győződjön meg arról, hogy be van jelentkezve a virtuális gép egy felhasználói fiókkal, amely tagja az *AAD DC rendszergazdák* csoport.

1. A **Start** menüben válassza a **Windows felügyeleti eszközök parancsot.** Az előző lépésben telepített AD felügyeleti eszközök listája szerepel.

    ![A kiszolgálón telepített felügyeleti eszközök listája](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Válassza az **Active Directory felügyeleti központ lehetőséget.**
1. Az Azure AD DS felügyelt tartományának megismeréséhez válassza ki a tartománynevet a bal oldali ablaktáblában, például *aaddscontoso.com.* Két *AADDC Computers* és *AADDC Users nevű* tároló található a lista tetején.

    ![Az Azure AD DS felügyelt tartományának elérhető tárolóinak listája](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Az Azure AD DS felügyelt tartományhoz tartozó felhasználók és csoportok megtekintéséhez válassza az **AADDC-felhasználók** tárolót. A felhasználói fiókok és csoportok az Azure AD-bérlő ebben a tárolóban vannak felsorolva.

    A következő példa kimeneti, egy felhasználói fiók nevű *Contoso Admin* és egy csoport *AAD TARTOMÁNYVEZÉRLŐ-rendszergazdák* jelennek meg ebben a tárolóban.

    ![Az Azure AD DS tartományi felhasználóinak listájának megtekintése az Active Directory felügyeleti központban](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Az Azure AD DS felügyelt tartományhoz csatlakozott számítógépek megtekintéséhez válassza az **AADDC számítógépek** tárolót. Az aktuális virtuális gép egy bejegyzése, például a *myVM,* szerepel a listában. Az Azure AD DS felügyelt tartományhoz csatlakozott összes számítógép számítógépfiókjait ebben az *AADDC-számítógépek* tárolóban tárolja.

Az Active Directory felügyeleti központ gyakori műveletei, például a felhasználói fiók jelszavának alaphelyzetbe állítása vagy a csoporttagság kezelése elérhetők. Ezek a műveletek csak az Azure AD DS által felügyelt tartományban közvetlenül létrehozott felhasználók és csoportok számára működnek. Az identitásadatok csak az Azure AD és az Azure AD DS *között* szinkronizálódnak. Nincs visszaírás az Azure AD DS-ből az Azure AD-be. Az Azure AD-ből szinkronizált felhasználók nem módosíthatja a jelszavakat vagy a felügyelt csoporttagságot, és ezeket a módosításokat szinkronizálhatja.

A felügyeleti eszközök részeként telepített *Active Directory-modult*is használhatja az Azure AD DS felügyelt tartományában a gyakori műveletek kezeléséhez.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure AD DS felügyelt tartományában rendelkezésre álló felügyeleti feladatok megismerése
> * Az Active Directory felügyeleti eszközeinek telepítése Windows Server virtuális gépre
> * Gyakori feladatok végrehajtása az Active Directory felügyeleti központ használatával

Az Azure AD DS felügyelt tartományának biztonságos kezeléséhez engedélyezze a biztonságos Lightweight Directory Access Protocol (LDAPS) protokollt.

> [!div class="nextstepaction"]
> [Biztonságos LDAP konfigurálása a felügyelt tartományhoz](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
