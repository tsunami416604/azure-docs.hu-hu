---
title: "Gyors útmutató: Azure AD SSPR | Microsoft Docs"
description: "Az Azure AD önkiszolgáló jelszó-visszaállításának gyors üzembe helyezése"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7d97fad04a0aa549d0e3a182282f898302e8e41a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Az Azure AD új jelszó önkiszolgáló kérési funkciójának gyors üzembe helyezése

> [!IMPORTANT]
> **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md).

Az önkiszolgáló jelszó-visszaállítás (SSPR) egyszerű módot kínál a rendszergazdáknak arra, hogy felhatalmazzák a felhasználókat jelszavuk és fiókjuk visszaállítására vagy zárolásának feloldására. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhasználatáról, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

Ez a tájékoztató feltételezi, hogy már rendelkezik egy Azure AD-bérlő érvényes próbaverziójával vagy licencével. Ha segítségre van szüksége az Azure AD beállításához, tekintse meg [az Azure AD rövid bemutató cikkét](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Az SSPR engedélyezése az Azure AD-bérlő számára

1. A meglévő Azure AD-bérlőjében válassza ki a **„Jelszó átállítása”** lehetőséget.

2. A **„Tulajdonságok”** képernyő „Új jelszó önkiszolgáló kérése engedélyezve” területén válassza valamelyiket a következők közül:
    * Nincs – Senki nem használhatja az SSPR funkciót.
    * Kiválasztva – Csak az Ön által kiválasztott Azure AD-csoport tagjai használhatják az SSPR funkciót. Amikor megvalósíthatósági próbát végez, ezt a beállítást javasoljuk egy meghatározott felhasználói csoporttal.
    * Összes – Minden felhasználó használhatja az SSPR funkciót, aki rendelkezik fiókkal az Azure AD-bérlőjében. Ezt akkor ajánlott beállítani, amikor készen áll a funkció üzembe helyezésére a teljes bérlőn, a megvalósíthatósági próba elvégzése után.

3. Válasszon a **„Hitelesítési módszerek”** képernyőről
    * Az új jelszó kéréséhez szükséges módszerek száma – Minimum egyet, maximum kettőt támogatunk
    * Felhasználók által választható módszerek – Legalább egyre van szükségünk, de egy plusz lehetőség sosem árt
        * Az **E-mail** beállítás egy kódot tartalmazó e-mailt küld a felhasználó konfigurált hitelesítési e-mail-címére.
        * A **Mobiltelefon** beállítás lehetővé teszi a felhasználó számára, hogy egy kódot tartalmazó hívást vagy SMS-t fogadhasson konfigurált mobiltelefon-számán.
        * Az **Irodai telefon** beállítás felhívja a felhasználót a konfigurált irodai telefonszámán, és egy kódot olvas be.
        * A **Biztonsági kérdések** területen a következőket kell kiválasztani:
            * A biztonsági kérdésekre való regisztráláshoz szükséges kérdések száma – A sikeres regisztrációhoz szükséges minimális kérdések számát jelenti. Tehát egy felhasználó dönthet úgy, több választ ad meg egy olyan kérdéskészlet létrehozásához, amiből előhívhatóak a kérdések. Ezt a beállítást 3 és 5 közötti értékre lehet beállítani, és nagyobbnak vagy egyenlőnek kell lennie, mint a visszaállítást igénylő kérdések száma.
                * Egyéni kérdéseket az „Egyéni” gombra kattintva lehet hozzáadni a biztonsági kérdések kiválasztásakor
            * Az új jelszó kéréséhez szükséges kérdések száma lehetőséget 3 és 5 közötti értékre lehet beállítani. Az új jelszó kéréséhez vagy a zárolás feloldásához ennyi helyesen megválaszolandó kérdés szükséges.
            
    ![Hitelesítés][Authentication]

4. AJÁNLOTT: A **„Testreszabás”** lehetővé teszi, hogy megváltoztathassa a „Forduljon a rendszergazdához” hivatkozást a kívánt oldalra vagy e-mail-címre. Ajánlott ezt a hivatkozást például olyan e-mail-címre vagy webhelyre beállítani, amelyet a felhasználók támogatásként használnak.

5. VÁLASZTHATÓ: A **„Regisztráció”** képernyő rendszergazdák részére az alábbi lehetőségeket kínálja:
    * Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor?
    * A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését

6. VÁLASZTHATÓ: Az **„Értesítés”** terület a rendszergazdák részére az alábbi lehetőségeket kínálja:
    * Értesítse a felhasználókat új jelszó kérésekor?
    * Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek?

**Most már konfigurálta az SSPR funkciót Azure AD-bérlője számára**. A felhasználók most a [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md) és az [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md) cikkekben található utasítások szerint frissíthetik a jelszavukat a rendszergazda beavatkozása nélkül. Megállhat itt, ha csak felhőt használ, vagy folytathatja a jelszavak helyszíni AD-tartományra szinkronizálásának konfigurálásával.

> [!IMPORTANT]
> Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai típusú fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](active-directory-passwords-policy.md#administrator-password-policy-differences) talál további információt.

## <a name="configure-synchronization-to-existing-identity-source"></a>Szinkronizálás konfigurálása létező identitásforráshoz

Annak engedélyezéséhez, hogy a helyszíni identitás szinkronizálhasson az Azure AD-val, telepítenie és konfigurálnia kell az [Azure AD Connect](./connect/active-directory-aadconnect.md) programot szervezete egyik kiszolgálójára. Ez az alkalmazás kezeli a felhasználók és csoportok szinkronizálását a létező identitásforrásról az Azure AD-bérlővel.

* [Frissítés DirSyncről vagy Azure AD Syncről Azure AD Connectre](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](./connect/active-directory-aadconnect-get-started-express.md)
* [Jelszóvisszaíró konfigurálása](active-directory-passwords-writeback.md#configuring-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához.

### <a name="on-premises-policy-change"></a>Helyszíni szabályzat módosítása

Ha helyszíni Active Directory-tartományról szinkronizál felhasználókat, és lehetővé szeretné tenni, hogy a felhasználók azonnal új jelszót kérjenek, módosítsa a helyszíni jelszóházirendet a következőképpen:

**Számítógép konfigurációja** > **Szabályzatok** > **Windows-beállítások** > **Biztonsági beállítások** > **Fiókházirend** > **Jelszóházirend**

**Jelszó minimális kora** – 0 nap

Ez a biztonsági beállítás határozza meg azt az időszakot (napokban), ameddig a jelszót használni kell, mielőtt a felhasználó módosíthatná. Ha **0 napra** állítja, a felhasználók használhatják az SSPR-t, ha a támogatási csapatuk módosította a jelszavukat.

![Szabályzat][Policy]

## <a name="disabling-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszókérés letiltása egyszerű: nyissa meg Azure AD-bérlőjét, keresse meg a **Jelszó visszaállítása, majd a Tulajdonságok** elemet, és válassza a **Senki** lehetőséget az **Önkiszolgáló jelszóátállítás engedélyezve** alatt.

### <a name="learn-more"></a>Részletek
Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Következő lépések

Ebben a gyors útmutatóban megismerhette, hogyan konfigurálhat önkiszolgáló jelszókérést felhasználói számára. Az alábbi hivatkozással az Azure Portalra léphet, ahol elvégezheti a szükséges lépeseket.

> [!div class="nextstepaction"]
> [Önkiszolgáló jelszóátállítás engedélyezése](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "0 napra állított helyszínijelszó-csoportszabályzat"
