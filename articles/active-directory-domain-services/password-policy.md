---
title: Jelszóházirendek létrehozása és használata az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogyan és miért használhat részletes jelszóházirendeket a fiókjelszavak védelméhez és vezérléséhez egy Azure AD DS által felügyelt tartományban.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: cc1863ff4200ee4cca94f18e3adfffa7ea42db96
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475947"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Jelszó- és fiókzárolási házirendek felügyelt tartományokon

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felhasználói biztonságának kezeléséhez részletes jelszóházirendeket határozhat meg, amelyek szabályozzák a fiókzárolási beállításokat vagy a jelszó minimális hosszát és összetettségét. Az alapértelmezett részletes jelszóházirend jön létre, és egy Azure AD DS felügyelt tartomány összes felhasználója számára vonatkozik. A részletes vezérlés és az adott üzleti vagy megfelelőségi igények kielégítése érdekében további szabályzatok hozhatók létre és alkalmazhatók a felhasználók meghatározott csoportjaira.

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy részletes jelszóházirendet az Azure AD DS-ben az Active Directory felügyeleti központ használatával.

> [!NOTE]
> A jelszóházirendek csak az Azure AD DS által felügyelt tartományokhoz érhetők el, amelyeket az Erőforrás-kezelő telepítési modelljével hoztak létre. A Klasszikus használatával létrehozott régebbi felügyelt tartományok esetében [telepítse át a klasszikus virtuális hálózati modellről az Erőforrás-kezelőre.][migrate-from-classic]

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
  * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
  * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
  * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
  * Az Azure AD DS-példányt az Erőforrás-kezelő telepítési modelljével kell létrehozni. Szükség esetén [az Áttelepítés a klasszikus virtuális hálózati modellről az Erőforrás-kezelőbe.][migrate-from-classic]
* Az Azure AD DS felügyelt tartományához csatlakozott Windows Server felügyeleti virtuális gép.
  * Szükség esetén fejezze be az [oktatóanyagot egy felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]
* Egy felhasználói fiók, amely az *Azure AD DC rendszergazdák* csoportjának tagja az Azure AD-bérlőben.

## <a name="default-password-policy-settings"></a>Alapértelmezett jelszóházirend-beállítások

A részletes jelszóházirendek (FGPP-k) lehetővé teszik, hogy a tartomány különböző felhasználóira speciális korlátozásokat alkalmazzon a jelszó- és fiókzárolási házirendekre vonatkozóan. Például a kiemelt jogosultságú fiókok biztonságossá tétele szigorúbb fiókzárolási beállításokat alkalmazhat, mint a hagyományos, nem kiemelt jogosultságú fiókok. Több FGPPs egy Azure AD DS felügyelt tartományban, és adja meg a prioritási sorrendet, hogy alkalmazza őket a felhasználókra.

A szabályzatok egy Azure AD DS által felügyelt tartományban lévő csoporttársításon keresztül kerülnek elosztásra, és a módosítások a következő felhasználó bejelentkezéskor lesznek alkalmazva. A házirend módosítása nem oldja fel a már zárolt felhasználói fiókot.

A jelszóházirendek kissé eltérően viselkednek attól függően, hogy a felhasználói fiók, amelyre alkalmazzák őket, hogyan lett létrehozva. Az Azure AD DS-ben kétféleképpen hozható létre egy felhasználói fiók:

* A felhasználói fiók szinkronizálható az Azure AD-ből. Ez magában foglalja a közvetlenül az Azure-ban létrehozott csak felhőalapú felhasználói fiókokat, valamint a helyszíni AD DS-környezetből az Azure AD Connect használatával szinkronizált hibrid felhasználói fiókokat.
    * Az Azure AD DS felhasználói fiókjainak többsége az Azure AD szinkronizálási folyamatán keresztül jön létre.
* A felhasználói fiók manuálisan létrehozható egy Azure AD DS felügyelt tartományban, és nem létezik az Azure AD-ben.

