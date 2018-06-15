---
title: A Microsoft Authenticator alkalmazáshoz Súgó és támogatás szolgáltatásában |} Microsoft Docs
description: Gyakori kérdések és válaszok kapcsolódik a Microsoft Authentication alkalmazást vagy az Azure multi-factor Authentication listáját tartalmazza.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: e9c152fddbfcd603a84caf2c168ec4f12368dcfa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795867"
---
# <a name="microsoft-authenticator-app-faq"></a>A Microsoft hitelesítő alkalmazást – gyakori kérdések

Ez a cikk a Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdésekre ad választ. Ha nem lát választ a kérdésére, folytassa a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Emellett tekintse át az alkalmazás, egy adott szolgáltatás egy másik gyakori kérdések [jelentkezzen be a telefon – gyakori kérdések](microsoft-authenticator-app-phone-signin-faq.md).

A Microsoft Authenticator alkalmazása helyett az Azure Authenticator alkalmazást, és Azure multi-factor Authentication használata ajánlott alkalmazás esetén. A Microsoft Authenticator alkalmazás érhető el [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), és [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Milyen adatokat a hitelesítő tárol a nevében, és hogyan törölné?

A Microsoft Authenticator tárolja fiókot hoz létre, ha a fiók beállítása. Hitelesítő használata esetén a diagnosztikai naplófájl hibakeresési célra létrejön, és segíti a Microsoftot előre nem látott probléma merül fel a hasznos adatot tárol. A naplóadatok férhetnek megnyitása **súgó** > **naplók küldése** > **naplók megtekintése**.

A fiók csempéjére törlésével törölni lehet az adatokat. A fiók csempéjére törlésével, beleértve a naplók az alkalmazás által használt fiók adatait is törli. 

Hogyan Microsoft használja az adatok a további tudnivalókért keresse fel: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

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

Ha ezek a hibaelhárítási javaslatok próbált, de továbbra is problémát tapasztal, elküldheti a diagnosztikai naplókat. Az alkalmazás beállításait, majd jelölje ki **Súgó és visszajelzés** és **elküldeni a naplókat**. Ezt követően folytassa a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) és tudassa velünk, milyen probléma most azt láthatja, és hogyan próbált eddig.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Már használom a Microsoft Authenticator alkalmazás az ellenőrző kódok kezelésére. Hogyan egy kattintással leküldéses értesítések váltani?
A bejelentkezés révén leküldéses értesítés jóváhagyása csak érhető el személyes Microsoft-fiókok vagy munkahelyi és iskolai fiókok Microsoft, nem a harmadik fél fiókok például a Google vagy Facebook. Ha a munkahelyi vagy iskolai Microsoft-fiókkal rendelkezik, a szervezet választhat letiltja ezt a beállítást.

Ha Ön egy Microsoft-fiókot használni a személyes fiókot és leküldéses értesítések vált át, vegye fel újra a fiók szeretné. Regisztrálja újra az eszközt a fiókjához, és leküldéses értesítések beállítása.  

