---
title: Mi a biztonságos identitás-pontszám? – Az azure Active Directory
description: Hogyan használhatja a biztonságos identitás-pontszám a könyvtár biztonsági irányelvei, tiltják javítása érdekében
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988690"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Mi az az identitás biztonságos pontszám, az Azure Active Directoryban?

Mennyire biztonságos az Ön Azure AD-bérlője? Ha nem ismeri ezt a legkönnyebben, ez a cikk bemutatja, hogyan a biztonságos identitás-pontszám segítségével megfigyelheti és javíthatja biztonsági helyzetét identitás.

## <a name="what-is-an-identity-secure-score"></a>Mi az az identitásbiztonsági pontszám?

Az identitás biztonságos pontszám: 1 és 223, mely hogyan igazított a van a Microsoft kapcsolatos bevált gyakorlatokat biztonsági mutatója közötti számot. Minden egyes fokozása művelethez biztonságos identitás-pontszám a konkrét konfigurációs személyre szabott.  

![Biztonsági pontszám](./media/identity-secure-score/identity-secure-score-overview.png)

A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

A pontszámot és a kapcsolódó információkat az identitásbiztonsági pontszám irányítópultján találhatja meg. Ezen az irányítópulton a következők találhatók:

- A biztonságos identitás-pontszám
- Összehasonlítás grafikont hogyan hasonlítja össze a biztonságos identitás-pontszám a más bérlők számára az azonos iparágba és hasonló mérete
- Trend grafikont hogyan változott meg a biztonságos identitás-pontszám időbeli alakulása
- Fejlesztési lehetőségek listája

A fejlesztési műveletek végrehajtásával a következőket érheti el:

- Javíthatja biztonsági helyzetét és a pontszám
- Kihasználhatja a szervezet identitás befektetéseit részeként elérhető funkciók

## <a name="how-do-i-get-my-secure-score"></a>Hogyan tekinthetem meg a saját biztonsági pontszámomat?

A biztonságos identitás-pontszám az Azure AD minden változatban érhető el. Pontszámát az [Azure AD áttekintési irányítópultján](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore) érheti el.

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure 48 óránként áttekinti az Ön biztonsági beállításait, és összehasonlítja azokat az ajánlott eljárásokkal. Az értékelés eredménye alapján a új pontszámának számítják ki a címtár számára. Akkor lehet, hogy a biztonsági beállítások nem teljesen felel az ajánlott eljárásokkal kapcsolatos útmutatás és a javító műveleteket csak részlegesen teljesülnek. Ezekben az esetekben, fog csak oda a vezérlő elérhető maximális pontszám egy részét.

A rendszer az Ön Azure AD-konfigurációjától függően tesz javaslatokat. Ajánlott eljárás ajánlás engedélyezése harmadik féltől származó termékekre használ, ha ezt a konfigurációt a beállítások egy fokozása művelet adhatja meg. Akkor is állíthatja be a javaslatok a rendszer figyelmen kívül hagyja, ha a környezet nem vonatkoznak. A figyelmen kívül hagyott javaslatok nincsenek hatással a pontszám kiszámítására.

