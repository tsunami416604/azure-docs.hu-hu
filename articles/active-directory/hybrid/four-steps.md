---
title: Négy lépést az Azure Active Directory egy erős identity Foundation
description: Ez a témakör ismerteti a hibrid identitás ügyfelek is igénybe vehet, hozhat létre egy erős identity foundation négy lépést.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96b5e8ab63c1784ff073c7ba38cd4a6319db43c5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452734"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Négy lépést az Azure Active Directory egy erős identity Foundation

Alkalmazásokhoz és adatokhoz való hozzáférés kezelése már nem támaszkodhat a hagyományos hálózati biztonsági határ stratégiák, például a szegélyhálózat és a tűzfalak miatt a felhőalapú alkalmazások gyors mozgása. Most már szervezeteknek meg kell bíznia az identitáskezelési megoldás szabályozni, és mi van a munkahelyi alkalmazásokhoz és adatokhoz való hozzáférés. Több szervezet lehetővé az alkalmazottak a saját eszközét, és eszközeiket használhatják bárhonnan képesek csatlakozni az internethez csatlakozva. A szervezet úgy dönt, hogy megvalósítása identitáskezelési megoldás a fontos szempont annak biztosítására, ezeknek az eszközöknek megfelelő, biztonságos vált. A mai digitális munkaterületen [identitás az elsődleges vezérlősík](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) bármely szervezet áthelyezése a felhőbe.

Az Azure Active Directory (Azure AD) hibrid identitáskezelési megoldás bevezetése, a szervezetek számára elérhet prémium szolgáltatásokat, amelyek az automation, a delegálást, önkiszolgáló, és egyszeri bejelentkezést biztosító funkciókkal lehetővé hatékonyságát zárolásának feloldásához. Lehetővé teszi a dolgozók hozzáférhet a céges erőforrásokhoz, bárhol is szükségük van végzik a munkájukat, miközben lehetővé teszi az informatikai csapat annak a szabályozására, hogy a hozzáférés biztosításával, hogy a megfelelő emberek számára a megfelelő erőforrásokat létesíteni biztonságos termelékenység megfelelő hozzáféréssel rendelkeznek.

A tapasztalatainkat alapján, ezzel az ellenőrzőlistával, ajánlott eljárások segítségével gyorsan üzembe helyezhet a javasolt műveleteket hozhat létre egy *erős* identity foundation a szervezetben:

* Könnyedén csatlakozhat alkalmazások
* Minden felhasználó egyetlen identitás automatikus létrehozása
* Lehetővé teszik a felhasználók biztonságosan
* Az elemzések üzembe helyezése

## <a name="step-1---connect-to-apps-easily"></a>1\. lépés – alkalmazások könnyedén csatlakozhat

Az alkalmazások az Azure AD-csatlakozással, növelheti a végfelhasználói hatékonyság és a biztonság engedélyezése az egyszeri bejelentkezés (SSO), és tegye a felhasználók átadásának. Egyetlen helyen történő, Azure AD-ben az alkalmazások kezelését minimalizálása érdekében az adminisztratív terhelést, és a biztonsági és megfelelőségi szabályzatok egy egyetlen ellenőrzési pontról lehetőségekre van szükségük.

Ez a szakasz ismerteti az alkalmazások, a belső alkalmazásokat, és az alkalmazások az Azure AD-be való migrálása előnyeinek biztonságos távoli hozzáférés lehetővé teszi a felhasználói hozzáférés kezelésére vonatkozó beállítások.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Alkalmazások elérhetővé tétele a felhasználók zökkenőmentesen

