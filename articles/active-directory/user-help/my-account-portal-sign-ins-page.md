---
title: A legutóbbi bejelentkezési tevékenység megtekintése és keresése a Bejelentkezés (előzetes verzió) lapon – Azure Active Directory | Microsoft dokumentumok
description: A legutóbbi bejelentkezési tevékenység megtekintésének és keresésének részletei a Saját fiók portál Saját bejelentkezések lapján.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064019"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>A legutóbbi bejelentkezési tevékenység megtekintése és keresése a Bejelentkezések (előzetes verzió) lapon

Megtekintheti az összes legutóbbi munkahelyi vagy iskolai fiókbejelentkezési tevékenységét a **Saját fiók** portál **Bejelentkezések** lapján. A bejelentkezési előzmények áttekintése segít a szokatlan tevékenységek ellenőrzésében azáltal, hogy segít a következők megtekintésében:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha a támadó sikeresen bejelentkezett a fiókjába, és milyen helyről.

- Milyen alkalmazásokat próbált a támadó elérni.

## <a name="view-your-recent-sign-in-activity"></a>A legutóbbi bejelentkezési tevékenység megtekintése

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

2. Válassza a **Bejelentkezések (előzetes verzió)** lehetőséget a bal oldali navigációs ablakban, vagy válassza a **Legutóbbi tevékenységek áttekintése** hivatkozást a **Bejelentkezések (előzetes verzió)** blokkból.

    ![Saját fiók lap, kiemelve a Legutóbbi tevékenység hivatkozásai](media/my-account-portal/my-account-portal-sign-ins.png)

3. Bontsa ki és tekintse át a bejelentkezési elemeket, és győződjön meg arról, hogy felismeri az egyes elemeket. Ha olyan bejelentkezési elemet talál, amely nem tűnik ismerősnek, javasoljuk, hogy változtassa meg jelszavát, hogy megvédje fiókját, ha az feltört.

    ![Legutóbbi tevékenység lap a bővített bejelentkezés részleteivel](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Ha sikeres bejelentkezést lát

Fel kell ismerned, hogy a saját tevékenységed normális. Ha azonban egy sikeres bejelentkezést észlel furcsa helyről, böngészőből vagy operációs rendszerről, az azt jelentheti, hogy a támadó hozzáfért a fiókjához. Ebben az esetben javasoljuk, hogy azonnal változtassa meg jelszavát, majd a biztonsági beállítások frissítéséhez lépjen a [Biztonsági adatok](https://mysignins.microsoft.com/security-info) lapra.

Mielőtt megállapítja, hogy valami helytelen, győződjön meg róla, hogy nem lát hamis pozitív eredményt (ahol az elem megkérdőjelezhetőnek tűnik, de rendben van). Például az ÖN IP-címe alapján határozzuk meg az Ön hozzávetőleges tartózkodási helyét és térképét. A mobilhálózatokat különösen nehéz pontosan meghatározni, mivel néha távoli helyeken keresztül irányítják a forgalmat. Ha tehát Washington államban, a mobileszközével jelentkezett be, előfordulhat, hogy a hely a Kaliforniából érkező bejelentkezést mutatja. Emiatt javasoljuk, hogy ellenőrizze a további részleteket, túl csak a helyét. Azt is meg kell győződnie arról, hogy az operációs rendszernek, a böngészőnek és az alkalmazásnak is van értelme.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Ha sikertelen bejelentkezést lát

A sikertelen bejelentkezés munkamenet-tevékenység nélkül azt jelenti, hogy az elsődleges ellenőrzési módszer (felhasználónév/jelszó) sikertelen volt. Ez azt jelentheti, hogy elgépelte a felhasználónevét vagy a jelszavát, de azt is jelentheti, hogy a támadó megpróbálta kitalálni a jelszavát. Ha úgy gondolja, hogy a támadó sikertelenül próbálta kitalálni a jelszavát, nem kell módosítania a jelszavát, de javasoljuk, hogy regisztráljon az Azure többtényezős hitelesítésre (MFA). Az MFA-val, még akkor is, ha a hacker végül kitalálja a jelszavát, nem lesz elég a fiókjához való hozzáféréshez.

Ha sikertelen bejelentkezést lát, és a Munkamenet-tevékenység csoportban a következő megjegyzés jelenik meg: **A további ellenőrzés sikertelen, érvénytelen kód**, ez azt jelenti, hogy az elsődleges hitelesítés (felhasználónév/jelszó) sikeres volt, de az MFA nem sikerült. Ha ez egy támadó volt, helyesen kitalálták a jelszavát, de továbbra sem tudtak átmenni az MFA-kihíváson. Ebben az esetben azt javasoljuk, hogy módosítsa a jelszavát, mivel a támadó helyesen kapta meg a részt, majd lépjen a [Biztonsági adatok](https://mysignins.microsoft.com/security-info) lapra a biztonsági beállítások frissítéséhez.

## <a name="search-for-specific-sign-in-activity"></a>Adott bejelentkezési tevékenység keresése

A legutóbbi bejelentkezési tevékenységben a rendelkezésre álló információk bármelyike alapján kereshet. Rákereshet például a legutóbbi bejelentkezési tevékenységére az operációs rendszer, a hely, az alkalmazás és így tovább.

1. A **Legutóbbi tevékenységek áttekintése** lapon írja be a keresett adatokat a **keresősávba.** Írja be `My Account` például a Saját fiók alkalmazás által gyűjtött összes tevékenység keresését.

2. A keresés megkezdéséhez kattintson a **Keresés** gombra.

    ![Legutóbbi tevékenységek lap, kiemelt keresősáv, keresőgomb és találatok megjelenítése](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>További lépések

A legutóbbi bejelentkezési tevékenység megtekintése után a következőket teheti:

- A biztonsági [adatok](user-help-security-info-overview.md)megtekintése vagy kezelése.

- A csatlakoztatott [eszközök](my-account-portal-devices-page.md)megtekintése és kezelése.

- A [szervezetek](my-account-portal-organizations-page.md)megtekintése vagy kezelése.

- Megtekintheti, hogy szervezete hogyan [használja fel az adatvédelemmel kapcsolatos adatokat.](my-account-portal-privacy-page.md)