Minden felhasználó, függetlenül attól, hogy hogyan jönnek létre, rendelkezik a következő fiókzárolási szabályzatok az azure-beli AD DS alapértelmezett jelszóházirendje által alkalmazva:

* **Fiókzárolás időtartama:** 30
* **Az engedélyezett sikertelen bejelentkezési kísérletek száma:** 5
* **A sikertelen bejelentkezési kísérletek visszaállítása a következő:** 30 perc után
* **A jelszó maximális életkora (élettartam):** 90 nap

Ezekkel az alapértelmezett beállításokkal a felhasználói fiókok 30 percre zárolva lesznek, ha 2 percen belül öt érvénytelen jelszót használ. A fiókok zárolása 30 perc elteltével automatikusan feloldódik.

A fiókzárolások csak a felügyelt tartományon belül történnek. A felhasználói fiókok csak az Azure AD DS-ben vannak zárolva, és csak a felügyelt tartomány elleni sikertelen bejelentkezési kísérletek miatt. Az Azure AD-ből vagy a helyszíni felhasználókból szinkronizált felhasználói fiókok nincsenek zárolva a forráskönyvtárakban, csak az Azure AD DS-ben.

Ha rendelkezik egy Azure AD jelszószabályzat, amely megadja a jelszó maximális életkora nagyobb, mint 90 nap, a jelszó kor az Azure AD DS alapértelmezett házirend je. Egyéni jelszóházirend konfigurálásával az Azure AD DS-ben egy másik maximális jelszókor definiálása. Legyen óvatos, ha egy Azure AD DS jelszóházirendben rövidebb maximális jelszókort konfigurált, mint az Azure AD-ben vagy egy helyszíni AD DS-környezetben. Ebben a forgatókönyvben a felhasználó jelszava lejár az Azure AD DS-ben, mielőtt a rendszer kéri, hogy módosítsa az Azure AD vagy egy helyszíni AD DS-környezetben.

Az Azure AD DS felügyelt tartományban manuálisan létrehozott felhasználói fiókok esetében a következő további jelszóbeállítások at az alapértelmezett házirend is alkalmazza. Ezek a beállítások nem vonatkoznak az Azure AD-ből szinkronizált felhasználói fiókokra, mivel a felhasználó nem frissítheti jelszavát közvetlenül az Azure AD DS-ben.

* **Minimális jelszóhossz (karakterek):** 7
* **A jelszavaknak meg kell felelniük az összetettségi követelményeknek**

Az alapértelmezett jelszóházirendben nem módosíthatja a fiókzárolási vagy jelszóbeállításokat. Ehelyett az *AAD tartományvezérlő-rendszergazdák* csoport tagjai egyéni jelszóházirendeket hozhatnak létre, és úgy konfigurálhatják, hogy felülírják (elsőbbséget élvezzenek) az alapértelmezett beépített házirenden, ahogy az a következő szakaszban látható.

## <a name="create-a-custom-password-policy"></a>Egyéni jelszóházirend létrehozása

Az Azure-beli alkalmazások létrehozása és futtatása során érdemes lehet egyéni jelszóházirendet konfigurálni. Létrehozhat például egy házirendet a fiókzárolási házirend különböző beállításainak beállításához.

Egyéni jelszó szabályzatok az Azure AD DS felügyelt tartományban lévő csoportokra vonatkoznak. Ez a konfiguráció hatékonyan felülbírálja az alapértelmezett házirendet.

Egyéni jelszóházirend létrehozásához használja az Active Directory felügyeleti eszközöket egy tartományhoz csatlakozó virtuális gépről. Az Active Directory felügyeleti központ lehetővé teszi, hogy egy Azure AD DS által felügyelt tartományban tekintsen, szerkesszen és hozzon létre erőforrásokat, beleértve a saját erőforrásokat is.

> [!NOTE]
> Egyéni jelszóházirend létrehozásához egy Azure AD DS felügyelt tartományban, be kell jelentkeznie egy felhasználói fiókba, amely az *AAD DC rendszergazdák* csoport tagja.

