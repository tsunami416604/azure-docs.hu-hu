---
title: Jelszavas szabályzatok létrehozása és használata a Azure AD Domain Servicesban | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogyan és miért érdemes részletes jelszóházirendek használatával védeni és vezérelni a fiókok jelszavait egy Azure AD DS felügyelt tartományban.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: b08c3854ef330081b4c55331cb410c5925f00dec
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512759"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Jelszó-és fiókzárolási házirendek a felügyelt tartományokban

A Azure Active Directory Domain Services (Azure AD DS) felhasználói biztonságának kezeléséhez olyan részletes jelszóházirendek is megadhatók, amelyek a fiókzárolás beállításait vagy a jelszó minimális hosszát és összetettségét vezérlik. A rendszer az Azure AD DS felügyelt tartomány összes felhasználójára vonatkozóan létrehoz és alkalmaz egy alapértelmezett részletes jelszóházirendek-házirendet. A részletes szabályozás biztosításához és adott üzleti vagy megfelelőségi igények kielégítéséhez további szabályzatok hozhatók létre és alkalmazhatók a felhasználók bizonyos csoportjaira.

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat részletes jelszóházirendek az Azure-AD DS a Active Directory felügyeleti központ használatával.

> [!NOTE]
> A jelszóházirend csak a Resource Manager-alapú üzemi modell használatával létrehozott Azure AD DS felügyelt tartományokhoz érhető el. A klasszikus használatával létrehozott régebbi felügyelt tartományok esetében [telepítse át a klasszikus virtuális hálózati modellből a Resource Managerbe][migrate-from-classic].

