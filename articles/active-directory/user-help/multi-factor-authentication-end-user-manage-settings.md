---
title: A kétfaktoros ellenőrzési módszer és beállítások módosítása – Azure Active Directory
description: A További biztonsági ellenőrzés oldalon megtudhatja, hogyan módosíthatja a munkahelyi vagy iskolai fiókjához szükséges biztonsági ellenőrzési módszert és beállításokat.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253246"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>A kétfaktoros ellenőrzési módszer és beállítások módosítása

Miután beállította a munkahelyi vagy iskolai fiókjához tartozó biztonsági ellenőrzési módszereket, frissítheti a kapcsolódó adatokat, beleértve a következőket:

- Alapértelmezett biztonsági ellenőrzési módszer

- A biztonsági ellenőrzési módszer részletei, például a telefonszám

- Hitelesítő alkalmazás beállítása vagy eszköz törlése a hitelesítő alkalmazásból

## <a name="using-the-additional-security-verification-page"></a>A További biztonsági ellenőrzés lap használata

Ha a szervezet konkrét lépéseket adott a kétfaktoros ellenőrzés bekapcsolásával és kezelésével kapcsolatban, először kövesse ezeket az utasításokat. Ellenkező esetben a biztonsági ellenőrzési módszer beállításait a További biztonsági ellenőrzés oldalon [érheti](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time) el.

>[!Note]
>Ha a képernyőn látható szöveg nem egyezik a cikkben tárgyalt adatokkal, az azt jelenti, hogy a rendszergazda bekapcsolta a **Biztonsági adatok (előzetes verzió)** felületet, vagy hogy a szervezet saját egyéni portált adott meg. Az új biztonsági adatokról a [Biztonsági adatok (előzetes verzió) című témakörben olvashat bővebben.](user-help-security-info-overview.md) A szervezet egyéni portáljával kapcsolatos további információkért forduljon a szervezet ügyfélszolgálatához.

### <a name="to-get-to-the-additional-security-verification-page"></a>A További biztonsági ellenőrzés lap hoz