![Hagyja figyelmen kívül vagy harmadik fél által biztosított művelet megjelölése](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Miben nyújt segítséget?

A biztonsági pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="who-can-use-the-identity-secure-score"></a>Kik használhatják az identitásbiztonság pontszámot?

Az identitásbiztonság pontszámot a következő szerepkörök használhatják:

- Globális rendszergazda
- Biztonsági rendszergazda
- Biztonsági olvasók

### <a name="how-are-controls-scored"></a>Hogyan kell kiértékelni vezérlők?

Vezérlők kétféle módon lehet pontozandó. Néhány bináris módon kell kiértékelni, – 100 %-a pontszámot kap, ha a szolgáltatás vagy az ajánlott megoldás a konfigurált beállítás alapján. Más pontszámok kiszámítása a teljes konfiguráció százalékában. Például ha 30 pontok fog kapni, ha a felhasználókat az MFA az összes védett, és csak 5 védett 100 teljes felhasználói állapotokat a fokozása javaslat, volna adni egy részleges pontszám körülbelül 2 pont (védett 5 / 100 teljes * 30 maximális pt = 2, PT részleges pontszám) .

### <a name="what-does-not-scored-mean"></a>Mit jelent a [Not Scored]?

A [Not Scored] jelzéssel ellátott műveletek olyan műveletek, amelyeket elvégezhet a vállalatában, de a pontszám kiszámításakor a rendszer nem veszi őket figyelembe, mert (még!) nincsenek csatolva az eszközhöz. Így is javíthat a biztonsági beállításain, azonban a rendszer ilyen esetekben egyelőre nem veszi figyelembe ezeket a műveleteket.

### <a name="how-often-is-my-score-updated"></a>Milyen gyakran frissül a pontszám?

A pontszámot a rendszer naponta számítja újra (PST idő szerint kb. 01:00-kor). Ha változtatást eszközöl valamelyik mért műveleten, a pontszám a következő napon automatikusan frissül. Ahhoz, hogy egy változás megjelenjen a pontszámban is, akár 48 órának is el kell telnie.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Megváltozott a pontszámom. Hogyan tudhatom meg, mi ennek az oka?

Látogasson el a [a security center a Microsoft 365](https://security.microsoft.com/), a teljes Microsoft biztonságos pontszám találja. Egyszerűen megtekintheti összes módosítást a biztonságos pontszámot a részletes módosításokat az Előzmények lapon áttekintésével.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Nem a biztonságos pontszám saját első megsértették kockázatát mérik?

Röviden: nem. A biztonságos pontszám nem express egy abszolút mérték hogy mennyire valószínű, hogy szeretné-e get-megszegést kiváltó. Ez a számérték inkább azt mutatja, milyen mértékben tett lépéseket azért, hogy az illetéktelen behatolást megakadályozó funkciókat bevezessen. Nincs szolgáltatás tud garantálni, hogy Ön nem fog megsértették, és a biztonságos pontszám annak garantálására semmilyen módon nem kell értelmezni.

### <a name="how-should-i-interpret-my-score"></a>Hogyan értelmezendő a pontszám?

Akkor kap pontokat, ha a javasolt módon konfigurálja a biztonsági funkciókat, vagy a biztonsághoz kötődő műveleteket végez (például jelentéseket olvas). Egyes műveletek részleges végrehajtása is pontot ér – ilyen például a többtényezős hitelesítés (MFA) engedélyezése a felhasználók számára. A biztonságos pontszám közvetlenül is használhatja a Microsoft biztonsági szolgáltatások reprezentatív. Ne feledje, hogy a biztonságot a használhatósági kell meghatározni. Minden biztonsági vezérlő hatással van a felhasználókra is. A kis felhasználói hatású vezérlők elenyésző hatással vannak a felhasználók mindennapi műveleteire.

A pontszám előzmények megtekintéséhez látogasson el a [a security center a Microsoft 365](https://security.microsoft.com/) és a teljes Microsoft biztonságos pontszámát. Áttekintheti a módosításokat, a teljes biztonságos pontszám kattintva lehet előzményeinek megtekintése. Egy adott dátumot kiválasztva megtekintheti, mely vezérlők voltak engedélyezve az adott napon, és hány pontot kapott ezekre.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hogyan kapcsolódik az identitásbiztonsági pontszám az Office 365 biztonsági pontszámhoz?

A [Microsoft biztonságos pontszám](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) tartalmaz öt különböző vezérlő és pontszám kategóriák:

- Identitás
- Adatok
- Eszközök
- Infrastruktúra
- Alkalmazások

A biztonságos identitás-pontszám a Microsoft biztonságos pontszám identitás részét jelöli. Az átfedés azt jelenti, hogy az identitás a javaslatok biztonságos pontszám a Microsoft identity pontszám megegyeznek.

## <a name="next-steps"></a>További lépések

[Tudjon meg többet a Microsoft biztonságos pontszám](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
