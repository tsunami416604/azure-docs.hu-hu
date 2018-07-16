---
title: Súgó a Microsoft Authenticator alkalmazás – Azure ad-ben |} A Microsoft Docs
description: Gyakori kérdések és válaszok a Microsoft Authentication alkalmazás és az Azure multi-factor Authentication kapcsolatos listáját tartalmazza.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: d86bc84653e38a9b64a336b8ce9ed7e657129e8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059829"
---
# <a name="microsoft-authenticator-app-faq"></a>A Microsoft Authenticator alkalmazás – gyakori kérdések

Ez a cikk a Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdésekre ad választ. Ha nem látja a választ a kérdésére, lépjen a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Emellett áttekintheti az alkalmazást, egy adott szolgáltatást egy másik gyakori kérdések [bejelentkezés telefonnal kapcsolatos gyakori kérdések](microsoft-authenticator-app-phone-signin-faq.md).

A Microsoft Authenticator alkalmazás helyett az Azure Authenticator alkalmazást, és az Azure multi-factor Authentication használata esetén ajánlott alkalmazás. A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) rendszereken érhető el.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>Milyen adatokat a hitelesítő tárol a nevében, és hogyan lehet törölni?

A Microsoft Authenticator tárolja a fiók adatait, ha a fiók létrehozása. Authenticator használata esetén a diagnosztikai naplót hibakeresési célra létrejön, és hozzájárult a Microsoft bármely előre nem látható problémák diagnosztizálása a hasznos adatokat tárol. A naplóadatok történő megnyitásával is elérheti **súgó** > **naplók küldése** > **megtekinthetők a naplófájlok**.

A fiók csempe törlésével törölheti az adatokat. A fiók csempe törlése is törli a többek között a naplókat az alkalmazás által használt fiók adatait. 

Az adatok Microsoft általi további információért látogasson el: https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>Mik azok a kódokat az alkalmazásban? Miért nem szám szám legyen?

A Microsoft Authenticator alkalmazás megnyitásakor látni a hozzáadott fiókok és a egy hat vagy nyolc számjegyű szám szerint, azok. Egy 30 másodperces számlálóval jelenhet meg.

Ezek a kódok használatosak, amikor bejelentkezik a fiókjába. Miután megadta a felhasználónevét és jelszavát, előfordulhat, hogy kéri, adja meg egy ellenőrző kódot. Nyissa meg a Microsoft Authenticator alkalmazást, és másolja a jelenleg látható kódot. A bejelentkezési oldal befejezéséhez írja be a kódot.

Az okot, amely a kód 30 másodpercenként módosítása, hogy soha ne használja ugyanazt a kódot kétszer van. Például egy jelszó ne felejtse el, amelyeknek nem áll. A cél pedig az, hogy a telefonon való hozzáférést csak a személy tudja-e az ellenőrző kódot.

Így nem kell aggódnia kellene phone szolgáltatást, hogy jelentkezzen be a kódok internet vagy az adatokat, nincs szükség. Ha az alkalmazás bezárásához, nem folyamatosan fut a háttérben, és azt nem kiürítési az akkumulátor. Zárja be az alkalmazást, és figyelmen kívül hagyja a következő időpontig jelentkezik be.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Tudok csak értesítések küldése, ha az alkalmazás van nyitva. Ha az alkalmazás még nincs megnyitva, értesítések nem jelenik meg.

Ha értesítéseket kap, de nem győződjön meg arról, a háttérzaj vagy mozog, annak ellenére, hogy az a csengető, először ellenőrizze az alkalmazás beállításait. Az alkalmazás hang vagy mozog, az értesítések engedélyezéséhez.

Ha egyáltalán nem kap értesítést, ellenőrizze a következő esetekben:

- A telefon az elfoglalt állapot és a csendes módban? Mód alkalmazások megtartása értesítések küldéséhez.
- Értesítéseket kaphat az egyéb alkalmazásokból? Ha nem, előfordulhat, hogy a hálózati kapcsolatokat a telefonjára, vagy az Android- vagy Apple értesítések csatornát a probléma. Meg lehet oldani az első lehetőség a telefon között, de szükség lehet a szolgáltató, a második lehetőség segítség kommunikáljon.
- Kaphat az egyes fiókok, az alkalmazást, de nem más értesítések? Ha igen, távolítsa el a problémát okozó fiókot az alkalmazásból, és adja hozzá újra a leküldéses értesítések engedélyezéséhez.