Ha a Microsoft Authenticator használatát a munkahelyi vagy iskolai fiókkal, majd a szervezet úgy dönt, hogy az egy kattintással értesítések engedélyezi-e.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Egy kattintással leküldéses értesítések a-Microsoft fiókok működnek?
Leküldéses értesítések nem, csak működik a Microsoft-fiókok és az Azure Active Directory-fiókokat. Ha a munkahelyi vagy iskolai használ az Azure AD-fiókok, akkor előfordulhat, hogy letiltani ezt a funkciót.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>I készült új eszköz vagy az eszköz biztonsági másolatból történt visszaállítása. Hogyan állíthatom be a fiókot a Microsoft Authenticator alkalmazásban újra?
Ha egy iOS-eszközön fut, be van kapcsolva **automatikus biztonsági mentési**, és a régi eszköz; a fiók biztonsági létrehozott biztonsági másolat segítségével helyreállíthatja a fiók hitelesítő adatait az új eszköz. További információ: a [biztonsági mentés és helyreállítás fiók hitelesítő adatait a Microsoft Authenticator alkalmazással](microsoft-authenticator-app-backup-and-recovery.md) cikk. 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>Lehet az eszköz elvesztése vagy áthelyezni új eszköz. Hogyan tehetem, értesítések nem továbbra is a régi eszköz Ugrás?  
A Microsoft Authenticator alkalmazás hozzáadása az új iOS-eszköz nem távolítja el automatikusan az alkalmazást a régi eszközről. Az alkalmazás régi eszköz törlésével még nem elegendőek. Is törölnie kell az alkalmazást a régi eszközről, majd kérje meg a Microsoft vagy a szervezet elfelejti a régi eszköz és a regisztráció fiókjából.
- **Eltávolítja az alkalmazást egy eszközről, a személyes Microsoft-fiókkal.** A kétlépéses ellenőrzés területén lépjen a [fiók biztonsági](https://account.microsoft.com/security) lapon, és a régi eszköz ellenőrzési kikapcsolhatja.  
- **A távolíthatja el az alkalmazást a munkahelyi vagy iskolai Microsoft-fiókkal.** A kétlépéses ellenőrzés területén lépjen a [MyApps](https://myapps.microsoft.com/) lapján vagy a szervezete egyéni portálra és a régi eszköz ellenőrzési kikapcsolhatja. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Hogyan távolíthatom egy fiókot az alkalmazásból?
* iOS: a fő képernyőjén lehúzása balra, az a fiók csempéjére. Válassza a **Törlés** elemet.
* Windows Phone: A fő ablakban válassza ki a menü gombjára kattintva, majd **fiókok szerkesztése**. Koppintson a **X** a fiók neve mellett.
* Android: A fő ablakban válassza ki a menü gombjára kattintva, majd **fiókok szerkesztése**. Koppintson a **X** a fiók neve mellett.

Ha a szervezete regisztrált eszközről, szükség lehet egy további lépésre távolítsa el a fiókját. Ezeken az eszközökön a Microsoft Authenticator alkalmazás automatikusan regisztrálva, mint eszközrendszergazdát. Ha szeretné teljesen távolítsa el az alkalmazást, akkor először szüntesse meg az alkalmazás az alkalmazás beállításaiban szereplő regisztrációját.

### <a name="why-does-the-app-request-so-many-permissions"></a>Miért érdemes az alkalmazás olyan sok engedélyek kéréséhez?
Az engedélyeket, is meg kell adnia a teljes listáját itt, és megismerkedhet használatukkal, az alkalmazásban. Látni az adott engedélyekre van phone függ.

* **Kamera**: QR-kódokat beolvasni, amikor a munkahelyi, iskolai vagy -Microsoft fiók használatával.
* **Ügyfelek és a telefon**: a folyamat leegyszerűsítése érdekében a meglévő fiókok a telefonján található, amikor jelentkezzen be személyes Microsoft-fiókjával használt.
* **SMS**: biztosítja a telefonszámát megegyezik a rekordok száma. Ha a személyes Microsoft fiókkal jelentkezik először.  Küldünk egy szöveges üzenetet a telefonján, amelybe letöltötte az alkalmazást, amely tartalmazza az ellenőrző kódot 6 – 8 számjegyű. Ahelyett, hogy ez a kód található, és írja be az alkalmazás kéri, talált a szöveges üzenet meg.
* **Más alkalmazások keresztül megrajzolásához**: bármely alkalmazás, potenciálisan futó is megjelenik az értesítést kap, amely ellenőrzi a személyazonosságát.
* **Adatok fogadása az internetről**: Ez az engedély szükség, az értesítések küldésével.
* **Megakadályozza, hogy a telefon alvó**: az eszköz regisztrálása a szervezet, ha a szervezet módosíthatja ezt a házirendet a telefonján.
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

## <a name="next-steps"></a>További lépések

### <a name="contact-us"></a>Kapcsolat
Kérdését itt nem választ, ha azt szeretné, hogy a véleményét. Lépjen a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) tegye fel a kérdését, és segítséget kérhet a közösségi, vagy szóljon hozzá ezen a lapon.


### <a name="related-topics"></a>Kapcsolódó témakörök
* [Tudnivalók a kétlépéses ellenőrzést](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) Microsoft-fiók
* [Hiba történt a kétlépéses ellenőrzéshez használttal](multi-factor-authentication-end-user-troubleshoot.md) munkahelyi vagy iskolai fiókja?
* [A Microsoft Authenticator használatával jelentkezzen be a telefonjáról](microsoft-authenticator-app-phone-signin-faq.md)
