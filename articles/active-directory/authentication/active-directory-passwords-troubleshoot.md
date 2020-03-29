---
title: Önkiszolgáló jelszó-visszaállítás – Az Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-alaphelyzetbe állításának hibaelhárítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22c0cc922e021edc37dfbb2d89fdd20c77b2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848765"
---
# <a name="troubleshoot-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének hibaelhárítása

Problémája van az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítással (SSPR) ? Az alábbi információk segíthetnek abban, hogy a dolgok újra működjenek.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>A felhasználó által észlelt önkiszolgáló jelszó-visszaállítási hibák elhárítása

| Hiba | Részletek | Technikai részletek |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Sajnáljuk, jelenleg nem állíthatja vissza a jelszavát, mert a rendszergazda letiltotta a jelszó-visszaállítást a szervezetszámára. A helyzet megoldása érdekében nem tehet további lépéseket. Kérjük, forduljon a rendszergazdához, és kérje meg őket, hogy engedélyezzék ezt a funkciót. További információ: [Súgó, Elfelejtettem az Azure AD-jelszavamat.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions) | SSPR_0009: Azt észleltük, hogy a rendszergazda nem engedélyezte a jelszó-visszaállítást. Lépjen kapcsolatba a rendszergazdával, és kérje meg őket, hogy engedélyezzék a jelszó-visszaállítást a szervezetszámára. |
| WritebackNotEnabled = 10 |Sajnáljuk, jelenleg nem állíthatja vissza a jelszavát, mert a rendszergazda nem engedélyezte a szükséges szolgáltatást a szervezet számára. A helyzet megoldása érdekében nem tehet további lépéseket. Lépjen kapcsolatba a rendszergazdával, és kérje meg őket, hogy ellenőrizzék a szervezet konfigurációját. A szükséges szolgáltatásról a [Jelszó-visszaírás konfigurálása](howto-sspr-writeback.md)című témakörben olvashat bővebben. | SSPR_0010: Azt észleltük, hogy a jelszó-visszaírás nincs engedélyezve. Kérjük, forduljon a rendszergazdához, és kérje meg őket, hogy engedélyezzék a jelszó-visszaírást. |
| SsprNotEnabledInUserPolicy = 11 | Sajnáljuk, jelenleg nem állíthatja vissza a jelszavát, mert a rendszergazda nem konfigurálta a jelszó-visszaállítást a szervezethez. A helyzet megoldása érdekében nem tehet további lépéseket. Lépjen kapcsolatba a rendszergazdával, és kérje meg őket a jelszó-visszaállítás konfigurálása. A jelszó-alaphelyzetbe állítás konfigurációjáról a [Rövid útmutató: Azure AD önkiszolgáló jelszó-visszaállítás című](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)témakörben olvashat bővebben. | SSPR_0011: A szervezet nem adott meg jelszó-visszaállítási házirendet. Kérjük, forduljon a rendszergazdához, és kérje meg őket, hogy határozzanak meg egy jelszó-visszaállítási szabályzatot. |
| UserNotLicensed = 12 | Sajnáljuk, jelenleg nem állíthatja vissza a jelszavát, mert hiányoznak a szükséges licencek a szervezetből. A helyzet megoldása érdekében nem tehet további lépéseket. Kérjük, forduljon a rendszergazdához, és kérje meg őket, hogy ellenőrizzék a licenc-hozzárendelést. A licencelésről az [Azure AD önkiszolgáló jelszó-visszaállításlicencelési követelményei](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing)című témakörben olvashat bővebben. | SSPR_0012: A szervezet nem rendelkezik a jelszó-visszaállítás végrehajtásához szükséges licencekkel. Kérjük, forduljon a rendszergazdához, és kérje meg őket, hogy vizsgálják felül a licenc-hozzárendeléseket. |
| UserNotMemberOfScopedAccessGroup = 13 | Sajnáljuk, jelenleg nem állíthatja vissza a jelszavát, mert a rendszergazda nem állította be a fiókját a jelszó-visszaállítás használatára. A helyzet megoldása érdekében nem tehet további lépéseket. Kérjük, forduljon a rendszergazdához, és kérje meg őket, hogy konfigurálják a fiók jelszavát a jelszó-visszaállításhoz. Ha többet szeretne tudni a fiókbeállításokról a jelszó-visszaállításhoz, olvassa el [a jelszó-visszaállítás bevezetése a felhasználók számára](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices)című témakört. | SSPR_0013: Nem tagja a jelszó-visszaállításra engedélyezett csoportnak. Vedd fel a kapcsolatot a rendszergazdával, és kérj a csoporthoz való felvételért. |
| A felhasználónem megfelelően konfigurálva = 14 | Sajnáljuk, jelenleg nem állíthatja vissza a jelszavát, mert hiányoznak a szükséges adatok a fiókjából. A helyzet megoldása érdekében nem tehet további lépéseket. Kérjük, vegye fel önnel a kapcsolatot admin, és kérje meg őket, hogy állítsa vissza a jelszót az Ön számára. Miután ismét hozzáfért a fiókjához, regisztrálnia kell a szükséges információkat. Az adatok regisztrálásához kövesse az [önkiszolgáló jelszó-visszaállítási regisztrálása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) című cikk lépéseit. | SSPR_0014: További biztonsági adatokra van szükség a jelszó alaphelyzetbe állításához. A folytatáshoz vedd fel a kapcsolatot a rendszergazdával, és kérd meg, hogy állítsa vissza a jelszavadat. Miután hozzáfért a fiókjához, további biztonsági https://aka.ms/ssprsetupadatokat regisztrálhat a következő helyen: . A rendszergazda további biztonsági adatokat adhat hozzá a fiókjához a [Jelszó-visszaállításhoz szükséges hitelesítési adatok beállítása és olvasása](howto-sspr-authenticationdata.md)című részben leírt lépések végrehajtásával. |
| OnPremisesAdminActionRequired = 29 | Sajnáljuk, jelenleg nem tudjuk visszaállítani a jelszavát, mert probléma van a szervezet jelszó-visszaállítási konfigurációjával. A helyzet megoldása érdekében nem tehet további lépéseket. Kérjük, forduljon az admin, és kérje meg őket, hogy vizsgálja meg. A lehetséges problémáról a [Jelszó-visszaírás – hibaelhárítás című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)olvashat bővebben. | SSPR_0029: A helyszíni konfiguráció hibája miatt nem tudjuk alaphelyzetbe állítani a jelszavát. Kérjük, forduljon az admin, és kérje meg őket, hogy vizsgálja meg. |
| OnPremisesConnectivityError = 30 | Sajnáljuk, jelenleg nem tudjuk visszaállítani a jelszavát a szervezethez való kapcsolódási problémák miatt. Jelenleg nincs teendő, de a probléma megoldódhat, ha később újra megpróbálja. Ha a probléma továbbra is fennáll, forduljon az adminisztrátorhoz, és kérje meg, hogy vizsgálja meg. A kapcsolódási problémákról a [Jelszó-visszaírási kapcsolat elhárítása című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity) | SSPR_0030: A helyszíni környezettel való rossz kapcsolat miatt nem tudjuk alaphelyzetbe állítani a jelszavát. Vegye fel a kapcsolatot az adminisztrátorsal, és kérje meg őket, hogy vizsgálják ki.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>A jelszó-visszaállítási konfiguráció elhárítása az Azure Portalon

| Hiba | Megoldás |
| --- | --- |
| Nem látom a **jelszó-visszaállítás** szakasz az Azure AD az Azure Portalon. | Ez akkor fordulhat elő, ha nem rendelkezik a műveletet végző rendszergazdához rendelt Azure AD-licenccel. <br> <br> Rendeljen licencet a kérdéses rendszergazdai fiókhoz. A [Licencek hozzárendelése, ellenőrzése és megoldása](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) című cikkben ismertetett lépéseket követheti.|
| Nem látok egy adott konfigurációs lehetőséget. | A felhasználói felület számos eleme el van rejtve, amíg szükség nem lesz rájuk. Próbálja meg engedélyezni az összes megtekinteni kívánt beállítást. |
| Nem látom a **helyszíni integrációlapot.** | Ez a beállítás csak akkor válik láthatóvá, ha letöltötte az Azure AD Connectet, és konfigurálta a jelszó-visszaírást. További információ: [Az Azure AD Connect használatának első lépései a gyorsbeállítások használatával](../hybrid/how-to-connect-install-express.md)című témakörben olvashat. |

