---
title: Rövid útmutató új jelszó kérésére vonatkozó önkiszolgáló folyamathoz – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállításának gyors üzembe helyezése
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 6add6e71c9acf6d63858ea921ec85924b4e6c60f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Az Azure AD-beli önkiszolgáló jelszómódosítás gyors üzembe helyezése

> [!IMPORTANT]
> **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, tekintse meg a következő cikket: [Segítség, elfelejtettem Azure AD-jelszavamat](../active-directory-passwords-update-your-own-password.md).

Az önkiszolgáló jelszó-visszaállítás (SSPR) egyszerű módot kínál a rendszergazdáknak arra, hogy felhatalmazzák a felhasználókat jelszavuk és fiókjuk visszaállítására vagy zárolásának feloldására. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

Ez az útmutató feltételezi, hogy már rendelkezik egy Azure Active Directory-bérlő (Azure AD-bérlő) érvényes próbaverziójával vagy licencével. Ha segítségre van szüksége az Azure AD beállításához, tekintse meg az [Ismerkedés az Azure AD szolgáltatással](../get-started-azure-ad.md) című részt.

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Az SSPR engedélyezése az Azure AD-bérlő számára

1. A meglévő Azure AD-bérlőjében, az **Azure Portalon** **Azure Active Directory** területén válassza az **Új jelszó kérése** lehetőséget.

2. A **Tulajdonságok** képernyő **Önkiszolgáló jelszóátállítás engedélyezve** lehetőségnél válassza a következők egyikét:
   * **Senki**: Senki nem használhatja az SSPR funkciót.
   * **Kiválasztva**: Csak az Ön által kiválasztott Azure AD-csoport tagjai használhatják az SSPR funkciót. Amikor megvalósíthatósági próbát végez a funkcióval, ezt a beállítást javasoljuk egy meghatározott felhasználói csoporttal.
   * **Összes**: Minden felhasználó használhatja az SSPR funkciót, aki rendelkezik fiókkal az Azure AD-bérlőjében. Ezt akkor ajánlott beállítani, amikor készen áll a funkció üzembe helyezésére a teljes bérlőn, a megvalósíthatósági próba elvégzése után.

   > [!IMPORTANT]
   > Az Azure rendszergazdai fiókok a beállítás értékétől függetlenül mindig kérhetnek új jelszót. 

3. A **Hitelesítési módszerek** lapon válasszon az alábbiak közül:
   * **Az új jelszó kéréséhez szükséges módszerek száma**: Minimum egyet, maximum kettőt támogatunk.
   * **Felhasználók által választható módszerek**: Legalább egyre van szükségünk, de egy plusz lehetőség sosem árt.
      * **E-mail**: Egy kódot tartalmazó e-mailt küld a felhasználó konfigurált hitelesítési e-mail-címére.
      * **Mobiltelefon**: Lehetővé teszi a felhasználó számára, hogy egy kódot tartalmazó hívást vagy SMS-t fogadhasson konfigurált mobiltelefonszámán.
      * **Irodai telefon**: Felhívja a felhasználót a konfigurált irodai telefonszámán, és egy kódot olvas be.
      * **Biztonsági kérdések**: Itt a következőket kell kiválasztani:
         * **A biztonsági kérdésekre való regisztráláshoz szükséges kérdések száma**: A sikeres regisztrációhoz szükséges minimális kérdések számát jelenti. A felhasználó dönthet úgy, hogy több választ ad meg egy olyan kérdéskészlet létrehozásához, amiből előhívhatók a kérdések. Ezt a beállítást három és öt kérdés közötti értékre lehet beállítani, és nagyobbnak vagy egyenlőnek kell lennie, mint a visszaállításhoz szükséges kérdések száma. A felhasználó egyéni kérdéseket is hozzáadhat, ha a biztonsági kérdések kiválasztásakor az **Egyéni** gombra kattint.
         * **A biztonsági kérdésekre való regisztráláshoz szükséges kérdések száma**: Három és öt kérdés közötti értékre lehet beállítani. Az új jelszó kéréséhez vagy a zárolás feloldásához ennyi helyesen megválaszolandó kérdés szükséges.
            
    ![Hitelesítés][Authentication]