Ezt a hivatkozást a További biztonsági ellenőrzés lapra mutató hivatkozásra kattintva [követheti.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

![További biztonsági ellenőrző lap, a rendelkezésre álló biztonsági ellenőrzési módszer adataival](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

A További biztonsági **ellenőrzés** lap az alábbi lépésekkel is megtérülhet:

1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)a ikonra.

1. Válassza ki a fiók nevét a jobb felső sarokban, majd válassza a **profil**lehetőséget.

1. Válassza **a További biztonsági ellenőrzés lehetőséget.**  

    ![A Saját alkalmazások hivatkozása a További biztonsági ellenőrzés lapra](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>A **További biztonsági ellenőrzés** lap **Alkalmazásjelszavak** szakaszának használatáról az [Alkalmazásjelszavak kezelése a kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md)című témakörben talál. Az alkalmazásjelszavakat csak olyan alkalmazásokhoz szabad használni, amelyek nem támogatják a kétfaktoros ellenőrzést.

## <a name="change-your-default-security-verification-method"></a>Az alapértelmezett biztonsági ellenőrzési módszer módosítása

Miután bejelentkezett munkahelyi vagy iskolai fiókjába a felhasználónevével és jelszavával, automatikusan megjelenik a választott biztonsági ellenőrzési módszer. A szervezet követelményeitől függően ez lehet egy értesítési vagy ellenőrző kód egy hitelesítő alkalmazáson, egy szöveges üzeneten vagy egy telefonhíváson keresztül.

Ha úgy dönt, hogy módosítani szeretné a használt alapértelmezett biztonsági ellenőrzési módszert, azt innen teheti meg.

### <a name="to-change-your-default-security-verification-method"></a>Az alapértelmezett biztonsági ellenőrzési módszer módosítása

1. A **További biztonsági ellenőrzés** lapon válassza ki a használni kívánt módszert a **Mi az előnyben részesített beállítás** listában. Az összes lehetőség megjelenik, de csak azokat választhatja ki, amelyeket a szervezet elérhetővé tett.

    - **Értesítés az alkalmazáson keresztül:** A hitelesítő alkalmazáson keresztül értesítést kap arról, hogy várakozási ellenőrző kérése van.

    - **Hívja fel a hitelesítési telefonomat:** Telefonhívást fog kapni a mobileszközén, amely arra kéri, hogy ellenőrizze adatait.

    - **Szöveges kód a hitelesítési telefonomra:** A mobileszközön lévő szöveges üzenet részeként ellenőrző kódot kap. Ezt a kódot be kell írnia a munkahelyi vagy iskolai fiókjához szükséges ellenőrző üzenetbe.

    - **Hívja fel az irodai telefonomat:** Az irodai telefonon telefonhívást kap, és arra kéri, hogy ellenőrizze adatait.

    - **Ellenőrző kód használata az alkalmazásból:** A hitelesítő alkalmazássegítségével bekell szereznie egy ellenőrző kódot, amelyet a munkahelyi vagy iskolai fiókjából ír a kérdésbe.

2. Kattintson a **Mentés** gombra.

## <a name="add-or-change-your-phone-number"></a>Telefonszám hozzáadása vagy módosítása

A **További biztonsági ellenőrzés** lapon új telefonszámokat adhat hozzá, vagy frissítheti a meglévő számokat.

>[!Important]
>Javasoljuk, hogy adjon meg egy másodlagos telefonszámot, hogy megakadályozza a fiókból való kizárást, ha az elsődleges telefon elveszett vagy ellopták, vagy ha új telefont kap, és már nem rendelkezik az eredeti, elsődleges telefonszámával.

### <a name="to-change-your-phone-numbers"></a>A telefonszámok módosítása

1. A További **biztonsági ellenőrzés** lap **Hogyan szeretne válaszolni?** **Authentication phone** **Office phone**

1. Jelölje be az **Alternatív hitelesítési telefon** beállítás melletti jelölőnégyzetet, majd írjon be egy másodlagos telefonszámot, ahol szöveges üzeneteket vagy telefonhívásokat fogadhat, ha nem tudja elérni az elsődleges eszközt.

1. Kattintson a **Mentés** gombra.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Új fiók hozzáadása a Microsoft hitelesítő alkalmazásához

Munkahelyi vagy iskolai fiókját az [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) vagy iOS rendszerre készült Microsoft Authenticator alkalmazásban [állíthatja](https://apps.apple.com/app/microsoft-authenticator/id983156458)be.

Ha már beállította munkahelyi vagy iskolai fiókját a Microsoft Authenticator alkalmazásban, nem kell újra megtennie.

1. A **További biztonsági ellenőrzés** lap **Hogyan szeretne válaszolni?** **Set up Authenticator app**

    ![Munkahelyi vagy iskolai fiók beállítása a Microsoft Authenticator alkalmazásban](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Kövesse a képernyőn megjelenő utasításokat, például a mobileszköz használatával a QR-kód beolvasóját, majd válassza a **Tovább**gombot.

    Az adatok ellenőrzéséhez jóvá kell hagynia egy értesítést a Microsoft Authenticator alkalmazáson keresztül.

1. Kattintson a **Mentés** gombra.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Fiók vagy eszköz törlése a Microsoft Authenticator alkalmazásból

A fiókot törölheti a Microsoft Authenticator alkalmazásból, és törölheti az eszközt munkahelyi vagy iskolai fiókjából. Általában törli az eszközt, hogy véglegesen eltávolítson egy elveszett, ellopott vagy régi eszközt a fiókjából, és törli a fiókját, hogy megpróbálja kijavítani a csatlakozási problémákat, vagy hogy megoldjon egy fiókváltozást, például egy új felhasználónevet.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>A készülék törlése munkahelyi vagy iskolai fiókjából

1. A **További biztonsági ellenőrzés** lap **Hogyan szeretne válaszolni?** **Set up Authenticator app**

1. Kattintson a **Mentés** gombra.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>A fiók törlése a Microsoft Authenticator alkalmazásból

A Microsoft Authenticator alkalmazásban válassza a törölni kívánt eszköz melletti **Törlés** gombot.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>A kétfaktoros ellenőrzési kérések bekapcsolása megbízható eszközön

A szervezeti beállításoktól függően megjelenhet egy jelölőnégyzet, amely en a böngészőkétlépéses ellenőrzés végrehajtásakor x nap esetén a **Ne kérjen újra X napot.** Ha ezt a lehetőséget választotta a kétfaktoros ellenőrzési kérések leállításához, majd elveszíti az eszközét, vagy az eszköz potenciálisan veszélybe kerül, a fiók védelme érdekében kapcsolja vissza a kétfaktoros ellenőrzési utasításokat. Az összes eszközre vonatkozóan egyszerre kell bekapcsolnia a figyelmeztetést. Sajnos a kérdésnem kapcsolható vissza csak egy adott eszközre.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>A kétfaktoros ellenőrzés üzenetének visszakapcsolása az eszközökön

A [ **További biztonsági ellenőrzés** lapon](#to-get-to-the-additional-security-verification-page)válassza a **Többtényezős hitelesítés visszaállítása korábbi megbízható eszközökön**lehetőséget. Amikor legközelebb bejelentkezik valamelyik eszközre, a rendszer kéri a kétfaktoros ellenőrzés elvégzését.

## <a name="next-steps"></a>További lépések

A kétfaktoros ellenőrzési beállítások hozzáadása vagy frissítése után kezelheti az alkalmazásjelszavakat, bejelentkezhet, vagy segítséget kaphat néhány gyakori, kéttényezős ellenőrzéssel kapcsolatos problémához.

- [Alkalmazásjelszavak kezelése a kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md) minden olyan alkalmazáshoz, amely nem támogatja a kétfaktoros ellenőrzést.

- [Bejelentkezés kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-signin.md)

- [Gyakori problémák megoldása kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md)
