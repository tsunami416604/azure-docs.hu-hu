---
title: Kétlépéses ellenőrzés beállítása – Azure Active Directory | Microsoft Docs
description: Ha a vállalata az Azure multi-Factor Authenticationt konfigurálja, a rendszer kérni fogja, hogy regisztráljon a kétlépéses ellenőrzésre. Útmutató a beállításához.
services: active-directory
keywords: Az Azure Directory és az Active Directory használata a felhőben, Active Directory-oktatóanyag
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75bc067bfe8a98ef2337f368243b3221be1677d6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949894"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Saját fiók beállítása kétlépéses ellenőrzéshez
A kétlépéses ellenőrzés egy további biztonsági lépés, amely segít a fiók védelmében azáltal, hogy nehezebbé teszi a többi személy számára a betörést. Ha elolvasta ezt a cikket, valószínűleg egy e-mailt kapott a munkahelyi vagy iskolai rendszergazdától a többtényezős hitelesítésről. Vagy lehet, hogy megpróbált bejelentkezni, és üzenetet kapott, amely arra kéri, hogy állítson be további biztonsági ellenőrzést. Ebben az esetben nem lehet bejelentkezni, **amíg el nem végezte az automatikus regisztrálási folyamatot**.

Ebből a cikkből megtudhatja, hogyan állíthatja be **munkahelyi vagy iskolai fiókját**. Ha engedélyezni szeretné a kétlépéses ellenőrzést a saját, személyes Microsoft-fiók esetében, tekintse meg a kétlépéses [ellenőrzést ismertető](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)témakört.

## <a name="set-up-your-account"></a>Fiók beállítása

Ha a cég informatikai támogatási szolgálata megköveteli a kétlépéses ellenőrzés használatát, megjelenik egy képernyő, amely szerint **a rendszergazdának be kell állítania ezt a fiókot a további biztonsági ellenőrzéshez**:

![Beállítás](./media/multi-factor-authentication-end-user-first-time/first.png)

Első lépésként válassza a **beállítás most lehetőséget.**