4. Ajánlott: A **Testreszabás** lehetővé teszi, hogy megváltoztassa a **Forduljon a rendszergazdához** hivatkozást a kívánt oldalra vagy e-mail-címre. Ajánlott ezt a hivatkozást például olyan e-mail-címre vagy webhelyre beállítani, amelyet a felhasználók támogatásként használnak.

5. Választható: A **Regisztráció** terület a rendszergazdák részére az alábbi lehetőségeket kínálja:
   * Felhasználói regisztráció megkövetelése bejelentkezéskor.
   * A napok számának megadása, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését.

6. Választható: Az **Értesítések** terület a rendszergazdák részére az alábbi lehetőségeket kínálja:
   * Felhasználók értesítése új jelszó kérésekor.
   * Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek.

Most már konfigurálta az SSPR funkciót Azure AD-bérlője számára. A felhasználók most a [Regisztráció új jelszó önkiszolgáló kérésére](../active-directory-passwords-reset-register.md) és az [Új jelszó kérése vagy jelszó módosítása](../active-directory-passwords-update-your-own-password.md) cikkekben található utasítások szerint frissíthetik a jelszavukat a rendszergazda beavatkozása nélkül. Ha csak felhőalapú szolgáltatást használ, megállhat itt. Vagy továbbléphet a következő szakaszhoz, ahol konfigurálhatja a jelszavak helyi Active Directory-tartományra való szinkronizálását.

> [!TIP]
> Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](concept-sspr-policy.md#administrator-password-policy-differences) talál további információt.

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Szinkronizálás konfigurálása létező identitásforráshoz

Annak engedélyezéséhez, hogy a helyszíni identitás szinkronizálhasson az Azure AD-val, telepítenie és konfigurálnia kell az [Azure AD Connect](./../connect/active-directory-aadconnect.md) programot szervezete egyik kiszolgálójára. Ez az alkalmazás kezeli a felhasználók és csoportok szinkronizálását a létező identitásforrásról az Azure AD-bérlővel. További információkért lásd:

* [Frissítés DirSyncről vagy Azure AD Syncről Azure AD Connectre](./../connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](./../connect/active-directory-aadconnect-get-started-express.md)
* [Jelszóvisszaíró konfigurálása](howto-sspr-writeback.md#configure-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához

### <a name="on-premises-policy-change"></a>Helyszíni szabályzat módosítása

Ha helyszíni Active Directory-tartományról szinkronizál felhasználókat, és lehetővé szeretné tenni, hogy a felhasználók azonnal új jelszót kérjenek, módosítsa a helyszíni jelszóházirendet a következőképpen:

1. Keresse meg a következő helyet: **Számítógép konfigurációja** > **Szabályzatok** > **Windows-beállítások** > **Biztonsági beállítások** > **Fiókházirend** > **Jelszóházirend**.

2. A **Jelszó minimális kora** értéket állítsa **0 napra**.

Ez a biztonsági beállítás határozza meg azt az időszakot napokban, ameddig a jelszót használni kell, mielőtt a felhasználó módosíthatná. Ha a minimális használat beállítást **0 napra** állítja, a felhasználók használhatják az SSPR-t, ha a támogatási csapatuk módosította a jelszavukat.

![Szabályzat][Policy]

## <a name="disable-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszóátállítás letiltása egyszerű feladat. Nyissa meg Azure AD-bérlőjét, keresse meg a **Jelszó visszaállítása** > **Tulajdonságok** elemet, majd válassza a **Senki** elemet az **Önkiszolgáló jelszóátállítás engedélyezve** rész alatt.

### <a name="learn-more"></a>Részletek
Az alábbi cikkekben az Azure AD jelszóátállításáról olvashat további információkat:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Következő lépések

Ebben a gyors útmutatóban megismerhette, hogyan konfigurálhat önkiszolgáló jelszókérést felhasználói számára. A szükséges lépések elvégzéséhez lépjen az Azure Portalra:

> [!div class="nextstepaction"]
> [Önkiszolgáló jelszóátállítás engedélyezése](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Policy]: ./media/quickstart-sspr/password-policy.png "0 napra állított helyszínijelszó-csoportszabályzat"