## <a name="troubleshoot-password-reset-reporting"></a>Jelszó-visszaállítási jelentés – problémamegoldás

| Hiba | Megoldás |
| --- | --- |
| Nem látok jelszókezelési tevékenységtípusokat az **Önkiszolgáló jelszókezelés** naplózási eseménykategóriában. | Ez akkor fordulhat elő, ha nem rendelkezik a műveletet végző rendszergazdához rendelt Azure AD-licenccel. <br> <br> A problémát úgy oldhatja meg, hogy licencet rendel a kérdéses rendszergazdai fiókhoz. Kövesse a [Licencek hozzárendelésével, ellenőrzésével és megoldásával kapcsolatos problémákat](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) című cikklépéseit. |
| A felhasználói regisztrációk többször is megjelennek. | Jelenleg, amikor egy felhasználó regisztrál, minden egyes, külön eseményként regisztrált adatot naplózunk. <br> <br> Ha összesíteni szeretné ezeket az adatokat, és nagyobb rugalmasságot szeretne a megtekintés módjában, letöltheti a jelentést, és megnyithatja az adatokat kimutatástáblázatként az Excelben.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>A jelszó-visszaállításregisztrációs portál hibáinak elhárítása

| Hiba | Megoldás |
| --- | --- |
| A könyvtár nincs engedélyezve a jelszó-visszaállításhoz. **A rendszergazda nem engedélyezte a szolgáltatás használatát.** | Váltson az **Önkiszolgáló jelszó-visszaállítási engedélyezve lévő** jelzőre **a Selected** vagy **az All** értékre, majd válassza a **Mentés lehetőséget.** |
| A felhasználó nem rendelkezik Hozzárendelt Azure AD-licenccel. **A rendszergazda nem engedélyezte a szolgáltatás használatát.** | Ez akkor fordulhat elő, ha nem rendelkezik a műveletet végző rendszergazdához rendelt Azure AD-licenccel. <br> <br> A problémát úgy oldhatja meg, hogy licencet rendel a kérdéses rendszergazdai fiókhoz. Kövesse a [Licencek hozzárendelésével, ellenőrzésével és megoldásával kapcsolatos problémákat](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) című cikklépéseit.|
| Hiba történt a kérelem feldolgozása kor. | Ezt számos probléma okozhatja, de általában ezt a hibát szolgáltatáskimaradás vagy konfigurációs probléma okozza. Ha ezt a hibát látja, és ez hatással van a vállalkozására, további segítségért forduljon a Microsoft támogatási szolgálatához. |

## <a name="troubleshoot-the-password-reset-portal"></a>A jelszó-visszaállítási portál hibáinak elhárítása

| Hiba | Megoldás |
| --- | --- |
| A könyvtár nincs engedélyezve a jelszó-visszaállításhoz. | Váltson az **Önkiszolgáló jelszó-visszaállítási engedélyezve lévő** jelzőre **a Selected** vagy **az All** értékre, majd válassza a **Mentés lehetőséget.** |
| A felhasználó nem rendelkezik Hozzárendelt Azure AD-licenccel. | Ez akkor fordulhat elő, ha nem rendelkezik a műveletet végző rendszergazdához rendelt Azure AD-licenccel. <br> <br> A problémát akkor oldhatja meg, ha licencet rendel a kérdéses rendszergazdai fiókhoz. Kövesse a [Licencek hozzárendelésével, ellenőrzésével és megoldásával kapcsolatos problémákat](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) című cikklépéseit. |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz, de a felhasználó nak hiányoznak vagy hibásan formázott hitelesítési adatai vannak. | A folytatás előtt győződjön meg arról, hogy a felhasználó megfelelően alakította ki a névjegyadatokat a könyvtárban lévő fájlban. További információ: [Az Azure AD önkiszolgáló jelszó-visszaállítás által használt adatok.](howto-sspr-authenticationdata.md) |
| A könyvtár engedélyezve van a jelszó alaphelyzetbe állításához, de a felhasználó csak egy névjegyadattal rendelkezik a fájlban, ha a házirend két ellenőrzési módszert igényel. | A folytatás előtt győződjön meg arról, hogy a felhasználó nak legalább két megfelelően konfigurált kapcsolati módszere van. Erre példa egy mobiltelefonszám *és* egy irodai telefonszám is. |
| A címtár engedélyezve van a jelszó alaphelyzetbe állításához, és a felhasználó megfelelően van konfigurálva, de a felhasználóval nem lehet kapcsolatba lépni. | Ez lehet egy ideiglenes szolgáltatáshiba eredménye, vagy ha helytelen kapcsolati adatok vannak, amelyeket nem tudunk megfelelően észlelni. <br> <br> Ha a felhasználó 10 másodpercet vár, "próbálja újra" és "Lépjen kapcsolatba a rendszergazdával" hivatkozások jelennek meg. Ha a felhasználó a "próbálja újra" lehetőséget választja, újrapróbálkozik a hívással. Ha a felhasználó a "Forduljon a rendszergazdához" lehetőséget választja, akkor egy űrlape-mailt küld a rendszergazdának, amelyben jelszó-visszaállítást kér az adott felhasználói fiókhoz. |
| A felhasználó soha nem kapja meg a jelszó-visszaállítási SMS-t vagy telefonhívást. | Ez a címtárban lévő hibás telefonszám eredménye lehet. Győződjön meg róla, hogy a telefonszám "+ccc xxxyyyzzXXeeeee". <br> <br> A jelszó-visszaállítás nem támogatja a bővítményeket, még akkor sem, ha a címtárban ad meg egyet. A bővítményeket a hívás feladása előtt eltávolítjuk. Használjon egy bővítmény nélküli számot, vagy integrálja a bővítményt a privát fiókváltó (PBX) telefonszámába. |
| A felhasználó soha nem kapja meg a jelszó-visszaállítási e-mailt. | A probléma leggyakoribb oka az, hogy az üzenetet egy levélszemétszűrő elutasítja. Ellenőrizze az e-mail spam-, levélszemét- vagy törölt elemek mappáját. <br> <br> Győződjön meg arról is, hogy az üzenethez a megfelelő e-mail fiókot ellenőrzi. |
| Beállítottam egy jelszó-visszaállítási házirendet, de ha egy rendszergazdai fiók jelszó-visszaállítást használ, a rendszer nem alkalmazza ezt a házirendet. | A legmagasabb szintű biztonság biztosítása érdekében a Microsoft kezeli és szabályozza a rendszergazdajelszó-visszaállítási házirendet. |
| A felhasználó nem kísérelheti meg a jelszó-visszaállítás túl sokszor egy nap. | Automatikus szabályozási mechanizmust vezetünk be, amely megakadályozza, hogy a felhasználók rövid idő alatt túl sokszor próbálják meg visszaállítani a jelszavukat. A szabályozás akkor történik, ha: <br><ul><li>A felhasználó egy óra alatt ötször kísérli meg a telefonszám érvényesítését.</li><li>A felhasználó egy óra alatt ötször próbálja használni a biztonsági kérdések kapuját.</li><li>A felhasználó egy óra alatt ötször kísérli meg ugyanannak a felhasználói fióknak a jelszavát alaphelyzetbe állítani.</li></ul>A probléma megoldásához utasítsa a felhasználót, hogy az utolsó kísérlet után várjon 24 órát. A felhasználó ezután új jelszót állíthat be. |
| A felhasználó hibát lát a telefonszám érvényesítésekor. | Ez a hiba akkor fordul elő, ha a megadott telefonszám nem egyezik meg a fájlban szereplő telefonszámmal. Győződjön meg arról, hogy a felhasználó megadja a teljes telefonszámot, beleértve a körzetszámot és az országkódot, amikor telefonalapú módszert próbál használni a jelszó-visszaállításhoz. |
| Hiba történt a kérelem feldolgozása kor. | Ezt számos probléma okozhatja, de általában ezt a hibát szolgáltatáskimaradás vagy konfigurációs probléma okozza. Ha ezt a hibát látja, és ez hatással van a vállalkozására, további segítségért forduljon a Microsoft támogatási szolgálatához. |
| Helyszíni házirend megsértése | A jelszó nem felel meg az Active Directory helyszíni jelszóházirendjének. |
| A jelszó nem felel meg az intelligens házirendnek | A használt jelszó megjelenik a [tiltott jelszavak listájában,](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) és nem használható. |

