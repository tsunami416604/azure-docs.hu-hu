---
title: Csoportházirend létrehozása és kezelése az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogyan szerkesztheti a beépített csoportházirend-objektumokat, és hogyan hozhat létre saját egyéni szabályzatokat egy Azure Active Directory tartományi szolgáltatások által felügyelt tartományban.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: bce71355eef19ec3cc85525033274f57b1a3e0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946409"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Csoportházirend felügyelete Egy Azure AD tartományi szolgáltatások által kezelt tartományban

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felhasználói és számítógép-objektumainak beállításait gyakran csoportházirend-objektumok (GSo-k) használatával kezelik. Az Azure AD DS beépített csoportházirend-azonosítókat tartalmaz az *AADDC-felhasználók* és az *AADDC-számítógépek tárolókhoz.* Ezeket a beépített csoportházirend-csoportokat testreszabhatja a csoportházirend szükség szerint történő konfigurálásához. Az *Azure AD DC rendszergazdák csoportjának* tagjai csoportházirend-felügyeleti jogosultságokkal rendelkeznek az Azure AD DS-tartományban, és egyéni csoportházirend-azonosítókat és szervezeti egységeket is létrehozhatnak. A csoportházirendről és annak működéséről a [Csoportházirend áttekintése][group-policy-overview]című témakörben talál további információt.

Hibrid környezetben a helyszíni Activeád-ds-környezetben konfigurált csoportházirendek nincsenek szinkronizálva az Azure AD DS-sel. A felhasználók vagy számítógépek konfigurációs beállításainak definiálásához az Azure AD DS-ben, szerkesztse az egyik alapértelmezett csoportházirend-azonosítót, vagy hozzon létre egy egyéni csoportházirend-jámfort.

