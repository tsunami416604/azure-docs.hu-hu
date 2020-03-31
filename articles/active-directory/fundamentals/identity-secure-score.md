---
title: Mi az identitás biztonságos pontszám? - Azure Active Directory
description: Hogyan használhatja az identitás biztonságos pontszám, hogy javítsa a biztonsági testtartás a könyvtár
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523108"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Mi az identitás biztonságos pontszám az Azure Active Directoryban?

Mennyire biztonságos az Ön Azure AD-bérlője? Ha nem tudja, hogyan kell válaszolni erre a kérdésre, ez a cikk ismerteti, hogyan identitás biztonságos pontszám segít az identitás biztonsági állapotának figyelése és javítása.

## <a name="what-is-an-identity-secure-score"></a>Mi az az identitásbiztonsági pontszám?

Az identitás biztonságos pontszám a szám 1 és 223, amely függvény, mint egy mutató, hogy mennyire igazodik a Microsoft gyakorlati javaslatok at biztonság. Minden egyes fejlesztési művelet identitás biztonságos pontszám az adott konfigurációhoz igazodik.  

![Biztonsági pontszám](./media/identity-secure-score/identity-secure-score-overview.png)

A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

A pontszámot és a kapcsolódó információkat az identitásbiztonsági pontszám irányítópultján találhatja meg. Ezen az irányítópulton a következők találhatók:

- Az Ön személyazonosságának biztonságos pontszáma
- Egy összehasonlító grafikon, amely bemutatja, hogy az identitás biztonságos pontszám a többi bérlővel összehasonlítva ugyanabban az iparágban és hasonló méretű
- Egy trenddiagram, amely bemutatja, hogyan változott az identitás biztonságos pontszáma az idő múlásával
- A lehetséges fejlesztések listája

A fejlesztési műveletek végrehajtásával a következőket érheti el:

- Javítani kell a biztonsági testtartás és a pontszám
- Használja ki a szervezet számára az identitásbefektetés részeként elérhető funkciókat

## <a name="how-do-i-get-my-secure-score"></a>Hogyan tekinthetem meg a saját biztonsági pontszámomat?

Az identitás biztonságos pontszám érhető el az Azure AD összes kiadásában. A szervezetek az **Azure** > **Active Directory-biztonsági** > **Security** > identitás biztonságos pontszámából érhetik el az identitásbiztonságos**pontszámukat.**

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure 48 óránként áttekinti az Ön biztonsági beállításait, és összehasonlítja azokat az ajánlott eljárásokkal. Az értékelés eredménye alapján a rendszer új pontszámot számít ki a címtárhoz. Lehetséges, hogy a biztonsági konfiguráció nincs teljes mértékben igazodva az ajánlott eljárásra vonatkozó útmutatáshoz, és a fejlesztési műveletek csak részben teljesülnek. Ezekben a forgatókönyvekben csak a vezérlőhöz rendelkezésre álló maximális pontszám egy részét kapja meg.

A rendszer az Ön Azure AD-konfigurációjától függően tesz javaslatokat. Ha külső termékeket használ az ajánlott eljárásokra vonatkozó javaslat engedélyezéséhez, ezt a konfigurációt a fejlesztési művelet beállításaiban is jelezheti. Azt is beállíthatja, hogy a javaslatok figyelmen kívül hagyhatók legyenek, ha azok nem vonatkoznak a környezetére. A figyelmen kívül hagyott javaslatok nincsenek hatással a pontszám kiszámítására.

