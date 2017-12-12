---
title: "A Microsoft Authenticator alkalmazáshoz Súgó és támogatás szolgáltatásában |} Microsoft Docs"
description: "Gyakori kérdések és válaszok kapcsolódik a Microsoft Authentication alkalmazást vagy az Azure multi-factor Authentication listáját tartalmazza."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 073b8adfcbe5fdcc2a6d1dba820a1101fac83218
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="microsoft-authenticator-app-faq"></a>A Microsoft hitelesítő alkalmazást – gyakori kérdések

Ebben a cikkben megválaszolunk, amelyet a Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdésekre. Ha nem lát választ a kérdésére, folytassa a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Azt is rendelkezik egy adott szolgáltatás egy másik gyakori kérdések az alkalmazás [jelentkezzen be a telefon – gyakori kérdések](microsoft-authenticator-app-phone-signin-faq.md).

A Microsoft Authenticator alkalmazása helyett az Azure Authenticator alkalmazást, és Azure multi-factor Authentication használata ajánlott alkalmazás esetén. A Microsoft Authenticator alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Mik a kódot az alkalmazásban? Miért nem számbavételi szám legyen?

A Microsoft Authenticator alkalmazás megnyitásakor látni felvételét a fiókok és egy hat vagy nyolc-jegyű szám szerint. Egy 30 másodperces számlálóval jelenhet meg.

Ezek a kódok szolgálnak, amikor bejelentkezik a fiókjába. Miután megadta a felhasználónevét és jelszavát, előfordulhat, hogy kérni a ellenőrző kódot. Nyissa meg a Microsoft Authenticator alkalmazást, és másolja át a kódot, amely jelenleg jelenik. A bejelentkezési oldal befejezéséhez írja be a kódot.

A kódokat a 30 másodpercenként módosító oka az, hogy soha ne használja ugyanazt a kódot kétszer. Nincs például amelyeknek jegyezze meg jelszót. A lényege, hogy a telefon hozzáférést csak valaki tudja-e az ellenőrző kódot.

A kód nem feltétlenül szükséges, interneten vagy adatokat, így nem kell foglalkoznia telefonos való bejelentkezéshez. Az alkalmazás bezárása után nem tovább futnak a háttérben, és azt nem üríti ki a telep. Zárja be az alkalmazást, és figyelmen kívül hagyása a következő időpontig, amikor bejelentkezik a.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Csak kapok értesítések Ha az alkalmazás megnyitása. Ha az alkalmazás nincs megnyitva, nem jelenik meg értesítéseket.

Ha értesítést kap, de nem valamilyen hangot vagy mozog, annak ellenére, hogy az a csengető, először ellenőrizze az alkalmazás beállításait. Az alkalmazás és a hang- vagy mozog, az értesítések engedélyezéséhez.

Ha egyáltalán nem kérek értesítést, ellenőrizze a következő esetekben:

- A telefon távvezérlési vagy csendes módban van? Módban alkalmazások megtartása értesítések küldése.
- Értesítést kaphat a többi alkalmazás? Ha nem, előfordulhat, hogy a hálózati kapcsolatok telefonja, vagy az Android-vagy Apple értesítések csatorna kapcsolatos problémát. Meg lehet oldani az első lehetőség a telefon beállításait a, de a szolgáltató, a második lehetőség segítségre konzultáljon szeretne.
- Akkor kap értesítést, az alkalmazást, de nem mások bizonyos fiókok? Ha igen, távolítsa el a problémát okozó fiókot az alkalmazásból, és adja hozzá újra a leküldéses értesítések engedélyezéséhez.