1. A kezdőképernyőn válassza a **Felügyeleti eszközök lehetőséget.** Megjelenik az oktatóanyagban telepített elérhető felügyeleti eszközök listája a [felügyeleti virtuális gép létrehozásához.][tutorial-create-management-vm]
1. A felelős személyek létrehozásához és kezeléséhez válassza az **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblában válassza ki az Azure AD DS felügyelt tartományát, például *a aaddscontoso.com.*
1. Nyissa **meg** a Rendszer tárolót, majd a **Jelszóbeállítások tárolót**.

    Az Azure AD DS felügyelt tartomány beépített jelszóházirendje jelenik meg. Ez a beépített házirend nem módosítható. Ehelyett hozzon létre egy egyéni jelszóházirendet az alapértelmezett házirend felülbírálásához.

    ![Jelszóházirend létrehozása az Active Directory felügyeleti központban](./media/password-policy/create-password-policy-adac.png)

1. A jobb oldali **Feladatok** panelen válassza az **Új > jelszóbeállítások lehetőséget.**
1. A **Jelszóbeállítások létrehozása** párbeszédpanelen adja meg a házirend nevét, például *A MyCustomFGPP*.
1. Ha több jelszóházirend létezik, a rendszer a legmagasabb prioritású vagy prioritású házirendet alkalmazza a felhasználóra. Minél alacsonyabb a szám, annál magasabb a prioritás. Az alapértelmezett jelszóházirend prioritása *200*.

    Állítsa be az egyéni jelszóházirend elsőbbségét az alapértelmezett , például *1*felülbírálásához.

1. Szükség szerint szerkesztheti a jelszóházirend egyéb beállításait. Ne feledje a következő kulcsfontosságú pontokat:

    * Beállítások, például a jelszó összetettsége, kora vagy lejárati ideje csak az Azure AD DS felügyelt tartományban manuálisan létrehozott felhasználók számára.
    * A fiókzárolási beállítások minden felhasználóra vonatkoznak, de csak a felügyelt tartományon belül lépnek érvénybe, és nem magában az Azure AD-ben.

    ![Egyéni részletes jelszóházirend létrehozása](./media/password-policy/custom-fgpp.png)

1. Törölje a jelet **A véletlen törlés elleni védelem**. Ha ez a beállítás be van jelölve, nem menthető az FGPP.
1. A **Közvetlenül alkalmazandó** csoportban válassza a **Hozzáadás** gombot. A **Felhasználók vagy csoportok kiválasztása** párbeszédpanelen kattintson a **Helyek** gombra.

    ![Válassza ki azokat a felhasználókat és csoportokat, akikre alkalmazni szeretné a jelszóházirendet.](./media/password-policy/fgpp-applies-to.png)

1. A jelszóházirendek csak csoportokra alkalmazhatók. A **Helyek** párbeszédpanelen bontsa ki a tartománynevet, például *a aaddscontoso.com*, majd jelöljön ki egy szervezeti egységet, például **az AADDC-felhasználók at.** Ha egyéni szervezeti egysége olyan felhasználói csoportot tartalmaz, amelyet alkalmazni szeretne, jelölje ki azt a szervezeti egységet.

    ![Válassza ki azt a szervezeti egységet, amelyhez a csoport tartozik.](./media/password-policy/fgpp-container.png)

1. Írja be annak a csoportnak a nevét, amelyre alkalmazni szeretné a házirendet, majd válassza a **Nevek ellenőrzése** lehetőséget a csoport létezésének ellenőrzéséhez.

    ![Az FGPP alkalmazásához kívánt csoport keresése és kiválasztása](./media/password-policy/fgpp-apply-group.png)

1. A **közvetlenül alkalmazandó** csoportban most kiválasztott csoport nevével válassza az **OK** gombot az egyéni jelszóházirend mentéséhez.

## <a name="next-steps"></a>További lépések

A jelszóházirendekről és az Active Directory felügyeleti központ használatáról az alábbi cikkekben talál további információt:

* [További információ a részletes jelszóházirendekről](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Részletes jelszóházirendek konfigurálása az AD felügyeleti központ használatával](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