![A művelet figyelmen kívül hagyása vagy megjelölése harmadik fél által lefedettként](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>Hogyan történik az irányítás pontozhatósága?

Az ellenőrzések kétféleképpen pontozhatók. Néhány pontozott bináris módon - kapsz 100%-a pontszám, ha a funkció vagy beállítás konfigurálva a mi ajánlásunk alapján. A többi pontszámok kiszámítása a teljes konfiguráció százalékában történik. Ha például a javítási javaslat szerint 30 pontot kap, ha az összes felhasználóját többmint a többmint a rektuáris rendszerrel védi, és a 100 felhasználóból csak 5 védett, akkor 2 pont körüli részleges pontszámot kap (5 védett / 100 teljes * 30 maximális pont = 2 pont részleges pontszám).

### <a name="what-does-not-scored-mean"></a>Mit jelent a [Not Scored]?

A [Not Scored] jelzéssel ellátott műveletek olyan műveletek, amelyeket elvégezhet a vállalatában, de a pontszám kiszámításakor a rendszer nem veszi őket figyelembe, mert (még!) nincsenek csatolva az eszközhöz. Így is javíthat a biztonsági beállításain, azonban a rendszer ilyen esetekben egyelőre nem veszi figyelembe ezeket a műveleteket.

### <a name="how-often-is-my-score-updated"></a>Milyen gyakran frissül a pontszám?

A pontszámot a rendszer naponta számítja újra (PST idő szerint kb. 01:00-kor). Ha változtatást eszközöl valamelyik mért műveleten, a pontszám a következő napon automatikusan frissül. Ahhoz, hogy egy változás megjelenjen a pontszámban is, akár 48 órának is el kell telnie.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Megváltozott a pontszámom. Hogyan tudhatom meg, mi ennek az oka?

Menjen át a [Microsoft 365 biztonsági központjához,](https://security.microsoft.com/)ahol megtalálja a teljes Microsoft-biztonsági pontszámot. A biztonságos pontszám összes módosítását könnyedén megtekintheti, ha áttekinti az Előzmények lapon található részletes módosításokat.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A biztonságos pontszám méri-e a megszegésem kockázatát?

Röviden: nem. A biztonságos pontszám nem fejezi ki az abszolút mértéke, hogy mennyire valószínű, hogy kap megsértik. Ez a számérték inkább azt mutatja, milyen mértékben tett lépéseket azért, hogy az illetéktelen behatolást megakadályozó funkciókat bevezessen. Semmilyen szolgáltatás nem garantálja, hogy nem sérül, és a biztonságos pontszámot semmilyen módon nem szabad garanciaként értelmezni.

### <a name="how-should-i-interpret-my-score"></a>Hogyan értelmezendő a pontszám?

Akkor kap pontokat, ha a javasolt módon konfigurálja a biztonsági funkciókat, vagy a biztonsághoz kötődő műveleteket végez (például jelentéseket olvas). Egyes műveletek részleges végrehajtása is pontot ér – ilyen például a többtényezős hitelesítés (MFA) engedélyezése a felhasználók számára. A biztonságos pontszám közvetlenül az Ön által használt Microsoft biztonsági szolgáltatásokat képviseli. Ne feledje, hogy a biztonságot egyensúlyba kell hozni a használhatósággal. Minden biztonsági vezérlő hatással van a felhasználókra is. A kis felhasználói hatású vezérlők elenyésző hatással vannak a felhasználók mindennapi műveleteire.

A pontszámelőzmények megtekintéséhez látogasson el a [Microsoft 365 biztonsági központjába,](https://security.microsoft.com/) és tekintse át a Microsoft teljes biztonságos pontszámát. Megtekintheti a változásokat a teljes biztonságos pontszám kattintson a Megtekintési előzmények. Egy adott dátumot kiválasztva megtekintheti, mely vezérlők voltak engedélyezve az adott napon, és hány pontot kapott ezekre.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hogyan kapcsolódik az identitásbiztonsági pontszám az Office 365 biztonsági pontszámhoz?

A [Microsoft biztonságos pontszáma](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) öt különböző vezérlő- és pontszámkategóriát tartalmaz:

- Identitás
- Adatok
- Eszközök
- Infrastruktúra
- Alkalmazások

Az identitás biztonságos pontszám a Microsoft biztonságos pontszám identitásrészét jelöli. Ez az átfedés azt jelenti, hogy az identitás biztonságos pontszámára vonatkozó javaslatok és a Microsoft identitáspontszáma megegyeznek.

## <a name="next-steps"></a>További lépések

[További információ a Microsoft biztonságos pontszámáról](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