Az Azure AD lehetővé teszi a rendszergazdáknak [alkalmazásokat](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) a vállalati alkalmazások katalógusbeli a [az Azure portal](https://portal.azure.com/). Alkalmazások a vállalati alkalmazások katalógus hozzáadása megkönnyíti az alkalmazások Azure AD használata az identitás-szolgáltatóként konfigurálását. Azt is lehetővé teszi az alkalmazás a feltételes hozzáférési szabályzatokkal való felhasználói hozzáférés kezelését és konfigurálását egyszeri bejelentkezés (SSO) alkalmazások, hogy a felhasználók nem kell ismételten megadnia a jelszavukat, és automatikusan bejelentkezett a helyszínen és a felhőalapú alkalmazásokat.

Alkalmazások felvétele az Azure AD katalógusából származó az, ha a felhasználók láthatják alkalmazásokat, amelyek a nekik vannak kiosztva és keresés és más alkalmazások kérelmek igény szerint. Az Azure AD biztosít [több módszert is](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) érheti el az alkalmazásokat a felhasználók számára:

* Hozzáférési panel / saját alkalmazások
* Az Office 365 appindítója
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Közvetlen bejelentkezés hivatkozás

Alkalmazásokhoz való felhasználói hozzáférés kapcsolatos további információkért lásd: **3. lépés – a felhasználók biztosítson hatékony eszközöket** ebben a cikkben.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Alkalmazások migrálása az Active Directory összevonási szolgáltatások az Azure ad-hez

Áttelepítése egyszeri bejelentkezési konfigurációjának az Active Directory összevonási szolgáltatások (ADFS) az Azure AD lehetővé teszi, hogy további funkciókat biztosít a biztonság, egy egységes kezelhetőségi és együttműködés. Az optimális eredmények elérése érdekében javasoljuk, hogy telepít át az alkalmazások az AD FS az Azure ad-hez. Az Azure AD és az alkalmazás hitelesítési és engedélyezési nyújt a következő előnyökkel jár:

* Költségek kezelése
* Kockázatkezelés
* A termelékenység növelése
* Megfelelőségi és szabályozási-címzés

További tudnivalókért tekintse meg a [az alkalmazások áttelepítése az Azure Active Directoryhoz](https://aka.ms/migrateapps/whitepaper) tanulmány.

### <a name="enable-secure-remote-access-to-apps"></a>Alkalmazások biztonságos távoli hozzáférés engedélyezése

[Az Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) nyújt egy egyszerű megoldás a szervezetek számára, hogy a helyszíni alkalmazások közzététele a felhőben, a távoli felhasználók számára, akik hozzáférhetnek a belső alkalmazásokat biztonságos módon. Után egy egyszeri bejelentkezés az Azure ad-hez felhasználók hozzáférhessenek felhő- és helyszíni alkalmazásokhoz a külső URL-címek vagy egy belső alkalmazás portálon keresztül.

Az Azure AD-alkalmazásproxy a következő előnyöket biztosítja:

* Az Azure AD a helyszíni erőforrásokhoz való kiterjesztése
  * A felhőméretű biztonság és védelem
  * Szolgáltatásai, például a feltételes hozzáférés és a multi-factor Authentication szolgáltatás, amely könnyen engedélyezése
* Nincsenek összetevők, például a VPN- és a hagyományos fordított proxy megoldásokat a peremhálózaton
* Szükséges egy kimenő kapcsolatot sem
* Egyszeri bejelentkezés (SSO) eszközök, erőforrások és a felhőbeli és helyszíni alkalmazások
* Lehetővé teszi a végfelhasználók számára, hogy hatékonyan bármikor és bárhol

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Tárhatók fel olyan IT a Microsoft Cloud App Securityvel

Modern vállalati IT-részlegek számára ismerik gyakran nem végzik a munkájukat, a felhasználók által használt minden felhőalapú alkalmazásra. A rendszergazdák a rendszer felkéri a felhő hány azok úgy gondolja, hogy a felhasználók számára alkalmazások használatakor, átlag, például: 30 vagy 40. A valóságban a végzett az átlag több mint 1000, a szervezet alkalmazottai által használt különböző alkalmazás. 80 %-a használata nem engedélyezett alkalmazásokat, hogy senki nem ellenőrizte, és nem lehet a biztonsági és megfelelőségi házirendeknek megfelelő.

[A Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) alapján állapíthatja meg, hogy a felhasználók a népszerű hasznos alkalmazásokat, hogy az informatikai előfordulhat, hogy engedélyezését, és adja hozzá a vállalati alkalmazások-galériában, hogy a felhasználók előnyös például az egyszeri Bejelentkezést és feltételes hozzáférési képességeket.

*"** Cloud App Security** győződjön meg arról, hogy az megfelelően használják a felhő- és SaaS-alkalmazásokhoz, módszerekkel, amelyek támogatják az alapvető biztonsági házirendek, amelyek segítenek megvédeni az Accenture számunkra." *--- [John Blasi, igazgató, információ-biztonság Accenture kezelése](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Mellett az informatikai árnyék-infrastruktúra észlelése, MCAS is meghatározhatja a kockázati szint az alkalmazások, jogosulatlan hozzáférés elkerülése érdekében a céges adatok esetleges adatszivárgások és más alkalmazások járó biztonsági kockázatokat.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2\. lépés – minden felhasználó egyetlen identitás automatikus létrehozása

Összevonhatja a helyszíni és felhőalapú könyvtárak a az Azure AD hibrid identitáskezelési megoldás lehetővé teszi, hogy újból felhasználhatja a meglévő helyszíni Active Directory-befektetéseit a meglévő identitások a felhőben kiépítésével. A megoldás szinkronizálása a helyszíni identitások Azure AD-ben informatikai tartja során a helyszíni Active Directory minden olyan meglévő cégirányítási megoldásokkal futtató identitások hiteles elsődleges forrása. A Microsoft Azure AD hibrid identitáskezelési megoldás foglal el, a helyszíni és felhőalapú képességek, hitelesítés és engedélyezés az összes erőforráshoz, függetlenül azok helyétől közös felhasználói identitás létrehozása.

A helyszíni címtárak integrálása az Azure AD lehetővé teszi a felhasználók hatékonyabbak és megakadályozza, hogy a felhasználók több fiókot használják a különböző alkalmazásokat és szolgáltatásokat mindkét felhő eléréséhez közös identitás megadásával és a helyszíni erőforrások. Több fiók használata egy webfejlesztő, a végfelhasználók és informatikai egyaránt. A végfelhasználói szempontból hogy több fiókok azt jelenti, hogy meg kellene jegyezni több jelszóra. Ennek elkerülése érdekében hány felhasználó használja újra ugyanazt a jelszót minden fiók, amely biztonsági szempontból hibás. Informatikai szempontból, újból gyakran több jelszó-visszaállítási és vezet segélyszolgálat költségei, valamint a végfelhasználói panaszok.

Az Azure AD Connect a szinkronizálás a helyszíni identitások Azure ad-hez, amelyek ezután felhasználhatók a felhőalkalmazások eléréséhez használt eszköz. Miután az identitásokat az Azure ad-ben, akkor helyezhet üzembe a SaaS-alkalmazásokban, mint például a Salesforce-hoz vagy beleértve.

Ebben a szakaszban azt magas rendelkezésre állás biztosítása a felhőhöz, modern hitelesítést és a helyszíni által elfoglalt tárterület csökkentése javaslatok listája.

> [!NOTE]
> Ha szeretne további információ az Azure AD Connect, [Mi az Azure AD Connect szinkronizálási szolgáltatás?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Átmeneti kiszolgáló beállítása az Azure AD Connect és naprakészen

Az Azure AD Connect kulcsfontosságú szerepet játszik a kiépítési folyamat. A szinkronizálási kiszolgáló valamilyen okból elérhetetlenné válik, a helyszíni módosításait nem frissül a felhőben, és hozzáférési problémákat okozhat a felhasználók számára. Fontos meghatározása egy feladatátvételi stratégiát, amely lehetővé teszi a rendszergazdák gyorsan szinkronizálásának folytatásához, miután a szinkronizálási kiszolgáló offline állapotba kerül.

Az esemény a magas rendelkezésre állást biztosít az elsődleges Azure AD Connect-kiszolgáló halad offline, javasoljuk, hogy telepít egy külön [átmeneti kiszolgáló](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) az Azure AD Connect. Egy kiszolgáló központi telepítése lehetővé teszi, hogy a rendszergazda "támogatása érdekében" az éles átmeneti kiszolgálón egy egyszerű konfigurálás kapcsolót. Egy készenléti kiszolgáló, az átmeneti környezetű üzemmód konfigurálása is lehetővé teszi, hogy tesztelése és üzembe helyezése az új konfigurációs módosításokat, és vezessen be egy új kiszolgálóra, ha a régit leszerelése.

> [!TIP]
> Az Azure AD Connect rendszeresen frissül. Ezért erősen ajánlott, hogy őrizze meg az átmeneti kiszolgáló aktuális a teljesítménnyel kapcsolatos fejlesztések, hibajavítások és új képességeket, amely minden új verzió biztosít kihasználása érdekében.

### <a name="enable-cloud-authentication"></a>Felhőalapú hitelesítés engedélyezése

Szervezetek számára a helyszíni Active Directoryval kell a címtár kiterjesztése az Azure ad-bA az Azure AD Connect és a megfelelő hitelesítési módszer konfigurálása. [A megfelelő hitelesítési módszer választása](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) a szervezet juthat az alkalmazások a felhőbe való áthelyezésének első lépése. Az összes felhőbeli adatok és erőforrások elérését szabályozza, mivel egy kritikus összetevő.

A legegyszerűbb és ajánlott módszer az Azure ad-ben a helyszíni címtárobjektumok a felhőalapú hitelesítés engedélyezéséhez, hogy engedélyezze [Jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS-ben). Egyes szervezetek azt is megteheti, előfordulhat, hogy érdemes lehet engedélyezni az [átmenő hitelesítés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (ESP).

E nál vagy ESP választ, ne felejtse el engedélyezni [zökkenőmentes egyszeri bejelentkezést](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) , hogy a felhasználók a felhőalapú alkalmazások megadása nélkül érhetnek folyamatosan felhasználónevüket és jelszavukat az alkalmazás a vállalati Windows 7. és 8 eszközök használata hálózat. Nélkül egyszeri bejelentkezést felhasználók fontos figyelembe venni és alkalmazásspecifikus jelszavak és a bejelentkezési minden alkalmazásba. Hasonlóképpen informatikai személyzetet tart fenn kell létrehozni és frissíteni a felhasználói fiókok az egyes alkalmazások, például az Office 365-höz, a Box vagy a Salesforce. Felhasználók ne felejtse el a jelszavukat, valamint időt kelljen szánnia a jelentkezzen be a minden alkalmazás kell. Szabványos egyszeri bejelentkezés mechanizmust, az egész vállalat elengedhetetlen a legjobb felhasználói élmény, a kockázat, lehetővé teszi a jelentést és cégirányítási csökkentése.

A szervezet már használja az AD FS vagy más helyszíni hitelesítési szolgáltatót áthelyezése az Azure AD az identitás-szolgáltatóként is csökkenthető, és javítható a rendelkezésre állás. Kivéve, ha a összevonási adott használati eseteket, javasoljuk, hogy az összevont hitelesítés-ről vagy nál és közvetlen egyszeri bejelentkezés vagy ESP és közvetlen egyszeri bejelentkezés egy helyszíni csökkentett erőforrás-igényű és a rugalmasságot kínál a felhő előnyeit élvezheti továbbfejlesztett felhasználói élményeket. További információkért lásd: [Jelszókivonat-szinkronizálás az Azure Active Directory összevonási az áttelepítés](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Fiókok automatikus megszüntetés engedélyezése

Automatikus kiépítésének és megszüntetésének biztosítása az alkalmazásokhoz való engedélyezésével a legjobb stratégiát identitások életciklusát szabályozó több rendszer között. Az Azure AD támogatja [automatikus, a csoportházirend-alapú kiépítési és megszüntetési](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) számos különböző népszerű SaaS-alkalmazást például a ServiceNow és a Salesforce és mások megvalósító felhasználói fiókok a [SCIM 2.0 protokoll](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups). Ellentétben a hagyományos kiépítési megoldások, amelyek igényelnek egyéni kódok vagy CSV-fájlok manuális feltöltése, a kiépítési szolgáltatás a felhőben üzemel, és a szolgáltatások előre integrált, amely állíthatja, és az Azure portal használatával felügyelt összekötők. Automatikus megszüntetés egyik legfőbb előnye, ez segít a szervezet biztonságának felhasználók identitását azonnal távolítsa el a fő SaaS-alkalmazásokat, ha elhagyják a szervezet.

Automatikus felhasználói fiók kiépítése, és hogyan működik kapcsolatos további tudnivalókért lásd: [automatizálhatja a Felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>3\. lépés – lehetővé teszik a felhasználók biztonságosan

A mai digitális munkaterületen fontos egyenleg biztonság a termelékenység. Azonban a végfelhasználók gyakran le vissza, amely a hatékonyságot és a felhőalkalmazások elérése lelassíthatja biztonsági intézkedések. Ennek érdekében az Azure AD önkiszolgáló képességek, amelyek lehetővé teszik a felhasználók számára, miközben minimálisra csökkentik az adminisztratív terhelést célzó biztosít.

Ez a szakasz felsorolja a fennakadások nélkül használható eltávolítása a szervezet a felhasználók közben vigilant megszólítani vonatkozó javaslatokat.

### <a name="enable-self-service-password-reset-for-all-users"></a>Önkiszolgáló jelszóváltoztatás engedélyezése minden felhasználó számára

Az Azure [önkiszolgáló jelszó-visszaállítási](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) egyszerű módot kínál a felhasználók a jelszavuk és fiókjuk rendszergazdai beavatkozás nélkül alaphelyzetbe állítása és rendszergazdák számára. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

Alapértelmezés szerint az Azure AD fiókok feloldja a jelszó-visszaállítás végrehajtása során. Azonban, ha engedélyezi az Azure AD Connect [helyszíni integráció](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), két műveletek, amelyek lehetővé teszik a felhasználók saját fiók feloldása új jelszó nélkül külön lehetősége is van.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Minden felhasználó a többtényezős hitelesítés és az SSPR regisztrálásának

Az Azure MFA és az SSPR felhasználók regisztrálásának, és a szervezet által használható jelentéseket biztosít. Felhasználók, akik még nem regisztrált kell kell részesíteni a folyamatot.

Az MFA [bejelentkezésekre](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) többtényezős hitelesítés használatával kapcsolatos információkat tartalmaz, és betekintést nyerhet, többtényezős hitelesítés hogyan működik a szervezetben. Férhető hozzá bejelentkezési tevékenység (és a naplózás és a kockázati események) esetében az Azure AD alapvető fontosságú hibaelhárítási, használatelemzési információkat és a törvényszéki vizsgálatokat.

Hasonlóképpen a [önkiszolgáló jelszókezelés jelentés](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) segítségével határozza meg, kik (vagy még nem) SSPR regisztrálva.

### <a name="self-service-app-management"></a>Önkiszolgáló kezelés

Mielőtt a felhasználók saját felderítheti alkalmazásokat a hozzáférési paneljükön, engedélyeznie kell a [önkiszolgáló alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) bármely olyan alkalmazások, amelyek szeretné engedélyezni a felhasználók számára helyi felderítését, és kérjen hozzáférést. Az önkiszolgáló alkalmazás-hozzáférés kiválóan alkalmas, hogy a felhasználók saját felderítése az alkalmazásokat, és szükség esetén engedélyezze a jóváhagyásra váró ezeknek az alkalmazásoknak hozzáférést az üzleti csoport. Engedélyezheti a felhasználókhoz rendelt hitelesítő adatok kezelésére az üzleti csoport [alkalmazásokat az egyszeri bejelentkezés jelszó](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-the-application-for-password-single-sign-on) közvetlenül a hozzáférési paneljükön.

### <a name="self-service-group-management"></a>Önkiszolgáló csoportkezelés

Felhasználók hozzárendelése alkalmazások legjobb hozzárendelt csoportok használata esetén, mert lehetővé teszi a nagyfokú rugalmasságot és ipari méretekben kezelhetik:

* Attribútumalapú dinamikus csoporttagság alapján
* Az alkalmazástulajdonosok delegálás

Az Azure AD lehetővé teszi a biztonsági és Office 365-csoportok használatával erőforrásokhoz való hozzáférés kezelésére. Ezek a csoportok a csoport tulajdonosa, akik jóváhagyása vagy elutasítása tagsági kérelmeket és csoporttagság Vezérlés delegálása is felügyelhetők. Más néven [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), ez a funkció azáltal, hogy egy rendszergazdai szerepkör a csoportok létrehozása és kezelése nélkül használják a rendszergazdák saját kérések kezelésére nem hozzárendelő csoporttulajdonosok időt takaríthat meg.

## <a name="step-4---operationalize-your-insights"></a>4\. lépés – az elemzések üzembe helyezése

Naplózás és a biztonsággal kapcsolatos eseményeket és a kapcsolódó riasztások naplózása egy hatékony stratégia, hogy a felhasználók célzó, és a szervezete nem biztonságos alapvető összetevői. A biztonsági naplók és jelentések révén a kérdés megválaszolása például:

* Használja az Ön fizetnie?
* Van-e gyanús vagy rosszindulatú történik a bérlőm?
* Ki lett érinti őket egy biztonsági incidens?

A biztonsági naplók és jelentések biztosítja a gyanús tevékenységek és a mintákat, amelyek a hálózat és a belső támadásokkal külső behatolás történt kísérlet, vagy sikeres lehet, hogy észlelni a Súgó elektronikus rekord. Naplózás segítségével felhasználói tevékenység, a dokumentum a jogszabályoknak való megfelelőség figyelése, a nyomkereső elemzésekkel és egyéb lehetőségek. Riasztások adja meg a biztonsági események értesítések.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Műveletek a legalacsonyabb jogosultsági szintű rendszergazdai szerepkörök hozzárendelése

Ahogyan Ön gondol kapcsolatos műveletekhez a megközelítés, nincsenek felügyeleti kell figyelembe venni néhány szintjét. Az első szintjét helyezi el a felügyelet terhét a globális rendszergazdák férhetnek hozzá. Mindig használja a globális rendszergazdai szerepkörrel, kisebb méretű vállalatok számára megfelelő lehet. De a nagyobb vállalatok súgó ügyfélszolgálati munkatársak és a egy feladatának felelős rendszergazdák számára, a globális rendszergazdai szerepkör hozzárendelése egy biztonsági kockázatot jelenthet, mivel azok számára, lehetővé teszi a feladatokat, amelyek a fenti és nem Mik azok képesnek kell lennie ezzel.

Ebben az esetben fontolja meg a következő szintre a felügyelet. Azure AD-vel, kijelölheti a végfelhasználók számára "korlátozott rendszergazdák" feladatok kevésbé kiemelt jogosultságú szerepkörök kezelésére jogosult. Például hozzárendelheti a segítségére ügyfélszolgálati munkatársak a [biztonsági olvasó](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) ellátja kezelhetők a csak olvasási hozzáféréssel rendelkező biztonsággal kapcsolatos szolgáltatások szerepkört. Vagy esetleg logikus rendelje hozzá a [authentication rendszergazdája](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) személyek számára, hogy lehetővé teszi, jelszó-hitelesítő adatok alaphelyzetbe állítása, vagy olvassa el és konfigurálása az Azure Service Health szerepkört.

További tudnivalókért lásd: [rendszergazdája szerepkör engedélyei az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Hibrid-összetevők (az Azure AD Connect szinkronizálása, az AD FS) az Azure AD Connect Health

Az Azure AD Connect és az AD FS olyan alapvető fontosságú összetevője, amely potenciálisan megszakítja az életciklus-felügyelet és a hitelesítési és végső soron a leállások vezethet. Figyelési és jelentéskészítési összetevő, ezért érdemes üzembe helyezése az Azure AD Connect Health.

További információkért lépjen olvasási [figyelő AD FS az Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Elemzési adatok gyűjtését az Azure Monitor használatával

[Az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) egy egységes figyelési portál Azure ad-ben az összes napló, amellyel részletesebben elemezheti, speciális analitikai és intelligens gépi tanulási. Az Azure monitorral metrikákat és naplókat a portálon, és hogy értékes információkhoz juthat több állapota és teljesítménye az erőforrások API-kon keresztül használhatják. Lehetővé teszi egy egyetlen ablaktábla üveg tapasztalattal a portálon termékintegrációk keresztül támogatja a hagyományos külső SIEM rendszerekbe API-k és az adatok exportálási lehetőségek széles skáláját engedélyezése közben. Az Azure Monitor is biztosít értesítést kaphat, vagy automatikus műveletek végrehajtása az erőforrásokat érintő problémákat a riasztási szabályok beállításával.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>A vezetői és az összes napot, a nap egyéni irányítópultok létrehozása

A SIEM-megoldás nem rendelkező szervezeteknek töltse le a [Power BI-tartalomcsomag](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) Azure AD-hez. A Power BI-tartalomcsomag csomag tartalmazza az előre elkészített jelentéseinek segítségével ismerje meg, hogyan a felhasználók elfogadják, és használja az Azure AD-funkciók, amely lehetővé teszi, hogy betekintést nyerhet a címtárhoz tartozó összes tevékenység. Emellett létrehozhat saját [egyéni irányítópult](https://docs.microsoft.com/power-bi/service-dashboards) , és megoszthatja a jelentés a napi tevékenységek, a vezetőségi. Az irányítópultok olyan nagyszerűen nyomon követheti vállalkozását, és megjelenik az összes egyetlen pillantással a legfontosabb metrikákat. Az irányítópulton található Vizualizációk egy vagy több egy adatkészletből származó, és az alapjául szolgáló jelentés egy vagy több is származhatnak. Egy irányítópult egyesíti a helyszíni és felhőalapú adatokat, és a egy összevont nézetet, függetlenül az adatok hol találhatók.

![A Power BI egyéni irányítópult](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>A támogatási hívás illesztőprogramok megértéséhez.

Ebben a cikkben leírt módon hibrid identitáskezelési megoldás megvalósításának, meg kell tapasztalnia csökkentését végső soron a segélykérő hívások számát. Gyakori problémák elfelejtett jelszó és a fiók zárolásának problémák elhárításáról végrehajtása az Azure önkiszolgáló jelszó-visszaállítás, mint például az önkiszolgáló alkalmazás-hozzáférés engedélyezése során lehetővé teszi a felhasználóknak saját felderítése és alkalmazásokhoz való hozzáférés kérése nélkül a munkahelye informatikusainak.

Ha támogatási hívások csökkentése nem vizsgálja meg, javasoljuk, győződjön meg arról, ha az SSPR vagy az önkiszolgáló alkalmazás-hozzáférés megfelelően van konfigurálva, hogy a támogatási hívás illesztőprogramok elemez, vagy ha egyéb új merül fel, amelyek rendszeresen lehetnek címzett.

*"A digitális átalakulásunkhoz azt szükség egy megbízható identitás- és hozzáférés-kezelési szolgáltató velünk a kapcsolatot, a partnerek és felhőbeli szolgáltatók számára olyan hatékony környezetet; közötti zökkenőmentes, mégis biztonságos integráció elősegítésére Azure ad-ben volt a legjobb lehetőség ajánlat velünk a kapcsolatot a szükséges képességek és a számunkra, és reagálhassanak kockázatokra való láthatóság."* --- [Yazan Almasri, globális Információbiztonsági igazgató, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Az elemzések az alkalmazások használatának figyelése

Mellett az informatikai árnyék-infrastruktúrára, az alkalmazások használatát a szervezet használja keresztül történő figyelés felderítése [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) a szervezet segítségére lehetnek, teljes mértékben kihasználhatja a felhőalkalmazások ígérete váltáskor. Megkönnyíti az irányítást a tevékenységek jobb átláthatóságával az eszközök és a felhőalkalmazások kritikus adatok védelmének növelése. Az alkalmazások használatát a munkahelyi MCAS figyelése segíthet a következő kérdések megválaszolásával:

* Nem engedélyezett alkalmazásokat használ az alkalmazottak az adatok tárolására?
* Hol és mikor bizalmas adatok tárolása az a felhőben?
* Ki fér hozzá a felhőben tárolt bizalmas adatait?

*"A Cloud App Securityvel, hogy gyorsan rendellenességeket és művelet végrehajtása."* --- [Eric LePenske, vezető Manager, információ-biztonság Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Összefoglalás

Hibrid identitáskezelési megoldás megvalósításának számos szempontból, de a 4. lépés – ellenőrzőlista segít gyorsan elvégezni az identitás-infrastruktúrát, a felhasználók termelékenységének és biztonságos.

* Könnyedén csatlakozhat alkalmazások
* Minden felhasználó egyetlen identitás automatikus létrehozása
* Lehetővé teszik a felhasználók biztonságosan
* Az elemzések üzembe helyezése

Reméljük, ez a dokumentum egy hasznos ütemtervet az létrehozó szervezet Identitáskezelés alapjait.

## <a name="identity-checklist"></a>Identitás ellenőrzőlista

Azt javasoljuk, hogy a következő ellenőrzőlista a hivatkozási nyomtatása identitás több szilárd alapokat való foglalkozás megkezdése előtt a szervezetben.

### <a name="today"></a>Ma

|Bejelentkezett?|Elem|
|:-|:-|
||Próbaüzem önkiszolgáló - szolgáltatás jelszó alaphelyzetbe állítása (SSPR) csoport|
||Az Azure AD Connect Health hibrid-összetevők|
||A művelet a legalacsonyabb jogosultsági szintű rendszergazdai szerepkörök hozzárendelése|
||Tárhatók fel olyan IT a Microsoft Cloud App Securityvel|
||Az Azure Monitor használatával az elemzési adatok naplózása|

### <a name="next-two-weeks"></a>A következő két hét

|Bejelentkezett?|Elem|
|:-|:-|
||Az alkalmazás elérhetővé tétele a felhasználók számára|
||Az Azure AD-kiépítés egy SaaS-alkalmazás tetszőleges próbacsapat|
||Átmeneti kiszolgáló beállítása az Azure AD Connect és naprakészen|
||Indítsa el az alkalmazások áttelepítés az AD FS az Azure ad-ben|
||A vezetői és az összes napot, a nap egyéni irányítópultok létrehozása|

### <a name="next-month"></a>Következő hónapban

|Bejelentkezett?|Elem|
|:-|:-|
||Az elemzések az alkalmazások használatának figyelése|
||Próbaüzem biztonságos távoli hozzáférést az alkalmazásokhoz|
||Minden felhasználó a többtényezős hitelesítés és az SSPR regisztrálásának|
||Felhőalapú hitelesítés engedélyezése|

### <a name="next-three-months"></a>Következő három hónapban

|Bejelentkezett?|Elem|
|:-|:-|
||Az önkiszolgáló alkalmazás-kezelés engedélyezése|
||Önkiszolgáló csoportkezelési szolgáltatás engedélyezése|
||Az elemzések az alkalmazások használatának figyelése|
||A támogatási hívás illesztőprogramok megértéséhez.|

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan lehet növelni a funkciói segítségével biztonságos állapotáról az Azure Active Directory és az 5-lépés feladatlista - [öt lépést a személyazonosság-infrastruktúra védelme](https://aka.ms/securitysteps).

Az Azure AD identitás-funkciók segítségével gyorsabban és szabályozottabbá felhőfelügyelet azáltal, hogy a megoldások és funkciók lehetővé teszik a szervezetek gyorsan fogad el, és helyezze át a további, az Identitáskezelés, a hagyományos az átállás a helyszíni rendszerek Azure ad - [Azure AD kínál szabályozott Felhőfelügyeleti a helyszíni számítási feladatok](https://aka.ms/cloudgoverned).
