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
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617134"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Jelszó-és fiókzárolási házirendek a felügyelt tartományokban

A fiókok biztonságának kezeléséhez Azure Active Directory Domain Servicesban (Azure AD DS) megadhatja a beállításokat, például a jelszó minimális hosszát, a jelszó lejárati idejét vagy a jelszó bonyolultságát. Az alapértelmezett jelszóházirend egy Azure AD DS felügyelt tartomány összes felhasználójára vonatkozik. A részletes szabályozás biztosításához és adott üzleti vagy megfelelőségi igények kielégítéséhez további szabályzatok hozhatók létre és alkalmazhatók a felhasználók bizonyos csoportjaira.

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat egy részletes jelszóházirendek-házirendet a Active Directory felügyeleti központ használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
  * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
  * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure][associate-azure-ad-tenant]-előfizetést a fiókjához.
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
  * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
  * Ha szükséges, fejezze be az oktatóanyagot [egy felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Részletes jelszóházirendek (FGPP) – áttekintés

A részletes jelszóházirendek (Fgpp-EK) segítségével meghatározott korlátozásokat alkalmazhat a jelszó-és fiókzárolási házirendek számára a tartomány különböző felhasználói számára. A Kiemelt jogosultságú fiókok biztonságossá tételéhez például szigorúbb jelszavas beállításokat alkalmazhat, mint a normál, nem Kiemelt fiókok. Több Fgpp is létrehozhat egy Azure AD DS felügyelt tartományon belüli jelszóházirend megadásához.

A következő jelszó-beállítások konfigurálhatók a FGPP használatával:

* Jelszó minimális hossza
* Korábbi jelszavak
* A jelszavaknak meg kell felelniük a bonyolultsági követelményeknek
* Jelszó minimális kora
* Jelszó maximális kora
* Fiókzárolási házirend
  * Fiókzárolás időtartama
  * Sikertelen bejelentkezési kísérletek száma engedélyezett
  * Sikertelen bejelentkezési próbálkozások számának alaphelyzetbe állítása

A FGPP csak az Azure AD DSban létrehozott felhasználókat érinti. A felhőalapú felhasználókat és tartományi felhasználókat az Azure AD-ből szinkronizálták az Azure AD DS felügyelt tartományba, a jelszóházirend nem érinti.

A szabályzatokat az Azure AD DS felügyelt tartományán keresztül osztják el, és az elvégzett módosítások a következő felhasználói bejelentkezéskor lesznek alkalmazva. A házirend módosítása nem oldja fel a már kizárt felhasználói fiók zárolását.

## <a name="default-fine-grained-password-policy-settings"></a>A részletes jelszóházirendek alapértelmezett beállításai

Az Azure AD DS felügyelt tartományokban a következő jelszóházirend alapértelmezés szerint konfigurálva van, és az összes felhasználóra vonatkozik:

* **Jelszó minimális hossza (karakter):** 7
* **Jelszó maximális kora (élettartam):** 90 nap
* **A jelszavaknak meg kell felelniük a bonyolultsági követelményeknek**

Alapértelmezés szerint a következő fiókzárolási házirendek vannak konfigurálva:

* **Fiókzárolás időtartama:** 30
* **Sikertelen bejelentkezési kísérletek száma engedélyezett:** 5
* **Sikertelen bejelentkezési kísérletek számának visszaállítása a következő után:** 30 perc

Ezekkel az alapértelmezett beállításokkal a felhasználói fiókok 30 percig vannak kizárva, ha az öt érvénytelen jelszó 2 percen belül használatos. A fiókok automatikus zárolása 30 perc után automatikusan megtörténik.

Nem módosíthatja vagy törölheti az alapértelmezett beépített, részletes jelszóházirendek. Ehelyett a *HRE DC-rendszergazdák* csoport tagjai létrehozhatnak egyéni FGPP, és úgy konfigurálhatók, hogy felülbírálják (elsőbbséget élveznek) az alapértelmezett beépített FGPP, ahogy azt a következő szakaszban is látható.

## <a name="create-a-custom-fine-grained-password-policy"></a>Egyéni részletes jelszóházirendek létrehozása

Az Azure-ban felépített és alkalmazásaiban érdemes lehet egyéni FGPP konfigurálni. Néhány példa arra, hogy egyéni FGPP kell létrehoznia, például egy másik fiókzárolási házirend beállításához, vagy egy alapértelmezett jelszó-élettartam beállítás megadásához a felügyelt tartományhoz.

Létrehozhat egy egyéni FGPP, és alkalmazhatja azt az Azure AD DS felügyelt tartomány meghatározott csoportjaira. Ez a konfiguráció hatékonyan felülbírálja az alapértelmezett FGPP. Létrehozhat egyéni, részletes jelszóházirendek is, és alkalmazhatja azokat az Azure AD DS felügyelt tartományában létrehozott egyéni szervezeti egységekre is.

A részletes jelszóházirendek létrehozásához a Active Directory felügyeleti eszközöket kell használnia egy tartományhoz csatlakoztatott virtuális gépről. A Active Directory felügyeleti központ segítségével megtekintheti, szerkesztheti és létrehozhatja az erőforrásokat egy Azure AD DS felügyelt tartományban, beleértve a szervezeti egységeket is.

> [!NOTE]
> Ha egy Azure AD DS felügyelt tartományban szeretné létrehozni a részletes jelszóházirendek, be kell jelentkeznie egy olyan felhasználói fiókba, amely tagja az *HRE DC-rendszergazdák* csoportnak.

1. A kezdőképernyőn válassza a **felügyeleti eszközök**elemet. Megjelenik a rendelkezésre álló felügyeleti eszközök listája, amely az oktatóanyagban a [felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm]lett telepítve.
1. A szervezeti egységek létrehozásához és kezeléséhez válassza ki a **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblán válassza ki az Azure AD DS felügyelt tartományát, például *contoso.com*.
1. A jobb oldali **feladatok** panelen válassza az **új > a jelszó beállításai**lehetőséget.
1. A **jelszó-beállítások létrehozása** párbeszédpanelen adja meg a szabályzat nevét (például *MyCustomFGPP*). Állítsa be a sorrendet megfelelően az alapértelmezett FGPP felülbírálásához ( *200*), például: *1*.

    Módosítsa a többi jelszóházirend-beállítást igény szerint, például a korábbi jelszavak megváltoztatásához, hogy a felhasználó olyan jelszót hozzon létre, amely eltér az előző *24* jelszótól.

    ![Egyéni részletes jelszóházirendek létrehozása](./media/how-to/custom-fgpp.png)

1. Törölje **a jelet a védelem véletlen törlésből**jelölőnégyzetből. Ha ez a beállítás be van jelölve, nem mentheti a FGPP.
1. A **közvetlenül alkalmazandó** szakaszban válassza a **Hozzáadás** gombot. A **felhasználók vagy csoportok kiválasztása** párbeszédpanelen kattintson a **helyszínek** gombra.

    ![Válassza ki azokat a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a jelszóházirend alkalmazását](./media/how-to/fgpp-applies-to.png)

1. A részletes jelszóházirendek csak csoportokra alkalmazhatók. A **helyszínek** párbeszédpanelen bontsa ki a tartománynevet (például *contoso.com*), majd válasszon ki egy szervezeti egységet, például a **AADDC-felhasználók**elemet. Ha olyan egyéni szervezeti egységtel rendelkezik, amely az alkalmazni kívánt felhasználói csoportot tartalmazza, válassza ki azt a szervezeti egységet.

    ![Válassza ki azt a szervezeti egységet, amelyhez a csoport tartozik](./media/how-to/fgpp-container.png)

1. Írja be annak a csoportnak a nevét, amelyre alkalmazni kívánja a szabályzatot , majd válassza a Névellenőrzés lehetőséget annak ellenőrzéséhez, hogy a csoport létezik-e.

    ![Keresse meg és válassza ki a FGPP alkalmazni kívánt csoportot](./media/how-to/fgpp-apply-group.png)

1. Ha a kiválasztott csoport neve **közvetlenül** a (z) szakaszban látható, kattintson az **OK** gombra az egyéni jelszóházirend mentéséhez.

## <a name="next-steps"></a>További lépések

A részletes jelszóházirendek és a Active Directory felügyeleti központ használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [További tudnivalók a részletes jelszóházirendek](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Részletes jelszóházirendek konfigurálása az AD felügyeleti központban](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
