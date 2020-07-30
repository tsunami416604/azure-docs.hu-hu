---
title: A legutóbbi bejelentkezési tevékenység megtekintése és keresése a saját bejelentkezés (előzetes verzió) lapról – Azure Active Directory | Microsoft Docs
description: Részletes információ arról, hogyan tekintheti meg és keresheti meg a legutóbbi bejelentkezési tevékenységeit a fiókom portál saját bejelentkezések lapján.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 738e6507a1642a1ab76938eeaf3294668bcea964
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422290"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>A legutóbbi bejelentkezési tevékenység megtekintése és keresése a saját bejelentkezések (előzetes verzió) lapról

A legutóbbi munkahelyi vagy iskolai fiók bejelentkezési tevékenységeit a **saját fiók** portál **saját bejelentkezések** lapján tekintheti meg. A bejelentkezési előzmények áttekintésével a következő módon tekintheti meg a szokatlan tevékenységeket:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha egy támadó sikeresen bejelentkezett a fiókjába, és az adott helyről.

- Milyen alkalmazások próbáltak hozzáférni a támadóhoz.

## <a name="view-your-recent-sign-in-activity"></a>A legutóbbi bejelentkezési tevékenység megtekintése

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd lépjen a https://myaccount.microsoft.com/ lapra.

2. A bal oldali navigációs ablaktáblán válassza a **saját bejelentkezések (előzetes verzió)** lehetőséget, vagy a **saját bejelentkezések (előzetes verzió)** blokkból válassza a **Legutóbbi tevékenység áttekintése** hivatkozást.

    ![Fiókom oldal, amely a legutóbbi tevékenységek Kiemelt hivatkozásait mutatja](media/my-account-portal/my-account-portal-sign-ins.png)

3. Bontsa ki és tekintse át az egyes bejelentkezési elemeket, és ügyeljen rá, hogy mindegyiket felismerje. Ha olyan bejelentkezési tételt talál, amely nem ismerősnek tűnik, javasoljuk, hogy változtassa meg a jelszavát, hogy megvédje a fiókját, ha az biztonsága sérült.

    ![Legutóbbi tevékenység lap bővített bejelentkezési adataival](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Ha sikeres bejelentkezést lát

A szokásos módon fel kell ismernie a saját tevékenységeit. Ha azonban a furcsa helyről, böngészőből vagy operációs rendszerből sikeres bejelentkezést észlel, az azt jelentheti, hogy egy támadó hozzáfért a fiókjához. Ebben az esetben javasoljuk, hogy azonnal módosítsa a jelszavát, majd lépjen a [biztonsági adatok](https://mysignins.microsoft.com/security-info) lapra a biztonsági beállítások frissítéséhez.

A probléma meghatározása előtt győződjön meg arról, hogy nem jelenik meg hamis pozitív (ahol az elem megkérdőjelezhető, de rendben van). Meghatározhatja például az IP-címe alapján a hozzávetőleges helyet és a leképezést. A mobileszközök különösen nehezen azonosíthatók, mivel időnként távoli telephelyeken keresztül irányítják a forgalmat. Ha tehát Washington államban jelentkezett be a mobileszköz használatával, a hely a California-ból érkező bejelentkezést is megjeleníti. Ezért erősen javasoljuk, hogy a hely helyett további részleteket is keressen. Gondoskodjon arról is, hogy az operációs rendszer, a böngésző és az alkalmazás is legyen értelme.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Ha sikertelen bejelentkezést lát

Sikertelen bejelentkezés – a munkamenet-tevékenység nélkül – az elsődleges ellenőrzési módszer (username/Password) nem sikerült. Ez azt jelentheti, hogy helytelenül adta meg a felhasználónevet vagy a jelszót, de azt is jelentheti, hogy egy támadó megpróbálta kitalálni a jelszavát. Ha úgy gondolja, hogy a támadó sikeresen próbálta kitalálni a jelszavát, nem kell módosítania a jelszavát, de határozottan javasoljuk, hogy regisztrálja az Azure Multi-Factor Authentication (MFA). Az MFA-val még akkor is, ha a hacker végül kitalálta a jelszavát, nem lesz elég a fiókjához való hozzáféréshez.

Ha nem sikerül bejelentkeznie, és a munkamenet-tevékenység alatt megjegyzés jelenik meg, a **további ellenőrzés meghiúsult, a kód érvénytelen**, az azt jelenti, hogy az elsődleges hitelesítés (felhasználónév/jelszó) sikeres volt, de az MFA nem sikerült. Ha ez egy támadó, akkor helyesen kitalálta a jelszavát, de még nem tudta átadni az MFA-feladatot. Ebben az esetben javasoljuk, hogy változtassa meg a jelszavát, mert a támadó megkapta ezt a részt, majd lépjen a [biztonsági adatok](https://mysignins.microsoft.com/security-info) lapra a biztonsági beállítások frissítéséhez.

## <a name="search-for-specific-sign-in-activity"></a>Adott bejelentkezési tevékenység keresése

A legutóbbi bejelentkezési tevékenységeit bármely elérhető információ alapján keresheti meg. Megkeresheti például a legutóbbi bejelentkezési tevékenységeit operációs rendszer, hely, alkalmazás stb. alapján.

1. A **Legutóbbi tevékenység áttekintése** lapon adja **meg a keresési sávon keresendő** adatokat. Írja be például a következőt: a fiókom `My Account` alkalmazás által összegyűjtött összes tevékenység keresése.

2. Kattintson a **Keresés** gombra a keresés megkezdéséhez.

    ![Közelmúltbeli tevékenység lap, a Kiemelt keresősáv, a Keresés gomb és az eredmények megjelenítése](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Következő lépések

A legutóbbi bejelentkezési tevékenység megtekintése után a következőket teheti:

- [Biztonsági adatok](user-help-security-info-overview.md)megtekintése és kezelése.

- A csatlakoztatott [eszközök](my-account-portal-devices-page.md)megtekintése és kezelése.

- A [szervezetek](my-account-portal-organizations-page.md)megtekintése és kezelése.

- Megtekintheti, hogy a szervezet hogyan [használja az adatvédelmet érintő adatokat](my-account-portal-privacy-page.md).

- A fiókom [portál beállításainak](my-account-portal-settings.md) módosítása