## <a name="before-you-begin"></a>Előzetes teendők

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
  * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
  * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
  * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
  * Az Azure AD DS-példányt a Resource Manager-alapú üzemi modell használatával kell létrehozni. Szükség esetén [telepítse át a klasszikus virtuális hálózati modellből a Resource Managerbe][migrate-from-classic].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
  * Ha szükséges, fejezze be az oktatóanyagot [egy felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="default-password-policy-settings"></a>Alapértelmezett jelszóházirend-beállítások

A részletes jelszóházirendek (Fgpp-EK) segítségével meghatározott korlátozásokat alkalmazhat a jelszó-és fiókzárolási házirendek számára a tartomány különböző felhasználói számára. A Kiemelt jogosultságú fiókok biztonságossá tételéhez például szigorúbb fiókzárolási beállításokat alkalmazhat, mint a normál, nem Kiemelt fiókok. Több Fgpp is létrehozhat egy Azure AD DS felügyelt tartományon belül, és meghatározhatja a prioritási sorrendet, hogy azok a felhasználókra vonatkozzanak.

A házirendek elosztása egy Azure AD DS felügyelt tartományon keresztül történik, és az elvégzett módosítások a következő felhasználói bejelentkezéskor lesznek alkalmazva. A házirend módosítása nem oldja fel a már kizárt felhasználói fiók zárolását.

A jelszóházirend egy kicsit másképp viselkedik attól függően, hogy a felhasználói fiók hogyan lett létrehozva. Az Azure AD DS kétféleképpen hozhat létre felhasználói fiókot:

* A felhasználói fiók szinkronizálható az Azure AD-ből. Ez magában foglalja a közvetlenül az Azure-ban létrehozott felhőalapú felhasználói fiókokat, valamint a helyi AD DS környezetből Azure AD Connect használatával szinkronizált hibrid felhasználói fiókokat.
    * Az Azure AD DS felhasználói fiókjainak többsége az Azure AD szinkronizálási folyamatán keresztül jön létre.
* A felhasználói fiók manuálisan is létrehozható egy Azure AD DS felügyelt tartományban, és nem létezik az Azure AD-ben.

Minden felhasználó, függetlenül attól, hogy ezek hogyan jönnek létre, a következő fiókzárolási házirendeket alkalmazza az alapértelmezett jelszóházirend az Azure AD DSban:

* Fiókzárolás **időtartama:** 30
* **Sikertelen bejelentkezési kísérletek száma engedélyezett:** 5
* **Sikertelen bejelentkezési kísérletek számának visszaállítása ennyi idő után:** 30 perc
* **Jelszó maximális kora (élettartam):** 90 nap

Ezekkel az alapértelmezett beállításokkal a felhasználói fiókok 30 percig vannak kizárva, ha az öt érvénytelen jelszó 2 percen belül használatos. A fiókok automatikus zárolása 30 perc után automatikusan megtörténik.

A fiókzárolás csak a felügyelt tartományon belül történik. A felhasználói fiókok csak az Azure AD DSban vannak kizárva, és csak a felügyelt tartományon végrehajtott sikertelen bejelentkezési kísérletek miatt. Az Azure AD-ból vagy a helyszíni környezetből szinkronizált felhasználói fiókok nincsenek kizárva a forrás-címtárakban, csak az Azure AD DSban.

Ha rendelkezik egy Azure AD-beli jelszavas házirenddel, amely 90 napnál hosszabb ideig adja meg a jelszót, akkor a rendszer az Azure AD DS alapértelmezett házirendjére alkalmazza a jelszót. Beállíthatja, hogy az Azure-AD DS eltérő jelszavakat adjon meg egy egyéni jelszóházirend számára. Ügyeljen arra, hogy az Azure AD DS jelszóházirend esetében az Azure AD-ban vagy a helyszíni AD DS-környezetben rövidebb jelszóval rendelkezzen. Ebben az esetben előfordulhat, hogy a felhasználó jelszava lejár az Azure AD DSban, mielőtt a rendszer megkéri az Azure AD-ben vagy egy helyszíni AD DS-környezetben való megváltoztatását.

Az Azure AD DS felügyelt tartományokban manuálisan létrehozott felhasználói fiókok esetében az alapértelmezett házirend az alábbi további jelszavas beállításokat is alkalmazza. Ezek a beállítások nem vonatkoznak az Azure AD-ből szinkronizált felhasználói fiókokra, mivel a felhasználó nem tudja közvetlenül frissíteni a jelszavát az Azure AD DSban.

* **Jelszó minimális hossza (karakter):** 7
* **A jelszavaknak meg kell felelniük a bonyolultsági követelményeknek**

A fiókzárolás vagy a jelszó beállításai nem módosíthatók az alapértelmezett jelszó-házirendben. Ehelyett a *HRE DC-rendszergazdák* csoport tagjai létrehozhatnak egyéni jelszóházirend-beállításokat, és konfigurálhatók a felülbíráláshoz (elsőbbséget élveznek) az alapértelmezett beépített házirenddel szemben, ahogy az a következő szakaszban is látható.

## <a name="create-a-custom-password-policy"></a>Egyéni jelszóházirend létrehozása

Az Azure-beli alkalmazások létrehozásakor és futtatásakor előfordulhat, hogy egyéni jelszóházirend-szabályzatot szeretne konfigurálni. Létrehozhat például egy házirendet a különböző fiókzárolási házirend-beállítások megadásához.

Az egyéni jelszóházirendek egy Azure AD DS felügyelt tartomány csoportjaira vonatkoznak. Ez a konfiguráció hatékonyan felülbírálja az alapértelmezett házirendet.

Egyéni jelszóházirend létrehozásához a Active Directory felügyeleti eszközöket kell használnia egy tartományhoz csatlakoztatott virtuális gépről. A Active Directory felügyeleti központ segítségével megtekintheti, szerkesztheti és létrehozhatja az erőforrásokat egy Azure AD DS felügyelt tartományban, beleértve a szervezeti egységeket is.

> [!NOTE]
> Egyéni jelszóházirend Azure AD DS felügyelt tartományban való létrehozásához be kell jelentkeznie egy olyan felhasználói fiókba, amely az *HRE DC-rendszergazdák* csoport tagja.

1. A kezdőképernyőn válassza a **felügyeleti eszközök**elemet. Megjelenik a rendelkezésre álló felügyeleti eszközök listája, amely az oktatóanyagban a [felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm]lett telepítve.
1. A szervezeti egységek létrehozásához és kezeléséhez válassza ki a **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblán válassza ki az Azure AD DS felügyelt tartományát, például *aadds.contoso.com*.
1. Nyissa meg a **rendszer** tárolót, majd a **jelszóbeállítás-tároló**.

    Megjelenik az Azure AD DS felügyelt tartomány beépített jelszavas szabályzata. Ez a beépített házirend nem módosítható. Ehelyett hozzon létre egy egyéni jelszóházirend-szabályzatot az alapértelmezett házirend felülbírálásához.

    ![Jelszavas szabályzat létrehozása a Active Directory felügyeleti központban](./media/password-policy/create-password-policy-adac.png)

1. A jobb oldali **feladatok** panelen válassza az **új > a jelszó beállításai**lehetőséget.
1. A **jelszó-beállítások létrehozása** párbeszédpanelen adja meg a szabályzat nevét (például *MyCustomFGPP*).
1. Ha több jelszóházirend létezik, a rendszer egy felhasználóra alkalmazza a legmagasabb prioritású házirendet vagy prioritást. Minél kisebb a szám, annál magasabb a prioritás. Az alapértelmezett jelszóházirend prioritása *200*.

    Állítsa be az egyéni jelszóházirend elsőbbségét az alapértelmezett érték felülbírálásához, például: *1*.

1. Szükség szerint szerkessze a többi jelszóházirend-beállítást. Jegyezze fel a következő kulcsfontosságú pontokat:

    * A beállítások, például a jelszó bonyolultsága, az életkor vagy a lejárati idő csak az Azure AD DS felügyelt tartományában manuálisan létrehozott felhasználók számára.
    * A fiókzárolási beállítások minden felhasználóra érvényesek, de csak a felügyelt tartományon belül lépnek életbe, és nem az Azure AD-ben.

    ![Egyéni részletes jelszóházirendek létrehozása](./media/how-to/custom-fgpp.png)

1. Törölje **a jelet a védelem véletlen törlésből**jelölőnégyzetből. Ha ez a beállítás be van jelölve, nem mentheti a FGPP.
1. A **közvetlenül alkalmazandó** szakaszban válassza a **Hozzáadás** gombot. A **felhasználók vagy csoportok kiválasztása** párbeszédpanelen kattintson a **helyszínek** gombra.

    ![Válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a jelszóházirend alkalmazását](./media/how-to/fgpp-applies-to.png)

1. A jelszóházirend csak csoportokra alkalmazható. A **helyszínek** párbeszédpanelen bontsa ki a tartománynevet (például *aadds.contoso.com*), majd válasszon ki egy szervezeti egységet, például a **AADDC-felhasználók**elemet. Ha olyan egyéni szervezeti egységtel rendelkezik, amely az alkalmazni kívánt felhasználói csoportot tartalmazza, válassza ki azt a szervezeti egységet.

    ![Válassza ki azt a szervezeti egységet, amelyhez a csoport tartozik](./media/how-to/fgpp-container.png)

1. Írja be annak a csoportnak a nevét, amelyre alkalmazni kívánja a szabályzatot, majd válassza a **Névellenőrzés lehetőséget annak ellenőrzéséhez** , hogy a csoport létezik-e.

    ![Keresse meg és válassza ki a FGPP alkalmazni kívánt csoportot](./media/how-to/fgpp-apply-group.png)

1. Ha a kiválasztott csoport neve **közvetlenül** a (z) szakaszban látható, kattintson az **OK** gombra az egyéni jelszóházirend mentéséhez.

## <a name="next-steps"></a>Következő lépések

A jelszóházirend és a Active Directory felügyeleti központ használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [További tudnivalók a részletes jelszóházirendek](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Részletes jelszóházirendek konfigurálása az AD felügyeleti központban](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