## <a name="troubleshoot-password-writeback"></a>Jelszó-visszaírás – problémamegoldás

| Hiba | Megoldás |
| --- | --- |
| A jelszó-visszaállítási szolgáltatás nem indul el a helyszínen. A 6800-as hiba megjelenik az Azure AD Connect-gép alkalmazáseseménynaplójában. <br> <br> A bevezetés, az összevont, az átadó hitelesítés vagy a jelszókivonat-szinkronizált felhasználók nem tudják alaphelyzetbe állítani a jelszavukat. | Ha a jelszó-visszaírás engedélyezve van, a szinkronizálási motor meghívja a visszaíró függvénytárat a konfiguráció (bevezetés) végrehajtásához a felhőalapú bevezetési szolgáltatással való kommunikációval. A windows communication foundation (WCF) jelszó-visszaírási végpontjának indításakor előforduló hibák az eseménynaplóban hibákat eredményeznek az Azure AD Connect-gépen. <br> <br> Az Azure AD Sync (ADSync) szolgáltatás újraindítása során, ha a visszaírás konfigurálva volt, a WCF-végpont elindul. De ha a végpont indítása sikertelen, naplózni fogjuk a 6800-as eseményt, és hagyjuk, hogy a szinkronizálási szolgáltatás elinduljon. Az esemény jelenléte azt jelenti, hogy a jelszó-visszaírási végpont nem indult el. Eseménynapló adatait az esemény 6800, valamint a PasswordResetService összetevő által létrehozott eseménynapló-bejegyzések, jelzik, hogy miért nem tudja elindítani a végpontot. Tekintse át ezeket az eseménynapló-hibákat, és próbálja meg újraindítani az Azure AD Connectet, ha a jelszó-visszaírás továbbra sem működik. Ha a probléma továbbra is fennáll, próbálja meg letiltani, majd újra engedélyezni a jelszó-visszaírást.
| Amikor egy felhasználó megpróbálja alaphelyzetbe állítani egy jelszót, vagy feloldani egy fiókot, ha a jelszóvisszaírás engedélyezve van, a művelet sikertelen lesz. <br> <br> Emellett megjelenik egy esemény az Azure AD Connect eseménynaplójában, amely a következőket tartalmazza: "A szinkronizálási motor hr=800700CE, message=A fájlnév vagy kiterjesztés túl hosszú" hibaüzenetet adott vissza a feloldási művelet után. | Keresse meg az Azure AD Connect Active Directory-fiókját, és állítsa alaphelyzetbe a jelszót, hogy az legfeljebb 256 karaktert tartalmazzon. Ezután nyissa meg a **Szinkronizálási szolgáltatást** a **Start** menüből. Tallózással keresse meg az **Összekötők** és keresse meg az **Active Directory-összekötőt.** Jelölje ki, majd válassza **a Tulajdonságok lehetőséget.** Tallózással keresse meg a **Hitelesítő adatok lapot,** és írja be az új jelszót. A lap bezárásához **kattintson** az OK gombra. |
| Az Azure AD Connect telepítési folyamat ának utolsó lépésében megjelenik egy hiba, amely azt jelzi, hogy a jelszó-visszaírás nem konfigurálható. <br> <br> Az Azure AD Connect alkalmazás eseménynaplója tartalmazza a 32009-es hibát a "Hiba az auth token beszerzése" szöveggel. | Ez a hiba a következő két esetben fordul elő: <br><ul><li>Helytelen jelszót adott meg az Azure AD Connect telepítési folyamat elején megadott globális rendszergazdai fiókhoz.</li><li>Az Azure AD Connect telepítési folyamat elején megadott globális rendszergazdai fiókhoz egy összevont felhasználót próbált meg használni.</li></ul> A probléma megoldásához győződjön meg arról, hogy nem a telepítési folyamat elején megadott globális rendszergazda összevont fiókját használja. Győződjön meg arról is, hogy a megadott jelszó helyes. |
| Az Azure AD Connect gép eseménynaplója tartalmazza a 32002-es hibát, amelyet a PasswordResetService futtat. <br> <br> A hiba így szól: "Hiba a ServiceBus-hoz való csatlakozáskor. A jogkivonat-szolgáltató nem tudott biztonsági jogkivonatot biztosítani." | A helyszíni környezet nem tud csatlakozni az Azure Service Bus-végponthoz a felhőben. Ezt a hibát általában egy tűzfalszabály okozza, amely blokkolja a kimenő kapcsolatot egy adott porttal vagy webcímmel. További információ: [Kapcsolat előfeltételei.](../hybrid/how-to-connect-install-prerequisites.md) Miután frissítette ezeket a szabályokat, indítsa újra az Azure AD Connect-gépet, és a jelszó-visszaírásnak újra működnie kell. |
| Munka után egy ideig, összevont, átadó hitelesítés, vagy a jelszó-hash-szinkronizált felhasználók nem tudja visszaállítani a jelszavakat. | Néhány ritka esetben a jelszó-visszaíró szolgáltatás nem újraindítása, amikor az Azure AD Connect újraindult. Ezekben az esetekben először ellenőrizze, hogy a jelszó-visszaírás engedélyezve van-e a helyszínen. Ellenőrizheti az Azure AD Connect varázsló vagy a PowerShell használatával (lásd az előző HowTos szakasz). Ha úgy tűnik, hogy a szolgáltatás engedélyezve van, próbálja meg újra engedélyezni vagy letiltani a funkciót a felhasználói felületen vagy a PowerShellen keresztül. Ha ez nem működik, próbálja meg az Azure AD Connect teljes eltávolítását és újratelepítését. |
| Az összevont, átmenő hitelesítést vagy jelszókivonat-szinkronizált felhasználók, akik megpróbálják alaphelyzetbe állítani jelszavukat, hibaüzenetet látnak, miután megpróbálták elküldeni a jelszavukat. A hiba azt jelzi, hogy szolgáltatási probléma merült fel. <br ><br> A probléma mellett a jelszó-visszaállítási műveletek során előfordulhat, hogy egy hiba, hogy a felügyeleti ügynök megtagadták a hozzáférést a helyszíni eseménynaplókban. | Ha ezeket a hibákat látja az eseménynaplóban, ellenőrizze, hogy a varázslóban a konfiguráció időpontjában megadott Active Directory-kezelőügynök (ADMA) fiók rendelkezik-e a jelszó-visszaíráshoz szükséges engedélyekkel. <br> <br> Az engedély megadása után akár egy órát is igénybe vehet, amíg az engedélyek leszivárognak a `sdprop` tartományvezérlő (DC) háttérfeladatán keresztül. <br> <br> A jelszó-visszaállítás működéséhez az engedélyt annak a felhasználói objektumnak a biztonsági leírójára kell pecsételni, amelynek jelszavát alaphelyzetbe állítja. Amíg ez az engedély meg nem jelenik a felhasználói objektumon, a jelszó visszaállítása továbbra is sikertelen, ha a hozzáférés-megtagadási üzenet nem jelenik meg. |
| Összevont, átmenő hitelesítés vagy jelszókivonat-szinkronizált felhasználók, akik megpróbálják alaphelyzetbe állítani a jelszavukat, a jelszó elküldése után hibaüzenet jelenik meg. A hiba azt jelzi, hogy szolgáltatási probléma merült fel. <br> <br> A probléma mellett a jelszó-visszaállítási műveletek során előfordulhat, hogy az Azure AD Connect szolgáltatás eseménynaplóiban egy "Objektum nem található" hibaüzenet jelenik meg. | Ez a hiba általában azt jelzi, hogy a szinkronizálási motor nem találja sem a felhasználói objektumot az Azure AD-összekötő térben vagy a csatolt metaverzum (MV) vagy az Azure AD-összekötő térobjektum. <br> <br> A probléma elhárításához győződjön meg arról, hogy a felhasználó valóban szinkronizálva van a helyszíni Azure AD az Azure AD Connect aktuális példányán keresztül, és vizsgálja meg az objektumok állapotát az összekötő terekben és az MV-ben. Ellenőrizze, hogy az Active Directory tanúsítványszolgáltatás (AD CS) objektum a "Microsoft.InfromADUserAccountEnabled.xxx" szabály on keresztül csatlakozik-e az MV-objektumhoz.|
| Az összevont, átmenő hitelesítést vagy jelszókivonat-szinkronizált felhasználók, akik megpróbálják alaphelyzetbe állítani jelszavukat, hibaüzenetet látnak a jelszó elküldése után. A hiba azt jelzi, hogy szolgáltatási probléma merült fel. <br> <br> A probléma mellett a jelszó-visszaállítási műveletek során előfordulhat, hogy az Azure AD Connect szolgáltatás eseménynaplóiban olyan hiba jelenik meg, amely azt jelzi, hogy "Több találat található" hiba történt. | Ez azt jelzi, hogy a szinkronizálási motor azt észlelte, hogy az MV objektum egynél több AD CS-objektumhoz csatlakozik a "Microsoft.InfromADUserAccountEnabled.xxx" segítségével. Ez azt jelenti, hogy a felhasználó egynél több erdőben rendelkezik engedélyezett fiókkal. Ez a forgatókönyv nem támogatott a jelszó visszaírása. |
| A jelszóműveletek konfigurációs hibával nem hibásak. Az alkalmazás eseménynaplója tartalmazza az Azure AD Connect 6329-es hibáját a "0x8023061f" szöveggel (A művelet nem sikerült, mert a jelszó-szinkronizálás nincs engedélyezve ezen a felügyeleti ügynökön)". | Ez a hiba akkor fordul elő, ha az Azure AD Connect konfigurációja módosul, hogy új Active Directory erdőt adjon hozzá (vagy egy meglévő erdőt távolítson el és olvasson be), miután a jelszó-visszaírási szolgáltatás már engedélyezve van. A legutóbb hozzáadott erdők felhasználóinak jelszóműveletei sikertelenek. A probléma megoldásához tiltsa le, majd engedélyezze újra a jelszó-visszaírási szolgáltatást az erdő konfigurációs módosításainak befejezése után. |

