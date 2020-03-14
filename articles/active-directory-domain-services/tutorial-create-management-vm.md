---
title: Oktatóanyag – felügyeleti virtuális gép létrehozása a Azure Active Directory Domain Serviceshoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy Windows rendszerű virtuális gépet, amelyet a Azure Active Directory Domain Services-példány felügyeletéhez használ.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: f422d1dd6c76d78448ae4fb1012a5dae8d6108b3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239139"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Oktatóanyag: felügyeleti virtuális gép létrehozása Azure Active Directory Domain Services felügyelt tartomány konfigurálásához és felügyeletéhez

A Azure Active Directory Domain Services (AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP és a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezt a felügyelt tartományt ugyanazzal a Távoli kiszolgálófelügyelet eszközei (RSAT) felügyelheti, mint a helyszíni Active Directory tartományi szolgáltatások tartománnyal. Mivel az Azure AD DS felügyelt szolgáltatás, bizonyos adminisztratív feladatok nem hajthatók végre, például a távoli asztal protokoll (RDP) használata a tartományvezérlőkre való kapcsolódáshoz.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows Server rendszerű virtuális gépet az Azure-ban, és hogyan telepítheti az Azure AD DS felügyelt tartomány felügyeletéhez szükséges eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure AD DS felügyelt tartományában elérhető felügyeleti feladatok ismertetése
> * A Active Directory felügyeleti eszközök telepítése Windows Server rendszerű virtuális gépen
> * Általános feladatok elvégzése a Active Directory felügyeleti központ használatával

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, tekintse meg az első oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server-alapú virtuális gép.
    * Ha szükséges, tekintse meg az előző oktatóanyagot [egy Windows Server rendszerű virtuális gép létrehozásához és egy felügyelt tartományhoz való csatlakoztatásához][create-join-windows-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.
* Az Azure-beli AD DS virtuális hálózatban üzembe helyezett Azure Bastion-gazdagép.
    * Ha szükséges, [hozzon létre egy Azure Bastion-gazdagépet][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban egy felügyeleti virtuális gépet hoz létre és konfigurál a Azure Portal használatával. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Elérhető felügyeleti feladatok az Azure AD DS

Az Azure AD DS felügyelt tartományt biztosít a felhasználók, az alkalmazások és a felhasznált szolgáltatások számára. Ez a megközelítés módosítja az elérhető felügyeleti feladatok némelyikét, és azt, hogy milyen jogosultságokkal rendelkezik a felügyelt tartományon belül. Ezek a feladatok és engedélyek eltérőek lehetnek, mint a hagyományos helyszíni Active Directory tartományi szolgáltatások környezettel kapcsolatos tapasztalatok. A Távoli asztal használatával nem tud csatlakozni az Azure AD DS felügyelt tartományhoz tartozó tartományvezérlőkön is.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS felügyelt tartományon végrehajtható felügyeleti feladatok

Az *HRE DC-rendszergazdák* csoport tagjai jogosultságokat kapnak az Azure AD DS felügyelt tartományon, amely lehetővé teszi, hogy a következő feladatokat hajtsák végre:

* Konfigurálja a beépített csoportházirend-objektumot (GPO) a *AADDC számítógépekhez* és a *AADDC felhasználói* tárolókat a felügyelt tartományban.
* DNS-kezelés a felügyelt tartományon.
* Egyéni szervezeti egységeket (OU-ket) hozhat létre és felügyelhet a felügyelt tartományon.
* Felügyeleti hozzáférés szerzése a felügyelt tartományhoz csatlakoztatott számítógépekhez.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Az Azure AD DS felügyelt tartományában nem szereplő rendszergazdai jogosultságok

Az Azure AD DS felügyelt tartománya le van zárva, így nincs jogosultsága bizonyos rendszergazdai feladatok végrehajtásához a tartományban. A következő példák némelyike nem hajtható végre:

* Bővítse a felügyelt tartomány sémáját.
* Kapcsolódjon a felügyelt tartományhoz tartozó tartományvezérlőkhöz a Távoli asztal használatával.
* Tartományvezérlők hozzáadása a felügyelt tartományhoz.
* Nem rendelkezik *tartományi rendszergazdai* vagy *vállalati rendszergazdai* jogosultsággal a felügyelt tartományhoz.

## <a name="sign-in-to-the-windows-server-vm"></a>Bejelentkezés a Windows Server rendszerű virtuális gépre

Az előző oktatóanyagban egy Windows Server rendszerű virtuális gép lett létrehozva és csatlakoztatva az Azure AD DS felügyelt tartományhoz. Ezzel a virtuális géppel telepítheti a felügyeleti eszközöket. Ha szükséges, [kövesse az oktatóanyag lépéseit a Windows Server rendszerű virtuális gépek felügyelt tartományhoz való létrehozásához és csatlakoztatásához][create-join-windows-vm].

> [!NOTE]
> Ebben az oktatóanyagban egy olyan Windows Server rendszerű virtuális gépet használ az Azure-ban, amely csatlakozik az Azure AD DS felügyelt tartományhoz. A felügyelt tartományhoz csatlakoztatott Windows-ügyfelet, például a Windows 10 rendszert is használhatja.
>
> A felügyeleti eszközök Windows-ügyfélre történő telepítésével kapcsolatos további információkért lásd: [install Távoli kiszolgálófelügyelet eszközei (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Első lépésként kapcsolódjon a Windows Server rendszerű virtuális géphez a következő módon:

1. A Azure Portal válassza az **erőforráscsoportok** lehetőséget a bal oldalon. Válassza ki azt az erőforráscsoportot, amelyben a virtuális gép létrejött, például *myResourceGroup*, majd válassza ki a virtuális gépet, például *myVM*.
1. A virtuális gép **Áttekintés** paneljén válassza a **kapcsolat**, majd a **Bastion**lehetőséget.

    ![Kapcsolódás Windows rendszerű virtuális géphez a Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Adja meg a virtuális gép hitelesítő adatait, majd válassza a **kapcsolat**lehetőséget.

   ![Kapcsolódjon a megerősített gazdagépen a Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

Ha szükséges, engedélyezze a webböngésző számára az előugró ablak megnyitását a megerősített kapcsolat megjelenítéséhez. A virtuális géphez való kapcsolódás elvégzése néhány másodpercig tart.

## <a name="install-active-directory-administrative-tools"></a>Active Directory felügyeleti eszközök telepítése

Az Azure AD DS felügyelt tartományait ugyanazok a felügyeleti eszközök használják, mint a helyszíni AD DS környezetek, például a Active Directory felügyeleti központ (ADAC) vagy az AD PowerShell. Ezek az eszközök a Windows Serveren és az ügyfélszámítógépeken is telepíthetők a Távoli kiszolgálófelügyelet eszközei (RSAT) szolgáltatás részeként. Az *HRE DC-rendszergazdák* csoport tagjai ezt követően távolról felügyelhetik az Azure AD DS felügyelt tartományokat a felügyelt tartományhoz csatlakoztatott számítógépekről az ad felügyeleti eszközök használatával.

A Active Directory felügyeleti eszközök tartományhoz csatlakoztatott virtuális gépekre való telepítéséhez hajtsa végre a következő lépéseket:

1. Ha a **Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő**elemet.
1. A **Kiszolgálókezelő** ablak *irányítópult* paneljén válassza a **szerepkörök és szolgáltatások hozzáadása**lehetőséget.
1. A *szerepkörök és szolgáltatások hozzáadása varázsló*alapismeretek **lapján kattintson a** **Tovább gombra**.
1. A *telepítés típusa*beállításnál hagyja bejelölve a **szerepköralapú vagy a szolgáltatáson alapuló telepítési** beállítást, majd kattintson a **Tovább gombra**.
1. A **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgáló készletéből, például *myvm.aaddscontoso.com*, majd kattintson a **tovább**gombra.
1. A **kiszolgálói szerepkörök** lapon kattintson a **tovább**gombra.
1. A **szolgáltatások** lapon bontsa ki a **Távoli kiszolgálófelügyelet eszközei** csomópontot, majd bontsa ki a **szerepkör-felügyeleti eszközök** csomópontot.

    Válassza a **AD DS és AD LDS eszközök** lehetőséget a szerepkör-felügyeleti eszközök listájából, majd kattintson a **tovább**gombra.

    ![A "AD DS és AD LDS eszközök" telepítése a szolgáltatások lapról](./media/tutorial-create-management-vm/install-features.png)

1. A **jóváhagyás** lapon válassza a **telepítés**lehetőséget. A felügyeleti eszközök telepítéséhez egy-két percet is igénybe vehet.
1. A szolgáltatás telepítésének befejezése után a **Bezárás** gombra kattintva lépjen ki a **szerepkörök és szolgáltatások hozzáadása** varázslóból.

## <a name="use-active-directory-administrative-tools"></a>Active Directory felügyeleti eszközök használata

Ha a felügyeleti eszközök telepítve vannak, lássuk, hogyan használhatja őket az Azure AD DS felügyelt tartomány felügyeletére. Győződjön meg arról, hogy bejelentkezett a virtuális gépre egy olyan felhasználói fiókkal, amely tagja az *HRE DC-rendszergazdák* csoportnak.

1. A **Start** menüben válassza a **Windows felügyeleti eszközök**elemet. Az előző lépésben telepített AD felügyeleti eszközök szerepelnek a felsorolásban.

    ![A kiszolgálóra telepített felügyeleti eszközök listája](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Válassza a **Active Directory felügyeleti központ**lehetőséget.
1. Az Azure AD DS felügyelt tartomány megismeréséhez válassza ki a tartománynevet a bal oldali ablaktáblán, például *aaddscontoso.com*. A *AADDC számítógépek* és a *AADDC-felhasználók* nevű tárolók a lista elejére kerülnek.

    ![Az Azure AD DS felügyelt tartomány elérhető tárolóinak listázása](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Az Azure AD DS felügyelt tartományhoz tartozó felhasználók és csoportok megtekintéséhez válassza a **AADDC Users** tárolót. Az Azure AD-bérlőből származó felhasználói fiókok és csoportok szerepelnek ebben a tárolóban.

    A következő példában a tárolóban megjelenik egy *contoso admin* nevű felhasználói fiók és egy *HRE DC-rendszergazdák* csoportja.

    ![Az Azure AD DS tartományi felhasználók listájának megtekintése a Active Directory felügyeleti központ](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Az Azure AD DS felügyelt tartományhoz csatlakoztatott számítógépek megtekintéséhez válassza a **AADDC Computers** tárolót. Megjelenik egy bejegyzés az aktuális virtuális géphez (például *myVM*). Az Azure AD DS felügyelt tartományhoz csatlakoztatott összes számítógép számítógépfiókja a *AADDC Computers* tárolóban történik.

Az általános Active Directory felügyeleti központ műveletek, például a felhasználói fiók jelszavának alaphelyzetbe állítása vagy a csoporttagság kezelése elérhető. Ezek a műveletek csak az Azure AD DS felügyelt tartományában közvetlenül létrehozott felhasználók és csoportok esetében működnek. Az azonosító adatok csak az Azure AD- *ről* az Azure AD DS-ra szinkronizálhatók. Az Azure AD DSról az Azure AD-re nem írható vissza. Az Azure AD-ből szinkronizált felhasználók jelszavait vagy a felügyelt csoporttagság nem módosítható, és a módosítások szinkronizálva lesznek.

A felügyeleti eszközök részeként telepített *Windows PowerShell Active Directory modulját*is használhatja az Azure AD DS felügyelt tartomány általános műveleteinek kezeléséhez.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure AD DS felügyelt tartományában elérhető felügyeleti feladatok ismertetése
> * A Active Directory felügyeleti eszközök telepítése Windows Server rendszerű virtuális gépen
> * Általános feladatok elvégzése a Active Directory felügyeleti központ használatával

Az Azure AD DS felügyelt tartománnyal való biztonságos interakcióhoz engedélyezze a Secure Lightweight Directory Access Protocol (LDAPs) szolgáltatást.

> [!div class="nextstepaction"]
> [Biztonságos LDAP konfigurálása a felügyelt tartományhoz](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
