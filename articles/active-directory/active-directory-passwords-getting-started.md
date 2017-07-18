---
title: "Gyors üzembe helyezés: Azure AD SSPR | Microsoft Docs"
description: "Az Azure AD önkiszolgáló jelszó-visszaállításának gyors üzembe helyezése"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 52f89520fef8fc9ddb6e8e10a59ec35600affb2a
ms.contentlocale: hu-hu
ms.lasthandoff: 07/05/2017

---
# <a name="quick-start-azure-ad-self-service-password-reset"></a>Gyors üzembe helyezés: Azure AD önkiszolgáló jelszó-visszaállítás

## <a name="rapidly-deploy-self-service-password-reset"></a>Az önkiszolgáló jelszó-visszaállítás gyors üzembe helyezése

Az önkiszolgáló jelszó-visszaállítás (SSPR) egyszerű módot kínál a rendszergazdáknak arra, hogy felhatalmazzák a felhasználókat jelszavuk és fiókjuk visszaállítására vagy zárolásának feloldására. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhasználatáról, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

Ez a tájékoztató feltételezi, hogy már rendelkezik egy Azure AD-bérlő érvényes próbaverziójával vagy licencével. Ha segítségre van szüksége az Azure AD beállításához, tekintse meg [az Azure AD rövid bemutató cikkét](https://azure.microsoft.com/trial/get-started-active-directory/).

1. A meglévő Azure AD-bérlőjében válassza ki a **„Jelszó átállítása”** lehetőséget.

2. A **„Tulajdonságok”** képernyőn, az „Önkiszolgáló jelszóátállítás engedélyezve” lehetőség alatt válassza valamelyiket a következők közül
    * Senki – Senki nem használhatja az SSPR funkciót
    * Egy csoport – Csak egy adott, a kiválasztott Azure AD-csoport tagjai használhatják az SSPR funkciót
    * Mindenki – Minden felhasználó használhatja az SSPR funkciót, aki rendelkezik fiókkal az Azure AD-bérlőjében

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

4. AJÁNLOTT: A **„Testreszabás”** lehetővé teszi, hogy megváltoztathassa a „Forduljon a rendszergazdához” hivatkozást a kívánt oldalra vagy e-mail-címre

5. VÁLASZTHATÓ: A **„Regisztráció”** képernyő rendszergazdák részére az alábbi lehetőségeket kínálja:
    * Szükséges a felhasználóknak regisztrálniuk a bejelentkezéskor?
    * A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését

6. VÁLASZTHATÓ: Az **„Értesítés”** terület a rendszergazdák részére az alábbi lehetőségeket kínálja:
    * Értesítse a felhasználókat új jelszó kérésekor?
    * Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek?

**Most már konfigurálta az SSPR funkciót Azure AD-bérlője számára**. Megállhat itt, vagy folytathatja a jelszavak helyszíni AD-tartományra szinkronizálásának konfigurálásával.

> [!NOTE]
> Az SSPR-t egy felhasználóval tesztelje, ne egy rendszergazdával, mert a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai típusú fiókjaihoz. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](active-directory-passwords-policy.md#administrator-password-policy-differences) talál további információt.

## <a name="configure-synchronization-to-existing-identity-source"></a>Szinkronizálás konfigurálása létező identitásforráshoz

Annak engedélyezéséhez, hogy a helyszíni identitás szinkronizálhasson az Azure AD-val, telepítenie és konfigurálnia kell az [Azure AD Connect](./connect/active-directory-aadconnect.md) programot szervezete egyik kiszolgálójára. Ez az alkalmazás kezeli a felhasználók és csoportok szinkronizálását a létező identitásforrásról az Azure AD-bérlővel.

* [Frissítés DirSyncről vagy Azure AD Syncről Azure AD Connectre](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](./connect/active-directory-aadconnect-get-started-express.md)
* [Jelszóvisszaíró konfigurálása](active-directory-passwords-writeback.md#configuring-password-writeback) jelszavak az Azure AD-ből a helyszíni könyvtárba történő visszaírásához.

## <a name="disabling-self-service-password-reset"></a>Önkiszolgáló jelszóátállítás letiltása

Az önkiszolgáló jelszóátállítás letiltása egyszerű: nyissa meg Azure AD-bérlőjét, keresse meg a **Jelszó visszaállítása, majd a Tulajdonságok** elemet, és válassza a **Senki** lehetőséget az **Önkiszolgáló jelszóátállítás engedélyezve** alatt

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [**Licencelés**](active-directory-passwords-licensing.md) – Az Azure AD licencelésének konfigurálása.
* [**Adatok**](active-directory-passwords-data.md) – A szükséges adatok megismerése, és az adatok használata a rendszer jelszókezelésre.
* [**Bevezetés**](active-directory-passwords-best-practices.md) – Az SSPR funkció tervezése és üzembe helyezése a felhasználók számára az itt található útmutató segítségével.
* [**Testreszabás**](active-directory-passwords-customize.md) – Az SSPR-felület megjelenésének és működésének testre szabása a cége számára.
* [**Szabályzat**](active-directory-passwords-policy.md) – Megismerheti és beállíthatja az Azure AD jelszószabályzatait.
* [**Jelentéskészítés**](active-directory-passwords-reporting.md) – Megtudhatja, mikor és hol érik el a felhasználói az SSPR funkcióit.
* [**Részletes műszaki bemutatás**](active-directory-passwords-how-it-works.md) – Egy pillantás a függöny mögé, hogy megértse, hogyan is működik.
* [**Gyakori kérdések**](active-directory-passwords-faq.md) – Hogyan? Hogy miért? Mi? Hová? Ki? Mikor? – Válaszok olyan kérdésekre, amiket mindig is fel akart tenni
* [**Hibaelhárítás**](active-directory-passwords-troubleshoot.md) – Ismerje meg, hogyan oldhat meg általános, az SSPR működése során jelentkező hibákat.