## <a name="password-writeback-event-log-error-codes"></a>Jelszó-visszaírási eseménynapló hibakódok

A jelszó-visszaírással kapcsolatos problémák elhárításakor ajánlott az alkalmazás eseménynaplójának vizsgálata az Azure AD Connect-gépen. Ez az eseménynapló két fontos forrásból származó eseményeket tartalmaz a jelszó-visszaíráshoz. A PasswordResetService forrás a jelszó-visszaírás működésével kapcsolatos műveleteket és problémákat ismerteti. Az ADSync-forrás az Active Directory-környezetben a jelszavak beállításával kapcsolatos műveleteket és problémákat ismerteti.

### <a name="if-the-source-of-the-event-is-adsync"></a>Ha az esemény forrása ADSync

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 6329 | ÓVADÉK: MMS(4924) 0x80230619: "A korlátozás megakadályozza, hogy a jelszó a megadott jelszóra változzon." | Ez az esemény akkor fordul elő, amikor a jelszó-visszaíró szolgáltatás olyan jelszót próbál beállítani a helyi címtárban, amely nem felel meg a tartomány jelszókorának, előzményeinek, összetettségének vagy szűrési követelményeinek. <br> <br> Ha a jelszó minimális életkora van, és a közelmúltban módosította a jelszót az adott időszakban, addig nem módosíthatja újra a jelszót, amíg el nem éri a tartományban megadott kort. Tesztelési célokra az alsó korhatár0-ra kell esnie. <br> <br> Ha a jelszóelőzményekre vonatkozó követelmények engedélyezve vannak, akkor olyan jelszót kell választania, amelyet az elmúlt *N-időkben* nem használtak, ahol *N* a jelszóelőzmények beállítása. Ha olyan jelszót választ ki, amelyet az elmúlt *N-időkben* használtak, akkor ebben az esetben hibát lát. Tesztelési célokra a jelszóelőzményeket 0-ra kell állítani. <br> <br> Ha jelszó-összetettségi követelményekkel rendelkezik, a rendszer mindegyiket érvényesíti, amikor a felhasználó megpróbálja módosítani vagy alaphelyzetbe állítani a jelszót. <br> <br> Ha engedélyezve van a jelszószűrők, és a felhasználó olyan jelszót választ, amely nem felel meg a szűrési feltételeknek, akkor az alaphelyzetbe állításvagy módosítás sikertelen lesz. |
| 6329 | MMS(3040): admaexport.cpp(2837): A kiszolgáló nem tartalmazza az LDAP jelszóházirend-vezérlőjét. | Ez a probléma akkor fordul elő, ha LDAP_SERVER_POLICY_HINTS_OID vezérlő (1.2.840.113556.1.4.2066) nincs engedélyezve a tartományvezérlőkön. A jelszóvisszaírási szolgáltatás használatához engedélyeznie kell a vezérlőt. Ehhez a tartományvezérlőknek Windows Server 2008R2 vagy újabb rendszeren kell lenniük. |
| HR 8023042 | A szinkronizálási motor hr=80230402 hibát adott vissza, message=Objektum beszerzésére tett kísérlet nem sikerült, mert ugyanazzal a horgonysal duplikált bejegyzések vannak. | Ez a hiba akkor fordul elő, ha ugyanaz a felhasználói azonosító több tartományban is engedélyezve van. Ilyen például az, ha fiók- és erőforráserdőket szinkronizál, és minden erdőben ugyanaz a felhasználói azonosító jelen van és engedélyezve van. <br> <br> Ez a hiba akkor is előfordulhat, ha nem egyedi horgonyattribútumot használ, például aliast vagy upn-t, és két felhasználó ugyanazt a horgonyattribútumot használja. <br> <br> A probléma megoldásához győződjön meg arról, hogy a tartományokon belül nincsenek duplikált felhasználók, és hogy minden felhasználóhoz egyedi horgonyattribútumot használ. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Ha az esemény forrása PasswordResetService

