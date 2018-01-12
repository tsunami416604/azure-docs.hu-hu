---
title: "Az önkiszolgáló jelszó-átállítási hibaelhárítási - Azure Active Directoryban"
description: "Hibaelhárítás az Azure AD az önkiszolgáló jelszóváltoztatás"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: c489cf13574c49161b2dde22500f4ab7478a928b
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="troubleshoot-self-service-password-reset"></a>Önkiszolgáló jelszóváltoztatás hibaelhárítása

Azure Active Directory (Azure AD) önkiszolgáló jelszó-változtatási (SSPR) probléma lépett fel? A következő információk segítségére lehetnek a folyamat ismét működik.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>A felhasználó láthat önkiszolgáló jelszó-visszaállítási hibák elhárítása

| Hiba | Részletek | Technikai részletek |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Sajnáljuk, nem lehet új jelszót jelenleg, mert a rendszergazda letiltotta a jelszó alaphelyzetbe állítása a szervezet számára. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a szolgáltatás engedélyezéséhez. További tudnivalókért lásd: [segítségre van szüksége, az Azure AD-jelszó elfelejtettem](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: a rendszer azt észlelte, hogy a jelszó-visszaállítás nincs engedélyezve a rendszergazda által. Forduljon a rendszergazdához, és kérje meg a szervezet új jelszó engedélyezéséhez. |
| WritebackNotEnabled = 10 |Sajnáljuk, nem lehet új jelszót jelenleg, mert a rendszergazda nem engedélyezte a szervezet számára szükséges szolgáltatás. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a szervezet konfigurációjának ellenőrzése. További információk a szükséges, [jelszóvisszaírás konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: a rendszer azt észlelte, hogy a jelszóvisszaíró nincs engedélyezve. Forduljon a rendszergazdához, és kérje meg őket a jelszóvisszaírás engedélyezése. |
| SsprNotEnabledInUserPolicy = 11 | Sajnáljuk, nem lehet új jelszót jelenleg, mert a rendszergazda nem állított be a szervezet új jelszó. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg, konfigurálhatja a jelszó alaphelyzetbe állítása. További információ a jelszó alaphelyzetbe állítása konfigurációs információkért lásd: [– első lépések: Azure AD az önkiszolgáló jelszó-átállítási](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: A szervezet nincs definiálva a jelszó-visszaállítási házirend. Forduljon a rendszergazdához, és kérje meg a jelszó-visszaállítási házirend meghatározásához. |
| UserNotLicensed = 12 | Sajnáljuk, nem lehet új jelszót jelenleg, mert a szükséges licencek hiányoznak a szervezete. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg őket, ellenőrizze a licenc-hozzárendelést. Licenceléssel kapcsolatos további tudnivalókért lásd: [licencelés követelményei az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: A szervezet nem rendelkezik a szükséges licencek kell végrehajtani a jelszó alaphelyzetbe állítása. Forduljon a rendszergazdához, és kérje meg, hogy tekintse át a licenc-hozzárendeléseivel. |
| UserNotMemberOfScopedAccessGroup = 13 | Sajnáljuk, nem lehet új jelszót jelenleg, mert a rendszergazda nem állított be a jelszó alaphelyzetbe állítása használandó fiókot. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a fiók, jelszó-visszaállításhoz konfigurálása. Jelszó-visszaállításhoz fiók konfigurálásával kapcsolatban további tudnivalókért lásd: [jelszó alaphelyzetbe állítása a felhasználók fokozatosan](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Nincsenek engedélyezve a jelszó alaphelyzetbe állítása csoport tagja. Kérje a rendszergazda és a kérelem a csoporthoz lehet hozzáadni. |
| UserNotProperlyConfigured = 14 | Sajnáljuk, nem lehet új jelszót jelenleg, mert hiányzik a szükséges információkat a fiókhoz tartozó. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazda, és kérje meg a jelszó alaphelyzetbe állításához. Elérhető lesz a fiók újra, miután a szükséges információkat regisztrálnia kell. Információk regisztrálásához kövesse a [regisztrálni az önkiszolgáló jelszóváltoztatás](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) cikk. | SSPR_0014: A további biztonsági adatokat a jelszó van szükség. A folytatáshoz, forduljon a rendszergazdához, és kérje meg a jelszó. Után hozzáférhet a fiókját, regisztrálhatja a https://aka.ms/ssprsetup további biztonsági adatokat. A rendszergazda adhat hozzá további biztonsági adatokat a fiók a lépések [és a jelszó-visszaállításhoz olvasási hitelesítési adatok](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Sajnáljuk, nem lehet új jelszavát jelenleg a szervezet jelszó alaphelyzetbe állítása konfigurációjával kapcsolatos probléma miatt. Nincs semmilyen további műveletet lehet feloldani ebben az esetben is igénybe vehet. Forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal. A lehetséges problémával kapcsolatos további információkért lásd: [jelszóvisszaírás hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Jelenleg nem lehet új jelszót a helyi konfigurációs hiba miatt. Forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal. |
| OnPremisesConnectivityError = 30 | Sajnáljuk, nem lehet új jelszavát jelenleg a szervezetben csatlakozási problémák miatt. Nincs művelet azonnal életbe, de a probléma feloldása lehet, ha nem, később próbálja meg újból. Ha a probléma továbbra is fennáll, forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal. Kapcsolódási problémák kapcsolatos további információkért lásd: [jelszó visszaírási kapcsolatok](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nem lehet új jelszavát a helyszíni környezet gyenge kapcsolat miatt. Forduljon a rendszergazdához, és kérje meg, hogy kivizsgálja a Microsofttal.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>A jelszó alaphelyzetbe állítása konfigurálása az Azure portálon hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| Nem szerepel a **jelszó-átállítási** szakasz alatt az Azure AD-t az Azure-portálon. | Ez akkor fordulhat elő, ha még nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> <br> Licenc hozzárendelése a szóban forgó rendszergazdai fiókot. A lépéseket követheti a [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) cikk.|
| Egy adott konfigurációs beállítás nem látható. | A felhasználói felület számos eleme csak szükség esetén rejtve maradnak. Próbálja meg szeretné tekinteni az összes beállítások engedélyezése. |
| Nem szerepel a **helyszíni integráció** fülre. | Ez a beállítás csak válik láthatóvá Ha letöltötte az Azure AD Connect és jelszóvisszaíró konfigurált. További információkért lásd: [első lépések az Azure AD Connecttel a gyorsbeállítások használatával](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Jelszó alaphelyzetbe állítása reporting hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| Bármely jelszó felügyeleti tevékenység típusa a nem látható a **önkiszolgáló jelszókezelés** naplózási esemény kategóriát. | Ez akkor fordulhat elő, ha még nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> <br> Ez a probléma megoldható a licenc hozzárendelése a szóban forgó rendszergazdai fiókot. Kövesse a [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) cikk. |
| Felhasználói regisztrációk megjelenítése több alkalommal. | Jelenleg amikor a felhasználó regisztrál, azt jelentkezzen minden egyes adat, amely egy különálló esemény néven van regisztrálva. <br> <br> Ha szeretné összesíteni az adatokat, és nagyobb rugalmasságot biztosítanak a hogyan tekintheti, töltse le a jelentést, és nyissa meg az adatok az Excel programban egy kimutatás.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>A jelszó-visszaállítási portál hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| A címtárban nincs engedélyezve a jelszó alaphelyzetbe állítása. **A rendszergazda nem engedélyezte, hogy a szolgáltatás használatához.** | Kapcsoló a **önkiszolgáló jelszó-visszaállításhoz engedélyezett** jelzőt **kijelölt** vagy **összes** , és válassza **mentése**. |
| A felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel. **A rendszergazda nem engedélyezte, hogy a szolgáltatás használatához.** | Ez akkor fordulhat elő, ha még nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> <br> Ez a probléma megoldható a licenc hozzárendelése a szóban forgó rendszergazdai fiókot. Kövesse a [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) cikk.|
| Nincs hiba történt a kérelem feldolgozása közben. | Ez sok problémákat okozhatja, de általában ez a hiba oka szolgáltatáskimaradás vagy konfigurációs probléma. Ha ezt a hibaüzenetet látja, és az üzleti hatással van, további segítségért forduljon a Microsoft támogatási szolgálatához. |

## <a name="troubleshoot-the-password-reset-portal"></a>A jelszó-változtatási portál hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| A címtárban nincs engedélyezve a jelszó alaphelyzetbe állítása. | Kapcsoló a **önkiszolgáló jelszó-visszaállításhoz engedélyezett** jelzőt **kijelölt** vagy **összes** , és válassza **mentése**. |
| A felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel. | Ez akkor fordulhat elő, ha még nem rendelkezik egy Azure AD prémium vagy alapszintű licenccel a rendszergazda a művelet végrehajtása. <br> <br> A probléma megoldásához is, ha a rendszergazdai fiókot az adott rendel egy licencet. Kövesse a [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) cikk. |
| A könyvtár engedélyezve van a jelszó alaphelyzetbe állítása, de a felhasználó rendelkezik-e hiányzó vagy hibás hitelesítési adatokat. | A folytatás előtt győződjön meg arról, ez a felhasználó rendelkezik helyes kialakításáról kapcsolattartási adatokat a fájl a könyvtárban. További információkért lásd: [az Azure AD az önkiszolgáló jelszó által használt adatok alaphelyzetbe állítása](active-directory-passwords-data.md). |
| A könyvtár engedélyezve van a jelszó alaphelyzetbe állítása, de a felhasználó rendelkezik kapcsolattartási adatok csak egy részét a fájlt, ha a házirend beállítása két ellenőrzési módszert igényel. | A folytatás előtt győződjön meg arról, hogy a felhasználó rendelkezik-e legalább két, megfelelően konfigurált kapcsolattartási módszerek. Például mind a mobiltelefonszám nehézségekkel *és* az irodai telefonszámát. |
| A könyvtár engedélyezve van a jelszó alaphelyzetbe állítása és a felhasználó helyesen van konfigurálva, de a felhasználó nem tud kapcsolódni. | Ez egy ideiglenes szolgáltatási hiba eredménye, vagy ha helytelen kapcsolattartási adatokat, amelyek megfelelően nem lehet észlelni. <br> <br> Ha a felhasználó 10 másodpercet vár, "újra" és "forduljon a rendszergazdához" hivatkozások jelennek meg. Ha a felhasználó a "újra" gombra kattint, a hívás újrapróbálkozik. Ha a felhasználó a "forduljon a rendszergazdához" gombra kattint, egy űrlap e-mailt küld a rendszergazdák a felhasználói fiók végrehajtandó új jelszót kér. |
| A felhasználó nem kap, a jelszó alaphelyzetbe állítása SMS vagy a telefonhívás. | Ez lehet egy nem megfelelően formázott telefonszámot a címtárban eredményét. Ellenőrizze, hogy a telefonszám nem formátumú "+ kap xxxyyyzzzzXeeee". <br> <br> Jelszó alaphelyzetbe állítása nem támogatja a kiterjesztések, még akkor is, ha megad egy, a címtárban. A bővítmények a program eltávolítja, mielőtt a hívás megtörténik. Használjon kiterjesztés nélkül, vagy a bővítmény integrálja a telefonszámot a titkos fiókirodai exchange (PBX). |
| A felhasználó nem kap a jelszó alaphelyzetbe állítása e-mailben. | A leggyakoribb probléma oka, hogy az üzenet a levélszemét-szűrő elutasította. Ellenőrizze a levélszemét, az e-mailt levélszemetet vagy törölt elemek mappában. <br> <br> Bizonyosodjon meg arról, hogy van-e ellenőrzése a helyes e-mail fiókot az üzenet. |
| Rendelkezik egy jelszó-visszaállítási házirend állítható be, de amikor egy rendszergazdai fiókot használ jelszó alaphelyzetbe állítása, az adott házirendnek nem alkalmazza. | A Microsoft felügyeli, és szabályozza a rendszergazdai jelszó-visszaállítási házirend a legmagasabb szintű biztonság biztosításához. |
| A felhasználó egy napon belül túl sokszor új jelszót próbálnak miatt nem sikerült. | A visszaállíthassák a jelszavukat rövid időn belül túl sokszor próbált megtiltják a felhasználók számára történő megvalósítása egy automatikus szabályozási mechanizmus. Sávszélesség-szabályozás akkor fordul elő, ha: <br><ul><li>A felhasználó megpróbálja ellenőrizni egy telefonszámot 5-ször egy óra.</li><li>A felhasználó megpróbálja használni a biztonsági kérdések kapu 5-ször egy óra.</li><li>A felhasználó megpróbál egy órával az 5-ször ugyanazt a felhasználói fiókot a jelszó alaphelyzetbe állításához.</li></ul>A probléma megoldásához kérje meg a felhasználó számára, Várjon 24 órát az utolsó kísérlet után. A felhasználó majd alaphelyzetbe állíthatja a jelszavát. |
| A felhasználó hibaüzenetet, a telefonszámát érvényesítése során. | Ez a hiba akkor fordul elő, ha a megadott telefonszám nem felel meg a fájl telefonszám. Győződjön meg arról, hogy a felhasználó írja be a teljes telefonszámot, beleértve a terület és ország, amikor azok megkísérlik a módszert használja a jelszó-visszaállításhoz. |
| Nincs hiba történt a kérelem feldolgozása közben. | Ez sok problémákat okozhatja, de általában ez a hiba oka szolgáltatáskimaradás vagy konfigurációs probléma. Ha ezt a hibaüzenetet látja, és az üzleti hatással van, további segítségért forduljon a Microsoft támogatási szolgálatához. |

## <a name="troubleshoot-password-writeback"></a>A jelszóvisszaírás hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| A jelszó alaphelyzetbe állítása nem indul el a helyszínen. Az alkalmazások eseménynaplójában keresse meg az Azure AD Connect gép 6800 hiba jelenik meg. <br> <br> Bevezetési, után összevont vagy a jelszó-kivonat-szinkronizált felhasználók nem visszaállíthassák a jelszavukat. | Ha engedélyezve van a jelszóvisszaírás, a szinkronizálási motor a visszaírási könyvtár a konfiguráláshoz (Bevezetés) hívások a bevezetési felhőszolgáltatáshoz való kommunikáció során. Minden előforduló hiba bevezetése során vagy a jelszó visszaírási eredmények elérése érdekében a Windows Communication Foundation (WCF) végpont indításakor hibákat az eseménynaplóban, az Azure AD Connect gépen. <br> <br> Során az Azure AD Sync (ADSync) szolgáltatás újraindítását Ha visszaírási be lett állítva, a WCF-végpont indítása. De a végpont a indítása sikertelen lesz, ha rendszer eseménynaplóba 6800 és lehetővé teszik a szinkronizálási szolgáltatás elindításához. Ez az esemény jelenlétét, az azt jelenti, hogy a jelszó visszaírási végpont nem indult el. Eseménynapló részletei az eseményhez 6800, eseménynapló-bejegyzések készítése az PasswordResetService összetevő, valamint jelzi, hogy miért nem lehet elindítani a végpontot. Tekintse át a Eseménynapló hibák, és próbálja meg újraindítani az Azure AD Connect, ha a jelszóvisszaírás még nem működik. Ha a probléma továbbra is fennáll, próbálja meg letiltani és újra engedélyezni a jelszóvisszaírás.
| Amikor egy felhasználó próbál állítani a jelszót, vagy egy fiók feloldása a jelszóvisszaírás engedélyezve van, a művelet sikertelen lesz. <br> <br> Ezenkívül megjelenik egy eseményt, amely tartalmazza az Azure AD Connect eseménynaplóban: "szinkronizálási motor egy hiba hr adott vissza = 800700CE, üzenet = a fájlnév vagy bővítmény túl hosszú" a feloldási művelet után. | Keresse meg az Active Directory-fiókot az Azure AD Connect, és a jelszó alaphelyzetbe állítása, így legfeljebb 127 karaktert tartalmaz. Nyissa meg a **szinkronizálási szolgáltatás** a a **Start** menü. Keresse meg a **összekötők** keresse meg a **Active Directory-összekötő**. Válassza ki, majd **tulajdonságok**. Keresse meg a **hitelesítő adatok** lapon, és adja meg az új jelszót. Válassza ki **OK** a lap bezárásához. |
| Az Azure AD Connect telepítési folyamat utolsó lépését: nem sikerült konfigurálni a jelszóvisszaírást jelző hibaüzenet jelenik meg. <br> <br> Az Azure AD Connect alkalmazásnaplót hibát tartalmaz 32009 szöveget a "Hiba történt a hitelesítési jogkivonat." | Ez a hiba akkor fordul elő, a következő két esetben: <br><ul><li>A globális rendszergazdai fiók az Azure AD Connect telepítési folyamat elején meg helytelen jelszót adott meg.</li><li>A globális rendszergazdai fiók az Azure AD Connect telepítési folyamat elején megadott egy összevont felhasználó használatára történt kísérlet.</li></ul> A probléma megoldásához, győződjön meg arról, hogy nem használja-e be a pedig összevont fiók a globális rendszergazda, a telepítési folyamat elején megadott. Bizonyosodjon meg arról, hogy a megadott jelszó helyességéről. |
| Az Azure AD Connect gép Eseménynapló PasswordResetService futtatásával történt hiba 32002 tartalmaz. <br> <br> Beolvassa a hiba: "hiba csatlakozás Szolgáltatásbusz. A jogkivonat-szolgáltató nem tudta biztonsági jogkivonatot biztosítani." | A helyszíni környezetben nem tud csatlakozni az Azure Service Bus-végpont a felhőben. Ez a hiba általában egy tűzfalszabály blokkolja egy adott port vagy a webhely címét egy kimenő kapcsolat okozza. Lásd: [kapcsolat Előfeltételek](./connect/active-directory-aadconnect-prerequisites.md) vonatkozó információ. Miután ezek a szabályok frissítette, az Azure AD Connect újraindítás, és jelszóvisszaíró kell ismét dolgozni. |
| Után működő egy kis ideig, összevont vagy a jelszó-kivonat-szinkronizált felhasználó nem állíthatja alaphelyzetbe a jelszavukat. | Néhány esetben a jelszó visszaírási szolgáltatás sikertelen lehet az Azure AD Connect újraindításakor újraindítására. Ebben az esetben először, ellenőrizze, hogy jelszóvisszaírás úgy tűnik, hogy engedélyezve van a helyszíni. Ellenőrizheti az Azure AD Connect varázsló vagy a PowerShell használatával (lásd az előző HowTos). Ha a szolgáltatás valószínűleg engedélyezve van, próbálja meg engedélyezése vagy letiltása a szolgáltatás ismét vagy a felhasználói felület vagy a Powershellen keresztül. Ha ez sem működik, próbálja meg eltávolításhoz, és telepítse újra az Azure AD Connect. |
| Összevont vagy a jelszó-kivonat-szinkronizált felhasználók visszaállíthassák a jelszavukat megkísérlő tekintse meg a hiba után megpróbálta elküldeni a jelszavát. A hiba azt jelzi, hogy probléma merült fel szolgáltatás. <br ><br> Probléma mellett jelszó alaphelyzetbe állítása a műveletek során előfordulhat, hogy hibaüzenet jelenik meg, hogy a felügyeleti ügynök meg lett tagadva a hozzáférés a helyszíni eseménynaplóiban lesz naplózva. | Ha ezeket a hibákat az eseménynaplóban jelenik meg, győződjön meg arról, hogy az Active Directory-kezelőügynök (ADMA) konfigurációs időpontjában a varázslóban megadott fióknak a szükséges engedélyekkel a jelszóvisszaírás. <br> <br> Vegye figyelembe, hogy ezt az engedélyt megadják, után is igénybe vehet az engedélyek keresztül le trickle akár egy óráig a `sdprop` háttérfeladat a tartományvezérlőn (DC). <br> <br> Jelszó-változtatási működjön az engedély kell a felhasználói objektum, amelynek a jelszó alaphelyzetbe állítása a biztonsági leírót kell jelölni. Ezt az engedélyt a user objektum jeleníti meg, amíg a jelszó alaphelyzetbe állítása továbbra is sikertelen, és a hozzáférés megtagadásáról. |
| Összevont vagy a jelszó-kivonat-szinkronizált próbálják visszaállíthassák a jelszavukat, hibaüzenet jelenik meg elküldenék a jelszavát. A hiba azt jelzi, hogy probléma merült fel szolgáltatás. <br> <br> Mellett ez a probléma jelszó alaphelyzetbe állítása a műveletek során előfordulhat, hogy hibaüzenet jelenik meg az Azure AD Connect szolgáltatásból "Objektum nem található" hibát jelző az eseménynaplóban található. | Ez a hiba általában azt jelzi, hogy a szinkronizálási motor nem található a user objektum az az Azure AD-kapcsolódási térbe vagy a csatolt metaverse (MV) vagy az Azure AD-összekötő terület objektum. <br> <br> Ez a probléma elhárításához győződjön meg arról, hogy a felhasználó valóban szinkronizálása a helyszíni és az Azure AD az aktuális példány az Azure AD Connect használatával, és vizsgálja meg az objektumok összekötőterek és MV állapotát. Győződjön meg arról, hogy az Active Directory tanúsítványszolgáltatások (AD CS) objektumot a MV-objektum a "Microsoft.InfromADUserAccountEnabled.xxx" szabály keresztül csatlakozik.|
| Összevont vagy a jelszó-kivonat-szinkronizált próbálják visszaállíthassák a jelszavukat hibaüzenet jelenik meg elküldenék a jelszavát. A hiba azt jelzi, hogy probléma merült fel szolgáltatás. <br> <br> Mellett ez a probléma jelszó alaphelyzetbe állítása a műveletek során előfordulhat, hogy hibaüzenet jelenik meg az Azure AD Connect szolgáltatásból, amely azt jelzi, hogy nincs-e egy "Több találat" hiba az eseménynaplóban található. | Ez azt jelzi, hogy a szinkronizálási motor észleli, hogy a MV-objektum egynél több Active Directory Tanúsítványszolgáltatások objektum "Microsoft.InfromADUserAccountEnabled.xxx" keresztül csatlakozik. Ez azt jelenti, hogy a felhasználó rendelkezik-e egynél több erdő engedélyezett fióknak. Vegye figyelembe, hogy ebben a forgatókönyvben a jelszóvisszaírás nem támogatott. |
| Jelszó-műveletek egy konfigurációs hiba miatt sikertelen. Az alkalmazások eseménynaplójában keresse meg az Azure AD Connect hibát 6329 "0x8023061f (a művelet sikertelen volt, mert a jelszó-szinkronizálás nincs engedélyezve a kezelőügynök)" szöveget tartalmaz. | Ez a hiba akkor fordul elő, ha az Azure AD Connect konfiguráció változtatják egy új Active Directory-erdő hozzáadása (vagy távolítsa el, majd újra vegye fel a meglévő erdőben) után a jelszót a visszaírási szolgáltatás már engedélyezve van. Ezek a felhasználók jelszó műveletek nemrégiben erdők sikertelen lesz. A probléma elhárításához tiltsa le, és majd engedélyezze újra a jelszót a visszaírási szolgáltatás az erdő konfigurációs módosítások befejezése után. |

## <a name="password-writeback-event-log-error-codes"></a>Jelszó visszaírási eseménynapló hibakódok

A jelszóvisszaírás problémák elhárításakor ajánlott az alkalmazások eseménynaplójában keresse meg az Azure AD Connect gépen vizsgálata. Ehhez az eseménynaplóhoz a jelszóvisszaírás érdeklő két forrásból származó eseményeket tartalmazza. A PasswordResetService forrás műveletek és a jelszóvisszaíró működésével kapcsolatos problémákat ismerteti. Az ADSync forrás műveletek és a jelszavak beállítása az Active Directory környezetben kapcsolatos problémákat ismerteti.

### <a name="if-the-source-of-the-event-is-adsync"></a>Ha a forrás az esemény ADSync

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 6329 | FEDEZETE: MMS(4924) 0x80230619: "korlátozás megakadályozza, hogy a jelszó a következőre lesz módosítva a megadott jelenlegivel." | Ez az esemény akkor következik be, amikor a jelszó visszaírási szolgáltatás kísérel meg kell adnia egy jelszót a helyi könyvtárban, amely nem felel meg a jelszó élettartama, előzmények, összetettségi vagy a tartomány szűrési követelményeinek. <br> <br> Ha a jelszó minimális korú és nemrég módosította a jelszó az idő ezt az ablakot, Ön nem képes újra módosítani a jelszót, amíg ez betölti a megadott abban a tartományban. Tesztelési célokra, a minimális korú 0-ra kell állítani. <br> <br> Ha a jelszó előzményekre vonatkozó követelményeknek engedélyezve van, akkor ki kell választania egy jelszót, amely még egyszer sem használták a legutóbbi *N* időpontokat, where *N* a jelszó-előzmények beállítása. Ha kiválaszt egy jelszót, amely már használta az utolsó *N* időkorlátja, és ebben az esetben tekintse meg a hiba. Tesztelési célokra, a jelszóelőzmények 0-ra kell állítani. <br> <br> Ha összetettségi követelményeknek, az összes rendszer lépnek érvénybe, ha a felhasználó megpróbálja módosítani vagy a jelszó alaphelyzetbe állítása. <br> <br> Ha jelszó szűrőket, és a felhasználó kiválaszt egy jelszót, amely nem felel meg a szűrési feltételeket, majd az alaphelyzetbe állítás, illetve nem módosította a művelet sikertelen lesz. |
| 6329 | MMS(3040): admaexport.cpp(2837): A kiszolgáló nem tartalmaz az LDAP-jelszó házirend vezérlő. | Ez a probléma akkor fordul elő, ha LDAP_SERVER_POLICY_HINTS_OID vezérlő (1.2.840.113556.1.4.2066) nincs engedélyezve a tartományvezérlők. A jelszó visszaírási szolgáltatás használatához engedélyeznie kell a vezérlő. Ehhez a tartományvezérlők a Windows Server 2008 (legújabb szervizcsomaggal) vagy újabb kell lennie. Ha a tartományvezérlők 2008 (R2 előtti), akkor is gyorsjavítást kell alkalmaznia [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | Szinkronizálási motor adott vissza egy hibakód: hr = 80230402, message = lekérése sikertelen volt, mert ne legyenek ismétlődő bejegyzések horgonyhoz az objektum. | Ez a hiba akkor fordul elő, ha ugyanazt a felhasználói Azonosítót engedélyezve van több tartományban. Például akkor, ha van fiók- és erőforrás-erdők szinkronizálása, és minden erdő ugyanazt a felhasználói Azonosítót jelen, és engedélyezve van. <br> <br> Ez a hiba akkor is előfordulhat, ha nem egyedi horgonyattribútum, például az alias vagy a egyszerű felhasználónév és a két felhasználó osztozik, hogy ugyanazon horgonyattribútum. <br> <br> Ez a probléma elhárításához győződjön meg arról, hogy minden ismétlődő felhasználók nem rendelkezik a tartományban, és minden felhasználóhoz egyedi horgonyattribútum használata. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Ha a forrás az esemény PasswordResetService

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 31001 | PasswordResetStart | Az esemény azt jelzi, hogy a helyszíni szolgáltatás észlelt egy jelszó-átállítási kérelem egy összevont vagy a jelszó-kivonat-szinkronizált felhasználó, amelyek a felhőből származnak. Ez az esemény az az első esemény minden jelszóváltoztatás visszaírási műveletben. |
| 31002 | PasswordResetSuccess | Az esemény azt jelzi, hogy egy felhasználó egy új jelszót kiválasztva a jelszó-visszaállítási művelet során. Azt határozza meg, hogy a jelszó megfelel-e a vállalati vonatkozó követelményeket. A jelszó sikeresen írt vissza a helyi Active Directory-környezetben. |
| 31003 | PasswordResetFail | Az esemény azt jelzi, hogy a felhasználó megadott jelszót, és a jelszó sikeresen megérkezett a a helyszíni környezetben. De során a jelszó beállításához a helyi Active Directory-környezetben, hiba történt. Ez a hiba akkor fordulhat elő, több okból: <br><ul><li>A jelszó nem a korszűrő, előzmények, bonyolultsági megfeleljen vagy szűrheti a tartomány követelményei. Ez a probléma megoldása érdekében hozzon létre egy új jelszót.</li><li>Az ADMA szolgáltatásfiók rendelkezik a megfelelő engedélyeket az új jelszót állíthat be a kérdéses felhasználói fiók.</li><li>A felhasználói fiók pedig egy védett csoport, például a tartományi vagy vállalati rendszergazdai csoport, amely nem engedélyezi a jelszó set műveletek.</li></ul>|
| 31004 | OnboardingEventStart | Ez az esemény akkor következik be, ha engedélyezi a jelszóvisszaírást az Azure AD Connect és kezdték bevezetése, azt a szervezet a jelszó visszaírási webszolgáltatáshoz. |
| 31005 | OnboardingEventSuccess | Az esemény azt jelzi, hogy sikeres volt-e a bevezetési folyamat, és hogy a jelszó visszaírási funkció készen áll a használatra. |
| 31006 | ChangePasswordStart | Az esemény azt jelzi, hogy a helyszíni szolgáltatás észleli a felhőből származnak összevont vagy a jelszó-kivonat-szinkronizált felhasználó jelszó változáskérés. Ez az esemény az az első esemény minden jelszómódosítás visszaírási műveletben. |
| 31007 | ChangePasswordSuccess | Ez az esemény azt jelzi, hogy egy felhasználó egy új jelszót kiválasztott egy jelszó-változtatási művelet során kiderült, hogy a jelszó megfelel-e a vállalati vonatkozó követelményeket, és, hogy a jelszó sikeresen írt vissza a helyi Active Directory-környezetben. |
| 31008 | ChangePasswordFail | Az esemény azt jelzi, hogy a felhasználó választott-e jelszót, és a jelszó sikeresen megérkezett a helyszíni környezetbe, de során a jelszó beállításához a helyi Active Directory-környezetben, hiba történt. Ez a hiba akkor fordulhat elő, több okból: <br><ul><li>A jelszó nem a korszűrő, előzmények, bonyolultsági megfeleljen vagy szűrheti a tartomány követelményei. Ez a probléma megoldása érdekében hozzon létre egy új jelszót.</li><li>Az ADMA szolgáltatásfiók rendelkezik a megfelelő engedélyeket az új jelszót állíthat be a kérdéses felhasználói fiók.</li><li>A felhasználói fiók pedig egy védett csoport, például a tartomány vagy a vállalati rendszergazdák, amely nem engedélyezi a jelszó set műveletek.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | A helyszíni szolgáltatás észlelte, hogy a jelszó-átállítási kérelem egy felhasználó nevében a rendszergazda származó összevont vagy a jelszó-kivonat-szinkronizált felhasználó számára. Ez az esemény az az első esemény minden, a rendszergazda által kezdeményezett jelszóváltoztatás visszaírási műveletben. |
| 31010 | ResetUserPasswordByAdminSuccess | A rendszergazda egy új jelszót ki egy rendszergazda által kezdeményezett jelszó-visszaállítási művelet során. Azt határozza meg, hogy a jelszó megfelel-e a vállalati vonatkozó követelményeket. A jelszó sikeresen írt vissza a helyi Active Directory-környezetben. |
| 31011 | ResetUserPasswordByAdminFail | A rendszergazdai jelszó kiválasztott egy felhasználó nevében. A jelszó sikeresen megérkezett a a helyszíni környezetben. De során a jelszó beállításához a helyi Active Directory-környezetben, hiba történt. Ez a hiba akkor fordulhat elő, több okból: <br><ul><li>A jelszó nem a korszűrő, előzmények, bonyolultsági megfeleljen vagy szűrheti a tartomány követelményei. Próbálja meg a probléma megoldásához új jelszót.</li><li>Az ADMA szolgáltatásfiók rendelkezik a megfelelő engedélyeket az új jelszót állíthat be a kérdéses felhasználói fiók.</li><li>A felhasználói fiók pedig egy védett csoport, például a tartomány vagy a vállalati rendszergazdák, amely nem engedélyezi a jelszó set műveletek.</li></ul>  |
| 31012 | OffboardingEventStart | Ez az esemény akkor fordul elő, ha letiltja a jelszóvisszaírást az Azure AD Connect, és jelzi, hogy azt kivezetési a szervezet a jelszó visszaírási webszolgáltatáshoz. |
| 31013| OffboardingEventSuccess| Az esemény azt jelzi, hogy a kivezetési folyamat sikeres volt-e, és hogy jelszó visszaírási funkció sikeresen letiltva. |
| 31014| OffboardingEventFail| Az esemény azt jelzi, hogy a kivezetési folyamat sikertelen volt. Ennek oka engedélyekkel kapcsolatos hibát konfigurálása során meghatározott a felhő vagy a helyi rendszergazda fiók lehet. A hiba akkor is előfordulhat, ha egy összevont felhő globális rendszergazda használandó, ha letiltja a jelszóvisszaírás végrehajtani kívánt. A probléma megoldásához ellenőrizze a felügyeleti engedélyeit, és győződjön meg arról, hogy nem használja-e be a pedig összevont fiók a jelszó visszaírási funkció konfigurálása közben.|
| 31015| WriteBackServiceStarted| Az esemény azt jelzi, hogy a jelszó visszaírási szolgáltatás sikeresen elindult. A felhő jelszó felügyeleti kéréseket fogad készen áll.|
| 31016| WriteBackServiceStopped| Az esemény azt jelzi, hogy a jelszó visszaírási szolgáltatás leállt. A jelszó felügyeleti kérések a felhőből nem lesz sikeres.|
| 31017| AuthTokenSuccess| Az esemény azt jelzi, hogy a Microsoft sikeresen beolvasta a globális rendszergazda Azure AD Connect telepítés a kivezetési vagy a bevezetési folyamat során megadott egy engedélyezési jogkivonatot.|
| 31018| KeyPairCreationSuccess| Az esemény azt jelzi, hogy sikeresen létrehozott a jelszó-titkosítási kulcs. Ezt a kulcsot a helyszíni környezetben küldését a felhőből jelszavak titkosítására szolgál.|
| 32000| UnknownError| Az esemény azt jelzi, hogy ismeretlen hiba történt a jelszó felügyeleti művelet során. A kivétel szövege a esemény további részletekért tekintse meg. Ha problémákat tapasztal, próbálja meg letiltásával és újbóli engedélyezésével jelszóvisszaírás. Ha ez nem segít, tartalmazza a együtt a követési eseménynaplóban másolatát megadott belső azonosítója a támogatási szakembernek.|
| 32001| ServiceError| Ez az esemény azt jelzi hogy a felhő jelszó kapcsolódás során fellépő hiba szolgáltatás alaphelyzetbe állítása. Ez a hiba általában akkor fordul elő, ha a helyszíni szolgáltatás nem tudott kapcsolódni a jelszó-visszaállítási webszolgáltatás.|
| 32002| ServiceBusError| Az esemény azt jelzi, hogy a bérlő Service Bus példányhoz kapcsolódva hiba történt. Ez akkor fordulhat elő, ha éppen blokkolja a kimenő kapcsolatok a helyszíni környezetben. Ellenőrizze a tűzfalat, hogy ellenőrizze, hogy kapcsolatok engedélyezése a https://ssprsbprodncu-sb.accesscontrol.windows.net/ és TCP 443-as porton keresztül, és próbálja meg újból. Ha továbbra is problémákat tapasztal, próbálja meg letiltásával és újbóli engedélyezésével jelszóvisszaírás.|
| 32003| InPutValidationError| Az esemény azt jelzi, hogy a bemenet, a webszolgáltatási API átadott érvénytelen volt. Próbálja megismételni a műveletet.|
| 32004| DecryptionError| Ez az esemény azt jelzi, hogy a felhőből érkező jelszavának visszafejtésekor hiba. Ennek oka a visszafejtési kulcs a felhőszolgáltatás és a helyszíni környezetben eltérő lehet. A probléma megoldásához tiltsa le, és majd újból engedélyezi a jelszavak visszaírását a helyszíni környezetben.|
| 32005| ConfigurationError| Bevezetési, során a bérlő-specifikus adatait. a konfigurációs fájlban, a helyszíni környezetben elmentjük. Az esemény azt jelzi, hogy hiba történt a fájl mentése vagy, hogy a szolgáltatás indítása, ha hiba történt a fájl olvasásakor. A probléma megoldásához próbálja letiltásával és újbóli engedélyezésével jelszóvisszaírás kényszerítése a módosítsa úgy a konfigurációs fájl.|
| 32007| OnBoardingConfigUpdateError| Bevezetési, során azt adatokat küldeni a felhőből a helyszíni jelszóváltoztatás szolgáltatás. Majd írása memórián belüli fájlba lemezen biztonságosan tárolni a szinkronizálási szolgáltatás való továbbítás előtt. Az esemény azt jelzi, hogy nincs-e probléma írása vagy a memóriában adatok frissítése. A probléma megoldásához próbálja letiltásával és újbóli engedélyezésével jelszóvisszaírás kényszerítése a módosítsa úgy a konfigurációs fájl.|
| 32008| ValidationError| Az esemény azt jelzi, hogy a jelszóátállítás webszolgáltatás érvénytelen választ kapott azt. A probléma megoldásához próbálja letiltásával és újbóli engedélyezésével jelszóvisszaírás.|
| 32009| AuthTokenError| Az esemény azt jelzi, hogy nem sikerült beolvasni egy hitelesítési jogkivonat a globális rendszergazdai fiók az Azure AD Connect telepítés során megadott. Ezt a hibát okozhatja egy hibás felhasználónév vagy a globális rendszergazdai fiókhoz megadott jelszót. Ez a hiba akkor is előfordulhat, ha a megadott globális rendszergazdai fiókja össze van vonva. A probléma megoldásához futtassa újra a konfigurációt a helyes felhasználónévvel és jelszóval, és győződjön meg arról, hogy a rendszergazda felügyelt fiók (csak felhőalapú vagy a jelszó szinkronizálva).|
| 32010| CryptoError| Az esemény azt jelzi, hogy hiba történt a jelszó-titkosítási kulcs létrehozása vagy egy jelszót, amely a felhőszolgáltatás nem érkezik visszafejtése. Ezt a hibát valószínűleg az környezettel kapcsolatos problémát jelez. Tekintse meg az eseménynaplóban talál további információt a probléma megoldásához részleteit. Megpróbálhatja letiltásával és újbóli engedélyezésével a jelszó visszaírási szolgáltatást is.|
| 32011| OnBoardingServiceError| Az esemény azt jelzi, hogy a helyszíni szolgáltatás megfelelően nem sikerült kommunikálni a jelszó-visszaállítási webszolgáltatással a bevezetési folyamat kezdeményezéséhez. Ez akkor fordulhat elő egy olyan tűzfalszabályt miatt, vagy ha probléma merül fel egy hitelesítési jogkivonat beolvasása a bérlő számára. A probléma megoldásához, győződjön meg arról, hogy Ön most nem blokkolja-e kimenő kapcsolatok TCP 443-as és a TCP 9350 – 9354-es vagy https://ssprsbprodncu-sb.accesscontrol.windows.net/ keresztül. Bizonyosodjon meg arról, hogy a bevezetésében nem összevont használata az Azure AD rendszergazdai fiókot.|
| 32013| OffBoardingError| Az esemény azt jelzi, hogy a helyszíni szolgáltatással megfelelően nem sikerült kommunikálni a jelszó-visszaállítási webszolgáltatással kivezetési folyamat elindítása. Ez akkor fordulhat elő egy olyan tűzfalszabályt miatt, vagy ha engedélyezési jogkivonat beolvasása a bérlő probléma van. Ez a probléma elhárításához győződjön meg arról, hogy nem blokkolja-e kimenő kapcsolatok 443-as porton keresztül vagy a https://ssprsbprodncu-sb.accesscontrol.windows.net/, és, hogy az Azure Active Directory rendszergazdai fiók segítségével offboard nem összevont.|
| 32014| ServiceBusWarning| Az esemény azt jelzi, hogy le kellett próbálja meg újra csatlakozni a bérlő Service Bus-példányhoz. Normál körülmények között ez kell nem lehet probléma, de ha ez az esemény számos esetben fontolja meg a Service Bus hálózati kapcsolat ellenőrzése különösen akkor, ha nagy késleltetésű vagy alacsony sávszélességű kapcsolat.|
| 32015| ReportServiceHealthError| Ahhoz, hogy a jelszó visszaírási szolgáltatás állapotának figyelésére, azt szívverés adatokat küldeni a jelszó-visszaállítási webszolgáltatás 5 perc. Az esemény azt jelzi, hogy hiba történt a állapottal kapcsolatos adatok vissza a felhő webszolgáltatáshoz való küldésekor. Ez állapottal kapcsolatos adatok nem tartalmazza az objektum azonosításra alkalmas adatok (OII) és a személyes azonosításra alkalmas adatokat (PII) adatok, és egy tisztán szívverés és alapszintű statisztikákat, azt több szolgáltatás állapot adatai a felhőben.|
| 33001| ADUnKnownError| Az esemény azt jelzi, hogy az Active Directory által visszaadott ismeretlen hiba történt. Az eseménynaplóban az Azure AD Connect kiszolgáló további információt az ADSync forrásból származó események.|
| 33002| ADUserNotFoundError| Az esemény azt jelzi, hogy a jelszó megváltoztatására kívánó felhasználó nem található a helyszíni címtárba. Ez a hiba akkor fordulhat elő, amikor a felhasználó le lett törölve a helyszínen, de nem a felhőben. Ez a hiba akkor is előfordulhat, ha a probléma és a szinkronizálás. Ellenőrizze a szinkronizálási naplókat, és az utolsó néhány szinkronizálás futtatása részleteiben talál további információt.|
| 33003| ADMutliMatchError| Jelszó-létrehozási, vagy a felhőből származnak változáskérés, a felhő horgony megadva a telepítés során az Azure AD Connect használatával határozza meg, hogy a kérelem vissza csatolása a felhasználó a helyszíni környezetben. Az esemény azt jelzi, hogy két felhasználók észleltünk a felhő azonos horgonyattribútum a helyszíni a könyvtárban. Ellenőrizze a szinkronizálási naplókat, és az utolsó néhány szinkronizálás futtatása részleteiben talál további információt.|
| 33004| ADPermissionsError| Az esemény azt jelzi, az Active Directory-kezelőügynök (ADMA) szolgáltatás fiókja nem rendelkezik a megfelelő engedélyeket kell adnia egy új jelszót a szóban forgó fiók. Győződjön meg arról, hogy a felhasználó erdőjében ADMA fiókjának visszaállította az erdőben lévő összes objektumra jelszó engedélyeinek módosítása. Az engedélyek beállításával kapcsolatos további információkért lásd a 4. lépés: a megfelelő Active Directory-engedélyek beállítása.|
| 33005| ADUserAccountDisabled| Az esemény azt jelzi, hogy azt megpróbálta alaphelyzetbe állítani, vagy módosítsa a jelszavát, de a letiltása a helyszínen. Engedélyezze a fiókot, majd próbálja megismételni a műveletet.|
| 33006| ADUserAccountLockedOut| Az esemény azt jelzi, hogy azt kísérlet történt egy olyan fiók, amely a helyszíni kizárt jelszó megváltoztatására. Zárolásokat akkor fordulhat elő, amikor egy felhasználó próbálta meg a módosítása vagy alaphelyzetbe állítja a jelszót művelet rövid időn belül túl sokszor. A fiók zárolását kívánja feloldani, majd próbálja megismételni a műveletet.|
| 33007| ADUserIncorrectPassword| Az esemény azt jelzi, hogy a felhasználó aktuális érvénytelen jelszót adott, ha a művelet végrehajtása a jelszó módosítása. Adja meg a megfelelő jelenlegi jelszavát, és próbálkozzon újra.|
| 33008| ADPasswordPolicyError| Ez az esemény akkor következik be, amikor a jelszó visszaírási szolgáltatás kísérel meg kell adnia egy jelszót a helyi könyvtárban, amely nem felel meg a jelszó élettartama, előzmények, összetettségi vagy a tartomány szűrési követelményeinek. <br> <br> Ha a jelszó minimális korú és nemrég módosította a jelszó az idő ezt az ablakot, Ön nem képes újra módosítani a jelszót, amíg ez betölti a megadott abban a tartományban. Tesztelési célokra, a minimális korú 0-ra kell állítani. <br> <br> Ha a jelszó előzményekre vonatkozó követelményeknek engedélyezve van, akkor ki kell választania egy jelszót, amely még egyszer sem használták a legutóbbi *N* időpontokat, where *N* a jelszó-előzmények beállítása. Ha kiválaszt egy jelszót, amely már használta az utolsó *N* időkorlátja, és ebben az esetben tekintse meg a hiba. Tesztelési célokra, a jelszóelőzmények 0-ra kell állítani. <br> <br> Ha összetettségi követelményeknek, az összes rendszer lépnek érvénybe, ha a felhasználó megpróbálja módosítani vagy a jelszó alaphelyzetbe állítása. <br> <br> Ha jelszó szűrőket, és a felhasználó kiválaszt egy jelszót, amely nem felel meg a szűrési feltételeket, majd az alaphelyzetbe állítás, illetve nem módosította a művelet sikertelen lesz.|
| 33009| ADConfigurationError| Ez az esemény azt jelzi hogy a probléma írást, az Active Directory konfigurációs probléma miatt a helyszíni címtár biztonsági jelszót. Ellenőrizze az Azure AD Connect gép alkalmazások eseménynaplójában további információt a hiba történt az ADSync szolgáltatás üzeneteit.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Jelszó visszaírási kapcsolatok

Ha az Azure AD Connect a jelszó visszaírási összetevővel szolgáltatáskiesés fennáll, az alábbiakban néhány gyors lépéseket kell elvégeznie a probléma megoldása:

* [Erősítse meg a hálózati kapcsolat](#confirm-network-connectivity)
* [Az Azure AD Connect szinkronizálási szolgáltatás újraindítása](#restart-the-azure-ad-connect-sync-service)
* [Tiltsa le, majd engedélyezze újra a jelszót a visszaírási szolgáltatás](#disable-and-re-enable-the-password-writeback-feature)
* [Az Azure AD Connect legújabb kiadásának telepítése](#install-the-latest-azure-ad-connect-release)
* [A jelszóvisszaírás hibaelhárítása](#troubleshoot-password-writeback)

Általában a szolgáltatás helyreállítása a leggyorsabb módon, azt javasoljuk, hogy a korábban tárgyalt sorrendben hajtsa végre ezeket a lépéseket.

### <a name="confirm-network-connectivity"></a>Erősítse meg a hálózati kapcsolat

A leggyakoribb pontot, hogy a tűzfal, és vagy proxyhoz és az üresjárati időkorlát nem megfelelően vannak konfigurálva. 

Az Azure AD Connect 1.1.443.0 verzió vagy újabb, meg kell kimenő HTTPS a hozzáférést a következő:

   - passwordreset.microsoftonline.com
   - servicebus.Windows.NET

Több Granularitás frissített listáját lásd [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) frissítése minden szerdán és a következő hatályba hétfő.

További információkért tekintse át a kapcsolat Előfeltételek a [Előfeltételek az Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) cikk.



### <a name="restart-the-azure-ad-connect-sync-service"></a>Az Azure AD Connect szinkronizálási szolgáltatás újraindítása

Kapcsolódási problémák vagy a szolgáltatás átmeneti problémáinak megoldása érdekében indítsa újra az Azure AD Connect szinkronizálási szolgáltatás:

   1. Rendszergazdaként, válassza ki a **Start** az Azure AD Connect futtató kiszolgálón.
   2. Adjon meg **services.msc** a keresőmezőbe, és válasszon **Enter**.
   3. Keresse meg a **Microsoft Azure AD Sync** bejegyzés.
   4. Kattintson a jobb gombbal a szolgáltatás bejegyzést, jelölje be **indítsa újra a**, majd várjon a művelet befejezéséhez.

   ![Az Azure AD Sync szolgáltatás újraindítása][Service restart]

Ezeket a lépéseket helyre a kapcsolatot a felhőalapú szolgáltatással, és oldja meg a megszakításokat, léptek fel. Ha az ADSync szolgáltatás újraindítása nem oldja meg a problémát, javasoljuk, próbálja meg letiltani, és majd engedélyezze újra a jelszót a visszaírási szolgáltatás.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Tiltsa le, majd engedélyezze újra a jelszót a visszaírási szolgáltatás

Kapcsolódási problémák megoldása érdekében tiltsa le, és majd engedélyezze újra a jelszót a visszaírási szolgáltatás:

   1. A rendszergazdák az Azure AD Connect konfigurálása varázsló megnyitásához.
   2. A **az Azure AD Connect**, az Azure AD globális rendszergazda hitelesítő adatait.
   3. A **csatlakozás az Active Directory tartományi szolgáltatások**, AD tartományi szolgáltatások rendszergazdai hitelesítő adatait.
   4. A **felhasználók egyedi azonosítása**, jelölje be a **következő** gombra.
   5. A **választható szolgáltatások**, törölje a jelet a **jelszóvisszaírás** jelölőnégyzetet.
   6. Válassza ki **következő** keresztül nélkül semmit nem változtat amíg elér a fennmaradó párbeszédpanelek a **beállíthatja az** lap.
   7. Győződjön meg arról, hogy a **beállíthatja az oldal** jeleníti meg a **jelszóvisszaírás** lehetőség szerint **le van tiltva** , és válassza a zöld **konfigurálása** gombra a módosítások véglegesítéséhez.
   8. A **befejezett**, törölje a jelet a **szinkronizálás most** lehetőséget, majd válassza ki **Befejezés** a varázsló bezárásához.
   9. Nyissa meg újra az Azure AD Connect konfigurációs varázsló.
   10. Ismételje meg a 2-8, azzal a különbséggel győződjön meg arról, hogy válassza ki a **jelszóvisszaírás** beállítást a **választható szolgáltatások** lapon engedélyezze újra a szolgáltatást.

Ezeket a lépéseket helyre a kapcsolatot a felhőalapú szolgáltatással, és oldja meg a megszakításokat, léptek fel.

Letiltásával és újbóli engedélyezésével a jelszó visszaírási funkció nem oldja meg a problémát, azt javasoljuk, hogy az Azure AD Connect újratelepítése.

### <a name="install-the-latest-azure-ad-connect-release"></a>Az Azure AD Connect legújabb kiadásának telepítése

Az Azure AD Connect újratelepítése oldhatja konfigurációs és a problémák a felhőszolgáltatások és a helyi Active Directory-környezet között.

Azt javasoljuk, hogy elvégzi ezt a lépést csak azután kísérli meg a korábban leírt két lépést.

> [!WARNING]
> Ha testreszabott out-of-az-box szinkronizálási szabályok *róluk biztonsági másolatot mielőtt folytatná a frissítést, és ezután manuálisan telepíteni őket amikor befejezte a.*

   1. Töltse le az Azure AD Connect legújabb verzióját a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
   2. Mivel már telepítette az Azure AD Connect, meg kell helyben történő frissítést végez az Azure AD Connect telepítés frissítése a legújabb verzióra.
   3. A letöltött csomagot, és kövesse a képernyőn megjelenő utasításokat az Azure AD Connect gép frissítése.

Az előző lépést kell helyre a kapcsolatot a felhőalapú szolgáltatással, és hárítsa el a megszakításokat, léptek fel.

Ha az Azure AD Connect-kiszolgáló legújabb verziójának telepítése nem oldja meg a problémát, ajánlott letiltásával és újbóli engedélyezésével jelszóvisszaírás utolsó lépésként legújabb kiadásának telepítése után próbálja.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Győződjön meg arról, hogy rendelkezik-e a szükséges engedélyekkel a jelszóvisszaírást az Azure AD Connect

Az Azure AD Connect szükséges Active Directory **jelszó-átállítási** engedéllyel a jelszóvisszaírás elvégzéséhez. Ha szeretné tudni, ha az Azure AD Connect rendelkezik a szükséges engedélyekkel, egy adott a helyszíni Active Directory felhasználói fiók, használhatja a Windows hatékony engedély szolgáltatást:

   1. Jelentkezzen be az Azure AD Connect-kiszolgáló, majd indítsa el a **Synchronization Service Managert** kiválasztásával **Start** > **szinkronizálási szolgáltatás**.
   2. Az a **összekötők** lapra, válassza ki a helyszíni **Active Directory tartományi szolgáltatások** összekötő, és válassza **tulajdonságok**.  

   ![Hatékony engedély - 2. lépés](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. Az előugró ablakban válassza ki **csatlakozás az Active Directory-erdőben** és jegyezze fel a **felhasználónév** tulajdonság. Ez a tulajdonság az Azure AD Connect címtár-szinkronizálás végrehajtásához használt AD DS-fiókjához. Az Azure AD Connect jelszóvisszaírás végrehajtásához az AD DS-fiókjához alaphelyzetbe jelszó engedéllyel kell rendelkeznie.  
   
   ![Hatékony engedély - 3. lépés](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. Jelentkezzen be a helyi tartományvezérlőre, és indítsa el a **Active Directory – felhasználók és számítógépek** alkalmazás.
   5. Válassza ki **nézet** , és győződjön meg arról, hogy a **speciális funkciók** engedélyezve van.  
   
   ![Hatékony engedély - 5. lépés](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. Keresse meg az Active Directory felhasználói fiók ellenőrizni szeretné. Kattintson a jobb gombbal a fiók nevét, és válassza ki **tulajdonságok**.  
   
   ![Hatékony engedély - 6. lépés](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. Az előugró ablakban, keresse meg a **biztonsági** lapot, és válasszon **speciális**.  
   
   ![Hatékony engedély - 7. lépés](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. Az a **rendszergazda speciális biztonsági beállítások** előugró ablakban, keresse fel a **hatályos hozzáférés** fülre.
   9. Válassza ki **válasszon ki egy felhasználót**, válassza ki az Azure AD által használt Tartományi fiók Connect (lásd a 3. lépés), majd válassza ki **hatályos hozzáférés megtekintése**.  
   
   ![Hatékony engedély - 9. lépés](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. Görgessen lefelé, és keresse meg **jelszó-átállítási**. Ha a bejegyzés jelölőnégyzet be van jelölve, az Active Directory tartományi szolgáltatások fióknak legyen jogosultsága a kijelölt Active Directory felhasználói fiók jelszavának.  
   
   ![Hatékony engedély - 10. lépés](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-fórumok

Ha az Azure AD általános kérdése van, és az önkiszolgáló jelszó-visszaállítás, megkérheti a közösségi Ha segítségre van szüksége a a [az Azure AD-fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartozik a mérnökök, termék kezelők, MVP és ahol informatikai szakemberek számára.

## <a name="contact-microsoft-support"></a>Forduljon a Microsoft támogatási szolgálatához.

Ha nem találja a választ, hogy egy probléma megoldásához, a támogatási csoportokkal mindig elérhetők további segítséget.

Megfelelően segítséget, kérjük, hogy megadja mértékű részletes lehető esetek megnyitásakor. Ezek az adatok a következők:

* **A hiba leírása általános**: Mi az, hogy a hibát? Mi volt volt megfigyelhető viselkedés? Hogyan azt Reprodukálja a hibát? Adja meg a legnagyobb részletességgel lehető.
* **Lap**: milyen lap volt meg, ha a hiba első fellépése? Például az URL-címet, ha elvégezheti és egy Képernyőkép az oldal.
* **Támogatja a kód**: Mi volt a támogatási kód lett létrehozva, amikor a felhasználó látta a hibát?
    * Ez a kód megkereséséhez Reprodukálja a hibát, majd jelölje ki a **támogatja a kód** hivatkozást a képernyő alján, és a támogatási szakember küldése a GUID, amely.

    ![A támogatási kód a képernyő alján található][Support code]

    * Ha oldalon szerepel a lap alján támogatás nélkül, válassza ki az F12, és keresse meg a biztonsági AZONOSÍTÓT és a CID, és két eredményeket elküldik a támogatási szakértőhöz.
* **Dátum, idő és időzóna**: a pontos dátum és idő *az időzónát a* , amely a hiba történt.
* **Felhasználói azonosító**: ki volt a hiba látott felhasználó? Példa:  *user@contoso.com* .
    * Az egy összevont felhasználó?
    * A jelszó-kivonat-szinkronizált felhasználó van szó?
    * Az egy kizárólag felhőalapú felhasználót?
* **Licencelési**: a felhasználó nem rendelkezik az Azure AD prémium vagy alapszintű Azure AD-licenccel?
* **Alkalmazások eseménynaplójában**: jelszóvisszaírás használja, és a hiba: a helyszíni-infrastruktúrájában, ha az alkalmazások eseménynaplójában keresse meg az Azure AD Connect-kiszolgáló a tömörített másolatát tartalmazza.



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Az Azure AD Sync szolgáltatás újraindítása"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Az ablak jobb alsó a támogatási kód"

## <a name="next-steps"></a>További lépések

A következő cikkekben talál további információt nyújtanak azokról az Azure AD használatával új jelszó.

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Jelszó visszaállítása vagy módosítása](active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