Ha ezek a hibaelhárítási javaslatok próbált, de még mindig problémákat tapasztal, elküldheti a diagnosztikai naplók. Nyissa meg az alkalmazás beállításait, majd válassza ki **Súgó és visszajelzés** és **naplók küldése**. Ezután nyissa meg a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) és tudassa velünk, mi a probléma akkor jelent meg, és milyen lépések próbálta eddig.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Már használom a Microsoft Authenticator alkalmazás az ellenőrző kódok kezelésére. Hogyan válthatok az egy kattintással leküldéses értesítések?
A bejelentkezési keresztül leküldéses értesítés jóváhagyása csak akkor érhető el a személyes Microsoft-fiókok vagy munkahelyi és iskolai Microsoft-fiókok, mint a Google vagy a Facebook külső fiókok esetében nem. Ha rendelkezik munkahelyi vagy iskolai Microsoft-fiókot, tiltsa le ezt a beállítást a szervezet választhat.

Ha Microsoft-fiókot használ a személyes fiókjához, és átválthat a leküldéses értesítéseket szeretne, vegye fel újra a fiókját szeretné. Regisztrálja újra az eszközt a fiókjához, és beállíthatja a leküldéses értesítéseket.  

Ha használja a Microsoft Authenticator a munkahelyi vagy iskolai fiókkal, majd a szervezet úgy dönt, hogy egyetlen kattintással értesítéseket engedélyezik-e.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Egykattintásos leküldéses értesítések a Microsoft fiókok esetében működnek?
Leküldéses értesítések nem, csak munkahelyi Microsoft-fiókok és az Azure Active Directory-fiókokat. Ha a munkahelyi vagy iskolai Azure AD-fiókokat használnak, azok előfordulhat, hogy letiltja ezt a szolgáltatást.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Tudok itt van egy új eszköz vagy az eszköz biztonsági másolatból. Hogyan állíthatok be a Microsoft Authenticator alkalmazást a saját fiókok újra?
Ha iOS-eszközt futtatja, be van kapcsolva **automatikus biztonsági mentés**, és a régi eszközön; biztonsági másolatot a fiókokhoz létrehozott biztonsági másolat segítségével helyreállíthatja a a fiók hitelesítő adatait az új eszköz. További információ: a [biztonsági mentés és helyreállítás fiók hitelesítő adatait, a Microsoft Authenticator alkalmazás](microsoft-authenticator-app-backup-and-recovery.md) cikk. 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>E az eszköz elvész, vagy áthelyezni új eszköz. Hogyan ellenőrizhetem, hogy értesítések továbbra is Ugrás a régi-eszközöm nem?  
A Microsoft Authenticator alkalmazást ad hozzá az új iOS-eszköz nem távolítja el automatikusan az alkalmazást a régi eszközről. Az alkalmazás még régi az eszköz törlésével nem elég. Kell mind az alkalmazás törli a régi eszközről, és mondja el a Microsoft vagy a szervezet felejtse el a régi eszközök és regisztrációját a fiókját.
- **Szeretné eltávolítani az alkalmazást az eszközről személyes Microsoft-fiókkal.** A kétlépéses ellenőrzés területén lépjen a [fiók biztonsági](https://account.microsoft.com/security) lapon és a régi eszköz ellenőrzési kikapcsolására.  
- **A távolítsa el az alkalmazást az eszközről a munkahelyi vagy iskolai Microsoft-fiókjával.** A kétlépéses ellenőrzés területén lépjen a [MyApps](https://myapps.microsoft.com/) lapján vagy a szervezet egyéni portálra és a régi eszköz ellenőrzési kikapcsolására. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>Hogyan távolíthatom el a egy fiókot az alkalmazásból?
* iOS: a fő képernyőről, pöccintsen balra, az a fiók csempéjére. Válassza a **Törlés** elemet.
* Windows Phone: A fő képernyőjén válassza ki a menügombra, majd **fiókok szerkesztése**. Koppintson a **X** a fiók neve mellett.
* Android: A fő képernyőjén válassza ki a menügombra, majd **fiókok szerkesztése**. Koppintson a **X** a fiók neve mellett.

Ha a szervezet regisztrált eszközzel rendelkezik, szükség lehet egy plusz lépésre távolítsa el a fiókját. Ezeken az eszközökön a Microsoft Authenticator alkalmazás automatikusan regisztrálva van egy eszköz rendszergazdaként. Ha szeretne teljesen távolítsa el az alkalmazást, először szüntesse meg az alkalmazás az alkalmazás beállításaiban regisztrációját szeretné.

### <a name="why-does-the-app-request-so-many-permissions"></a>Miért érdemes az alkalmazás sok engedélyek kéréséhez?
Íme az engedélyeket, amelyek is meg kell adnia a teljes listáját, és azok hogyan használhatók az alkalmazásban. Láthatja a konkrét engedélyeket rendelkezik telefonos típusától függenek.

* **Kamera**: használt QR-kódok beolvasása egy munkahelyi, iskolai vagy -Microsoft fiók hozzáadásakor.
* **Névjegyek és phone**: használja a folyamat leegyszerűsítése érdekében a meglévő fiókok a telefonján felderítésével, amikor a személyes Microsoft-fiókkal jelentkezik be.
* **SMS**:, hogy a telefonszám megegyezik a rekordok száma. Amikor bejelentkezik a személyes Microsoft-fiókkal jelentkezik be először.  Azt szöveges üzenetet küldjön a telefonjára, amelyre letöltötte az alkalmazást, amelyet 6-8 jegyű ellenőrző kódot tartalmaz. Ahelyett, hogy keresse meg a kódot, és írja be az alkalmazás kéri, megtalálható a szöveges üzenetben az Ön számára.
* **Más alkalmazások keresztül rajzolása**: esetlegesen futó többi alkalmazást is megjelenik az értesítést kap, amely ellenőrzi a személyazonosságát.
* **Az internetről érkező adatok fogadására**: erre az engedélyre szükség, az értesítések küldéséhez.
* **Megakadályozhatja, hogy a telefon alvó**: az eszköz regisztrálása a munkahelyén, ha a szervezet módosíthatja ezt a házirendet a telefonján.
* **Rezgés szabályozhatja**: választhat, hogy milyen egy rezgés, amikor a rendszer értesíti a személyazonosságát.
* **Ujjlenyomat-alapú hardver**: egyes munkahelyi és iskolai fiókok egy további PIN-kód szükséges, minden alkalommal, amikor a személyazonosságát. A folyamat MEGKÖNNYÍTÉSE érdekében lehetővé tesszük az ujjlenyomat használatát a PIN-kód megadása helyett.
* **Hálózati kapcsolatok megtekintéséhez**: Amikor hozzáad egy Microsoft-fiókkal, az alkalmazás hálózat és internet kapcsolat szükséges.
* **A tároló tartalmának a beolvasása**: Ez az engedély csak akkor használatos, az alkalmazás beállításaival technikai probléma jelentése. A probléma diagnosztizálása érdekében gyűjt bizonyos adatokat, a storage-ból.
* **Teljes hálózati hozzáférést**: Ez az engedély megadása kötelező a személyazonosságát értesítések küldéséhez.
* **Indítási**: Ha a telefonja újra, ezt az engedélyt biztosítja, hogy továbbra is igazolhatja személyazonosságát értesítéseket kap.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Miért érdemes a Microsoft Authenticator alkalmazás lehetővé teszi, hogy hagyja jóvá a kérést az eszköz zárolásának feloldása nélkül?

Nem kell az ellenőrzési kérések jóváhagyása, mert az összes igazolnia kell, hogy rendelkezik-e a telefonjára, az eszköz zárolásának feloldásához. A kétlépéses ellenőrzés szükséges igazolására két dolgokat – tudja egy dolog, és a egy dolog van. A tudja lényeg a jelszavát. A rendelkezik lényeg a telefon (állítsa be a Microsoft Authenticator alkalmazás és a egy megvalósíthatósági példában MFA regisztrált.) Ezért a telefon kapcsolatban, és a kérés jóváhagyása megfelel a feltételeknek, a hitelesítés második tényezőjét, a.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>Mit jelent a lakat ikonra, a számla lista?

A lakat ikon azt jelzi, hogy az eszköz Azure AD-ben regisztrált és a fiókjába regisztrált. IOS-es eszközregisztráció a Microsoft Intune-regisztráció során történik.

## <a name="next-steps"></a>További lépések

### <a name="contact-us"></a>Kapcsolat
Ha a kérdését itt nem válaszolt, szeretnénk meghallgatnánk a véleményét. Nyissa meg a [Microsoft Authenticator alkalmazás fórum](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) tegye fel a kérdéseit, és segítséget kérhet a Közösség, vagy szóljon hozzá ezen a lapon.


### <a name="related-topics"></a>Kapcsolódó témakörök
* [Tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) Microsoft-fiókok
* [Problémákat tapasztal a kétlépéses ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md) munkahelyi vagy iskolai fiókja?
* [Jelentkezzen be a telefonról a Microsoft Authenticator használatával](microsoft-authenticator-app-phone-signin-faq.md)