Ha ezek a hibaelhárítási javaslataival megoldhatja próbált, de továbbra is problémát tapasztal, küldjön a diagnosztikai naplókat. Az alkalmazás beállításait, majd jelölje ki **Súgó és visszajelzés** és **elküldeni a naplókat**. Ezt követően folytassa a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) és tudassa velünk, milyen probléma most azt láthatja, és hogyan próbált eddig.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Már használom a Microsoft Authenticator alkalmazás az ellenőrző kódok kezelésére. Hogyan egy kattintással leküldéses értesítések váltani?
A bejelentkezés révén leküldéses értesítés jóváhagyása csak érhető el személyes Microsoft-fiókok vagy munkahelyi és iskolai fiókok Microsoft, nem a harmadik fél fiókok például a Google vagy Facebook. Ha a munkahelyi vagy iskolai Microsoft-fiókkal rendelkezik, a szervezet választhat letiltja ezt a beállítást.

Ha Ön egy Microsoft-fiókot használni a személyes fiókot és leküldéses értesítések vált át, vegye fel újra a fiók szeretné. Regisztrálja újra az eszközt a fiókjához, és leküldéses értesítések beállítása.  

Ha a Microsoft Authenticator használatát a munkahelyi vagy iskolai fiókkal, majd a szervezet úgy dönt, hogy az egy kattintással értesítések engedélyezi-e.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Egy kattintással leküldéses értesítések a-Microsoft fiókok működnek?
Leküldéses értesítések nem, csak működik a Microsoft-fiókok és az Azure Active Directory-fiókokat. Ha a munkahelyi vagy iskolai használ az Azure AD-fiókok, akkor előfordulhat, hogy letiltani ezt a funkciót.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>Szeretnék saját eszközt biztonsági másolatból történt visszaállítása, és a fiók kódok hiányzik vagy nem működik. mi történt?
Biztonsági okokból azt nem fiókok helyreállítása alkalmazás biztonsági másolatból.  Az alkalmazás visszaállítása után törli a fiókokat, és vegye fel újra.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>Egy új eszközt jelent meg. Hogyan távolíthatja el a Microsoft Authenticator alkalmazása a régi és az újat áthelyezése?
A Microsoft Authenticator alkalmazást ad hozzá egy új eszközt nem automatikusan eltávolítja azokat az eszközöket. Kezelni, mely eszközök vannak konfigurálva a fiókjához, látogasson el, amelyek segítségével kezelheti a kétlépéses ellenőrzést, és eltávolíthatja a régi alkalmazások ugyanazt a webhelyet.

