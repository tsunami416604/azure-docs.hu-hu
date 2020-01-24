---
title: A kétfaktoros ellenőrzési módszer és beállítások módosítása – Azure AD
description: A további biztonsági ellenőrzés lapról megtudhatja, hogyan módosíthatja a munkahelyi vagy iskolai fiókjához tartozó biztonsági ellenőrzési módszert és beállításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.openlocfilehash: e48834800f1fe3d34c13a87cfb3d8ef00c9e6bff
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705275"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>A kétfaktoros ellenőrzési módszer és beállítások módosítása

A munkahelyi vagy iskolai fiókjához tartozó biztonsági ellenőrzési módszerek beállítása után a kapcsolódó részletek bármelyikét frissítheti, beleértve a következőket:

- Válassza ki az alapértelmezett biztonsági ellenőrzési módszert.

- A biztonsági ellenőrzési módszer részleteinek, például a telefonszámának hozzáadása vagy frissítése.

- Új hitelesítő alkalmazás beállítása vagy egy eszköz törlése a hitelesítő alkalmazásból.

## <a name="using-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés lap használata

Ha a szervezete a kétfaktoros ellenőrzés bekapcsolásának és kezelésének részletes lépéseit ismerteti, kövesse ezeket az utasításokat. Ellenkező esetben a biztonsági ellenőrzési módszer beállításait a [további biztonsági ellenőrzés](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) oldalon érheti el.

>[!Note]
>Ha a képernyőn megjelenő információk nem egyeznek meg a jelen cikkben ismertetett lépésekkel, az azt jelenti, hogy a rendszergazda bekapcsolta a biztonsági adatok (előzetes verzió) felületét, vagy hogy a szervezete rendelkezik saját egyéni portálral. További információ a biztonsági információkkal kapcsolatos felhasználói élményről: [biztonsági adatok (előzetes verzió) – áttekintés](user-help-security-info-overview.md). Ha többet szeretne megtudni a szervezet egyéni portálján, forduljon az ügyfélszolgálathoz.

### <a name="to-get-to-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldalának beolvasása