Ez a cikk bemutatja, hogyan telepítheti a Csoportházirend-kezelési eszközöket, majd szerkesztheti a beépített csoportházirend-csoportokat, és egyéni csoportházirend-csoportokat hozhat létre.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
    * Szükség esetén végezze el az oktatóanyagot [a Windows Server virtuális gép létrehozásához, és csatlakozzon egy felügyelt tartományhoz.][create-join-windows-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.

> [!NOTE]
> A csoportházirend felügyeleti sablonjait úgy használhatja, hogy az új sablonokat a felügyeleti munkaállomásra másolja. Másolja az *.admx* fájlokat a `%SYSTEMROOT%\PolicyDefinitions` területi *.adml* `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`fájlokba, és másolja a programba, ahol `Language-CountryRegion` megegyezik az *.adml* fájlok nyelvével és régiójával.
>
> Másolja például az *.adml* fájlok angol,Egyesült Államok `\en-us` beli verzióját a mappába.
>
> Azt is megteheti, hogy központilag tárolja a csoportházirend felügyeleti sablonaz Azure AD DS felügyelt tartomány részét képezi tartományvezérlőkön. További információt a [Csoportházirend felügyeleti sablonjainak központi áruházának létrehozása és kezelése a Windows rendszerben](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)című témakörben talál.

## <a name="install-group-policy-management-tools"></a>Csoportházirend-kezelő eszközök telepítése

A csoportházirend-objektum (GSO-k) létrehozásához és konfigurálásához telepítenie kell a Csoportházirend-kezelő eszközöket. Ezek az eszközök a Windows Server szolgáltatásaként telepíthetők. A felügyeleti eszközök Windows-ügyfélre történő telepítéséről a [Távoli kiszolgálófelügyeleti eszközök (RSAT)][install-rsat]telepítése című témakörben talál további információt.

1. Jelentkezzen be a felügyeleti virtuális gép. Az Azure Portal használatával való csatlakozáslépéseiről a [Csatlakozás Windows Server virtuális géphez][connect-windows-server-vm]című témakörben található.
1. **A Kiszolgálókezelőnek** alapértelmezés szerint meg kell nyitnia, amikor bejelentkezik a virtuális gépre. Ha nem, válassza a **Start** menü **Kiszolgálókezelő parancsát.**
1. A **Kiszolgálókezelő** ablak *Irányítópult* ablakában válassza a **Szerepkörök és szolgáltatások hozzáadása**lehetőséget.
1. A *Szerepkörök és szolgáltatások hozzáadása varázsló*Kezdés **előtt** lapján válassza a **Tovább**lehetőséget.
1. A *Telepítés típusmezőben*hagyja bejelölve a **szerepkör- vagy szolgáltatásalapú telepítési** beállítást, és válassza a **Tovább**gombot.
1. A **Kiszolgálókiválasztása** lapon válassza ki az aktuális virtuális gépelemet a kiszolgálókészletből, például *myvm.aaddscontoso.com*, majd válassza a **Tovább**gombot.
1. A **Kiszolgálói szerepkörök** lapon kattintson a **Tovább gombra.**
1. A **Szolgáltatások** lapon válassza a **Csoportházirend kezelése** szolgáltatást.

    ![Telepítse a "Csoportházirend kezelése" a Szolgáltatások lapról](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. A **Megerősítés** lapon válassza a **Telepítés**lehetőséget. A Csoportházirend-kezelési eszközök telepítése eltarthat egy-két percig.
1. Ha a szolgáltatás telepítése befejeződött, válassza a **Bezárás** lehetőséget a **Szerepkörök és szolgáltatások hozzáadása** varázslóból való kilépéshez.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>A Csoportházirend kezelése konzol megnyitása és objektum szerkesztése

Alapértelmezett csoportházirend-objektumok (CSOPORTházirend-objektumok) léteznek a felhasználók és a számítógépek egy Azure AD DS felügyelt tartományban. Ha a Csoportházirend kezelése szolgáltatás telepítve van az előző szakaszból, tekintsünk meg és szerkesszünk egy meglévő csoportházirend-jámfort. A következő szakaszban egyéni csoportházirend-szolgáltatót hoz létre.

> [!NOTE]
> A csoportházirend felügyeletéhez egy Azure AD DS felügyelt tartományban, be kell jelentkeznie egy felhasználói fiókba, amely tagja az *AAD DC rendszergazdák* csoportnak.

1. A kezdőképernyőn válassza a **Felügyeleti eszközök lehetőséget.** Megjelenik az elérhető felügyeleti eszközök listája, beleértve az előző szakaszban telepített **Csoportházirend-kezelést** is.
1. A Csoportházirend kezelése konzol (GPMC) megnyitásához válassza a **Csoportházirend kezelése**lehetőséget.

    ![A Csoportházirend kezelése konzol készen áll a csoportházirend-objektumok szerkesztésére](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Az Azure AD DS által felügyelt tartományban két beépített csoportházirend-objektum (GSo) található – az egyik az *AADDC computers* tárolóhoz, a másik pedig az *AADDC-felhasználók* tárolóhoz tartozik. Ezeket a csoportházirend-ok testreszabásához konfigurálhatja a csoportházirendet szükség szerint az Azure AD DS felügyelt tartományon belül.

1. A **Csoportházirend kezelése** konzolon bontsa ki az **Erdő: aaddscontoso.com** csomópontot. Ezután **bontsa** ki a Tartományok csomópontokat.

    Az *AADDC számítógépek* és az *AADDC-felhasználók*számára két beépített tároló létezik. Ezek a tárolók mindegyike egy alapértelmezett csoportházirend-ormány t alkalmazva.

    ![Az alapértelmezett "AADDC számítógépek" és "AADDC-felhasználók" tárolókra alkalmazott beépített csoportházirend-kezelők](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Ezek a beépített csoportházirend-kezelők testreszabhatók az Azure AD DS felügyelt tartományának adott csoportszabályzatának konfigurálásához. Válassza ki jobbra az egyik csoportházirend-csoportházirend-csoport rendszert, például az *AADDC Computers csoportházirend-ormát,* majd válassza **a Szerkesztés... parancsot.**

    ![Válassza ki a "Szerkesztés" lehetőséget az egyik beépített csoportházirend-szervezet számára](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. A Csoportházirend-kezelés szerkesztője eszköz lehetővé teszi a csoportházirend-objektum testreszabását, például *a fiókházirendek*:

    ![A csoportházirend-kiszolgáló testreszabása a beállítások szükség szerint való konfigurálásához](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Ha végzett, a házirend mentéséhez válassza **a Fájl > mentés i.** A számítógépek alapértelmezés szerint 90 percenként frissítik a csoportházirendet, és alkalmazzák a módosításokat.

## <a name="create-a-custom-group-policy-object"></a>Egyéni csoportházirend-objektum létrehozása

Hasonló házirend-beállítások csoportosításához gyakran hoz létre további csoportházirend-csoportokat ahelyett, hogy az összes szükséges beállítást alkalmazna az egyetlen, alapértelmezett csoportházirend-rendszerben. Az Azure AD DS segítségével létrehozhatja vagy importálhatja saját egyéni csoportházirend-objektumait, és összekapcsolhatja őket egy egyéni szervezeti egységhez. Ha először létre kell hoznia egy egyéni szervezeti egységet, olvassa [el az Egyéni szervezeti egység létrehozása az Azure AD DS felügyelt tartományában című témakört.](create-ou.md)

1. A **Csoportházirend kezelése** konzolon válassza ki az egyéni szervezeti egységet( OU), például a *MyCustomOU-t.* Válassza ki a jobb gombot, és válassza **a Csoportházirend-kiszolgáló létrehozása lehetőséget ebben a tartományban, és itt kapcsolja össze...**:

    ![Egyéni csoportházirend-kiszolgáló létrehozása a Csoportházirend kezelése konzolon](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Adja meg az új csoportházirend-csoportházirend-orvának a nevét, például *az Egyéni csoportházirend-csoportházirend-csoport házirendje,* majd kattintson az **OK gombra.** Ezt az egyéni csoportházirend-ormányt egy meglévő csoportházirend-csoportházirend-szolgáltatóra és házirend-beállításokra is alapozhatja.

    ![Az új egyéni gpo nevének megadása](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Az egyéni gpo jön létre, és kapcsolódik az egyéni szervezeti egység. A házirend-beállítások konfigurálásához válassza ki a jobb gombot az egyéni csoportházirend-szolgáltatóval, és válassza a **Szerkesztés... parancsot:**

    ![Válassza ki az egyéni házirend-eszköz házirend-eszközének "szerkesztése" lehetőséget](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. A **Csoportházirend kezelése szerkesztő** vel testre szabhatja a csoportházirend-objektumot:

    ![A csoportházirend-kiszolgáló testreszabása a beállítások szükség szerint való konfigurálásához](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Ha végzett, a házirend mentéséhez válassza **a Fájl > mentés i.** A számítógépek alapértelmezés szerint 90 percenként frissítik a csoportházirendet, és alkalmazzák a módosításokat.

## <a name="next-steps"></a>További lépések

A Csoportházirend kezelése konzollal konfigurálható csoportházirend-beállításokról a [Csoportházirend-beállításelemek használata][group-policy-console]című témakörben olvashat bővebben.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