Személyes Microsoft-fiók, az ezen a webhelyen van a [fiókvédelme](https://account.microsoft.com/security) lap. Munkahelyi vagy iskolai Microsoft-fiók, az ezen a webhelyen lehet akár [MyApps](https://myapps.microsoft.com) vagy egy egyéni portál, amely a szervezet van beállítva.

### <a name="how-do-i-remove-an-account-from-the-app"></a>Hogyan távolíthatom egy fiókot az alkalmazásból?
* iOS: a fő képernyőjén lehúzása balra, az a fiók csempéjére. Válassza a **Törlés** elemet.
* Windows Phone: A fő ablakban válassza ki a menü gombjára kattintva, majd **fiókok szerkesztése**. Koppintson a **X** a fiók neve mellett.
* Android: A fő ablakban válassza ki a menü gombjára kattintva, majd **fiókok szerkesztése**. Koppintson a **X** a fiók neve mellett.

Ha a szervezete regisztrált eszközről, szükség lehet egy további lépésre távolítsa el a fiókját. Ezeken az eszközökön a Microsoft Authenticator alkalmazás automatikusan regisztrálva, mint eszközrendszergazdát. Ha szeretné teljesen távolítsa el az alkalmazást, akkor először szüntesse meg az alkalmazás az alkalmazás beállításaiban szereplő regisztrációját.

### <a name="why-does-the-app-request-so-many-permissions"></a>Miért érdemes az alkalmazás olyan sok engedélyek kéréséhez?
Engedélyek a Microsoft kérheti, hogy a teljes listáját itt, és megismerkedhet használatukkal, az alkalmazásban. Látni az adott engedélyekre van phone függ.

* **Kamera**: a kamera használatával QR-kódokat beolvasni, a munkahelyi, iskolai vagy -Microsoft fiók hozzáadásakor.
* **Ügyfelek és a telefon**: Jelentkezzen be személyes Microsoft-fiókjával, ha azt próbálja meg egyszerűsíteni a folyamat található meglévő fiókokat, amelyekkel a telefonján.
* **SMS**: Amikor első alkalommal jelentkezik be személyes Microsoft-fiókjával, győződjön meg arról, hogy a telefonszám megegyezik a rekord tudunk kell. Küldünk egy szöveges üzenetet a telefonján, amelybe letöltötte az alkalmazás. Az üzenet tartalmaz egy 6 – 8 számjegyű ellenőrzőkódot. Ahelyett, hogy ez a kód található, és írja be az alkalmazás kéri, találtunk azt a szöveges üzenet meg.
* **Más alkalmazások keresztül megrajzolásához**: személyazonosságát értesítést kap, ha azt, hogy az értesítési terület felett megjelenített bármely más potenciálisan futó alkalmazás.
* **Adatok fogadása az internetről**: Ez az engedély szükség, az értesítések küldésével.
* **Megakadályozza, hogy a telefon alvó**: Ha az eszköz regisztrálása a szervezet, módosíthatják ezt a házirendet a telefonján.
* **Vezérlő vibráció**: dönthet úgy, hogy milyen egy vibráció személyazonosságát értesítés fogadásakor.
* **Ujjlenyomat hardver**: néhány munkahelyi és iskolai fiókok egy további PIN-kód szükséges, amikor a személyazonosságát. A folyamat MEGKÖNNYÍTÉSE azt lehetővé teszi az ujjlenyomat használatát a PIN-kód megadása helyett.
* **Hálózati kapcsolatok megtekintéséhez**: Microsoft-fiók hozzáadásakor a az alkalmazás hálózati/internetkapcsolatot igényel.
* **A tároló tartalmának olvasását**: Ez az engedély csak akkor használatos, az alkalmazás beállításaiban műszaki probléma jelentése. A tárolóból bizonyos információkat gyűjt a probléma diagnosztizálása érdekében.
* **Teljes hálózati hozzáférés**: Ezzel az engedéllyel kell a személyazonosságát értesítések küldése.
* **Indítási**: Ha újraindítja a telefon, ez az engedély biztosítja, hogy folytatja a személyazonosság értesítéseket kapni.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Miért nem a Microsoft Hitelesítőalkalmazása lehetővé teszi a kérelem jóváhagyása az eszköz zárolásának feloldása nélkül?

Nincs hitelesítési kéréseket jóváhagyni, mivel minden igazolnia kell, hogy rendelkezik-e a telefon Önnel az eszköz zárolásának feloldásához. Kétlépéses ellenőrzést igényel, két dolgot – egy tudja dolog, és egy annyi teendője van igazolására. A tudja dolog a jelszavát. A annyi teendője van, akkor telefonja (állítsa be a Microsoft Authenticator alkalmazást, és egy MFA igazoló néven van regisztrálva.) Ezért a telefont, és meg a kérelem jóváhagyásáról megfelel a a második tényezős hitelesítés.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Mit jelent a lakat ikon a számla lista?

A lakat ikon azt jelzi, hogy az eszköz az Azure ad-ben regisztrált és a fiókot regisztrálni. IOS-eszközök regisztrációja a Microsoft Intune-regisztráció során történik.

## <a name="next-steps"></a>Következő lépések

### <a name="contact-us"></a>Kapcsolat
Kérdését itt nem választ, ha azt szeretné, hogy a véleményét. Lépjen a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) tegye fel a kérdését, és segítséget kérhet a közösségi, vagy szóljon hozzá ezen a lapon.


### <a name="related-topics"></a>Kapcsolódó témakörök
* [Tudnivalók a kétlépéses ellenőrzést](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) Microsoft-fiók
* [Hiba történt a kétlépéses ellenőrzéshez használttal](multi-factor-authentication-end-user-troubleshoot.md) munkahelyi vagy iskolai fiókja?
* [A Microsoft Authenticator használatával jelentkezzen be a telefonjáról](microsoft-authenticator-app-phone-signin-faq.md)
