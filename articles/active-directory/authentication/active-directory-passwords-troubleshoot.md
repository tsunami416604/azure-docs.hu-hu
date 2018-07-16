---
title: Az önkiszolgáló jelszó-visszaállítási hibaelhárítási – Azure Active Directory
description: Hibaelhárítás az Azure AD önkiszolgáló jelszó-visszaállítási
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e24f5070a793f62481bdc80044c97163c5b5c79f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045189"
---
# <a name="troubleshoot-self-service-password-reset"></a>Az önkiszolgáló jelszó-visszaállítás hibaelhárítása

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) problémát tapasztalt? A következő információk segítségével, hogy ismét működik.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Által is látható a felhasználó önkiszolgáló jelszó-visszaállítási hibáinak elhárítása

| Hiba | Részletek | Technikai részletek |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Sajnáljuk, nem lehet új jelszót most, mert a rendszergazda letiltotta a jelszóátállítást a szervezet számára. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazdához, és kérje meg őket, ez a funkció engedélyezéséhez. További tudnivalókért lásd: [segítség, elfelejtettem Azure AD-jelszavamat](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Azt észleltük, hogy a jelszó-visszaállítás nincs engedélyezve a rendszergazda által. Forduljon a rendszergazdához, és hogy engedélyezze a jelszó-visszaállítást a szervezet számára. |
| WritebackNotEnabled = 10 |Sajnáljuk, nem lehet új jelszót jelenleg, mert a rendszergazda nem engedélyezett egy szükséges szolgáltatást a szervezete számára. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazdához, és kérje meg őket a szervezet konfiguráció ellenőrzéséhez. A szükséges szolgáltatással kapcsolatos további tudnivalókért lásd: [konfigurálása a jelszóvisszaíró](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: Azt észleltük, hogy a jelszóvisszaíró nincs engedélyezve. Forduljon a rendszergazdához, és kérje meg őket a jelszóvisszaíró engedélyezéséhez. |
| SsprNotEnabledInUserPolicy = 11 | Sajnáljuk, nem lehet új jelszót most, mert a rendszergazda nem konfigurálta a jelszóátállítást a szervezet számára. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a jelszóátállítás konfigurálására. Konfiguráció alaphelyzetbe állítása jelszóval kapcsolatos további tudnivalókért lásd: [rövid útmutató: Azure AD önkiszolgáló jelszó-visszaállítás](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: A szervezet nem definiált egy jelszó kérésére vonatkozó szabályzatot. Forduljon a rendszergazdához, és kérje meg a jelszó-visszaállítási házirend meghatározásához. |
| UserNotLicensed = 12 | Sajnáljuk, nem lehet új jelszót jelenleg, mert a szükséges licencek hiányoznak a szervezet. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazdához, és hogy ellenőrizze a licenc-hozzárendelést. Licenceléssel kapcsolatos további tudnivalókért lásd: [licencelési követelményeivel az Azure AD önkiszolgáló jelszó-visszaállítási](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: A szervezet nem rendelkezik a szükséges licencekkel új jelszó kérésének végrehajtásához szükséges. Forduljon a rendszergazdához, és kérje meg őket, tekintse át a licenc-hozzárendeléseket. |
| UserNotMemberOfScopedAccessGroup = 13 | Sajnáljuk, nem lehet új jelszót jelenleg, mert a rendszergazda nem konfigurálta a jelszó-visszaállítás használandó fiókot. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazdához, és kérje meg a fiók, jelszó-visszaállítás beállítása. A jelszó-visszaállításhoz fiók konfigurálásával kapcsolatban további tudnivalókért lásd: [felhasználók jelszóátállítás bevezetése](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Ön nem tagja egy csoportnak, jelszó-visszaállításhoz engedélyezett. Kérje a rendszergazdát, hogy a csoporthoz hozzáadni. |
| UserNotProperlyConfigured = 14 | Sajnáljuk, nem lehet új jelszót most, mert hiányzik a szükséges információkat a fiókból. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazda, és kérje meg a jelszó alaphelyzetbe állításához. Miután hozzáférést a fiókjához újra, a szükséges információkat regisztrálnia kell. Információk regisztrálásához kövesse a [regisztrálása önkiszolgáló jelszó-visszaállítás](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) cikk. | SSPR_0014: További biztonsági információkat kelljen a jelszó szükséges. A folytatáshoz, forduljon a rendszergazdához, és kérje meg a jelszó alaphelyzetbe állításához. Miután hozzáférést a fiókjához, további biztonsági információkat kelljen, regisztrálhatja https://aka.ms/ssprsetup. A rendszergazda adhat hozzá további biztonsági adatok fiókja a lépéseket követve [beállítása és a jelszó-visszaállítás olvasási hitelesítési adatok](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Sajnáljuk, hogy nem állítható alaphelyzetbe a jelszót jelenleg szervezete jelszó alaphelyzetbe állítása konfigurációs probléma miatt. Nem tartoznak további műveletek oldja meg ezt a helyzetet is igénybe vehet. Forduljon a rendszergazdához, és kivizsgálására. A lehetséges problémák kapcsolatos további információkért lásd: [a jelszóvisszaíró hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nem tudjuk a helyszíni konfiguráció hibája miatt a jelszó alaphelyzetbe állításához. Forduljon a rendszergazdához, és kivizsgálására. |
| OnPremisesConnectivityError = 30 | Sajnáljuk, hogy nem állítható alaphelyzetbe a jelszót jelenleg a szervezet kapcsolódási problémák miatt. Nem tartoznak teendője most műveletek, de a probléma megoldódott lehet, ha később újra próbálkozik. Ha a probléma tartósan fennáll, forduljon a rendszergazdához, és kivizsgálására. Kapcsolati problémákra vonatkozó további tudnivalókért lásd: [a jelszóvisszaíró csatlakoztatási problémáinak hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: A Microsoft nem állítható vissza a helyszíni környezettel való gyenge kapcsolat miatt a jelszó. Forduljon a rendszergazdához, és kivizsgálására.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>A jelszó alaphelyzetbe állítása konfigurálása az Azure Portalon hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| Nem látom a **új jelszó kérésére vonatkozó** szakasz alatt az Azure AD az Azure Portalon. | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenc rendelve a rendszergazda a művelet végrehajtása. <br> <br> Rendeljen egy licencet a szóban forgó rendszergazdai fiókhoz. A regisztrálással kapcsolatban a [hozzárendelése, ellenőrzése és licencek kapcsolatos problémák megoldásához](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) cikk.|
| Egy adott konfigurációs beállítás nem látható. | A felhasználói felület számos elemét rejtve vannak, amíg rájuk. Próbálja meg szeretné tekinteni az összes beállítások engedélyezése. |
| Nem látom a **a helyszíni integrációs** fülre. | Ez a beállítás csak láthatóvá válik, ha az Azure AD Connect letöltése, és a jelszóvisszaíró konfigurálása. További információkért lásd: [első lépések az Azure AD Connecttel a gyorsbeállítások használatával](./../connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Jelszó alaphelyzetbe állítása reporting hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| Nem látom minden jelszó felügyeleti tevékenységtípusok az **önkiszolgáló jelszókezelés** naplózási esemény kategóriáját. | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenc rendelve a rendszergazda a művelet végrehajtása. <br> <br> Ezt a problémát megoldhatja, ha a licenc hozzárendelése a szóban forgó rendszergazdai fiók. Kövesse a [hozzárendelése, ellenőrzése és licencek kapcsolatos problémák megoldásához](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) cikk. |
| Felhasználói regisztrációk megjelenítése több alkalommal. | Jelenleg amikor egy felhasználó regisztrálja magát, azt jelentkezzen minden egyes adat, amely egy különálló esemény lesz regisztrálva. <br> <br> Ha szeretné ezeket az adatokat összesíteni és hogyan lehet megtekinteni, a nagyobb rugalmasságot biztosít, töltse le a jelentést, és nyisson meg egy kimutatást az Excelben az adatok.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>A jelszó-visszaállítási portál hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| A címtárban nincs engedélyezve a jelszó-visszaállításhoz. **A rendszergazda nem engedélyezte, hogy a funkció használatához.** | Kapcsoló a **önkiszolgáló jelszóátállítás engedélyezve** jelzőt **kijelölt** vagy **összes** , majd **mentése**. |
| A felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű licenc rendelve. **A rendszergazda nem engedélyezte, hogy a funkció használatához.** | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenc rendelve a rendszergazda a művelet végrehajtása. <br> <br> Ezt a problémát megoldhatja, ha a licenc hozzárendelése a szóban forgó rendszergazdai fiók. Kövesse a [hozzárendelése, ellenőrzése és licencek kapcsolatos problémák megoldásához](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) cikk.|
| Nincs hiba történt a kérelem feldolgozása közben. | Ez számos probléma okozhatja, de általában a hiba oka a szolgáltatás-kimaradás vagy konfigurációs hiba. Ha ezt a hibaüzenetet, és befolyásolhatná az üzleti, további segítségért forduljon a Microsoft ügyfélszolgálatához. |

## <a name="troubleshoot-the-password-reset-portal"></a>A jelszó-visszaállítási portál hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| A címtárban nincs engedélyezve a jelszó-visszaállításhoz. | Kapcsoló a **önkiszolgáló jelszóátállítás engedélyezve** jelzőt **kijelölt** vagy **összes** , majd **mentése**. |
| A felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű licenc rendelve. | Ez akkor fordulhat elő, ha nem rendelkezik egy Azure AD prémium vagy alapszintű licenc rendelve a rendszergazda a művelet végrehajtása. <br> <br> A probléma megoldásához is, ha a licenc hozzárendelése a szóban forgó rendszergazdai fiók. Kövesse a [hozzárendelése, ellenőrzése és licencek kapcsolatos problémák megoldásához](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) cikk. |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz, de a felhasználó rendelkezik hiányzik vagy hibás formátumú hitelesítési adatait. | A folytatás előtt győződjön meg arról, hogy a felhasználó rendelkezik formátuma megfelelő kapcsolattartási adatokat a fájl a könyvtárban. További információkért lásd: [adatokat használják az Azure AD önkiszolgáló jelszó-visszaállítási](howto-sspr-authenticationdata.md). |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz, de a felhasználó fájl rendelkezik kapcsolattartási adatok csak egy részét, ha a szabályzat két ellenőrzési módszerek sorát úgy van beállítva. | A folytatás előtt győződjön meg arról, hogy a felhasználó rendelkezik-e legalább két, vagyis a megfelelően konfigurált kapcsolattartási módszerek. Például mindkét mobiltelefonszám tapasztalja *és* az irodai telefonszámát. |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz és a felhasználó megfelelően van konfigurálva, de a felhasználó nem tud kapcsolódni. | Ez lehet egy átmeneti szolgáltatáshiba eredményét, vagy ha helytelen kapcsolattartási adatokat, hogy nem megfelelően észlelni. <br> <br> Ha a felhasználó 10 másodpercet vár, "újra" és "forduljon a rendszergazdához" hivatkozások jelennek meg. Ha a felhasználó "újra", újra megpróbálja a hívást. Ha a felhasználó a "forduljon a rendszergazdához" lehetőséget választja, a jelszó-visszaállításra a fiókhoz tartozó végrehajtandó igénylő rendszergazdák egy űrlap e-mailt küld. |
| A felhasználó soha nem kap az új jelszó kérése SMS vagy telefonhíváson alapuló. | Ez lehet egy helytelen telefonszámot a címtárban eredményét. Győződjön meg arról, hogy a telefonszám a következő formátumban van, "+ másolatot xxxyyyzzzzXeeee". <br> <br> Jelszó-visszaállítás nem támogatja a bővítményeket, még akkor is, ha megad egy, a címtárban. A bővítmények a program eltávolítja, a hívás elküldése előtt. Egy szám kiterjesztés nélkül használja, vagy a bővítmény integrálása a telefonszám a saját ágban exchange (PBX) a. |
| A felhasználó soha nem a jelszó alaphelyzetbe állítása az e-mailt kap. | A leggyakoribb probléma oka, hogy az üzenet levélszemétszűrőt elutasította. Ellenőrizze a levélszemét, levélszemét, vagy a törölt elemek mappát az e-mailt. <br> <br> Emellett győződjön meg arról, hogy Ön már ellenőrzése a helyes e-mail-fiókot, az üzenet. |
| Rendelkezik egy jelszó-visszaállítási házirend állítható be, de amikor rendszergazdai fiókot használ jelszó-visszaállítás, az adott házirendnek nem alkalmazza. | A Microsoft felügyeli, és szabályozza a rendszergazdai jelszóvisszaállítási szabályzat a legmagasabb szintű biztonság biztosítása érdekében. |
| A felhasználó megakadályozta a jelszó-visszaállításra egy napon belül túl sokszor próbált. | Az új jelszót kérjenek rövid időn belül túl sokszor próbált letiltása a felhasználók számára való megvalósítása automatikus szabályozási mechanizmussal. Szabályozás akkor fordul elő, ha: <br><ul><li>A felhasználó megpróbál egy telefonszámot egy óra alatt ötször ellenőrzése.</li><li>A felhasználó megpróbál egy óra múlva ötször a biztonsági kérdések kapu használja.</li><li>A felhasználó megpróbál egy óra múlva ötször ugyanazt a felhasználói fiókot a jelszó alaphelyzetbe állítása.</li></ul>A probléma megoldásához kérje a felhasználót, hogy Várjon 24 órát a legutóbbi kísérlet után. A felhasználó majd alaphelyzetbe állíthatja a jelszót. |
| A felhasználó hibaüzenetet látja, telefonszámát érvényesítése során. | Ez a hiba akkor fordul elő, ha a megadott telefonszám nem egyezik meg a fájl telefonszám. Győződjön meg arról, hogy a felhasználó a teljes telefonszám, többek között a terület- és ország kódot, amikor azok megkísérlik az olyan módszert használja a jelszó-visszaállításhoz lépjen be. |
| Nincs hiba történt a kérelem feldolgozása közben. | Ez számos probléma okozhatja, de általában a hiba oka a szolgáltatás-kimaradás vagy konfigurációs hiba. Ha ezt a hibaüzenetet, és befolyásolhatná az üzleti, további segítségért forduljon a Microsoft ügyfélszolgálatához. |
| A helyszíni szabályzat megsértése | A jelszó nem felel meg a helyszíni Active Directory jelszóházirend. |
| Jelszó nem felel meg az intelligens házirend | A használt jelszót a letiltott jelszavak listájában jelenik meg, és nem használhatók. |

## <a name="troubleshoot-password-writeback"></a>A jelszóvisszaíró hibaelhárítása

| Hiba | Megoldás |
| --- | --- |
| A jelszó-átállítási szolgáltatás nem indul el a helyszíni. Az Azure AD Connect gép alkalmazás eseménynaplója 6800 hiba jelenik meg. <br> <br> Az előkészítés, összevont, után átmenő hitelesítés vagy a jelszó-kivonat-szinkronizált felhasználók nem visszaállíthassák a jelszavukat. | Ha engedélyezve van a jelszóvisszaírás, a szinkronizálási motor meghívja a jelszóvisszaíró könyvtár végezheti el a konfigurálást (előkészítés) által a bevezetési felhőszolgáltatáshoz való kommunikációhoz. Az előkészítés során vagy a Windows Communication Foundation (WCF) végpontot, a jelszó-visszaírási eredmények elindítása a hibák során észlelt hibákat az eseménynaplóban jelentkezzen be az Azure AD Connect-gépen. <br> <br> Során az Azure AD-Szinkronizálóról (ADSync) szolgáltatás újraindítása Ha visszaírási lett konfigurálva, a WCF-végpont indítása. Azonban ha a végpont a indítása sikertelen, azt fogja 6800 eseményt, és lehetővé teszik a szinkronizáló szolgáltatás indítása. Ez az esemény jelenlétét, az azt jelenti, hogy a jelszó-visszaírási végpont nem indult el. Eseménynapló részletei eseménynapló-bejegyzéseket létrehozni az PasswordResetService összetevő, valamint az esemény 6800, adja meg, miért nem lehet elindítani a végpontot. Tekintse át a Eseménynapló hibák, és próbálja meg újraindítani az Azure AD Connect, ha a jelszóvisszaíró még mindig nem működik. Ha a probléma tartósan fennáll, próbálja meg letiltani és újra engedélyezni a jelszóvisszaíró.
| Amikor egy felhasználó megpróbál jelszó alaphelyzetbe állítása, vagy egy fiók feloldása a jelszóvisszaíró engedélyezve van, a művelet sikertelen lesz. <br> <br> Emellett tekintse meg az Azure AD Connect eseménynaplójában, amely tartalmazza az esemény: "Szinkronizáló visszaadott-hibakód: hr = 800700CE, üzenet a filename = bővítmény túl hosszú, vagy" után akkor fordul elő, a feloldási műveletet. | Keresse meg az Active Directory-fiókot az Azure AD Connect, és legfeljebb 127 karakter hosszú lehet benne, hogy új jelszót kérhet. Nyissa meg a **szinkronizálási szolgáltatás** származó a **Start** menü. Keresse meg a **összekötők** , és keresse meg a **Active Directory-összekötő**. Válassza ki, majd **tulajdonságok**. Keresse meg a **hitelesítő adatok** lapon, és adja meg az új jelszót. Válassza ki **OK** a lap bezárásához. |
| Az utolsó lépés az Azure AD Connect telepítési eljárása nem sikerült konfigurálni a jelszóvisszaírást jelző hibaüzenet jelenik meg. <br> <br> Az Azure AD Connect alkalmazás eseménynaplója tartalmaz 32009 hiba a szöveg a "Hiba első hitelesítési token." | Ez a hiba akkor fordul elő, az alábbi két esetben: <br><ul><li>A globális rendszergazdai fiók megadva, az Azure AD Connect telepítési folyamat elején helytelen jelszót adott meg.</li><li>A globális rendszergazdai fiók megadva, az Azure AD Connect telepítési folyamat elején egy összevont felhasználó használatára történt kísérlet.</li></ul> A probléma megoldásához, győződjön meg arról, hogy nem használ-e be a pedig összevont fiók a globális rendszergazda, a telepítési folyamat elején megadott. Emellett győződjön meg arról, hogy a megadott jelszó helyességéről. |
| Az Azure AD Connect gép Eseménynapló PasswordResetService futtatásával fordul elő hiba 32002 tartalmazza. <br> <br> Beolvassa a hiba: "hiba történt a ServiceBus csatlakozás. A jogkivonat-szolgáltató nem tudta meg egy biztonsági jogkivonatot." | A helyszíni környezet nem tud csatlakozni az Azure Service Bus-végponttal, a felhőben. Ez a hiba általában egy tűzfalszabály blokkolja-e egy adott porton vagy webes címet egy kimenő kapcsolatot okozza. Lásd: [kapcsolat Előfeltételek](./../connect/active-directory-aadconnect-prerequisites.md) további információ. Ezek a szabályok frissítése után az Azure AD Connect újraindítás, és a jelszóvisszaíró kell ismét dolgozni. |
| Egy kis ideig, összevont, működő után átmenő hitelesítés vagy a jelszó-kivonat-szinkronizált felhasználók nem visszaállíthassák a jelszavukat. | Egyes ritka esetekben a jelszóvisszaíró szolgáltatás is sikertelen, indítsa újra az Azure AD Connect újraindításakor. Ezekben az esetekben először ellenőrizze hogy a jelszóvisszaíró úgy tűnik, hogy engedélyezve van a helyszíni. Ellenőrizheti az Azure AD Connect varázsló vagy a PowerShell használatával (lásd az előző HowTos szakaszt). Ha a szolgáltatás úgy tűnik, hogy engedélyezve van, engedélyezése vagy letiltása a szolgáltatás ismét vagy a felhasználói felület vagy a Powershellen keresztül próbálja meg. Ha ez sem működik, próbálja ki az eltávolításhoz, és telepítse újra az Azure AD Connect. |
| Összevont, átmenő hitelesítést, vagy a jelszó-kivonat-szinkronizált felhasználók, akik megpróbálnak jelszavuk tekintse meg a hiba után megkísérli elküldeni a jelszavát. A hiba azt jelzi, hogy hiba történt a szolgáltatás során. <br ><br> Probléma mellett jelszó alaphelyzetbe állítása működés során előfordulhat, hogy hibaüzenet jelenik meg, hogy a felügyeleti ügynök nem volt hozzáférése a helyszíni eseménynaplóiban. | Ha látja ezeket a hibákat az eseménynaplóban, győződjön meg arról, hogy a konfigurációs idején a varázslóban megadott Active Directory Management Agent (ADMA) fiók rendelkezik-e a jelszóvisszaíró számára szükséges engedélyekkel. <br> <br> Miután ezt az engedélyt megadják, az engedélyek keresztül le trickle legfeljebb egy órát is igénybe vehet a `sdprop` háttérfeladat a tartományvezérlőn (DC). <br> <br> A jelszó-visszaállítás működéséhez az engedély kell a biztonsági leírót a felhasználói objektum, amelynek a jelszavát alaphelyzetbe kell jelölni. A user objektum megjelenik ez az engedély, amíg a jelszó-visszaállítás továbbra is sikertelen, és a "hozzáférés megtagadva" üzenet. |
| Összevont, átmenő hitelesítést, vagy a jelszó-kivonat-szinkronizált felhasználók, akik megpróbálnak a jelszavaikat, tekintse meg a hiba után ezek küldenek a jelszavát. A hiba azt jelzi, hogy hiba történt a szolgáltatás során. <br> <br> Mellett ez a probléma jelszó alaphelyzetbe állítása működés során előfordulhat, hogy hibaüzenet jelenik meg az eseménynaplóban az Azure AD Connect szolgáltatást, hogy "Objektum nem található" hiba a. | Ez a hiba általában azt jelzi, hogy a szinkronizálási motor nem található a felhasználói objektum az Azure AD-összekötő-térben vagy a társított metaverzum (MV) vagy az Azure ad-ben összekötőtér objektuma. <br> <br> A hiba elhárításához győződjön meg arról, hogy a felhasználó valóban szinkronizálása a helyszíni Azure ad-n keresztül az Azure AD Connect az aktuális példány, és vizsgálja meg az objektumok összekötőterek és MV állapotát. Győződjön meg arról, hogy az Active Directory tanúsítványszolgáltatások (AD CS) objektum a MV-objektum a "Microsoft.InfromADUserAccountEnabled.xxx" szabály keresztül csatlakozik.|
| Összevont, átmenő hitelesítést, vagy a jelszó-kivonat-szinkronizált felhasználók, akik megpróbálnak jelszavuk tekintse meg a hiba után ezek küldenek a jelszavát. A hiba azt jelzi, hogy hiba történt a szolgáltatás során. <br> <br> Mellett ez a probléma jelszó alaphelyzetbe állítása működés során előfordulhat, hogy hibaüzenet jelenik meg az Azure AD Connect szolgáltatástól, amely azt jelzi, hogy van-e egy "Több egyezés található" hiba történt az eseménynaplók. | Ez azt jelzi, hogy a szinkronizálási motor azt észlelte, hogy a MV-objektum egynél több Active Directory Tanúsítványszolgáltatások objektum "Microsoft.InfromADUserAccountEnabled.xxx" keresztül csatlakozik. Ez azt jelenti, hogy a felhasználó rendelkezik-e egynél több erdő egy engedélyezett fiók. Ebben a forgatókönyvben a jelszóvisszaírás nem támogatott. |
| Jelszó műveletek konfigurációs hiba miatt sikertelen. Az alkalmazás eseménynaplója tartalmazza az Azure AD Connect hibát 6329 a következő szöveggel: "0x8023061f (a művelet sikertelen volt, mert a jelszó-szinkronizálás nincs engedélyezve ezen a Kezelőügynökön)". | Ez a hiba akkor fordul elő, ha az Azure AD Connect konfigurációjának módosításakor adjon hozzá egy új Active Directory-erdőben (vagy a távolítsa el, és a egy meglévő erdőben szerepkörtagok) után a jelszót a jelszóvisszaíró szolgáltatás már engedélyezve van. Ezek a felhasználók számára jelszó műveletek nemrégiben erdők sikertelen. A probléma megoldásához tiltsa le, és majd engedélyezze újra a jelszót a jelszóvisszaíró szolgáltatást az erdő konfigurációs módosítások után. |

## <a name="password-writeback-event-log-error-codes"></a>Jelszó visszaírási-eseménynaplójának hibakódjai

A jelszóvisszaíró használata a problémák elhárításakor ajánlott eljárás, hogy vizsgálja meg az alkalmazások eseménynaplójában, az Azure AD Connect-gépen. Ehhez az eseménynaplóhoz a jelszóvisszaíró számára lényeges két forrásból származó eseményeket tartalmazza. A PasswordResetService forrás műveletek és a jelszóvisszaíró működésével kapcsolatos problémák – ismerteti. Az ADSync forrás műveletek és a jelszavakat az Active Directory-környezet beállításával kapcsolatos problémák – ismerteti.

### <a name="if-the-source-of-the-event-is-adsync"></a>Ha a forrás az esemény ADSync

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 6329 | FEDEZETE: MMS(4924) 0x80230619: "korlátozás megakadályozza, hogy a jelszó a következőre változik: a megadott jelenlegivel." | Ezt az eseményt akkor fordul elő, ha a jelszóvisszaíró szolgáltatás próbál állítson be jelszót a helyi címtárban, amely nem felel meg a jelszó kora, előzmények, bonyolultsága vagy a tartomány szűrési követelményeinek. <br> <br> Ha a jelszó minimális kora és nemrég módosította a jelszó az, hogy eltelt időszakot, Ön nem tudja módosítani a jelszót újra, addig a megadott kora a tartományban. Tesztelési célokra, a minimális korú 0-ra kell állítani. <br> <br> Ha jelszó előzményekre vonatkozó követelményeknek engedélyezve van, akkor ki kell választania egy jelszót, amelyet nem használtak az elmúlt *N* időpontok, hol *N* a jelszó-előzmények beállítása. Ha kiválaszt egy jelszót, amely használatban van az utolsó *N* időkorlátja, majd, egy hibaüzenet jelenik meg ebben az esetben. Tesztelési célokra, a jelszóelőzmények 0-ra kell állítani. <br> <br> Ha jelszó-összetettségi követelményeknek, ezek mindegyike kényszerítettek, amikor a felhasználó megpróbál módosítása vagy a jelszó alaphelyzetbe állítása. <br> <br> Ha a jelszószűrők engedélyezve van, és a felhasználó kiválaszt egy jelszót, amely nem felel meg a szűrési feltételeket, majd az alaphelyzetbe állítás vagy módosítási művelet meghiúsul. |
| 6329 | MMS(3040): admaexport.cpp(2837): A kiszolgáló nem tartalmaz az LDAP-jelszó házirend vezérlő. | Ez a probléma akkor fordul elő, ha LDAP_SERVER_POLICY_HINTS_OID vezérlő (1.2.840.113556.1.4.2066) nincs engedélyezve a tartományvezérlők a. A jelszó a jelszóvisszaíró szolgáltatás használatához engedélyeznie kell a vezérlőt. Ehhez a tartományvezérlők a Windows Server 2008 (a legújabb SP), vagy később kell lennie. Ha a tartományvezérlők 2008 (R2 előtti), akkor is gyorsjavítást kell alkalmaznia [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | Szinkronizáló vezérlő visszaadott-hibakód: hr = 80230402, message = tett kísérlet sikertelen volt, mert nincsenek ismétlődő bejegyzéseket a azonos jegyzetobjektum objektum lekérése. | Ez a hiba akkor fordul elő, ha ugyanazt a felhasználói azonosító engedélyezve van több tartományban. Ilyen például, ha a fiók- és erőforrás-erdők szinkronizál, és ugyanazt a felhasználói azonosító jelen, és engedélyezve van minden olyan erdőben. <br> <br> Ez a hiba akkor is előfordulhat, ha egy nem egyedi a forráshorgony attribútuma, mint egy aliast vagy egyszerű felhasználónév, és két felhasználót, hogy ugyanazt a forráshorgony attribútuma fájlmegosztások. <br> <br> A probléma megoldásához, győződjön meg arról, hogy nem rendelkezik ismétlődő felhasználókat a tartományokon belül, és használjon egy egyedi a forráshorgony attribútuma minden felhasználó számára. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Ha a forrás az esemény PasswordResetService

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 31001 | PasswordResetStart | Az esemény azt jelzi, hogy a helyszíni szolgáltatás azt észlelte, jelszó-létrehozási kérelem egy összevont, átmenő hitelesítést, vagy a jelszó-kivonat-szinkronizált felhasználót, hogy a felhőből származnak. Ezt az eseményt az az első esemény minden jelszó-visszaállítási visszaírási műveletben. |
| 31002 | PasswordResetSuccess | Az esemény azt jelzi, hogy egy felhasználó egy új jelszót kiválasztva a jelszó-visszaállítási művelet során. Azt határozza meg, hogy ez a jelszó megfelel-e a vállalati jelszó követelményeknek. A jelszó sikeresen meg lett írva vissza a helyi Active Directory-környezetet. |
| 31003 | PasswordResetFail | Az esemény azt jelzi, hogy egy felhasználó kiválasztott a jelszót, és a jelszó sikeresen megérkezett a helyszíni környezetbe. De ha próbált meg beállítani a jelszót a helyi Active Directory-környezetben, hiba történt. Ez a hiba akkor fordulhat elő, több okból: <br><ul><li>A jelszó nem felel meg az életkor, előzmények, bonyolultsága, vagy szűrheti a tartomány követelményei. A probléma megoldásához, hozzon létre egy új jelszót.</li><li>Az ADMA-szolgáltatásfiók rendelkezik a megfelelő engedélyeket az új jelszót beállítani az adott felhasználói fiók.</li><li>A felhasználói fiók pedig egy védelmi csoportban, például a tartományi vagy vállalati rendszergazdai csoport, amely tiltja a jelszóbeállítási műveletek.</li></ul>|
| 31004 | OnboardingEventStart | Ez az esemény akkor fordul elő, ha engedélyezte a jelszóvisszaírást az Azure AD Connect és elkezdjük bevezetése a szervezet számára a jelszóvisszaíró webes szolgáltatás. |
| 31005 | OnboardingEventSuccess | Az esemény azt jelzi, hogy a bevezetési folyamat sikeres volt-e, és hogy a jelszó-visszaírás funkció készen áll a használatra. |
| 31006 | ChangePasswordStart | Az esemény azt jelzi, hogy a helyszíni szolgáltatás azt észlelte, egy összevont, átmenő hitelesítést, vagy a jelszó Jelszókivonat szinkronizálása a felhőből származó felhasználói jelszó-módosítási kérelem. Ezt az eseményt az az első esemény minden a jelszóváltoztatás-visszaírási műveletben. |
| 31007 | ChangePasswordSuccess | Az esemény azt jelzi, hogy egy felhasználó egy új jelszót kiválasztott egy jelszó-változtatási művelet során, kiderült, hogy a jelszó megfelel-e a vállalati jelszó követelményeknek, és, hogy a jelszó sikeresen meg lett írva vissza a helyi Active Directory-környezetet. |
| 31008 | ChangePasswordFail | Az esemény azt jelzi, hogy a felhasználó kiválasztott a jelszót, és, hogy a jelszó sikeresen megérkezett a helyszíni környezetben, de a kísérlet történt a jelszó beállítása a helyi Active Directory-környezetben, ha hiba történt. Ez a hiba akkor fordulhat elő, több okból: <br><ul><li>A jelszó nem felel meg az életkor, előzmények, bonyolultsága, vagy szűrheti a tartomány követelményei. A probléma megoldásához, hozzon létre egy új jelszót.</li><li>Az ADMA-szolgáltatásfiók rendelkezik a megfelelő engedélyeket az új jelszót beállítani az adott felhasználói fiók.</li><li>A felhasználói fiók pedig egy védelmi csoportban, például a tartományi vagy a vállalati rendszergazdák, amely tiltja a jelszóbeállítási műveletek.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | A helyszíni szolgáltatás azt észlelte, hogy a jelszó-visszaállítási kérés összevont, átmenő hitelesítést, vagy egy felhasználó nevében a rendszergazda származó jelszó-kivonat-szinkronizált felhasználó számára. Ez az esemény minden rendszergazda által kezdeményezett jelszó-visszaállítási visszaírási művelet első esemény. |
| 31010 | ResetUserPasswordByAdminSuccess | A rendszergazda egy új jelszót egy rendszergazda által kezdeményezett jelszó-visszaállítási művelet során kiválasztott. Azt határozza meg, hogy ez a jelszó megfelel-e a vállalati jelszó követelményeknek. A jelszó sikeresen meg lett írva vissza a helyi Active Directory-környezetet. |
| 31011 | ResetUserPasswordByAdminFail | A rendszergazdai jelszó kijelölt felhasználó nevében. A jelszó sikeresen megérkezett a helyszíni környezetbe. De ha próbált meg beállítani a jelszót a helyi Active Directory-környezetben, hiba történt. Ez a hiba akkor fordulhat elő, több okból: <br><ul><li>A jelszó nem felel meg az életkor, előzmények, bonyolultsága, vagy szűrheti a tartomány követelményei. Próbálja meg a probléma megoldásához új jelszót.</li><li>Az ADMA-szolgáltatásfiók rendelkezik a megfelelő engedélyeket az új jelszót beállítani az adott felhasználói fiók.</li><li>A felhasználói fiók pedig egy védelmi csoportban, például a tartományi vagy a vállalati rendszergazdák, amely tiltja a jelszóbeállítási műveletek.</li></ul>  |
| 31012 | OffboardingEventStart | Ez az esemény akkor fordul elő, ha letiltja a jelszóvisszaírást az Azure AD Connect, és azt jelzi, hogy az azt elindító kivezetés a szervezet számára a jelszóvisszaíró webes szolgáltatás. |
| 31013| OffboardingEventSuccess| Az esemény azt jelzi, hogy a kivezetés folyamat sikeres volt-e, és hogy jelszó-visszaírás funkció letiltása sikerült. |
| 31014| OffboardingEventFail| Az esemény azt jelzi, hogy a kivezetés folyamat nem volt sikeres. Ez a konfiguráció során megadott felhőbeli vagy helyi rendszergazdafiók engedélyekkel kapcsolatos hiba oka lehet. A hiba akkor is előfordulhat, ha a végrehajtani kívánt használatára egy összevont felhőbeli globális rendszergazdai jelszóvisszaíró letiltása esetén. A probléma megoldásához ellenőrizze a rendszergazdai engedélyekkel, és győződjön meg arról, hogy nem használ-e be a pedig összevont fiók a jelszó-visszaírás funkció konfigurálása közben.|
| 31015| WriteBackServiceStarted| Az esemény azt jelzi, hogy a jelszóvisszaíró szolgáltatás sikeresen elindult. Elkészült a felhőből jelszó felügyeleti kérések fogadására.|
| 31016| WriteBackServiceStopped| Az esemény azt jelzi, hogy a jelszóvisszaíró szolgáltatás leállt. Minden jelszót felügyeleti kérelmek a felhőből nem lesz sikeres.|
| 31017| AuthTokenSuccess| Az esemény azt jelzi, hogy mi sikeresen beolvasta a globális rendszergazda az Azure AD Connect telepítés a kivezetés vagy a bevezetési folyamat során megadott egy engedélyezési jogkivonatot.|
| 31018| KeyPairCreationSuccess| Az esemény azt jelzi, hogy sikeresen létrehozott a jelszó-titkosítási kulcs. Ez a kulcs a helyszíni környezetben kell küldeni a felhőben jelszavakat titkosítására szolgál.|
| 32000| UnknownError| Az esemény azt jelzi, hogy ismeretlen hiba történt a jelszó felügyeleti művelet során. Tekintse meg az eseményhez tartozó további részleteket a kivétel szövege. Ha problémákat tapasztal, próbálja meg letiltani, majd ismét engedélyezni a jelszóvisszaíró. Ha ez nem segít, is tartalmaz. a követési együtt az Eseménynapló másolatát megadott belső azonosítója a támogatási szakembernek.|
| 32001| ServiceError| Az esemény azt jelzi, hogy hiba történt egy nem sikerül kapcsolódni a felhő jelszó-átállítási szolgáltatást. Ez a hiba általában akkor fordul elő, ha a helyszíni szolgáltatás nem tudott kapcsolódni a jelszó-visszaállítási webszolgáltatáshoz.|
| 32002| ServiceBusError| Az esemény azt jelzi, hogy hiba történt a bérlő a Service Bus-példányhoz csatlakozik. Ez akkor fordulhat elő, ha Ön blokkolja a kimenő kapcsolatok a helyszíni környezetben. Ellenőrizze a tűzfalat, győződjön meg arról, hogy engedélyezi-e a kapcsolatokat a TCP 443-as porton keresztül, és hogy https://ssprsbprodncu-sb.accesscontrol.windows.net/, majd próbálkozzon újra. Ha továbbra is problémákat tapasztal, próbálja meg letiltása és ismételt engedélyezése a jelszóvisszaíró.|
| 32003| InPutValidationError| Az esemény azt jelzi, hogy a szolgáltatás webes API átadott bemenet érvénytelen volt. Próbálja megismételni a műveletet.|
| 32004| DecryptionError| Az esemény azt jelzi, hogy hiba történt a felhőből érkező jelszavának visszafejtésekor. Ezt okozhatja az visszafejtési kulcsok nem egyeznek a felhőszolgáltatás és a helyszíni környezetben. A probléma megoldásához tiltsa le, és majd újból engedélyezze a jelszavak visszaírását a helyszíni környezetben.|
| 32005| ConfigurationError| Az előkészítés, során a bérlő-specifikus információkat egy konfigurációs fájlban a helyszíni környezetben elmentjük. Az esemény azt jelzi, hogy hiba történt a fájl mentése, illetve, hogy a szolgáltatás indítása, ha hiba történt a fájl olvasásakor. A probléma megoldásához próbálja meg letiltása és ismételt engedélyezése a konfigurációs fájl egy újraírási kényszerítése a jelszóvisszaíró.|
| 32007| OnBoardingConfigUpdateError| Az előkészítés, során küldjük adatok a felhőből a helyi jelszó-átállítási szolgáltatás. Ezután írása egy memórián belüli fájl biztonságosan tárolható a lemez szinkronizálási szolgáltatáshoz való továbbítás előtt. Az esemény azt jelzi, hogy nincs-e a probléma írása, vagy frissíti az adatokat a memóriában. A probléma megoldásához próbálja meg letiltása és ismételt engedélyezése a konfigurációs fájl egy újraírási kényszerítése a jelszóvisszaíró.|
| 32008| ValidationError| Az esemény azt jelzi, hogy érvénytelen választ kapott a jelszó-visszaállítási webszolgáltatásból. A probléma megoldásához, próbálja meg letiltani, majd ismét engedélyezni a jelszóvisszaíró.|
| 32009| AuthTokenError| Az esemény azt jelzi, hogy lekérése egy hitelesítési tokent a globális rendszergazdai fiók az Azure AD Connect telepítés során megadott. Ezt a hibát okozhatja egy hibás felhasználónév vagy a globális rendszergazdai fiók a megadott jelszó. Ez a hiba akkor is előfordulhat, ha a megadott globális rendszergazdai fiók össze van vonva. A probléma megoldásához futtassa újra a konfigurációt a helyes felhasználónévvel és jelszóval, és győződjön meg arról, hogy a rendszergazda az felügyelt (csak felhőalapú vagy a jelszó szinkronizálása) fiók.|
| 32010| CryptoError| Az esemény azt jelzi, hogy hiba történt a jelszó-titkosítási kulcs létrehozása, vagy egy jelszót, amely a felhőszolgáltatás nem érkezik visszafejtése. Ezt a hibát valószínűleg az Ön környezetével kapcsolatos problémát jelez. Tekintse meg az eseménynaplóban, a probléma megoldásával kapcsolatos további részleteit. Megpróbálhatja letiltása és ismételt engedélyezése a jelszóvisszaíró szolgáltatás is.|
| 32011| OnBoardingServiceError| Az esemény azt jelzi, hogy nem sikerült a helyszíni szolgáltatás megfelelő kommunikálni a jelszó-visszaállítási webszolgáltatás a bevezetési folyamat elindítására. Ez akkor fordulhat elő eredményeként egy tűzfalszabályt, vagy hogy van-e a bérlő-hitelesítési token beolvasása során. A probléma megoldásához, győződjön meg arról, hogy, már nem blokkolja kimenő kapcsolatok vagy a TCP 443-as és a 9350 – 9354 TCP keresztül https://ssprsbprodncu-sb.accesscontrol.windows.net/. Emellett ellenőrizze, hogy az Azure AD rendszergazdai fiók felvétele nem összevont használ.|
| 32013| OffBoardingError| Az esemény azt jelzi, hogy nem sikerült a helyszíni szolgáltatás megfelelő kommunikálni a jelszó-visszaállítási webszolgáltatás a kivezetés folyamatának elindításához. Ez akkor fordulhat elő eredményeként egy tűzfalszabályt, vagy hogy van-e a bérlő egy engedélyezési jogkivonat beolvasása során. A probléma megoldásához, győződjön meg arról, hogy Ön még nem blokkolja kimenő kapcsolatok a 443-as porton keresztül, vagy https://ssprsbprodncu-sb.accesscontrol.windows.net/, és a regisztrációjának megszüntetése-t használ az Azure Active Directory-rendszergazdai fiók nem összevont.|
| 32014| ServiceBusWarning| Az esemény azt jelzi, hogy korábban kellett ismételje meg a bérlő a Service Bus-példányhoz való csatlakozáshoz. Normál körülmények között ez kell nem érinti, de ha ez az esemény többször, célszerű lehet ellenőrzi a hálózati kapcsolatot a Service Bus, különösen, ha a nagy késésű és alacsony sávszélességű kapcsolat.|
| 32015| ReportServiceHealthError| Annak érdekében, hogy a jelszóvisszaíró szolgáltatás állapotának figyelésére, azt adatokat küldeni a szívverés a jelszó-visszaállítási webszolgáltatás öt percenként. Az esemény azt jelzi, hogy hiba történt, amikor a egészségügyi adatokat küld vissza a felhőszolgáltatás webes. Az egészségügyi információk nem tartalmazza az objektum azonosításra alkalmas adatok (OII) vagy a személyes azonosításra alkalmas adatokat (PII) adatok, és csak egy szívverés és az alapszintű szolgáltatási statisztikák, hogy a szolgáltatás állapotadatait a felhőben is kínálunk.|
| 33001| ADUnKnownError| Az esemény azt jelzi, hogy az Active Directory által visszaadott ismeretlen hiba történt. Ellenőrizze az Azure AD Connect server eseménynaplóban talál további információt az ADSync forrásból származó események.|
| 33002| ADUserNotFoundError| Az esemény azt jelzi, hogy a jelszó megváltoztatására kívánó felhasználó nem található a helyszíni címtárban. Ez a hiba akkor fordulhat elő, amikor a felhasználó lett törölve a helyszíni, de nem a felhőben. Ez a hiba akkor is előfordulhat, ha a szinkronizálási probléma van. Ellenőrizze a szinkronizálási naplókat és az utolsó néhány szinkronizálás futtatása részletei további információt.|
| 33003| ADMutliMatchError| A jelszó-visszaállítási vagy változáskérést származik a felhőben, a felhőbeli horgony megadva a telepítés során az Azure AD Connect használatával határozza meg a kérelmet vissza összekapcsolása a felhasználó a helyszíni környezetben. Az esemény azt jelzi, hogy két felhasználót találtunk a helyszíni címtárban felhőbeli horgony ugyanezzel az attribútummal való. Ellenőrizze a szinkronizálási naplókat és az utolsó néhány szinkronizálás futtatása részletei további információt.|
| 33004| ADPermissionsError| Az esemény azt jelzi, hogy az Active Directory Management Agent (ADMA) szolgáltatásfiók nem rendelkezik a megfelelő engedélyeket az új jelszót állíthat be az adott fiókon. Győződjön meg arról, hogy az ADMA-fiók, a felhasználó erdőjében alaphelyzetbe állt és az erdő összes objektum jelszó engedélyeinek módosítása. Az engedélyek beállításával kapcsolatos további információkért lásd: 4. lépés: a megfelelő Active Directory-engedélyek beállítása.|
| 33005| ADUserAccountDisabled| Az esemény azt jelzi, hogy azt megpróbálta alaphelyzetbe állítására és módosítására, amely a letiltott helyi rendszer fiók jelszavát. Engedélyezze a fiókot, és próbálja megismételni a műveletet.|
| 33006| ADUserAccountLockedOut| Az esemény azt jelzi, hogy mi megpróbálta alaphelyzetbe állítása, vagy módosítsa a jelszót, amely a helyszínen lett zárolva. Próbálkozhassanak akkor fordulhat elő, amikor egy felhasználó próbált meg módosítása vagy jelszó művelete rövid időn belül túl sokszor alaphelyzetbe állítása. A fiók feloldása, és próbálja megismételni a műveletet.|
| 33007| ADUserIncorrectPassword| Az esemény azt jelzi, hogy a felhasználó által megadott aktuális helytelen jelszót, amikor a művelet végrehajtása a jelszó módosítása. Adja meg a megfelelő jelenlegi jelszavát, és próbálkozzon újra.|
| 33008| ADPasswordPolicyError| Ezt az eseményt akkor fordul elő, ha a jelszóvisszaíró szolgáltatás próbál állítson be jelszót a helyi címtárban, amely nem felel meg a jelszó kora, előzmények, bonyolultsága vagy a tartomány szűrési követelményeinek. <br> <br> Ha a jelszó minimális kora és nemrég módosította a jelszó az, hogy eltelt időszakot, Ön nem tudja módosítani a jelszót újra, addig a megadott kora a tartományban. Tesztelési célokra, a minimális korú 0-ra kell állítani. <br> <br> Ha jelszó előzményekre vonatkozó követelményeknek engedélyezve van, akkor ki kell választania egy jelszót, amelyet nem használtak az elmúlt *N* időpontok, hol *N* a jelszó-előzmények beállítása. Ha kiválaszt egy jelszót, amely használatban van az utolsó *N* időkorlátja, majd, egy hibaüzenet jelenik meg ebben az esetben. Tesztelési célokra, a jelszóelőzmények 0-ra kell állítani. <br> <br> Ha jelszó-összetettségi követelményeknek, ezek mindegyike kényszerítettek, amikor a felhasználó megpróbál módosítása vagy a jelszó alaphelyzetbe állítása. <br> <br> Ha a jelszószűrők engedélyezve van, és a felhasználó kiválaszt egy jelszót, amely nem felel meg a szűrési feltételeket, majd az alaphelyzetbe állítás vagy módosítási művelet meghiúsul.|
| 33009| ADConfigurationError| Az esemény azt jelzi, hogy a helyszíni címtár és az Active Directory konfigurációs hiba miatt biztonsági jelszó írása közben hiba történt. Ellenőrizze az Azure AD Connect gép alkalmazások eseménynaplójában keresse meg, amelyen hiba történt. További információ a ADSync szolgáltatás üzeneteit.|

## <a name="troubleshoot-password-writeback-connectivity"></a>A jelszóvisszaíró csatlakoztatási problémáinak hibaelhárítása

Ha problémákat tapasztal az Azure AD Connect a jelszó-visszaírás összetevővel szolgáltatáskiesések, az alábbiakban néhány gyors lépésben, a probléma megoldása érdekében:

* [Erősítse meg a hálózati kapcsolat](#confirm-network-connectivity)
* [Az Azure AD Connect szinkronizálási szolgáltatás újraindítása](#restart-the-azure-ad-connect-sync-service)
* [Tiltsa le majd engedélyezze újra a jelszót a jelszóvisszaíró szolgáltatás](#disable-and-re-enable-the-password-writeback-feature)
* [Telepítse az Azure AD Connect legújabb kiadását](#install-the-latest-azure-ad-connect-release)
* [A jelszóvisszaíró hibaelhárítása](#troubleshoot-password-writeback)

Általánosságban véve a szolgáltatás helyreállításához a leggyorsabb módon, azt javasoljuk, hogy a korábban tárgyalt sorrendben hajtsa végre ezeket a lépéseket.

### <a name="confirm-network-connectivity"></a>Erősítse meg a hálózati kapcsolat

A leggyakoribb rendszerkritikus meghibásodási pontot, hogy a tűzfal, és vagy proxyhoz és üresjárati időkorlát nem megfelelően vannak konfigurálva. 

Az Azure AD Connect verziója 1.1.443.0 és újabb, akkor kimenő HTTPS hozzáféréssel kell a következőket:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

Részletesebben követheti nyomon, tekintse meg a frissített listájának [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) minden szerdán frissítve, és a következő hétfőn hatályba.

További információkért tekintse át a kapcsolat előfeltételeket a [az Azure AD Connect előfeltételei](./../connect/active-directory-aadconnect-prerequisites.md) cikk.



### <a name="restart-the-azure-ad-connect-sync-service"></a>Az Azure AD Connect szinkronizálási szolgáltatás újraindítása

Csatlakozási problémák vagy egyéb átmeneti problémák a szolgáltatással elhárításához indítsa újra az Azure AD Connect szinkronizálási szolgáltatás:

   1. Válassza ki a rendszergazdák **Start** Azure AD Connectet futtató kiszolgálón.
   2. Adjon meg **services.msc** a keresőmezőbe, és válassza a **Enter**.
   3. Keresse meg a **a Microsoft Azure AD Sync** bejegyzés.
   4. Válassza ki a kattintson a jobb gombbal a szolgáltatás bejegyzés **indítsa újra a**, és ezután Várjon, amíg a művelet befejeződik.

   ![Az Azure AD Sync szolgáltatás újraindítása][Service restart]

Ezeket a lépéseket újra létrehozza a kapcsolatot a felhőalapú szolgáltatással, és hárítsa el a megakadás, léptek fel. Az ADSync szolgáltatás újraindítása nem oldja meg a probléma, azt javasoljuk, hogy megpróbálja tiltsa le, majd engedélyezze újra a jelszót a jelszóvisszaíró szolgáltatás.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Tiltsa le majd engedélyezze újra a jelszót a jelszóvisszaíró szolgáltatás

A kapcsolati problémák elhárításához, tiltsa le, és majd engedélyezze újra a jelszót a jelszóvisszaíró szolgáltatás:

   1. A rendszergazdák Azure AD Connect konfigurálása varázsló megnyitásához.
   2. A **az Azure AD Connect**, adja meg az Azure AD globális rendszergazdai hitelesítő adatait.
   3. A **csatlakozhat az AD DS**, adja meg az AD tartományi szolgáltatások rendszergazdai hitelesítő adatait.
   4. A **felhasználók egyedi azonosítása**, jelölje be a **tovább** gombra.
   5. A **választható funkciók**, törölje a **jelszóvisszaíró** jelölőnégyzetet.
   6. Válassza ki **tovább** keresztül a fennmaradó párbeszédpaneljeivel módosítása semmit, amíg nem kap nélkül a **konfigurálásra kész** lapot.
   7. Ügyeljen arra, hogy a **lapjának konfigurálása kész** jeleníti meg a **jelszóvisszaíró** lehetőséget igény **le van tiltva** és kattintson a zöld **konfigurálása** gombra a módosítások véglegesítéséhez.
   8. A **befejezett**, törölje a **szinkronizálás most** lehetőséget, majd válassza ki **Befejezés** zárja be a varázslót.
   9. Nyissa meg újra az Azure AD Connect konfigurációs varázsló.
   10. Ismételje meg a 2-8, azzal a különbséggel győződjön meg arról, hogy válassza ki a **jelszóvisszaíró** beállítást a **választható funkciók** lapon engedélyezheti újra a szolgáltatást.

Ezeket a lépéseket újra létrehozza a kapcsolat a felhőszolgáltatással, és hárítsa el a megakadás, léptek fel.

Ha letiltása és ismételt engedélyezése a jelszót a jelszóvisszaíró szolgáltatás nem oldja meg a problémát, javasoljuk, hogy az Azure AD Connect újratelepítése.

### <a name="install-the-latest-azure-ad-connect-release"></a>Telepítse az Azure AD Connect legújabb kiadását

Az Azure AD Connect újratelepítése megoldhatja a konfiguráció és a problémák léptek fel a cloud services és a helyi Active Directory-környezet között.

Azt javasoljuk, hogy ez a lépés végrehajtása csak azután kísérli meg a korábban bemutatott két lépést.

> [!WARNING]
> Ha testre szabott-a-beépített szinkronizálási szabályok *biztonsági Mentésükhöz frissítés folytatása előtt, és ezt követően manuálisan telepítse újra azokat után végzett.*

   1. Az Azure AD Connect legújabb verzióját töltse le a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
   2. Már telepítette az Azure AD Connect, mert helyben frissíthet az Azure AD Connect telepítés frissítése a legújabb verzióra van szükség.
   3. A letöltött csomagot, és kövesse a képernyőn megjelenő utasításokat az Azure AD Connect gép frissíteni.

Az előző lépésekben kell újra létrehozza a kapcsolat a felhőszolgáltatással való, és Ön tapasztalt megakadás megoldásához.

Ha az Azure AD Connect-kiszolgáló legújabb verziójának telepítése nem oldja meg a problémát, ajánlott letiltásával és újbóli engedélyezése a jelszóvisszaíró utolsó lépésben a legfrissebb kiadás telepítése után próbálkozzon.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Ellenőrizze, hogy az Azure AD Connect a jelszóvisszaírás szükséges engedéllyel rendelkezik-e

Az Azure AD Connectnek szüksége van az Active Directory **jelszó alaphelyzetbe állítása** engedéllyel a jelszóvisszaíró végrehajtásához. Ismerje meg, ha az Azure AD Connect egy adott a helyszíni Active Directory felhasználói fiók szükséges engedéllyel rendelkezik, használhatja a hatályos engedélyekhez Windows-szolgáltatás:

   1. Jelentkezzen be az Azure AD Connect-kiszolgáló, és indítsa el a **Synchronization Service Managert** kiválasztásával **Start** > **szinkronizálási szolgáltatás**.
   2. Alatt a **összekötők** lapra, válassza ki a helyszíni **Active Directory Domain Services** összekötő, és válassza ki **tulajdonságok**.  

   ![Hatályos engedélyek – 2. lépés](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. Az előugró ablakban válassza ki a **csatlakozhat az Active Directory-erdő** , és jegyezze fel a **felhasználónév** tulajdonság. Ez a tulajdonság az Azure AD Connect címtár-szinkronizálás végrehajtásához használt AD DS-fiókot. Az Azure AD Connect a jelszóvisszaírás végrehajtásához az AD DS-fiókot kell alaphelyzetbe állítása jelszó engedéllyel.  
   
   ![Hatályos engedélyek – 3. lépés](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. Jelentkezzen be egy helyszíni tartományvezérlővel, és indítsa el a **Active Directory – felhasználók és számítógépek** alkalmazás.
   5. Válassza ki **nézet** , és győződjön meg arról, hogy a **speciális funkciók** beállítás engedélyezve van.  
   
   ![Hatályos engedélyek – 5. lépés](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. Keresse meg az Active Directory felhasználói fiók ellenőrizni szeretné. Kattintson a jobb gombbal a fiók nevét, és válassza ki **tulajdonságok**.  
   
   ![Hatályos engedélyek – 6. lépés](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. A felugró ablakban nyissa meg a **biztonsági** lapot, és válasszon **speciális**.  
   
   ![Hatályos engedélyek – 7. lépés](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. Az a **rendszergazda speciális biztonsági beállításai** előugró ablakban, keresse fel a **hatályos hozzáférés** fülre.
   9. Válassza ki **válasszon ki egy felhasználót**, válassza ki az AD DS-fiókot az Azure AD által használt Connect (lásd a 3. lépés), és válassza ki **hatályos hozzáférés megtekintése**.  
   
   ![Hatályos engedélyek – 9. lépés](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. Görgessen lefelé, és keressen **jelszó alaphelyzetbe állítása**. Ha a bejegyzés jelölőnégyzet be van jelölve, az Active Directory tartományi szolgáltatások fióknak legyen jogosultsága a kijelölt Active Directory felhasználói fiók a jelszó alaphelyzetbe állítása.  
   
   ![Hatályos engedélyek – 10. lépés](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-fórumok

Ha az Azure AD általános kérdésem van, és az önkiszolgáló jelszó-visszaállítás, megkérheti a Közösség segítségért a a [az Azure AD-fórumokon](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartozik, mérnökök, termék kezelők, MVP-k és megosztana informatikai szakemberek számára.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha nem találja a választ egy problémára, a támogatási csapatuk mindig elérhetők, további segítséget.

Megfelelően, hogy tegye fel meg minél részletesebb információt a lehető eset megnyitásakor. Ezek az adatok a következők:

* **Általános hibaleírást**: Mi az a hiba? Mi volt, volt megfigyelhető viselkedés? Hogyan tudjuk reprodukálni a hibát? A lehető adja meg a lehető legtöbb részletet.
* **Oldal**: mely lap is, ha észrevette, hogy a hiba a? Például az URL-címet, ha tudja, és a egy lapjának képernyőképe, a.
* **Támogatási kód**: Mi volt a támogatási kódot, amely a jött létre, amikor a felhasználó látott a hiba?
    * Keresse meg a kódot, reprodukálni a hibát, majd jelölje ki a **támogatási kód** hivatkozásra a képernyő alján, és a támogatási szakértőhöz a globálisan egyedi Azonosítót, az eredmények küldése.

    ![Keresse meg a képernyő alján található támogatási kódot][Support code]

    * Ha egy lap alján támogatás nélkül használ, válassza ki az F12 billentyűt, és keresse meg a biztonsági azonosítója és a CID, és két eredmények küldése a támogatási szakértőhöz.
* **Dátum, idő és időzóna**: a pontos dátum és idő *az időzónával együtt* , amely a hiba történt.
* **Felhasználói azonosító**: ki lett-e a felhasználó, akik láttak a hiba? Például *user@contoso.com*.
    * Az összevont felhasználó?
    * Az átmenő hitelesítés felhasználó?
    * A jelszó-kivonat-szinkronizált felhasználó van szó?
    * Az egy csak felhőalapú felhasználói?
* **Licencelési**: a felhasználó nem rendelkezik egy Azure AD prémium vagy alapszintű Azure AD-licenccel?
* **Alkalmazás eseménynaplója**: jelszóvisszaíró használ, és a hiba: a helyszíni infrastruktúrában, ha az alkalmazások eseménynaplójában, az Azure AD Connect-kiszolgáló a tömörített másolatát tartalmazza.



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Az Azure AD Sync szolgáltatás újraindítása"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Az ablak alsó jobb támogatási kódot"

## <a name="next-steps"></a>További lépések

A következő cikkek nyújtanak jelszó-visszaállítás, az Azure AD-n keresztül további információt:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