Ha a bejelentkezéskor nem látja ezt a képernyőt, kövesse a [beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md#using-the-additional-security-verification-page) a kétlépéses ellenőrzéshez című témakör útmutatását, ahol megkeresheti a beállítások lapot, ahol kezelheti az ellenőrzési lehetőségeket.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Döntse el, hogyan szeretné ellenőrizni a bejelentkezéseket

A beléptetési folyamat első kérdése, hogy hogyan szeretné felvenni Önnel a kapcsolatot. Tekintse át a táblázat lehetőségeit, és használja a hivatkozásokat az egyes metódusok telepítési lépéseinek megtekintéséhez.

| Kapcsolattartási mód | Leírás |
| --- | --- |
| [Mobile-alkalmazás](#use-a-mobile-app-as-the-contact-method) |- **Értesítések fogadása az ellenőrzéshez.** Ez a beállítás egy értesítést küld az okostelefonján vagy a táblaszámítógépen lévő hitelesítő alkalmazásnak. Tekintse meg az értesítést, és ha az megbízható, válassza a **hitelesítés** lehetőséget az alkalmazásban. Előfordulhat, hogy a munkahelye vagy iskolája megköveteli, hogy a hitelesítés előtt PIN-kódot adjon meg.<br>- **Ellenőrző kód használata.** Ebben a módban a hitelesítő alkalmazás 30 másodpercenként frissülő ellenőrző kódot hoz létre. Adja meg a jelenlegi ellenőrző kódot a bejelentkezési felületen.<br>Az Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594) és [iOS](https://go.microsoft.com/fwlink/?linkid=866594)rendszerhez érhető el.|
| [Mobil telefonhívás vagy szöveg](#use-your-mobile-phone-as-the-contact-method) |- A **telefonhívás** egy automatikus hanghívást helyez el az Ön által megadott telefonszámra. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a # gombot a telefon billentyűzetén.<br>- A **szöveges üzenet** egy ellenőrző kódot tartalmazó szöveges üzenetet ér véget. A szövegben szereplő Rákérdezés után válaszoljon a szöveges üzenetre, vagy adja meg a bejelentkezési felületen megadott ellenőrző kódot. |
| [Irodai telefonhívás](#use-your-office-phone-as-the-contact-method) |Elhelyez egy automatikus hanghívást az Ön által megadott telefonszámon. Válaszolja meg a hívást, majd a hitelesítéshez nyomja meg a telefon billentyűzetén a # gombot. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Mobil alkalmazás használata kapcsolattartási módszerként
Ennek a módszernek a használata megköveteli, hogy egy hitelesítő alkalmazást telepítsen a telefonján vagy a Tableten. A cikkben ismertetett lépések az [Android](https://go.microsoft.com/fwlink/?Linkid=825072) és az [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)rendszerhez elérhető Microsoft Authenticator alkalmazáson alapulnak.

>[!NOTE]
>Nem kell használnia a Microsoft Authenticator alkalmazást. Ha már használ egy másik hitelesítő alkalmazást, továbbra is használhatja azt.

1. Válassza ki a **Mobile App** elemet a legördülő listából.
2. Válassza **az értesítések fogadása** vagy az **ellenőrző kód használata**lehetőséget, majd válassza a **beállítás**lehetőséget.

   ![További biztonsági ellenőrző képernyő](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. A telefonján vagy a táblaszámítógépen nyissa meg az **+** alkalmazást, és válassza a lehetőséget egy fiók hozzáadásához. (Androidos eszközökön válassza ki a három pontot, majd **vegye fel a fiókot**.)
4. Adja meg, hogy munkahelyi vagy iskolai fiókot kíván-e hozzáadni. Megnyílik a QR-kód képolvasó a telefonján. Ha a kamera nem működik megfelelően, választhatja a vállalati adatok manuális megadását. További információ: [fiók manuális hozzáadása](#add-an-account-manually).  
5. Olvassa be a mobil alkalmazás konfigurálására szolgáló képernyőn megjelenő QR-kód képét.  A QR-kód képernyő bezárásához kattintson a **kész** gombra.  

   ![QR-kód képernyő](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Ha az aktiválás befejeződik a telefonon, válassza a **Kapcsolatfelvétel**lehetőséget.  Ez a lépés vagy egy értesítést vagy egy ellenőrző kódot küld a telefonjára. Válassza az **ellenőrzés**lehetőséget.  
7. Ha a vállalatának PIN-kódot kell megadnia a bejelentkezési ellenőrzés jóváhagyásához, adja meg azt.

   ![PIN-kód megadására szolgáló mező](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Miután befejeződött a PIN-kód bevitele, válassza a **Bezárás**lehetőséget. Ezen a ponton az ellenőrzésnek sikeresnek kell lennie.
9. Azt javasoljuk, hogy adja meg mobiltelefonszámát, ha elveszti a hozzáférést a Mobile apps szolgáltatáshoz. Adja meg az országot/régiót a legördülő listából, és írja be mobiltelefonszámát az ország/régió neve melletti mezőbe. Kattintson a **Tovább** gombra.
10. Ekkor a rendszer felszólítja az alkalmazások jelszavainak beállítására a nem böngésző alkalmazásokhoz, például az Outlook 2010-as vagy régebbi verziójához, vagy az Apple-eszközökön futó natív e-mail alkalmazáshoz. Ennek az az oka, hogy egyes alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **kész** gombra, és hagyja ki a többi lépést.
11. Ha ezeket az alkalmazásokat használja, másolja a megadott jelszót, és illessze be az alkalmazásba a normál jelszó helyett. Több alkalmazáshoz ugyanazt az alkalmazás-jelszót használhatja. További információ: [Súgó az alkalmazások jelszavai számára].
12. Kattintson a **Done** (Kész) gombra.

### <a name="add-an-account-manually"></a>Fiók manuális hozzáadása
Ha a QR-olvasó helyett manuálisan szeretne fiókot hozzáadni a Mobile apps szolgáltatáshoz, kövesse az alábbi lépéseket.

1. Jelölje be a **fiók manuális megadása** gomb.  
2. Adja meg a kódot és az URL-címet, amely a vonalkódot megjelenítő oldalon található. Ez az információ a Mobile App **Code** és **URL** mezőiben található.

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Ha az aktiválás befejeződött, válassza a **Kapcsolatfelvétel**lehetőséget. Ez a lépés vagy egy értesítést vagy egy ellenőrző kódot küld a telefonjára. Válassza az **ellenőrzés**lehetőséget.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Mobiltelefon használata kapcsolattartási módszerként
1. Válassza a **hitelesítési telefon** lehetőséget a legördülő listából.  

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Válassza ki az országot/régiót a legördülő listából, és adja meg mobiltelefonszámát.
3. Válassza ki azt a módszert, amelyet használni szeretne a mobiltelefonos szöveggel vagy a hívással.
4. Válassza a **Kapcsolatfelvétel** lehetőséget a telefonszám ellenőrzéséhez. A kiválasztott módból függően szöveges üzenetet küldünk, vagy meghívja Önt. Kövesse a képernyőn megjelenő utasításokat, majd válassza az **ellenőrzés**lehetőséget.
5. Ekkor a rendszer felszólítja az alkalmazások jelszavainak beállítására a nem böngésző alkalmazásokhoz, például az Outlook 2010-as vagy régebbi verziójához, vagy az Apple-eszközökön futó natív e-mail alkalmazáshoz. Ennek az az oka, hogy egyes alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **kész** gombra, és hagyja ki a többi lépést.
6. Ha ezeket az alkalmazásokat használja, másolja a megadott jelszót, és illessze be az alkalmazásba a normál jelszó helyett. Több alkalmazáshoz ugyanazt az alkalmazás-jelszót használhatja. További információ: [Súgó az alkalmazások jelszavai számára].
7. Kattintson a **Done** (Kész) gombra.

## <a name="use-your-office-phone-as-the-contact-method"></a>Az irodai telefon használata kapcsolattartási módszerként
1. Válassza az **Office Phone** lehetőséget a legördülő menüből  

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A telefonszám mezőt a rendszer automatikusan kitölti a vállalati kapcsolattartási adataival. Ha a szám helytelen vagy hiányzik, kérje meg a rendszergazdát, hogy hajtsa végre a módosításokat.
3. Válassza a **Kapcsolatfelvétel** lehetőséget, hogy ellenőrizze a telefonszámát, és meghívjuk a számot. Kövesse a képernyőn megjelenő utasításokat, majd válassza az **ellenőrzés**lehetőséget.
4. Ekkor a rendszer felszólítja az alkalmazások jelszavainak beállítására a nem böngésző alkalmazásokhoz, például az Outlook 2010-as vagy régebbi verziójához, vagy az Apple-eszközökön futó natív e-mail alkalmazáshoz. Ennek az az oka, hogy egyes alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **kész** gombra, és hagyja ki a többi lépést.
5. Ha ezeket az alkalmazásokat használja, másolja a megadott jelszót, és illessze be az alkalmazásba a normál jelszó helyett. Több alkalmazáshoz ugyanazt az alkalmazás-jelszót használhatja. További információ: [Mi az alkalmazás jelszava](multi-factor-authentication-end-user-app-passwords.md).
6. Kattintson a **Done** (Kész) gombra.

## <a name="next-steps"></a>További lépések
* Az előnyben részesített beállítások módosítása és [a beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md) kétlépéses ellenőrzéshez
* Állítsa be a kétlépéses ellenőrzést nem támogató natív eszköz alkalmazásaihoz tartozó [alkalmazás jelszavait](multi-factor-authentication-end-user-app-passwords.md) .
* Tekintse meg a [Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) a gyors és biztonságos hitelesítéshez, még akkor is, ha nem rendelkezik a Sejtterápiás szolgáltatással.