| Kód | Név vagy üzenet | Leírás |
| --- | --- | --- |
| 31001 | PasswordResetStart | Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás jelszó-visszaállítási kérelmet észlelt egy összevont, áteresztő hitelesítésvagy a felhőből származó jelszókivonat-szinkronizált felhasználó számára. Ez az esemény az első esemény minden jelszó-visszaállítási visszaírási műveletben. |
| 31002 | PasswordResetSikeres | Ez az esemény azt jelzi, hogy egy felhasználó új jelszót választott ki a jelszó-visszaállítási művelet során. Megállapítottuk, hogy ez a jelszó megfelel a vállalati jelszókövetelményeinek. A jelszó sikeresen visszalett írva a helyi Active Directory-környezetbe. |
| 31003 | PasswordResetFail | Ez az esemény azt jelzi, hogy egy felhasználó kiválasztott egy jelszót, és a jelszó sikeresen megérkezett a helyszíni környezetbe. De amikor megpróbáltuk beállítani a jelszót a helyi Active Directory környezetben, hiba történt. Ennek a hibának több oka is lehet: <br><ul><li>A felhasználó jelszava nem felel meg a tartomány korra, előzményekre, összetettségre vagy szűrésre vonatkozó követelményeinek. A probléma megoldásához hozzon létre egy új jelszót.</li><li>Az ADMA szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel az új jelszó beállításához a kérdéses felhasználói fiókban.</li><li>A felhasználó fiókja egy védett csoportban található, például tartományban vagy vállalati felügyeleti csoportban, amely lehetővé teszi a jelszókészlet-műveleteket.</li></ul>|
| 31004 | OnboardingEventStart | Ez az esemény akkor fordul elő, ha engedélyezi a jelszó-visszaírást az Azure AD Connect használatával, és megkezdtük a szervezet bevezetést a jelszó-visszaírási webszolgáltatásba. |
| 31005 | BevezetésEseménysiker | Ez az esemény azt jelzi, hogy a bevezetési folyamat sikeres volt, és hogy a jelszó-visszaírási képesség készen áll a használatra. |
| 31006 | ChangePasswordStart | Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás jelszómódosítási kérelmet észlelt egy összevont, áteresztő hitelesítésvagy a felhőből származó jelszókivonat-szinkronizált felhasználó számára. Ez az esemény az első esemény minden jelszó-módosítási visszaírási műveletben. |
| 31007 | ChangePasswordSuccess (Módosítás: Sikeres ség) | Ez az esemény azt jelzi, hogy a felhasználó új jelszót választott ki a jelszómódosítási művelet során, megállapítottuk, hogy a jelszó megfelel a vállalati jelszókövetelményeinek, és hogy a jelszó sikeresen visszalett írva a helyi Active Directory-környezetbe. |
| 31008 | ChangePasswordFail | Ez az esemény azt jelzi, hogy egy felhasználó kiválasztott egy jelszót, és hogy a jelszó sikeresen megérkezett a helyszíni környezetbe, de amikor megpróbáltuk beállítani a jelszót a helyi Active Directory környezetben, hiba történt. Ennek a hibának több oka is lehet: <br><ul><li>A felhasználó jelszava nem felel meg a tartomány korra, előzményekre, összetettségre vagy szűrésre vonatkozó követelményeinek. A probléma megoldásához hozzon létre egy új jelszót.</li><li>Az ADMA szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel az új jelszó beállításához a kérdéses felhasználói fiókban.</li><li>A felhasználó fiókja egy védett csoportban található, például tartomány- vagy vállalati rendszergazdákban, amely lehetővé teszi a jelszókészlet-műveleteket.</li></ul> |
| 31009 | ResetUserPasswordByadminStart | A helyszíni szolgáltatás jelszó-visszaállítási kérelmet észlelt egy összevont, átmenő hitelesítésvagy jelszókivonat-szinkronizált felhasználó számára, amely a felhasználó nevében a rendszergazdától származik. Ez az esemény az első esemény minden jelszó-visszaállítási visszaírási műveletben, amelyet a rendszergazda kezdeményez. |
| 31010 | ResetUserPasswordByAdminSuccess | A rendszergazda új jelszót választott ki egy rendszergazda által kezdeményezett jelszó-visszaállítási művelet során. Megállapítottuk, hogy ez a jelszó megfelel a vállalati jelszókövetelményeinek. A jelszó sikeresen visszalett írva a helyi Active Directory-környezetbe. |
| 31011 | ResetUserPasswordByAdminFail | A rendszergazda egy felhasználó nevében választott ki egy jelszót. A jelszó sikeresen megérkezett a helyszíni környezetbe. De amikor megpróbáltuk beállítani a jelszót a helyi Active Directory környezetben, hiba történt. Ennek a hibának több oka is lehet: <br><ul><li>A felhasználó jelszava nem felel meg a tartomány korra, előzményekre, összetettségre vagy szűrésre vonatkozó követelményeinek. A probléma megoldásához próbálkozzon új jelszóval.</li><li>Az ADMA szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel az új jelszó beállításához a kérdéses felhasználói fiókban.</li><li>A felhasználó fiókja egy védett csoportban található, például tartomány- vagy vállalati rendszergazdákban, amely lehetővé teszi a jelszókészlet-műveleteket.</li></ul>  |
| 31012 | OffboardingEventStart | Ez az esemény akkor fordul elő, ha letiltja a jelszó-visszaírást az Azure AD Connect segítségével, és azt jelzi, hogy elkezdtük a szervezet kitiltását a jelszó-visszaíró webszolgáltatásba. |
| 31013| OffboardingEventSuccess| Ez az esemény azt jelzi, hogy a kiszervezési folyamat sikeres volt, és a jelszó-visszaírási képesség sikeresen le van tiltva. |
| 31014| OffboardingEventFail| Ez az esemény azt jelzi, hogy a kiszervezési folyamat nem sikerült. Ennek oka lehet a felhőbeli vagy a helyszíni rendszergazdai fiók a konfiguráció során megadott engedélyhiba. A hiba akkor is előfordulhat, ha a jelszó-visszaírás letiltásakor összevont globális felhőbeli rendszergazda használatával próbál meg használni. A probléma megoldásához ellenőrizze a rendszergazdai engedélyeket, és győződjön meg arról, hogy nem használ összevont fiókot a jelszó-visszaírási képesség konfigurálása során.|
| 31015| WriteBackServiceStarted| Ez az esemény azt jelzi, hogy a jelszó-visszaíró szolgáltatás sikeresen elindult. Készen áll a felhőből érkező jelszókezelési kérelmek fogadására.|
| 31016| WriteBackServiceLe| Ez az esemény azt jelzi, hogy a jelszó-visszaírási szolgáltatás leállt. A felhőből érkező jelszókezelési kérelmek sikertelenek lesznek.|
| 31017| AuthTokenSiker| Ez az esemény azt jelzi, hogy sikeresen lekértünk egy engedélyezési jogkivonatot az Azure AD Connect telepítése során megadott globális rendszergazda számára a bevezetési vagy bevezetési folyamat elindításához.|
| 31018| KeyPairCreationSuccess| Ez az esemény azt jelzi, hogy sikeresen létrehoztuk a jelszótitkosítási kulcsot. Ez a kulcs a felhőből a helyszíni környezetbe küldendő jelszavak titkosítására szolgál.|
| 32000| Ismeretlen hiba| Ez az esemény azt jelzi, hogy ismeretlen hiba történt egy jelszókezelési művelet során. További részletekért tekintse meg az esemény kivételszövegét. Ha problémái vannak, próbálja meg letiltani, majd újra engedélyezni a jelszó-visszaírást. Ha ez nem segít, mellékelje az eseménynapló egy példányát a támogató mérnöknek a megadott levéladatokkal együtt.|
| 32001| Szolgáltatáshiba| Ez az esemény azt jelzi, hogy hiba történt a felhőjelszó-visszaállítási szolgáltatáshoz való csatlakozáskor. Ez a hiba általában akkor fordul elő, ha a helyszíni szolgáltatás nem tudott csatlakozni a jelszó-visszaállítási webszolgáltatáshoz.|
| 32002| ServiceBusError| Ez az esemény azt jelzi, hogy hiba történt a bérlő Service Bus-példányához való csatlakozáskor. Ez akkor fordulhat elő, ha blokkolja a kimenő kapcsolatokat a helyszíni környezetben. Ellenőrizze a tűzfalat, hogy engedélyezi-e a kapcsolatokat a TCP 443-as és a tCP-n https://ssprdedicatedsbprodncu.servicebus.windows.netkeresztül, majd próbálkozzon újra. Ha továbbra is problémákat tapasztal, próbálja meg letiltani, majd újra engedélyezni a jelszóvisszaírást.|
| 32003| Bejövő érvényesítési hiba| Ez az esemény azt jelzi, hogy a webszolgáltatás API-nak átadott bemenet érvénytelen volt. Próbálkozzon újra a művelettel.|
| 32004| Visszafejtési hiba| Ez az esemény azt jelzi, hogy hiba történt a felhőből érkező jelszó visszafejtése. Ennek oka lehet a felhőszolgáltatás és a helyszíni környezet közötti visszafejtési kulcs eltérése. A probléma megoldásához tiltsa le, majd engedélyezze újra a jelszó-visszaírást a helyszíni környezetben.|
| 32005| ConfigurationError (Konfigurációhiba)| A bevezetés során bérlőspecifikus adatokat mentünk egy konfigurációs fájlba a helyszíni környezetben. Ez az esemény azt jelzi, hogy hiba történt a fájl mentésekor, vagy hogy a szolgáltatás indításakor hiba történt a fájl olvasásakor. A probléma megoldásához próbálja meg letiltani, majd újra engedélyezni a jelszóvisszaírást a konfigurációs fájl újraírásának kényszerítéséhez.|
| 32007| OnBoardingConfigUpdateError| A bevezetés során adatokat küldünk a felhőből a helyszíni jelszó-visszaállítási szolgáltatásnak. Ezeket az adatokat a rendszer ezután egy memórián belüli fájlba írja, mielőtt a szinkronizálási szolgáltatásba küldené őket, hogy biztonságosan tárolják a lemezen. Ez az esemény azt jelzi, hogy probléma van az adatok írásával vagy frissítésével a memóriában. A probléma megoldásához próbálja meg letiltani, majd újra engedélyezni a jelszóvisszaírást a konfigurációs fájl újraírásának kényszerítéséhez.|
| 32008| Érvényesítési hiba| Ez az esemény azt jelzi, hogy érvénytelen választ kaptunk a jelszó-visszaállítási webszolgáltatástól. A probléma megoldásához próbálja meg letiltani, majd újra engedélyezni a jelszóvisszaírást.|
| 32009| AuthTokenHiba| Ez az esemény azt jelzi, hogy nem sikerült lekérni egy engedélyezési jogkivonatot az Azure AD Connect telepítése során megadott globális rendszergazdai fiókhoz. Ezt a hibát a globális rendszergazdai fiókhoz megadott rossz felhasználónév vagy jelszó okozhatja. Ez a hiba akkor is előfordulhat, ha a megadott globális rendszergazdai fiók össze van vonva. A probléma megoldásához futtassa újra a konfigurációt a megfelelő felhasználónévvel és jelszóval, és győződjön meg arról, hogy a rendszergazda felügyelt (csak felhőalapú vagy jelszóval szinkronizált) fiók.|
| 32010| Titkosítási hiba| Ez az esemény azt jelzi, hogy hiba történt a jelszótitkosítási kulcs létrehozása vagy a felhőszolgáltatásból érkező jelszó visszafejtése. Ez a hiba valószínűleg a környezettel kapcsolatos problémára utal. A probléma megoldásáról az eseménynapló részleteiből megtudhatja, hogyan oldhatja meg a problémát. Megpróbálhatja letiltani, majd újra engedélyezni a jelszó-visszaírási szolgáltatást.|
| 32011| Bevezetési szolgáltatáshiba| Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás nem tudott megfelelően kommunikálni a jelszó-visszaállítási webszolgáltatással a bevezetési folyamat elindításához. Ez egy tűzfalszabály eredményeként fordulhat elő, vagy ha probléma van egy hitelesítési jogkivonat beszerzése a bérlő számára. A probléma megoldásához győződjön meg arról, hogy nem blokkolja a Kimenő kapcsolatokat a TCP 443 és a TCP 9350-9354 vagy a . https://ssprdedicatedsbprodncu.servicebus.windows.net Győződjön meg arról is, hogy az Azure AD-rendszergazdai fiók, amelyet a fedélzeti alkalmazáshoz használ, nem kap összevont.|
| 32013| OffBoardingError (Egyszeri beszállási hiba)| Ez az esemény azt jelzi, hogy a helyszíni szolgáltatás nem tudott megfelelően kommunikálni a jelszó-visszaállítási webszolgáltatással a kiszervezési folyamat elindításához. Ez egy tűzfalszabály eredményeként fordulhat elő, vagy ha probléma van egy engedélyezési jogkivonat beszerzése a bérlő számára. A probléma megoldásához győződjön meg arról, hogy nem blokkolja https://ssprdedicatedsbprodncu.servicebus.windows.neta kimenő kapcsolatok at 443 vagy a , és hogy az Azure Active Directory rendszergazdai fiók, amelyet a külső fedélzeti nem összevont.|
| 32014| ServiceBusFigyelmeztetés| Ez az esemény azt jelzi, hogy újra meg kellett próbálnunk újra csatlakozni a bérlő Service Bus-példányához. Normál körülmények között ez nem lehet aggodalomra ad okot, de ha ezt az eseményt többször is látja, fontolja meg a hálózati kapcsolat ellenőrzését a Service Bus-hoz, különösen akkor, ha nagy késleltetésű vagy alacsony sávszélességű kapcsolatról van szó.|
| 32015| ReportServiceHealthError (Jelentésszolgáltatás– HealthHiba| A jelszó-visszaírási szolgáltatás állapotának figyelése érdekében ötpercenként elküldjük a szívverésadatait a jelszó-visszaállítási webszolgáltatásunknak. Ez az esemény azt jelzi, hogy hiba történt, amikor ezt az állapotinformációt visszaküldte a felhőalapú webszolgáltatásnak. Ezek az állapotadatok nem tartalmaznak objektum-azonosításra alkalmas adatokat (OII) vagy személyazonosításra alkalmas adatokat (PII), és csak egy szívverés és alapvető szolgáltatásstatisztika, hogy a szolgáltatás állapotadatait a felhőben biztosíthassuk.|
| 33001| Adunknownerror (AdUnKnownerror)| Ez az esemény azt jelzi, hogy az Active Directory ismeretlen hibát adott vissza. További információkért tekintse meg az Azure AD Connect kiszolgáló eseménynaplójában az ADSync-forrásból származó eseményeket.|
| 33002| ADUserNotFounderror (ADUserNotFounderror)| Ez az esemény azt jelzi, hogy a jelszót alaphelyzetbe állítani vagy módosítani próbáló felhasználó nem található a helyszíni címtárban. Ez a hiba akkor fordulhat elő, ha a felhasználó tlettet a helyszínen, de nem a felhőben. Ez a hiba akkor is előfordulhat, ha probléma van a szinkronizálással. További információkért tekintse meg a szinkronizálási naplókat és az utolsó néhány szinkronizálási futtatási adatot.|
| 33003| ADMutliMatchHiba| Amikor egy jelszó-alaphelyzetbe állítási vagy módosítási kérelem származik a felhőből, az Azure AD Connect beállítási folyamata során megadott felhőhorgonyt használjuk annak meghatározásához, hogy a kérelmet hogyan kapcsolhatja vissza egy felhasználóhoz a helyszíni környezetben. Ez az esemény azt jelzi, hogy két felhasználót találtunk a helyszíni címtárban ugyanazzal a felhőhorgony attribútummal. További információkért tekintse meg a szinkronizálási naplókat és az utolsó néhány szinkronizálási futtatási adatot.|
| 33004| ADPermissionshiba| Ez az esemény azt jelzi, hogy az Active Directory felügyeleti ügynök (ADMA) szolgáltatásfiók nem rendelkezik a megfelelő engedélyekkel a kérdéses fiókhoz az új jelszó beállításához. Győződjön meg arról, hogy a felhasználó erdőjében lévő ADMA-fiók alaphelyzetbe állítja és módosítja a jelszóengedélyeket az erdő összes objektumára vonatkozóan. Az engedélyek beállításáról a 4. Ez a hiba akkor is előfordulhat, ha a felhasználó AdminCount attribútuma 1-re van állítva.|
| 33005| AduserAccountDisabled| Ez az esemény azt jelzi, hogy a helyszínen letiltott fiók jelszavát próbáltuk alaphelyzetbe állítani vagy módosítani. Engedélyezze a fiókot, és próbálkozzon újra a művelettel.|
| 33006| Aduseraccountlockedout| Ez az esemény azt jelzi, hogy megkíséreltük a helyszíni zárolt fiók jelszavának alaphelyzetbe állítását vagy módosítását. A zárolás oka akkor fordulhat elő, ha a felhasználó rövid idő alatt túl sokszor próbálkozott a jelszó módosításával vagy alaphelyzetbe állításával. Oldja fel a fiók zárolását, és próbálkozzon újra a művelettel.|
| 33007| Aduserincorrectpassword| Ez az esemény azt jelzi, hogy a felhasználó helytelen aktuális jelszót adott meg a jelszómódosítási művelet végrehajtásakor. Adja meg a helyes aktuális jelszót, majd próbálkozzon újra.|
| 33008| ADPasswordPolicyerror| Ez az esemény akkor fordul elő, amikor a jelszó-visszaíró szolgáltatás olyan jelszót próbál beállítani a helyi címtárban, amely nem felel meg a tartomány jelszókorának, előzményeinek, összetettségének vagy szűrési követelményeinek. <br> <br> Ha a jelszó minimális életkora van, és a közelmúltban módosította a jelszót az adott időszakban, addig nem módosíthatja újra a jelszót, amíg el nem éri a tartományban megadott kort. Tesztelési célokra az alsó korhatár0-ra kell esnie. <br> <br> Ha a jelszóelőzményekre vonatkozó követelmények engedélyezve vannak, akkor olyan jelszót kell választania, amelyet az elmúlt *N-időkben* nem használtak, ahol *N* a jelszóelőzmények beállítása. Ha olyan jelszót választ ki, amelyet az elmúlt *N-időkben* használtak, akkor ebben az esetben hibát lát. Tesztelési célokra a jelszóelőzményeket 0-ra kell állítani. <br> <br> Ha jelszó-összetettségi követelményekkel rendelkezik, a rendszer mindegyiket érvényesíti, amikor a felhasználó megpróbálja módosítani vagy alaphelyzetbe állítani a jelszót. <br> <br> Ha engedélyezve van a jelszószűrők, és a felhasználó olyan jelszót választ, amely nem felel meg a szűrési feltételeknek, akkor az alaphelyzetbe állításvagy módosítás sikertelen lesz.|
| 33009| ADConfigurationError (ADConfigurationError)| Ez az esemény azt jelzi, hogy az Active Directory konfigurációs problémája miatt probléma volt a jelszó visszaírása a helyszíni címtárba. Az ADSync szolgáltatástól származó üzeneteket az Azure AD Connect-gép alkalmazáseseménynaplójában talál további információt, hogy melyik hiba történt.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Jelszó-visszaírási kapcsolat – problémamegoldás

Ha az Azure AD Connect jelszó-visszaíró összetevőjével szolgáltatásmegszakításokat tapasztal, az alábbi gyors lépéseket teheti meg a probléma megoldásához:

* [Hálózati kapcsolat megerősítése](#confirm-network-connectivity)
* [Az Azure AD Connect Sync szolgáltatás újraindítása](#restart-the-azure-ad-connect-sync-service)
* [A jelszó-visszaírási szolgáltatás letiltása és újbóli engedélyezése](#disable-and-re-enable-the-password-writeback-feature)
* [Az Azure AD Connect legújabb kiadásának telepítése](#install-the-latest-azure-ad-connect-release)
* [Jelszó-visszaírás – problémamegoldás](#troubleshoot-password-writeback)

Általánosságban azt javasoljuk, hogy a szolgáltatás leggyorsabb helyreállításához hajtsa végre ezeket a lépéseket a korábban tárgyalt sorrendben.

### <a name="confirm-network-connectivity"></a>Hálózati kapcsolat megerősítése

A leggyakoribb hibapont az, hogy a tűzfal- és proxyportok és az üresjárati időmegmaradások helytelenül vannak konfigurálva. 

Az Azure AD Connect 1.1.443.0-s vagy újabb verzióihoz kimenő HTTPS-hozzáférésre van szüksége a következőkhöz:

* \*passwordreset.microsoftonline.com
* \*servicebus.windows.net

További részletességért hivatkozzon a Microsoft [Azure Datacenter adatközpont-adatközpontok minden](https://www.microsoft.com/download/details.aspx?id=41653) szerdán frissített és a következő hétfőn hatályba léptetett frissített listájára.

További információkért tekintse át a kapcsolódási előfeltételeket az [Azure AD Connect előfeltételei](../hybrid/how-to-connect-install-prerequisites.md) cikkben.

> [!NOTE]
> Az SSPR akkor is sikertelen lehet, ha a "Jelszó soha nem jár le" vagy a "Felhasználó nem tudja módosítani a jelszót" beállítások vannak konfigurálva a fiókban az AD DS helyszíni. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Az Azure AD Connect Sync szolgáltatás újraindítása

A szolgáltatással kapcsolatos kapcsolódási problémák vagy egyéb átmeneti problémák megoldásához indítsa újra az Azure AD Connect Sync szolgáltatást:

1. Rendszergazdaként válassza az **Indítás** az Azure AD Connect szolgáltatást futtató kiszolgálón lehetőséget.
1. Írja be a **services.msc kifejezést** a keresőmezőbe, és válassza az **Enter lehetőséget.**
1. Keresse meg a **Microsoft Azure AD Sync** bejegyzést.
1. Kattintson a jobb gombbal a szolgáltatásbejegyzésre, válassza **az Újraindítás**parancsot, majd várja meg, amíg a művelet befejeződik.

   ![Indítsa újra az Azure AD Sync szolgáltatást a felhasználói felület használatával][Service restart]

Ezek a lépések újra létre hozzák a kapcsolatot a felhőszolgáltatással, és feloldják az esetlegesen tapasztalt megszakításokat. Ha az ADSync szolgáltatás újraindítása nem oldja meg a problémát, javasoljuk, hogy próbálja meg letiltani, majd újra engedélyezni a jelszó-visszaírási szolgáltatást.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>A jelszó-visszaírási szolgáltatás letiltása és újbóli engedélyezése

A kapcsolódási problémák megoldásához tiltsa le, majd engedélyezze újra a jelszó-visszaíró szolgáltatást:

   1. Rendszergazdaként nyissa meg az Azure AD Connect konfigurációs varázslót.
   1. A **Csatlakozás az Azure AD-hez**alkalmazásban adja meg az Azure AD globális rendszergazdai hitelesítő adatait.
   1. A **Csatlakozás az AD DS szolgáltatáshoz**mezőbe írja be az AD tartományi szolgáltatások rendszergazdai hitelesítő adatait.
   1. A **Felhasználók egyedi azonosítása**mezőben válassza a **Tovább** gombot.
   1. A **Választható szolgáltatások párbeszédpanelen**törölje a jelet a **Jelszó visszaírása** jelölőnégyzetből.
   1. Válassza a **Tovább** gombot a fennmaradó párbeszédpaneleken anélkül, hogy bármit is módosítana, amíg el nem jut a **Konfigurálásra kész** lapra.
   1. Győződjön meg arról, hogy a **Konfigurálásra kész lapon** a **Jelszó visszaírási** lehetősége **le van tiltva,** majd a módosítások véglegesítéséhez válassza a zöld **Konfigurálás** gombot.
   1. A **Kész csoportban**törölje a jelet a **Szinkronizálás most** jelölőnégyzetből, majd a befejezés **gombra** a varázsló bezárásához.
   1. Nyissa meg újra az Azure AD Connect konfigurációs varázslót.
   1. Ismételje meg **a** 2-8. **Optional features**

Ezek a lépések újra létrehozzák a kapcsolatot a felhőszolgáltatásunkkal, és feloldják az esetlegesen tapasztalt megszakításokat.

Ha a jelszó-visszaírási funkció letiltása, majd újbóli engedélyezése nem oldja meg a problémát, javasoljuk, hogy telepítse újra az Azure AD Connectet.

### <a name="install-the-latest-azure-ad-connect-release"></a>Az Azure AD Connect legújabb kiadásának telepítése

Az Azure AD Connect újratelepítése megoldhatja a felhőszolgáltatások és a helyi Active Directory-környezet közötti konfigurációs és kapcsolódási problémákat.

Javasoljuk, hogy ezt a lépést csak a korábban leírt első két lépés megkísérlése után hajtsa végre.

> [!WARNING]
> Ha testre szabta a beépített szinkronizálási szabályokat, *a frissítés folytatása előtt, majd a telepítés után manuálisan telepítse újra őket.*

1. Töltse le az Azure AD Connect legújabb verzióját a [Microsoft letöltőközpontjából.](https://go.microsoft.com/fwlink/?LinkId=615771)
1. Mivel már telepítette az Azure AD Connectet, helyben kell frissítenie az Azure AD Connect telepítését a legújabb verzióra.
1. Hajtsa végre a letöltött csomagot, és kövesse a képernyőn megjelenő utasításokat az Azure AD Connect-gép frissítéséhez.

Az előző lépéseknek újra létre kell hozniuk a kapcsolatot a felhőszolgáltatásunkkal, és fel kell oldaniuk az esetleges enyelegős megszakításokat.

Ha az Azure AD Connect-kiszolgáló legújabb verziójának telepítése nem oldja meg a problémát, javasoljuk, hogy próbálja meg letiltani, majd a jelszó-visszaírást a legújabb kiadás telepítése után utolsó lépésként újra engedélyezni.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Annak ellenőrzése, hogy az Azure AD Connect rendelkezik-e a jelszó-visszaíráshoz szükséges engedéllyel

Az Azure AD Connect jelszó-visszaíráshoz az Active Directory **alaphelyzetbe állítási jelszóengedélyére** van szükség. Annak kiderítéséhez, hogy az Azure AD Connect rendelkezik-e a szükséges engedéllyel egy adott helyszíni Active Directory felhasználói fiókhoz, használhatja a Windows hatékony engedélyek szolgáltatását:

1. Jelentkezzen be az Azure AD Connect kiszolgálóra, és indítsa el a **Szinkronizálási szolgáltatáskezelőt** a**Szinkronizálási szolgáltatás** **indítása** > lehetőséget választva.
1. Az **Összekötők** lapon jelölje ki a helyszíni **Active Directory tartományi szolgáltatások** összekötőt, majd a Tulajdonságok **lehetőséget.**  
   ![A tulajdonságok szerkesztését bemutató szinkronizálási szolgáltatáskezelő](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. Az előugró ablakban jelölje be a **Csatlakozás az Active Directory erdőhöz lehetőséget,** és jegyezze fel a **Felhasználónév tulajdonságot.** Ez a tulajdonság az Azure AD Connect által a címtár-szinkronizálás végrehajtásához használt AD DS-fiók. Az Azure AD Connect jelszó-visszaírás végrehajtásához az AD DS-fióknak alaphelyzetbe kell állítania a jelszót.  

   ![A szinkronizálási szolgáltatás Active Directory felhasználói fiókjának megkeresése](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Jelentkezzen be egy helyszíni tartományvezérlőre, és indítsa el az **Active Directory – felhasználók és számítógépek** alkalmazást.
1. Válassza a **Nézet** lehetőséget, és győződjön meg arról, hogy a **Speciális funkciók** beállítás engedélyezve van.  

   ![Az Active Directory – felhasználók és számítógépek speciális szolgáltatásokat mutatnak](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Keresse meg az ellenőrizni kívánt Active Directory felhasználói fiókot. Kattintson a jobb gombbal a fiók nevére, és válassza **a Tulajdonságok parancsot.**  
1. Az előugró ablakban lépjen a **Biztonság lapRa,** és válassza a **Speciális**lehetőséget.  
1. A **Rendszergazda speciális biztonsági beállításai** előugró ablakban lépjen a **Hatékony hozzáférés** lapra.
1. Válassza **a Felhasználó kiválasztása**lehetőséget, válassza az Azure AD Connect által használt AD DS-fiókot (lásd a 3. lépést), majd válassza a Hatékony hozzáférés megtekintése **lehetőséget.**

   ![Az Effektív hozzáférés lap a Szinkronizálási fiókkal](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Görgessen le, és keresse meg a **Jelszó alaphelyzetbe állítása lehetőséget.** Ha a bejegyzés be van jelölve, az Active Directory tartományi szolgáltatások fiókja rendelkezik engedéllyel a kijelölt Active Directory felhasználói fiók jelszavának alaphelyzetbe állításához.  

   ![Annak ellenőrzése, hogy a szinkronizálási fiók jelszó alaphelyzetbe állítása engedéllyel rendelkezik-e](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-fórumok

Ha általános kérdése van az Azure AD-vel és az önkiszolgáló jelszó-visszaállítással kapcsolatban, az [Azure AD-fórumokon](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)kérhet segítséget a közösségtől. A közösség tagjai közé tartoznak a mérnökök, a termékmenedzserek, az MVP-k és az informatikai szakemberek.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha nem találja a választ egy problémára, ügyfélszolgálati csapataink mindig rendelkezésre állnak, hogy segítsenek Önnek.

A megfelelő segítségnyújtás érdekében kérjük, hogy az ügy megnyitásakor a lehető legrészletesebben nyújtson be. Ezek az adatok a következők:

* **A hiba általános leírása**: Mi a hiba? Milyen viselkedést észleltek? Hogyan tudjuk reprodukálni a hibát? Adja meg a lehető legtöbb részletet.
* **Oldal**: Melyik oldalon voltál, amikor észrevetted a hibát? Adja meg az URL-t, ha tudja, és egy képernyőképet az oldalról.
* **Támogatási kód**: Mi volt a támogatási kód, amely akkor jött létre, amikor a felhasználó meglátta a hibát?
   * A kód megkereséséhez reprodukálja a hibát, majd válassza ki a **támogatási kód** hivatkozását a képernyő alján, és küldje el a támogatási szakembernek az eredményül kapott GUID-ot.

   ![A támogatási kód megkeresése a képernyő alján][Support code]

  * Ha egy olyan oldalon van, amelynek alján nincs támogatási kód, válassza az F12 lehetőséget, és keresse meg a SID-et és a CID-t, és küldje el a két találatot a támogatási szakembernek.
* **Dátum, idő és időzóna:** Adja meg a pontos dátumot és időt a hiba előfordulásának *időzónájával.*
* **Felhasználói azonosító:** Ki volt az a felhasználó, aki látta a hibát? Ilyen például a *\@felhasználó contoso.com.*
   * Ez egy összevont felhasználó?
   * Ez egy áteressze hitelesítési felhasználó?
   * Ez egy jelszókivonat-szinkronizált felhasználó?
   * Ez csak felhőalapú felhasználó?
* **Licencelés:** A felhasználó rendelkezik egy Azure AD-licenc hozzárendelt?
* **Alkalmazás eseménynaplója:** Ha jelszó-visszaírást használ, és a hiba a helyszíni infrastruktúrában van, az alkalmazás eseménynaplójának egy tömörített példányát az Azure AD Connect-kiszolgálóról.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Az Azure AD Sync szolgáltatás újraindítása"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "A támogatási kód az ablak jobb alsó sarkában található"

## <a name="next-steps"></a>További lépések

Az alábbi cikkek további információt nyújtanak az Azure AD-n keresztüli jelszó-alaphelyzetbe állításról:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó alaphelyzetbe állítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció az önkiszolgáló jelszó alaphelyzetbe állításához](../user-help/active-directory-passwords-reset-register.md)
* [Van engedélyezési kérdése?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