- Lépjen a [további biztonsági ellenőrzés](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) lapra.

    ![További biztonsági ellenőrzés lap, az elérhető biztonsági ellenőrzési módszer részleteivel](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    Ha a hivatkozásra kattintva nem működik, az alábbi lépéseket követve is elérheti a **további biztonsági ellenőrzés** oldalt:

    1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).

    2. Válassza ki a fiók nevét a jobb felső sarokban, majd válassza a **profil**lehetőséget.

    3. Válassza a **további biztonsági ellenőrzés**lehetőséget.  

        ![Saját alkalmazások hivatkozás a további biztonsági ellenőrzés oldalára](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>További információ a **további biztonsági ellenőrzés** lap **alkalmazások jelszavai** szakaszának használatáról: [alkalmazások jelszavainak kezelése kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md). Az alkalmazás jelszavait csak olyan alkalmazásokhoz szabad használni, amelyek még nem támogatják a kétfaktoros ellenőrzést.

## <a name="change-your-default-security-verification-method"></a>Az alapértelmezett biztonsági ellenőrzési módszer módosítása

Miután bejelentkezett a munkahelyi vagy iskolai fiókjába a felhasználónevével és jelszavával, automatikusan megjelenik a választott biztonsági ellenőrzési módszer. A szervezet követelményeitől függően ez lehet értesítési vagy ellenőrző kód egy hitelesítő alkalmazásban, egy szöveges üzenetben vagy egy telefonhívásban.

Ha úgy dönt, hogy módosítani szeretné a használt alapértelmezett biztonsági ellenőrzési módszert, itt megteheti.

### <a name="to-change-your-default-security-verification-method"></a>Az alapértelmezett biztonsági ellenőrzési módszer módosítása

1. A **további biztonsági ellenőrzés** lapon válassza ki a **Mi az előnyben részesített lehetőség** legördülő listából a használni kívánt módszert. Ekkor megjelenik az összes lehetőség, de csak a szervezete számára elérhetővé tenni kívánt lehetőségek közül választhat.

    - **Értesítés az alkalmazáson keresztül.** A rendszer értesítést küld a hitelesítő alkalmazásban, amelyhez várakozási ellenőrzési kérés van.

    - **A hitelesítő telefon meghívása.** Telefonhívást kap a mobileszközön, és megkérdezi, hogy igazolja-e az adatokat.

    - **Szöveges kód a saját hitelesítési telefonjára.** Egy ellenőrző kódot fog kapni a mobileszközön lévő szöveges üzenet részeként. Ezt a kódot kell megadnia a munkahelyi vagy iskolai fiókjához tartozó ellenőrző kérésben.

    - **Hívja meg az irodai telefonját.** Telefonhívást kap az irodai telefonján, és megkéri, hogy ellenőrizze az adatait.

    - **Ellenőrző kód használata az alkalmazásból.** A hitelesítő alkalmazás használatával beolvashatja a munkahelyi vagy iskolai fiókjából a parancssorba beírni kívánt ellenőrző kódot.

2. Kattintson a **Mentés** gombra.

## <a name="add-or-change-your-phone-number"></a>Telefonszám hozzáadása vagy módosítása

A **további biztonsági ellenőrzés** lapról új telefonszámokat adhat hozzá, vagy frissítheti a meglévő számokat.

>[!Important]
>Javasoljuk, hogy adjon hozzá egy másodlagos telefonszámot, amely megakadályozza, hogy kizárja a fiókját, ha az elsődleges telefon elveszett vagy ellopták, vagy ha új telefont kap, és már nem rendelkezik az eredeti, elsődleges telefonszámmal.

### <a name="to-change-your-phone-numbers"></a>A telefonszámok módosítása

1. A **hogyan szeretne válaszolni?** szakaszban a **további biztonsági ellenőrzés** lapon frissítse a **hitelesítő telefon** (az elsődleges mobileszköz) telefonszámának adatait és az **irodai telefonját**.

2. Jelölje be a **másodlagos hitelesítő telefon** lehetőség melletti jelölőnégyzetet, majd írja be azt a másodlagos telefonszámot, ahol szöveges üzeneteket vagy telefonhívásokat fogadhat, ha nem fér hozzá az elsődleges eszközhöz.

3. Kattintson a **Mentés** gombra.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Új fiók felvétele a Microsoft hitelesítő alkalmazásba

Beállíthatja munkahelyi vagy iskolai fiókját az [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) vagy [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)rendszerhez készült Microsoft Authenticator alkalmazásban.

Ha korábban beállította a munkahelyi vagy iskolai fiókját a Microsoft Authenticator alkalmazásban, akkor nem kell újra végrehajtania.

1. A **hogyan szeretne válaszolni?** szakaszban a **további biztonsági ellenőrzés** lapon jelölje be a **hitelesítő alkalmazás beállítása** gomb.

    ![Munkahelyi vagy iskolai fiók beállítása a Microsoft Authenticator alkalmazásban](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. Kövesse a képernyőn megjelenő utasításokat, például a mobileszköz használatával ellenőrizze a QR-kódot, majd válassza a **tovább**lehetőséget.

    Az adatok ellenőrzéséhez meg kell adnia, hogy jóvá kell hagynia egy értesítést a Microsoft Authenticator alkalmazáson keresztül.

3. Kattintson a **Mentés** gombra.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Fiók vagy eszköz törlése a Microsoft Authenticator alkalmazásból

A fiókot törölheti a Microsoft Authenticator alkalmazásból, és törölheti az eszközt a munkahelyi vagy iskolai fiókjából. Általában törli az eszközt az elveszett, ellopott vagy régi eszközök fiókból való végleges eltávolításához, és törli a fiókját, hogy megpróbáljon valamilyen kapcsolódási problémát kijavítani, vagy egy fiók módosítását, például egy új felhasználónevet.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Az eszköz törlése munkahelyi vagy iskolai fiókból

1. A **hogyan szeretne válaszolni?** szakaszban a **további biztonsági ellenőrzés** lapon jelölje be a **hitelesítő alkalmazás beállítása** gomb.

2. Kattintson a **Mentés** gombra.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Fiók törlése a Microsoft Authenticator alkalmazásból

- A Microsoft Authenticator alkalmazásban kattintson a törölni kívánt eszköz melletti **Törlés** gombra.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>A kétfaktoros ellenőrzési kérések bekapcsolása egy megbízható eszközön

A szervezeti beállításoktól függően előfordulhat, hogy egy jelölőnégyzet jelenik meg, amely szerint a böngészőn kétfaktoros ellenőrzés végrehajtásakor a rendszer **nem kér újra X napra** . Ha bejelölte ezt a jelölőnégyzetet a kétfaktoros ellenőrző kérések leállításához, majd elveszíti az eszközét, vagy az eszköz esetleg sérült, akkor a fiók védelmének biztosításához kapcsolja be a kétfaktoros ellenőrzési kéréseket. Sajnos a kérések nem kapcsolhatók vissza egyetlen eszközre. A kéréseket egyszerre kell bekapcsolni az összes eszközön.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>A kétfaktoros ellenőrzés bekapcsolásának visszahívása az eszközökön

- A **további biztonsági ellenőrzés** lapon válassza a **többtényezős hitelesítés visszaállítása korábban megbízható eszközökön**lehetőséget.

    Amikor legközelebb bejelentkezik bármelyik eszközre, a rendszer a kétfaktoros ellenőrzés elvégzésére kéri.

## <a name="next-steps"></a>Következő lépések

A kétfaktoros ellenőrzési beállítások hozzáadása vagy frissítése után kezelheti az alkalmazás jelszavait, bejelentkezhet, vagy segítséget kérhet a kétfaktoros ellenőrzésekkel kapcsolatos gyakori problémákról.

- Az [alkalmazás jelszavainak kezelése kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md) minden olyan alkalmazás esetében, amely nem támogatja a kétfaktoros ellenőrzést.

- [Bejelentkezés kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [Segítség kérése kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md)
