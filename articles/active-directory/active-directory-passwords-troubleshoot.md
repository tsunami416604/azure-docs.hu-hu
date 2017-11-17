---
title: "Hibáinak elhárítása: Az Azure AD SSPR |} Microsoft Docs"
description: "Hibaelhárítás az Azure AD az önkiszolgáló jelszóváltoztatás"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5c4942dbeccbd97c6bfc0f2c086e8d6091f6ed64
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-troubleshoot-self-service-password-reset"></a>Önkiszolgáló jelszóváltoztatás hibaelhárítása

Ha az önkiszolgáló jelszóváltoztatás kezelése problémát tapasztal, az alábbi segítenek gyorsan működik-e a folyamat.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-may-see"></a>Jelenhet meg a felhasználó önkiszolgáló jelszó-visszaállítási hibák elhárítása

| Hiba | Részletek | Technikai részletek |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Sajnos <br> Jelenleg nem lehet új jelszót, mert a rendszergazda letiltotta a jelszó alaphelyzetbe állítása a szervezet számára. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a szolgáltatás engedélyezéséhez. További tudnivalókért olvassa el a [segítségre van szüksége, az Azure AD-jelszó elfelejtettem](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: a rendszer azt észlelte, hogy jelszó-átállítási nincs engedélyezve a rendszergazda által. Forduljon a rendszergazdához, és kérje, hogy engedélyezze a jelszó alaphelyzetbe állítása a szervezet számára. |
| WritebackNotEnabled = 10 |Sajnos <br> Jelenleg nem lehet új jelszót, mert a rendszergazda nem engedélyezte a szervezet számára szükséges szolgáltatás. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a szervezet konfigurációjának ellenőrzése. További információk a szükséges, olvassa el [jelszóvisszaírás konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configuring-password-writeback). | SSPR_0010: a rendszer azt észlelte, hogy a Jelszóvisszaírás nincs engedélyezve. Forduljon a rendszergazdához, és kérje meg őket a Jelszóvisszaírás engedélyezése. |
| SsprNotEnabledInUserPolicy = 11 | Sajnos  <br> Jelenleg nem lehet új jelszót, mert a rendszergazda nem állított be a szervezet új jelszó. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg, konfigurálhatja a jelszó alaphelyzetbe állítása. További információt a jelszó alaphelyzetbe állítása konfigurációs olvassa [gyors üzembe helyezés: az Azure AD az önkiszolgáló jelszó-átállítási](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: A szervezet nincs definiálva a jelszó-visszaállítási házirend. Forduljon a rendszergazdához, és kérje meg a jelszó-visszaállítási házirend meghatározásához. |
| UserNotLicensed = 12 | Sajnos <br> Mert a szükséges licencek hiányoznak a szervezet jelenleg nem lehet új jelszót. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg őket, ellenőrizze a licenc-hozzárendelést. Több licencelésével kapcsolatban olvassa el a további [licencelés követelményei az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: A szervezet nem rendelkezik a szükséges licencek kell végrehajtani a jelszó alaphelyzetbe állítása. Forduljon a rendszergazdához, és kérje meg, hogy tekintse át a licenc-hozzárendeléseivel. |
| UserNotMemberOfScopedAccessGroup = 13 | Sajnos <br> Jelenleg nem lehet új jelszót, mert a rendszergazda nem állított be a jelszó alaphelyzetbe állítása használandó fiókot. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a fiók, jelszó-visszaállításhoz konfigurálása. További fiók konfigurálásával kapcsolatban a jelszó alaphelyzetbe állítása a cikk elolvasása [jelszó alaphelyzetbe állítása a felhasználók fokozatosan](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Nincsenek engedélyezve a jelszó alaphelyzetbe állítása csoport tagja. Kérje a rendszergazda és a kérelem a csoporthoz lehet hozzáadni. |
| UserNotProperlyConfigured = 14 | Sajnos <br> Jelenleg nem lehet új jelszót, mert hiányzik a szükséges információkat a fiókhoz tartozó. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazda, és kérje meg a jelszó alaphelyzetbe állításához. Elérhető lesz a fiók újra, miután áttekintheti, hogyan regisztrálja a szükséges információkat a következő cikkben található lépéseket [regisztrálni az önkiszolgáló jelszóváltoztatás](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register). | SSPR_0014: A további biztonsági adatokat a jelszó van szükség. A folytatáshoz, forduljon a rendszergazdához, és kérje meg a jelszó. Miután a fiókjához további biztonsági adatokat a https://aka.ms/ssprsetup regisztrálhatja rendelkezik hozzáféréssel. A rendszergazda adhat hozzá további biztonsági adatokat a fiók a lépések [beállítása és a jelszó-változtatási olvasási hitelesítési adatok](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Sajnos <br> A szervezet jelszó alaphelyzetbe állítása konfigurációjával kapcsolatos probléma miatt jelenleg nem lehet új jelszavát. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal. További információt a lehetséges probléma olvassa [jelszóvisszaírás hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Jelenleg nem lehet új jelszót a helyi konfigurációs hiba miatt. Forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal. |
| OnPremisesConnectivityError = 30 | Sajnos <br> A szervezetben csatlakozási problémák miatt most nem lehet új jelszavát. Nincs művelet azonnal életbe, de a probléma feloldása lehet, ha később ismét próbálkozik. Ha a probléma továbbra is fennáll, forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal. Olvassa el a kapcsolódási problémák kapcsolatos további [Jelszóvisszaírás hibaelhárítása kapcsolat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nem lehet új jelszavát a helyszíni környezet gyenge kapcsolat miatt. Forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal.|


## <a name="troubleshoot-password-reset-configuration-in-the-azure-portal"></a>Jelszó alaphelyzetbe állítása konfigurálása az Azure portálon hibaelhárítása

| **Hiba történt** | Megoldás |
| --- | --- |
| Nem jelenik meg a **jelszó-átállítási** szakasz alatt az Azure AD-t az Azure-portálon | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> Ez a licenc hozzárendelése a rendszergazdai fiókot az adott cikk segítségével megoldhatók [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Nem szerepel az adott konfigurációs beállítás | A felhasználói felület számos eleme csak szükséges rejtve maradnak. Próbálja meg szeretné tekinteni az összes beállítások engedélyezése. |
| Nem szerepel a **helyszíni integráció** lap | Ez a beállítás csak akkor van látható, ha már letöltötte az Azure AD Connect és konfigurálni a jelszóvisszaírást. A témakörrel kapcsolatos további információkért lásd: a cikk [Ismerkedés az Azure AD Connect a gyorsbeállításokkal](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Jelszó alaphelyzetbe állítása reporting hibaelhárítása

| **Hiba történt** | Megoldás |
| --- | --- |
| Nem szerepel az bármely jelszó felügyeleti típusú tevékenységek szerepelnek a **önkiszolgáló jelszókezelés** naplózási esemény kategóriája | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> Ez a licenc hozzárendelése a rendszergazdai fiókot az adott cikk segítségével megoldhatók [rendelhet, ellenőrizze és licencek kapcsolatos problémák megoldásához] |
| Felhasználói regisztrációk megjelenítése többször | Jelenleg amikor a felhasználó regisztrál, a Microsoft jelenleg jelentkezzen minden egyes adat, amely egy különálló esemény néven van regisztrálva. <br> Ha azt szeretné, ezek az adatok összesítését, töltse le a jelentést, és nyissa meg az adatok, mint egy olyan kimutatás az Excelből nagyobb rugalmasságot biztosítanak.

## <a name="troubleshoot-password-reset-registration-portal"></a>Jelszó-visszaállítási portál hibaelhárítása

| **Hiba történt** | Megoldás |
| --- | --- |
| Directory nincs engedélyezve a jelszó alaphelyzetbe állítása **a rendszergazda nem engedélyezte, hogy a szolgáltatás használatához** | Kapcsoló a **önkiszolgáló jelszóváltoztatás szolgáltatás engedélyezve** jelzőt **kijelölt** vagy **összes** kattintson **mentése** |
| Felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel **a rendszergazda nem engedélyezte, hogy a szolgáltatás használatához** | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> Ez a licenc hozzárendelése a rendszergazdai fiókot az adott cikk segítségével megoldhatók [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Hiba a kérelem feldolgozása | Ez sok problémákat okozhatja, de általában ez a hiba vagy egy szolgáltatás kimaradás vagy konfigurációs probléma okozza. Ha ezt a hibaüzenetet látja, és azt az üzleti van hatással, további segítségért forduljon a Microsoft támogatási szolgálatához. |

## <a name="troubleshoot-password-reset-portal"></a>Jelszó-változtatási portál hibaelhárítása

| **Hiba történt** | Megoldás |
| --- | --- |
| Címtárban nincs engedélyezve a jelszó alaphelyzetbe állítása. | Kapcsoló a **önkiszolgáló jelszóváltoztatás szolgáltatás engedélyezve** jelzőt **kijelölt** vagy **összes** kattintson **mentése** |
| Felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> Ez a licenc hozzárendelése a rendszergazdai fiókot az adott cikk segítségével megoldhatók [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Directory engedélyezve van a jelszó alaphelyzetbe állítása, de a felhasználó rendelkezik-e hiányzó vagy helytelen formátumú hitelesítési adatokat | Győződjön meg arról, hogy a felhasználó rendelkezik megfelelően formázott kapcsolattartási adatokat a fájl a könyvtárban, a folytatás előtt. A témakörrel kapcsolatos további információkért lásd: a cikk [az Azure AD önkiszolgáló jelszó-változtatási által használt adatok](active-directory-passwords-data.md). |
| Directory engedélyezve van a jelszó alaphelyzetbe állítása, de a felhasználó csak rendelkezzen kapcsolattartási adatok egy részét fájl, ha a házirend van beállítva, hogy megkövetelje a két ellenőrzési lépések | Győződjön meg arról, hogy a felhasználó rendelkezik legalább két, megfelelően konfigurált kapcsolattartási módszerek (Példa: mobiltelefon **és** irodai telefon) a folytatás előtt. |
| Directory engedélyezve van a jelszó alaphelyzetbe állítása, és a felhasználó helyesen van konfigurálva, de a felhasználó nem tud kapcsolódni | Ennek oka lehet egy ideiglenes hiba vagy helytelen kapcsolattartási adatokat, amelyek nem sikerült megállapítani megfelelően. <br> <br> Ha a felhasználó Várjon 10 másodpercet, próbálja meg újra, és megjelenik a "forduljon a rendszergazdához" hivatkozásra. Újra gombra kattintva próbálja meg a hívás újrapróbálkozik, kattintson a "forduljon a rendszergazdához", míg egy űrlap e-mailt küld a rendszergazdáknak új felhasználói fiók végrehajtandó jelszót kér. |
| Felhasználó nem kap, a jelszó alaphelyzetbe állítása SMS vagy a telefonhívás | Ennek oka lehet a helytelen formátumú telefonszám a címtárban. Ellenőrizze, hogy a telefonszám nem formátumú "+ kap xxxyyyzzzzXeeee". <br> <br> Jelszó alaphelyzetbe állítása nem támogatja a kiterjesztések, még akkor is, ha megad egy (azok a hívás van elküldése előtt program eltávolítja) a címtárban. Próbálja meg egy számot, kiterjesztés nélkül használatával vagy a bővítmény integrálása a PBX a telefonszámot. |
| Felhasználó nem kap jelszó alaphelyzetbe állítása e-mail | A leggyakoribb probléma oka, hogy az üzenet a levélszemét-szűrő elutasította. Ellenőrizze a levélszemét, az e-mailt levélszemetet vagy törölt elemek mappában. <br> Bizonyosodjon meg arról, hogy vannak-e ellenőrzése a helyes e-mail üzenet. |
| Rendelkezik egy jelszó-visszaállítási házirend állítható be, de amikor egy rendszergazdai fiókot használ jelszó-visszaállítás, a rendszer nem alkalmazza az adott házirendnek | A Microsoft felügyeli, és szabályozza a rendszergazdai jelszó-visszaállítási házirend a legmagasabb szintű biztonság biztosításához. |
| Nem lehetséges a jelszó alaphelyzetbe állítása egy napon belül túl sokszor próbált felhasználó | A visszaállíthassák a jelszavukat rövid időn belül túl sokszor próbált megtiltják a felhasználók számára történő megvalósítása egy automatikus szabályozási mechanizmus. Ez akkor fordul elő, ha: <br> 1. Felhasználó próbál érvényesíteni egy telefonszámot 5-ször egy óra. <br> 2. Felhasználó próbál használni a biztonsági kérdések kapu 5-ször egy óra. <br> 3. Felhasználó megpróbál egy órával az 5-ször ugyanazt a felhasználói fiókot a jelszó alaphelyzetbe állítása. <br> A javítás érdekében kérje meg a felhasználót, hogy Várjon 24 órát az utolsó kísérlet, és a felhasználó fog majd tudni jelszó visszaállítása után. |
| Felhasználónál hiba telefonszámukat érvényesítésekor | Ez a hiba akkor fordul elő, ha a megadott telefonszám nem felel meg a fájl telefonszám. Győződjön meg arról, hogy a felhasználó írja be a teljes telefonszámot, beleértve az területről, és az ország, a jelszó alaphelyzetbe állítása a módszert használni. |
| Hiba a kérelem feldolgozása | Ez sok problémákat okozhatja, de általában ez a hiba vagy egy szolgáltatás kimaradás vagy konfigurációs probléma okozza. Ha ezt a hibaüzenetet látja, és azt az üzleti van hatással, további segítségért forduljon a Microsoft támogatási szolgálatához. |

## <a name="troubleshoot-password-writeback"></a>A jelszóvisszaírás hibaelhárítása

| **Hiba történt** | Megoldás |
| --- | --- |
| Jelszó alaphelyzetbe állítása nem indul el a helyszíni alkalmazások eseménynaplójában keresse meg az Azure AD Connect gép 6800 hibával. <br> <br> Bevezetési összevont vagy a Jelszókivonat szinkronizálása után a felhasználók nem visszaállíthassák a jelszavukat. | A Jelszóvisszaírás engedélyezése esetén a szinkronizálási motor meghívja a visszaírási könyvtár a konfiguráláshoz (Bevezetés) segítségével a felhőszolgáltatás bevezetése. Minden előforduló hiba bevezetése során vagy a WCF-végpont indításakor, a Jelszóvisszaíró az eseménynaplóban, az Azure AD Connect gépének eseménynaplóban hibát eredményez. <br> <br> Során ADSync szolgáltatás újraindítását Ha visszaírási be lett állítva, a WCF-végpont már elindult. Azonban a végpont a indítása sikertelen lesz, ha rendszer eseménynaplóba 6800 és lehetővé teszik a szinkronizálási szolgáltatás indítása. Ez az esemény jelenlétét azt jelenti, hogy a Jelszóvisszaíró végpont nem elindult. Ez az esemény (6800) együtt eseménynapló-bejegyzések Eseménynapló részletei összetevő azt jelzi, hogy miért a végpont nem indítható el PasswordResetService elő. Tekintse át a Eseménynapló hibák, és próbálja meg újraindítani az Azure AD Connect, ha a Jelszóvisszaírás még nem működik. Ha a probléma továbbra is fennáll, próbálja letiltani és újra a Jelszóvisszaírás engedélyezése.
| Amikor egy felhasználó próbál állítani a jelszót, vagy egy fiók feloldása a jelszóvisszaírás engedélyezve van, a művelet sikertelen lesz. <br> <br> Emellett tekintse meg az Azure AD Connect Eseménynapló tartalmazó esemény: "szinkronizálási motor egy hiba hr adott vissza = 800700CE, üzenet = a fájlnév vagy bővítmény túl hosszú" a feloldási művelet után. | Keresse meg az Active Directory-fiókot az Azure AD Connect, és tartalmazhat, legfeljebb 127 karakter hosszú lehet a jelszó alaphelyzetbe állítása. Ezután nyissa meg a Synchronization Service, a Start menüből. Navigáljon az összekötők, és az Active Directory-összekötő található. Válassza ki azt, és kattintson a Tulajdonságok elemre. Keresse meg a lap hitelesítő adatokat, és adja meg az új jelszót. Kattintson az OK gombra az oldal bezárásához. |
| Az Azure AD Connect telepítési folyamat utolsó lépésnél megjelenik egy hiba, amely jelzi, hogy a Jelszóvisszaírás nem konfigurálható. <br> <br> Az Azure AD Connect alkalmazások eseménynaplójában keresse meg a "Hiba történt hitelesítési token" hibát 32009 szövegre tartalmaz. | Ez a hiba akkor fordul elő, a következő két esetben:<br> <br> a. A globális rendszergazdai fiók az Azure AD Connect telepítési folyamat elején meg helytelen jelszót adott meg.<br> b. A globális rendszergazdai fiók az Azure AD Connect telepítési folyamat elején megadott egy összevont felhasználó használatára történt kísérlet.<br> <br> Ez a hiba elhárításához ellenőrizze, hogy nem pedig összevont fiók használata a globális rendszergazda az Azure AD Connect telepítési folyamat elején meg, és a megadott jelszó helyességéről. |
| Az Azure AD Connect gép eseménynaplójában keresse meg a PasswordResetService által okozott 32002 hibát tartalmaz. <br> <br> Beolvassa a hiba: "hiba a Szolgáltatásbusz szeretne csatlakozni, a jogkivonat-szolgáltató nem tudott egy biztonsági jogkivonatot..." | A helyszíni környezetben nem tud csatlakozni a service bus-végpont a felhőben. Ez a hiba általában egy tűzfalszabály blokkolja egy adott port vagy a webhely címét egy kimenő kapcsolat okozza. Lásd: [kapcsolat Előfeltételek](./connect/active-directory-aadconnect-prerequisites.md) vonatkozó információ. Amennyiben ezek a szabályok frissítette, az Azure AD Connect újraindítás, és a Jelszóvisszaíró kell ismét dolgozni. |
| Működő egyes idő összevont vagy a Jelszókivonat szinkronizálása után a felhasználók nem visszaállíthassák a jelszavukat. | Néhány esetben a Jelszóvisszaírás szolgáltatás lehetséges, hogy nem az Azure AD Connect újraindításakor indítsa újra. Ezekben az esetekben először, ellenőrizze, hogy a Jelszóvisszaírás úgy tűnik, hogy engedélyezett-memóriakapacitásánál Ehhez az Azure AD Connect varázsló vagy a powershell (lásd: HowTos szakasz fenti). Ha a szolgáltatás valószínűleg engedélyezve van, próbálja meg engedélyezése vagy letiltása a szolgáltatás ismét vagy a felhasználói felület vagy a Powershellen keresztül. Ha ez sem működik, próbálja meg teljesen eltávolítása és újratelepítése az Azure AD Connect. |
| Összevont, vagy a jelszó szinkronizálva kivonatoló próbálják visszaállíthassák a jelszavukat tekintse meg a hiba, jelzi a szolgáltatás hiba történt a jelszó elküldése után. <br ><br> Továbbá a jelszó alaphelyzetbe állítása a műveletek során jelenhet meg kezelőügynök kapcsolatos hiba volt hozzáférése a a helyi eseménynaplóiban lesz naplózva. | Ha ezeket a hibákat az eseménynaplóban jelenik meg, győződjön meg arról, hogy az AD MA fióknak (a varázsló konfigurációs időpontjában) jelszóvisszaírás a szükséges engedélyekkel rendelkezik-e. <br> <br> **Ha ezt az engedélyt megadják, az engedélyek sdprop háttérfeladat a tartományvezérlőn keresztül le trickle legfeljebb 1 óra is igénybe vehet.** <br> <br> Jelszó-változtatási működjön az engedély kell a felhasználói objektum, amelynek a jelszó alaphelyzetbe állítása a biztonsági leírót kell jelölni. Ezt az engedélyt a user objektum jeleníti meg, amíg a jelszó alaphelyzetbe állítása továbbra is sikertelen – hozzáférés megtagadva. |
| Összevont, vagy a jelszó szinkronizálva kivonatoló próbálják visszaállíthassák a jelszavukat tekintse meg a hiba, jelzi a szolgáltatás hiba történt a jelszó elküldése után. <br> <br> Továbbá a jelszó alaphelyzetbe állítása a műveletek során előfordulhat, hogy hibaüzenet jelenik meg az Azure AD Connect szolgáltatásból "Objektum nem található" hibát jelző az eseménynaplóban található. | Ez a hiba általában azt jelzi, hogy a szinkronizálási motor nem található a user objektum az AAD connector lemezterület vagy a csatolt MV vagy az AD-összekötő terület objektum. <br> <br> Elhárításához győződjön meg arról, hogy a felhasználó valóban szinkronizálása a helyszíni az aad-be az aktuális példány az Azure AD Connect használatával, és vizsgálja meg az objektumok összekötőterek és MV állapotát. Győződjön meg arról, hogy az Active Directory Tanúsítványszolgáltatások objektum a MV-objektum a "Microsoft.InfromADUserAccountEnabled.xxx" szabály keresztül összekötő.|
| Összevont, vagy a jelszó szinkronizálva kivonatoló próbálják visszaállíthassák a jelszavukat tekintse meg a hiba, jelzi a szolgáltatás hiba történt a jelszó elküldése után. <br> <br> Továbbá a jelszó alaphelyzetbe állítása a műveletek során előfordulhat, hogy hibaüzenet jelenik meg az Azure AD Connect szolgáltatásból egy "Több találat" hibát jelző az eseménynaplóban található. | Ez azt jelzi, hogy a szinkronizálási motor észlelte, hogy a MV-objektum a "Microsoft.InfromADUserAccountEnabled.xxx" keresztül csatlakozik-e egynél több Active Directory Tanúsítványszolgáltatások objektumok. Ez azt jelenti, hogy a felhasználó rendelkezik-e egynél több erdő engedélyezett fióknak. **Ebben a forgatókönyvben a jelszóvisszaírás nem támogatott.** |
| Jelszó-műveletek egy konfigurációs hiba miatt sikertelen. Az alkalmazások eseménynaplójában tartalmazza <br> <br> Az Azure AD Connect hiba 6329 szöveggel: 0x8023061f (a művelet sikertelen, mert a jelszó-szinkronizálás nincs engedélyezve a kezelőügynök.) | Ez akkor fordul elő, ha az Azure AD Connect konfiguráció változott hozzáadása egy új AD-erdőben (vagy távolítsa el, és egy meglévő erdőben readd) után a Jelszóvisszaírás szolgáltatás már engedélyezve van. A felhasználók számára, például a jelszó műveletek az újonnan telepített erdők sikertelen lesz. A probléma megoldása érdekében tiltsa le, majd engedélyezze újra a Jelszóvisszaírás szolgáltatás az erdő konfigurációs módosítások befejezése után. |

## <a name="password-writeback-event-log-error-codes"></a>Jelszó visszaírási eseménynapló hibakódok

Az ajánlott eljárás a Jelszóvisszaírás kapcsolatos hibák elhárításának esetén, hogy alkalmazások eseménynaplójában keresse meg az Azure AD Connect gépen vizsgálata. Ehhez az eseménynaplóhoz érdeklő két forrásból származó események Jelszóvisszaírás tartalmazza. A PasswordResetService forrás műveletek és a Jelszóvisszaíró működésével kapcsolatos problémákat ismerteti. Az ADSync forrás műveletek és a jelszavak beállítása az AD-környezetben kapcsolatos problémákat ismerteti.

### <a name="source-of-event-is-adsync"></a>Esemény forrása ADSync

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 6329 | FEDEZETE: MMS(4924) 0x80230619 – "korlátozás megakadályozza, hogy a jelszó a következőre lesz módosítva a megadott jelenlegivel." | Ezt az eseményt akkor fordul elő, amikor a Jelszóvisszaírás szolgáltatás kísérel meg kell adnia egy jelszót a helyi könyvtárban, amely nem felel meg a jelszó élettartama, előzmények, összetettségi vagy a tartomány szűrési követelményeinek. <br> <br> Ha a jelszó minimális korú, és a jelszó az adott eltelt időszakot nemrég módosította, tudunk nem azt betölti a megadott abban a tartományban, amíg újra módosítani a jelszót. Tesztelési célokra minimális korú 0-ra kell állítani. <br> <br> Ha jelszó előzményekre vonatkozó követelményeknek engedélyezve van, akkor ki kell választania egy jelszót, amely nem használták az utolsó N idő, ahol N az előzmények jelszóbeállítást. Ha egy jelszót, amely az utolsó N többször is szerepel, majd megjelenik egy hiba ebben az esetben. Tesztelési célokra előzmények 0-ra kell állítani. <br> <br> Ha összetettségi követelményeknek, az összes rendszer lépnek érvénybe, ha a felhasználó megpróbálja módosítani vagy a jelszó alaphelyzetbe állítása. <br> <br> Ha jelszó szűrőket, és a felhasználó kiválaszt egy jelszót, amely nem felel meg a szűrési feltételeket, majd az alaphelyzetbe állítás, illetve nem módosította a művelet sikertelen lesz. |
| 6329 | MMS(3040): admaexport.cpp(2837): A kiszolgáló nem tartalmaz az LDAP-jelszó házirend vezérlő. | Ez a probléma akkor fordul elő, ha LDAP_SERVER_POLICY_HINTS_OID vezérlő (1.2.840.113556.1.4.2066) nincs engedélyezve a tartományvezérlőkön. A jelszó visszaírási szolgáltatás használatához engedélyeznie kell a vezérlő. Ehhez a tartományvezérlők a Windows Server 2008 (legújabb szervizcsomaggal) vagy újabb kell lennie. Ha a tartományvezérlők 2008 (R2 előtti), akkor is gyorsjavítást kell alkalmaznia [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | Szinkronizálási motor adott vissza egy hibakód: hr = 80230402, message = lekérése sikertelen volt, mert ne legyenek ismétlődő bejegyzések horgonyhoz az objektum | Ez az esemény akkor fordul elő, ha ugyanazt a felhasználói azonosítót engedélyezve van több tartományban. Például ha a fiók és az erőforrások erdők szinkronizálását végzi, és ugyanazt a felhasználói azonosítót jelenlévő és az egyes engedélyezve van, ez a hiba fordulhat elő. <br> <br> Ez a hiba akkor is előfordulhat, ha egy nem egyedi horgonyattribútum (például az alias vagy a UPN) használ, és két felhasználó osztozik egy adott azonos horgonyattribútum. <br> <br> A probléma elhárításához győződjön meg arról, hogy nincs duplikált felhasználókat a tartományban, hogy minden felhasználóhoz egyedi horgonyattribútum használ. |

### <a name="source-of-event-is-passwordresetservice"></a>Esemény forrása PasswordResetService

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 31001 | PasswordResetStart | Az esemény azt jelzi, hogy a helyszíni szolgáltatás észleli a jelszó-változtatási kérelmet a felhőből származó összevont vagy a jelszó kivonatoló szinkronizált felhasználó számára. Ez az esemény, az első esemény a minden jelszó alaphelyzetbe állítása a visszaírási művelet. |
| 31002 | PasswordResetSuccess | Az esemény azt jelzi, hogy egy felhasználó egy új jelszót kiválasztott a jelszó alaphelyzetbe állítása közben, a rendszer megállapította, hogy a jelszó megfelel-e vállalati jelszó követelményeinek, és ezt a jelszót sikeresen írt vissza a helyi AD-környezet. |
| 31003 | PasswordResetFail | Az esemény azt jelzi, hogy a felhasználó választott-e a jelszó és a jelszó sikeresen megérkezett a helyszíni környezetbe, de során a jelszó beállításához a helyi AD-környezetben, hiba történt. Ez akkor fordulhat elő, több okból: <br> <br> A jelszó nem a korszűrő, előzmények, bonyolultsági megfeleljen vagy szűrheti a tartomány követelményei. Próbálja meg a probléma megoldásához egy teljesen új jelszót. <br> <br> MA-szolgáltatásfiókja nem rendelkezik a megfelelő engedélyeket az új jelszót állíthat be a kérdéses felhasználói fiók. <br> <br> A felhasználói fiók pedig egy védett csoport, például a tartomány vagy a vállalati rendszergazdák, amely nem engedélyezi a jelszó set műveletek. |
| 31004 | OnboardingEventStart | Ez az esemény akkor fordul elő, ha engedélyezte a Jelszóvisszaírást az Azure AD Connect, és jelzi, hogy azt bevezetése a szervezet a Jelszóvisszaírás webszolgáltatáshoz. |
| 31005 | OnboardingEventSuccess | Az esemény azt jelzi, a bevezetési folyamat sikeres volt, és hogy a Jelszóvisszaírás funkció készen áll a használatra. |
| 31006 | ChangePasswordStart | Az esemény azt jelzi, hogy a helyszíni szolgáltatás észleli a felhőből származó összevont vagy a jelszó szinkronizálva kivonatoló felhasználói jelszó változáskérést. Ez az esemény minden jelszó-változtatási visszaírási művelet első esemény. |
| 31007 | ChangePasswordSuccess | Ez az esemény azt jelzi, hogy egy felhasználó egy új jelszót kiválasztott egy jelszó-változtatási művelet során kiderült, hogy a jelszó megfelel-e vállalati jelszó követelményeinek, és ezt a jelszót sikeresen írt vissza a helyi AD-környezet. |
| 31008 | ChangePasswordFail | Az esemény azt jelzi, hogy a felhasználó választott-e a jelszó és a jelszó sikeresen megérkezett a helyszíni környezetbe, de során a jelszó beállításához a helyi AD-környezetben, hiba történt. Ez akkor fordulhat elő, több okból: <br> <br> A jelszó nem a korszűrő, előzmények, bonyolultsági megfeleljen vagy szűrheti a tartomány követelményei. Próbálja meg a probléma megoldásához egy teljesen új jelszót. <br> <br> MA-szolgáltatásfiókja nem rendelkezik a megfelelő engedélyeket az új jelszót állíthat be a kérdéses felhasználói fiók. <br> <br> A felhasználói fiók pedig egy védett csoport, például a tartomány vagy a vállalati rendszergazdák, amely nem engedélyezi a jelszó set műveletek. |
| 31009 | ResetUserPasswordByAdminStart | A helyszíni szolgáltatás észlelte, hogy a jelszó-átállítási kérelem egy összevont, vagy a Jelszókivonat szinkronizálása a rendszergazda egy felhasználó nevében származó felhasználói. Ezt az eseményt a rendszer az első esemény minden rendszergazda által kezdeményezett jelszó alaphelyzetbe állítása visszaírási műveletben. |
| 31010 | ResetUserPasswordByAdminSuccess | A rendszergazda egy rendszergazda által kezdeményezett jelszó-visszaállítási művelet során kiválasztották őket egy új jelszót, a rendszer megállapította, hogy a jelszó megfelel-e vállalati jelszó követelményeinek, és ezt a jelszót sikeresen írt vissza a helyi AD-környezet. |
| 31011 | ResetUserPasswordByAdminFail | A rendszergazdai jelszó kiválasztott egy felhasználó nevében, és, hogy a jelszó sikeresen megérkezett a helyszíni környezetbe, de során a jelszó beállításához a helyi AD-környezetben, hiba történt. Ez akkor fordulhat elő, több okból: <br> <br> A jelszó nem a korszűrő, előzmények, bonyolultsági megfeleljen vagy szűrheti a tartomány követelményei. Próbálja meg a probléma megoldásához egy teljesen új jelszót. <br> <br> MA-szolgáltatásfiókja nem rendelkezik a megfelelő engedélyeket az új jelszót állíthat be a kérdéses felhasználói fiók. <br> <br> A felhasználói fiók pedig egy védett csoport, például a tartomány vagy a vállalati rendszergazdák, amely nem engedélyezi a jelszó set műveletek. |
| 31012 | OffboardingEventStart | Ez az esemény akkor fordul elő, ha letiltja a Jelszóvisszaírást az Azure AD Connect, és jelzi, hogy azt kivezetési a szervezet a Jelszóvisszaírás webszolgáltatáshoz. |
| 31013| OffboardingEventSuccess| Az esemény azt jelzi, a kivezetési folyamat sikeres volt, és hogy a Jelszóvisszaírás funkció sikeresen letiltva. |
| 31014| OffboardingEventFail| Az esemény azt jelzi, hogy a kivezetési folyamat sikertelen volt. A hiba lehetséges oka a felhő vagy a helyi rendszergazdafiók konfigurálása során meghatározott engedélyekkel kapcsolatos hibát, vagy mert próbál egy összevont felhő globális rendszergazda használja, ha letiltja a Jelszóvisszaírás. A javítás érdekében ellenőrizze a felügyeleti engedélyeket használ, és, hogy nem minden összevont fiók a Jelszóvisszaírás funkció konfigurálása közben.|
| 31015| WriteBackServiceStarted| Az esemény azt jelzi, hogy a Jelszóvisszaíró szolgáltatás sikeresen elindult, és készen áll a felhőből jelszó felügyeleti kérelmek fogadására.|
| 31016| WriteBackServiceStopped| Az esemény azt jelzi, hogy a Jelszóvisszaíró szolgáltatás leállt, és a jelszó felügyeleti kérések a felhőből nem lesz sikeres.|
| 31017| AuthTokenSuccess| Az esemény azt jelzi, hogy a Microsoft sikeresen beolvasta a globális rendszergazda Azure AD Connect telepítés a kivezetési vagy a bevezetési folyamat során megadott egy engedélyezési jogkivonatot.|
| 31018| KeyPairCreationSuccess| Az esemény azt jelzi, hogy sikeresen létrehozott a jelszó-titkosítási kulcs, amely a helyszíni környezetben küldését a felhőből jelszavak titkosítására szolgál.|
| 32000| UnknownError| Ez az esemény azt jelzi, ismeretlen hiba a jelszó felügyeleti művelet során. A kivétel szövege a esemény további részletekért tekintse meg. Ha problémába ütközik, próbálja meg letiltásával és újbóli a Jelszóvisszaírás engedélyezése. Ha ez nem segít, együtt a követési azonosító van megadva az Eseménynapló másolatát tartalmazza a támogatási szakembernek belső.|
| 32001| ServiceError| Ez az esemény azt jelzi hogy a felhő jelszó kapcsolódás során fellépő hiba szolgáltatás alaphelyzetbe állítása, és általában akkor fordul elő, ha a helyszíni szolgáltatás nem tudott kapcsolódni a jelszó alaphelyzetbe állítása webszolgáltatáshoz.|
| 32002| ServiceBusError| Az esemény azt jelzi, hogy a bérlő service bus példányhoz kapcsolódva hiba történt. Ez akkor fordulhat elő, mert a kimenő kapcsolatokat blokkol a helyszíni környezetben. Ellenőrizze a tűzfalat annak biztosítására, kapcsolatok engedélyezése a https://ssprsbprodncu-sb.accesscontrol.windows.net/ és TCP 443-as porton keresztül, és próbálkozzon újra. Ha továbbra is problémába ütközik, próbálja meg letiltásával és újbóli a Jelszóvisszaírás engedélyezése.|
| 32003| InPutValidationError| Az esemény azt jelzi, hogy a bemenet, a webszolgáltatási API átadott érvénytelen volt. Próbálja megismételni a műveletet.|
| 32004| DecryptionError| Ez az esemény azt jelzi, hogy a felhőből érkező jelszavának visszafejtésekor hiba. Ezt okozhatják a visszafejtési kulcs eltérést a felhőszolgáltatás és a helyszíni környezetben. A probléma megoldásához, tiltsa le, majd engedélyezze újra a jelszavak visszaírását a helyszíni környezetben.|
| 32005| ConfigurationError| Bevezetési, során a bérlő-specifikus adatait. a konfigurációs fájlban, a helyszíni környezetben elmentjük. Az esemény azt jelzi, hiba történt a fájl mentése vagy, hogy ha a szolgáltatás nem indult el lett hiba történt a fájl olvasásakor. A probléma megoldásához próbálja meg egy módosítsa úgy a konfigurációs fájl kényszerített újbóli engedélyezése és letiltása Jelszóvisszaírás.|
| 32007| OnBoardingConfigUpdateError| Bevezetési, során küldünk szolgáltatás adatokat a felhőből a helyszíni jelszó alaphelyzetbe állítása. Majd írása memórián belüli fájlba előtt küldi el a szinkronizálási szolgáltatás a ezeket az információkat tárolja biztonságos helyen a lemezen. Ez az esemény azt jelzi, hogy írása vagy a memória az adatok frissítése. A probléma megoldásához próbálja meg egy átdolgozás konfiguráció kényszerített újbóli engedélyezése és letiltása Jelszóvisszaírás.|
| 32008| ValidationError| Az esemény azt jelzi, hogy a jelszó alaphelyzetbe állítása webes szolgáltatás érvénytelen választ kapott azt. A probléma megoldásához, próbálja meg újra engedélyezése és letiltása a Jelszóvisszaírás.|
| 32009| AuthTokenError| Az esemény azt jelzi, hogy azt nem sikerült egy engedélyezési jogkivonat a globális rendszergazdai fiók az Azure AD Connect telepítés során megadott. Ezt a hibát okozhatja egy hibás felhasználónév vagy jelszó a globális rendszergazdai fiók megadva, vagy mert a globális rendszergazdai fiókkal a megadott össze van vonva. A probléma megoldásához, futtassa újra a konfigurációt a helyes felhasználónévvel és jelszóval, és győződjön meg arról, a rendszergazda nem felügyelt fiók (csak felhőalapú vagy a jelszó szinkronizálva).|
| 32010| CryptoError| Az esemény azt jelzi, hogy hiba történt a jelszó létrehozásakor titkosítási kulcs, vagy egy jelszót, amely a felhőszolgáltatás nem érkezik visszafejtése. Ez a hiba valószínűleg a környezet hibát jelez. Tekintse meg az eseménynaplóban a probléma megoldásához, és további részleteit. Is megpróbálhatja újból letiltásával és újbóli engedélyezése a Jelszóvisszaírás szolgáltatás, a probléma megoldásához.|
| 32011| OnBoardingServiceError| Az esemény azt jelzi, hogy a helyszíni szolgáltatás nem tudott megfelelően kommunikálni a jelszó alaphelyzetbe állítása a bevezetési folyamat kezdeményezéséhez webszolgáltatás. Ezt okozhatja egy tűzfalszabály vagy probléma egy hitelesítési jogkivonatot a bérlő beolvasása. A javítás érdekében ügyeljen arra, hogy nem blokkolják kimenő kapcsolatok TCP 443-as és a TCP 9350 – 9354-es vagy https://ssprsbprodncu-sb.accesscontrol.windows.net/ keresztül, és az aad-ben rendszergazdai fiók segítségével végzi a bevezetni nem össze van vonva.|
| 32013| OffBoardingError| Az esemény azt jelzi, hogy a helyszíni szolgáltatás nem tudott megfelelően kommunikálni a jelszó alaphelyzetbe állítása webszolgáltatás számára a kivezetési megkezdéséhez. Ezt okozhatja egy tűzfalszabály vagy probléma a bérlő egy engedélyezési jogkivonat beolvasása. A javítás érdekében győződjön meg arról, hogy Ön nem blokkolják a kimenő kapcsolatok https://ssprsbprodncu-sb.accesscontrol.windows.net/ vagy 443-as porton keresztül, és, hogy az AAD-rendszergazdai fiók segítségével offboard nincs összevonva.|
| 32014| ServiceBusWarning| Az esemény azt jelzi, hogy le kellett a bérlő service bus-példány való kapcsolódáshoz próbálja meg újra. Normál körülmények között ez kell nem lehet probléma, de ha ez az esemény számos esetben fontolja meg a service Bus hálózati kapcsolat ellenőrzése különösen, ha a nagy késleltetésű vagy alacsony sávszélességű kapcsolat.|
| 32015| ReportServiceHealthError| A Jelszóvisszaírás szolgáltatás állapotának ellenőrzéséhez küldünk szívverés adatokat a jelszó alaphelyzetbe állítása webszolgáltatás 5 percenként. Az esemény azt jelzi, hogy hiba történt a állapottal kapcsolatos adatok vissza a felhő webszolgáltatáshoz való küldésekor. Ez állapottal kapcsolatos adatok nem tartalmaz egy OII vagy személyazonosításra alkalmas adatok, és egy tisztán szívverés és alapszintű statisztikákat, azt több szolgáltatás állapot adatai a felhőben.|
| 33001| ADUnKnownError| Ez az esemény azt jelzi, hogy ismeretlen hibát AD vissza, az eseménynaplóban az Azure AD Connect kiszolgáló ezzel a hibával kapcsolatos további információ az ADSync forrásból származó események.|
| 33002| ADUserNotFoundError| Az esemény azt jelzi, hogy a jelszó megváltoztatására kívánó felhasználó nem található a helyszíni címtárba. Ez akkor fordulhat elő, amikor a felhasználó le lett törölve a helyszínen, de nem a felhőben, vagy ha a szinkronizálási problémát. Ellenőrizze a szinkronizálási naplókat, és az utolsó néhány szinkronizálás részleteiben talál további információt.|
| 33003| ADMutliMatchError| Jelszó-létrehozási, vagy a felhőből származnak változáskérés, a felhő horgony megadva a telepítés során az Azure AD Connect használatával határozza meg, hogy a kérelem vissza csatolása a felhasználó a helyszíni környezetben. Az esemény azt jelzi, hogy két felhasználók észleltünk a felhő azonos horgonyattribútum a helyszíni a könyvtárban. Ellenőrizze a szinkronizálási naplókat, és az utolsó néhány szinkronizálás részleteiben talál további információt.|
| 33004| ADPermissionsError| Az esemény azt jelzi, hogy a felügyeleti ügynök szolgáltatás fióknak nincs a megfelelő engedélyekkel a fiók új jelszót állíthat be a kérdéses. Győződjön meg arról, hogy a MA fiók az a felhasználó erdőjében alaphelyzetbe állítása és módosítása jelszó engedélyekkel rendelkezzen az erdőben lévő összes objektumra. A további információt a, olvassa el 4. lépés: a megfelelő Active Directory-engedélyek beállítása.|
| 33005| ADUserAccountDisabled| Az esemény azt jelzi, hogy azt megpróbálta alaphelyzetbe állítani, vagy módosítsa a jelszavát, amely a helyszíni le lett tiltva. Engedélyezze a fiókot, majd próbálja megismételni a műveletet.|
| 33006| ADUserAccountLockedOut| Esemény azt jelzi, hogy azt kísérlet történt egy olyan fiók, amely a helyszíni kizárt jelszó megváltoztatására. Zárolásokat akkor fordulhat elő, amikor egy felhasználó próbálta meg a módosítása vagy alaphelyzetbe állítja a jelszót művelet rövid időn belül túl sokszor. A fiók zárolását kívánja feloldani, majd próbálja megismételni a műveletet.|
| 33007| ADUserIncorrectPassword| Az esemény azt jelzi, hogy a felhasználó aktuális érvénytelen jelszót adott, ha a művelet végrehajtása a jelszó módosítása. Adja meg a megfelelő jelenlegi jelszavát, és próbálkozzon újra.|
| 33008| ADPasswordPolicyError| Ezt az eseményt akkor fordul elő, amikor a Jelszóvisszaírás szolgáltatás kísérel meg kell adnia egy jelszót a helyi könyvtárban, amely nem felel meg a jelszó élettartama, előzmények, összetettségi vagy a tartomány szűrési követelményeinek. <br> <br> Ha a jelszó minimális korú, és a jelszó az adott eltelt időszakot nemrég módosította, tudunk nem azt betölti a megadott abban a tartományban, amíg újra módosítani a jelszót. Tesztelési célokra minimális korú 0-ra kell állítani. <br> <br> Ha jelszó előzményekre vonatkozó követelményeknek engedélyezve van, akkor ki kell választania egy jelszót, amely nem használták az utolsó N idő, ahol N az előzmények jelszóbeállítást. Ha egy jelszót, amely az utolsó N többször is szerepel, majd megjelenik egy hiba ebben az esetben. Tesztelési célokra előzmények 0-ra kell állítani. <br> <br> Ha összetettségi követelményeknek, az összes rendszer lépnek érvénybe, ha a felhasználó megpróbálja módosítani vagy a jelszó alaphelyzetbe állítása. <br> <br> Ha jelszó szűrőket, és a felhasználó kiválaszt egy jelszót, amely nem felel meg a szűrési feltételeket, majd az alaphelyzetbe állítás, illetve nem módosította a művelet sikertelen lesz.|
| 33009| ADConfigurationError| Az esemény azt jelzi, hogy egy probléma írás jelszót biztonsági másolatot a helyszíni címtár és az Active Directory konfigurációs probléma miatt történt. Eseménynaplóban az Azure AD Connect gép alkalmazás milyen hiba történt a további tájékoztatást a ADSync szolgáltatás üzeneteit.|

## <a name="troubleshoot-password-writeback-connectivity"></a>A Jelszóvisszaírás kapcsolatok

Ha az Azure AD Connect a Jelszóvisszaírás összetevővel szolgáltatáskiesés tapasztalja, az alábbiakban néhány gyors lépéseket kell elvégeznie a probléma megoldásához:

* [Erősítse meg a hálózati kapcsolat](#confirm-network-connectivity)
* [Indítsa újra az Azure AD Connect szinkronizálási szolgáltatás](#restart-the-azure-ad-connect-sync-service)
* [Tiltsa le, majd engedélyezze újra a Jelszóvisszaírás szolgáltatás](#disable-and-re-enable-the-password-writeback-feature)
* [Az Azure AD Connect legújabb kiadásának telepítése](#install-the-latest-azure-ad-connect-release)
* [A jelszóvisszaíró hibaelhárítása](#troubleshoot-password-writeback)

Általában javasoljuk, hogy a fenti sorrendben helyreállítása a szolgáltatás a leggyorsabb módon hajtható végre ezeket a lépéseket.

### <a name="confirm-network-connectivity"></a>Erősítse meg a hálózati kapcsolat

A leggyakoribb pontot, hogy a tűzfal, és vagy proxyhoz és az üresjárati időkorlát nem megfelelően vannak konfigurálva. Tekintse át a kapcsolat előfeltételeket, a cikkben [Előfeltételek az Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) további információt.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Indítsa újra az Azure AD Connect szinkronizálási szolgáltatás

Az Azure AD Connect szinkronizálási szolgáltatás újraindítása segítséget kapcsolat hibái vagy a szolgáltatás más átmeneti probléma megoldásához.

1. Ha Ön rendszergazda, kattintson a **Start** futtató kiszolgálón **az Azure AD Connect**.
2. Típus **"Services.msc parancs"** a keresési mezőbe, majd nyomja le az **Enter**.
3. Keresse meg a **Microsoft Azure AD Sync** bejegyzés.
4. Kattintson a jobb gombbal a szolgáltatás bejegyzésre, kattintson a **indítsa újra a**, és várjon, amíg a művelet elvégzéséhez.

   ![Az Azure AD Sync szolgáltatás újraindítása][Service Restart]

Ezeket a lépéseket helyre a kapcsolatot a felhőalapú szolgáltatással, és oldja meg a megszakításokat tapasztalhat. A szinkronizálási szolgáltatás újraindítása nem oldja meg a problémát, azt javasoljuk, hogy megpróbálja letiltását és újraengedélyezését a Jelszóvisszaírás szolgáltatás a következő lépésben.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Tiltsa le, majd engedélyezze újra a Jelszóvisszaírás szolgáltatás

Letiltásával és újbóli Jelszóvisszaíró engedélyezésével kapcsolódási problémák megoldásához segítséget.

1. Ha Ön rendszergazda, nyissa meg a **az Azure AD Connect konfigurációs varázsló**.
2. Az a **az Azure AD Connect** párbeszédpanelen adja meg a **Azure AD globális rendszergazda hitelesítő adatait**
3. Az a **csatlakozás az Active Directory tartományi szolgáltatások** párbeszédpanelen adja meg a **AD tartományi szolgáltatások rendszergazdai hitelesítő adataival**.
4. Az a **felhasználók egyedi azonosítása** párbeszédpanel, kattintson a **következő** gombra.
5. Az a **választható szolgáltatások** párbeszédpanelen törölje a jelet a **jelszóvisszaírás** jelölőnégyzetet.
6. Kattintson a **következő** keresztül nélkül semmit nem változtat amíg elér a fennmaradó párbeszédpanelek a **beállíthatja az** lap.
7. Győződjön meg arról, hogy a konfigurálás lap megjeleníti a **jelszó visszaírási beállítás le van tiltva** és kattintson a zöld **konfigurálása** gombra a módosítások véglegesítéséhez.
8. A a **befejezett** párbeszédpanelen törölje a **szinkronizálás most** lehetőséget, majd kattintson a **Befejezés** a varázsló bezárásához.
9. Nyissa meg újra a **az Azure AD Connect konfigurációs varázsló**.
10. **Ismételje meg a 2-8**, azzal a különbséggel győződjön meg arról, hogy **jelölje be a jelszót a visszaírási beállítást** a a **választható szolgáltatások** képernyő engedélyezze újra a szolgáltatást.

Ezeket a lépéseket helyre a kapcsolatot a felhőalapú szolgáltatással, és oldja meg a megszakításokat tapasztalhat.

Letiltásával és újbóli Jelszóvisszaíró engedélyezésével nem oldja meg a problémát, azt javasoljuk, hogy akkor próbálja meg újratelepíteni a következő lépésben az Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Az Azure AD Connect legújabb kiadásának telepítése

Az Azure AD Connect újratelepítése oldhatja konfigurációs és a problémák a felhőszolgáltatások és a helyi AD-környezet között.

Azt javasoljuk, akkor hajtsa végre ezt a lépést csak a fent leírt két lépést kísérlet után.

> [!WARNING]
> Ha testreszabott kívüli mezőben szinkronizálási szabályok, **mentésére ezeket, mielőtt folytatná a frissítést, és manuálisan telepítse újra őket befejezése után**.

1. Töltse le az Azure AD Connect legújabb verzióját a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
2. Mivel már telepítette az Azure AD Connect, helyben történő frissítést végez az Azure AD Connect telepítése a legújabb verzióra frissíteni kell.
3. A letöltött csomagot, és kövesse a képernyőn megjelenő utasításokat az Azure AD Connect gép frissítése.

Ezeket a lépéseket helyre a kapcsolatot a felhőalapú szolgáltatással, és oldja meg a megszakításokat tapasztalhat.

Ha az Azure AD Connect-kiszolgáló legújabb verziójának telepítése nem oldja meg a problémát, azt javasoljuk, hogy meg letiltása és újbóli engedélyezése a Jelszóvisszaírás utolsó lépésként legújabb kiadásának telepítése után.

## <a name="verify-whether-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Győződjön meg arról, hogy az Azure AD Connect rendelkezik-e a szükséges engedélyekkel a jelszóvisszaíró
Az Azure AD Connect szükséges AD **jelszó alaphelyzetbe állítása** engedéllyel a jelszóvisszaírás elvégzéséhez. Annak megállapítása, hogy az Azure AD Connect a engedéllyel rendelkezik-e egy adott a helyszíni AD-felhasználó fiók számára, a Windows hatékony engedély szolgáltatással:

1. Jelentkezzen be az Azure AD Connect-kiszolgáló, majd indítsa el a **Synchronization Service Managert** (kezdő → szinkronizálási szolgáltatás).
2. Az a **összekötők** lapra, válassza ki a helyszíni **címtárösszekötőben** kattintson **tulajdonságok**.  
![Hatékony engedély - 2. lépés](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
3. Az előugró párbeszédpanelen válassza ki a **az Active Directory Erdőfelderítési kapcsolódás** lapra, és jegyezze fel a **felhasználónév** tulajdonság. Ez az Azure AD Connect címtár-szinkronizálás végrehajtásához használt AD DS-fiókjához. Az Azure AD Connect jelszóvisszaírás végrehajtásához az AD DS-fiókjához új jelszó engedéllyel kell rendelkeznie.  
![Hatékony engedély - 3. lépés](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
4. Jelentkezzen be a helyi tartományvezérlőre, és indítsa el a **Active Directory – felhasználók és számítógépek** alkalmazás.
5. Kattintson a **nézet** , és győződjön meg arról, hogy **speciális funkciók** engedélyezve van.  
![Hatékony engedély - 5. lépés](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
6. Keresse meg a szeretné ellenőrizni AD felhasználói fiók. Kattintson a jobb gombbal a fiókot, és válassza ki a **tulajdonságok**.  
![Hatékony engedély - 6. lépés](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 
7. Az előugró párbeszédpanelen keresse meg a **biztonsági** fülre, és kattintson **speciális**.  
![Hatékony engedély - 7. lépés](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
8. A speciális biztonsági beállítások előugró párbeszédpanelen keresse meg a **hatályos hozzáférés** fülre.
9. Kattintson a **válasszon ki egy felhasználót** válassza ki az Azure AD Connect által használt AD DS-fiókjához (lásd a 3. lépését). Kattintson a **hatályos hozzáférés megtekintése**.  
![Hatékony engedély - 9. lépés](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
10. Görgessen lefelé, és keresse meg **jelszó-átállítási**. Ha a bejegyzés be van jelölve, az azt jelenti, hogy az Active Directory tartományi szolgáltatások fióknak legyen jogosultsága a kiválasztott AD-felhasználói fiók jelszavának.  
![Hatékony engedély - 10. lépés](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-fórumok

Ha az Azure AD általános kérdése van, és az önkiszolgáló jelszó-visszaállítás, megkérheti a közösségi Ha segítségre van szüksége a a [az Azure AD-fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartozik a mérnökök, termék kezelők, MVP és ösztöndíjas informatikai szakemberek számára.

## <a name="contact-microsoft-support"></a>Forduljon a Microsoft támogatási szolgálatához.

Ha nem találja a választ egy problémához a támogatási csoportokkal mindig elérhetők további segítséget.

Megfelelő támogatása, kérjük, hogy megadja mértékű részletes lehető eset beleértve megnyitásakor:

* **A hiba leírása általános** -Mi az, hogy a hibát? Mi volt volt megfigyelhető viselkedés? Hogyan azt Reprodukálja a hibát? Adja meg a legnagyobb részletességgel lehető.
* **Lap** -milyen lap volt meg, ha a hiba első fellépése? Tüntesse fel az URL-címet, ha tudja, és egy képernyőfelvételt.
* **Támogatja a kód** -mi volt a támogatási kód jön létre, ha a felhasználó látta a hibát?
    * Ez megkereséséhez Reprodukálja a hibát, majd a kód támogatja a hivatkozást a képernyő alján kattintson, és a támogatási szakember küldése a GUID, amely.
    ![A támogatási kód a képernyő alján található][Support Code]
    * Ha oldalon szerepel a lap alján támogatás nélkül, nyomja le az F12, majd keresse meg a biztonsági AZONOSÍTÓT és a CID, és két eredményeket elküldik a támogatási szakértőhöz.
* **Dátum, idő és időzóna** -tüntesse fel a pontos dátum és idő **az időzóna** , amely a hiba történt.
* **Felhasználói azonosító** -ki volt a hiba látott felhasználó? (user@contoso.com)
    * Az egy összevont felhasználó?
    * A jelszó szinkronizálva kivonatoló felhasználó van szó?
    * A felhő csak felhasználó van szó?
* **Licencelési** – a felhasználó nem rendelkezik az Azure AD prémium vagy alapszintű Azure AD-licenccel?
* **Alkalmazások eseménynaplójában** – Ha jelszóvisszaírás használ, és a hiba, a helyszíni infrastruktúra van az alkalmazások eseménynaplójában keresse meg az Azure AD Connect-kiszolgáló a tömörített másolatát tartalmazza, amikor kapcsolatba lép a támogatási szolgálathoz.



[Service Restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Az Azure AD Sync szolgáltatás újraindítása"
[Support Code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Az ablak jobb alsó támogatási kód"

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
