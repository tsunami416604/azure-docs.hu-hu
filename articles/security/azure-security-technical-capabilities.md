---
title: Az Azure biztonságtechnikai képességek |} A Microsoft Docs
description: Ismerje meg a felhőalapú számítástechnikai szolgáltatások széles választékával számítási példányokat tartalmazó & szolgáltatások, amelyek méretezhető felfelé és lefelé automatikusan az alkalmazás vagy a vállalat igényeinek.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: e7f8eef2c3422084846462e2f6159a8b8be7167f
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301825"
---
# <a name="azure-security-technical-capabilities"></a>Az Azure biztonságtechnikai képességek

Segít a jelenlegi és jövőbeli Azure ügyfelek ismertetése, és felhasználja az elérhető különböző biztonsági funkciókat és körülvevő az Azure Platform, Microsoft kifejlesztett egy sorozatát tanulmányok, biztonsági áttekintéseket, ajánlott eljárások és Ellenőrzőlistákat. A témakörök tartomány és teljes mértékű tekintetében, és rendszeresen frissül. Ez a dokumentum a sorozat része, az alábbi absztrakt szakaszban foglaltak szerint. További információ az Azure Security sorozat (URL) található.

## <a name="azure-platform"></a>Azure-platform

[A Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) infrastruktúra felhőalapú platform áll, és a Microsoft nyilvános felhőben adatok-ban üzemeltetett alkalmazás-szolgáltatások, integrált data services és a bővített analitika és a fejlesztői eszközöket és szolgáltatásokat, az adatközpontok. Ügyfeleink az Azure számos különböző kapacitások és forgatókönyvek, az alapszintű számítási, hálózati és tárolási, mobil és webes alkalmazás szolgáltatások, teljes körű felhőbeli helyzetek, például az eszközök internetes hálózatát, és használható a nyílt forráskódú technológiákkal, és hibrid felhő üzembe helyezve vagy a szolgáltatott ügyfél adatközponton belül. Az Azure biztosít a felhő-számítástechnika építőelemek segítségével a vállalatok költségeit, gyors innovációra, és proaktív módon kezelheti a rendszerek. Amikor épülnek, illetve telepíti át informatikai felhőszolgáltatók, az alkalmazások és a szolgáltatások és a vezérlőket, kezelheti a felhőalapú eszközeinek biztonságát biztosítanak az adatok védelme érdekében a szervezet képességekhez hagyatkoznia.

Microsoft Azure az egyetlen számítástechnikai felhőszolgáltató, amely biztonságos, konzisztens alkalmazás platform és infrastruktúra--szolgáltatásként belül a különböző felhőalapú szakértelmével és összetettségi szintjével projektet, az integrált adatok szolgáltatásokkal csoportmunka-kínál a elemzés, fedje fel üzletiintelligencia-adatokból, bárhol is létezik, a Microsoft és a nem Microsoft-platformokat, nyissa meg a keretrendszerek és eszközök, felhőbeli integrálása a helyszíni, valamint üzembe helyezése az Azure cloud services belül választási lehetőség biztosítása a helyszíni adatközpontjaik között. A Microsoft megbízható felhő részeként ügyfelek használja az Azure iparágvezető biztonsági, megbízhatóság, megfelelőség, adatvédelmi és a hatalmas hálózata, a személyeket, partnerek és folyamatok szervezetek támogatása a felhőben.

A Microsoft Azure révén a következőket teheti:

- Gyorsítsa fel az innováció a felhőben.

- & Döntéshozatali üzleti alkalmazások támogatása adatelemzéssel.

- Fejlesszen megkötések nélkül, és bárhol üzembe helyezhető.

- A vállalati védelme.

## <a name="scope"></a>Hatókör

Ebben a tanulmányban folytatásakor vonatkozik, biztonsági szolgáltatásokat és funkciókat, a Microsoft Azure-alapösszetevők, nevezetesen támogató [a Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction), [Microsoft Azure SQL Database](https://docs.microsoft.com/azure/sql-database/), [ A Microsoft Azure virtuális gép modell](https://docs.microsoft.com/azure/virtual-machines/), és az eszközök, infrastruktúra, amely minden kezelheti. A Microsoft Azure technikai elérhető lehetőségeket, az ügyfelek biztonsági és adatvédelmi adataik védelmére a szerepkörük teljesítéséhez tanulmány koncentrálhat.

A közös felelősség modell ismertetése fontossága alapvető fontosságú ügyfelek esetében, akik a felhőbe áthelyezni. A felhőszolgáltatók biztonság és megfelelőség erőfeszítések jelentős előnyöket kínál, de ezeket az előnyöket nem az ügyfél a felhasználók, alkalmazások és szolgáltatásajánlatok védjék absolve.

Az IaaS-megoldások az ügyfél felelős, vagy biztonságossá tétele és az operációs rendszer, hálózati konfiguráció, alkalmazások, identitás, az ügyfelek és adatok felügyelete egy megosztott felelősségre van.  PaaS-megoldásokat hozhat létre a szolgáltatott infrastruktúra telepített példányainak, az ügyfél továbbra is felelős, vagy egy megosztott felelős biztonságossá tétele és az alkalmazások, identitás, ügyfelek és az adatok kezeléséhez. Az SaaS-megoldások Nonetheless, az ügyfél továbbra is elszámolással. Biztosítaniuk kell, hogy az adatok megfelelően van-e besorolva, és oszthatnak meg a feladata, hogy a felhasználók és végponti eszközök kezelésére.

Ez a dokumentum nem biztosít részletes bármely, a kapcsolódó Microsoft Azure platform összetevők, például az Azure-webhelyek, Azure Active Directory, HDInsight, a Media Services és más szolgáltatások, amelyek alapvető összetevők interaktív irányítópultunkat réteges vannak. Bár a minimális szintű általános információ áll rendelkezésre, feltételezhető, hogy olvasók ismeri az Azure alapvető fogalmait mutató egyéb hivatkozásokat a Microsoft által biztosított, és tartalmazza a tanulmányban szereplő hivatkozásokra leírtak szerint.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Biztonságtechnikai képességek elérhető teljesíti a felhasználó (ügyfél) feladata - átfogó kép

A Microsoft Azure biztosít, szolgáltatásokat, amelyek segítségével az ügyfelek meg biztonsági, adatvédelmi és megfelelőségi igényeinek. A következő képen látható segít a különböző Azure-szolgáltatások elérhető a felhasználók számára egy biztonságos és megfelelő alkalmazás-infrastruktúra az iparági normák létrehozását ismertetik.

![Elérhető biztonsági műszaki képességek-nagy kép](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Felügyelheti és ellenőrizheti az identitás- és felhasználói hozzáférést (védelem)

Az Azure segít az üzleti és személyes adatok védelme, hogy a felhasználói identitások és a hitelesítő adatok kezelése és hozzáférés engedélyezésével.

### <a name="azure-active-directory"></a>Azure Active Directory

A Microsoft identitás- és hozzáférés felügyeleti megoldások segítséget nyújt az informatikai való hozzáférés védelme az alkalmazások és erőforrások a vállalati adatközpontok és a felhőben, például a többtényezős hitelesítés és feltételes hozzáférési érvényesítési szintű engedélyezése házirendek. A gyanús tevékenységek monitorozása speciális biztonsági jelentések kiadásán, naplózáson és riasztásokon keresztül valósul meg, így csökkenthető a potenciális biztonsági problémák kockázata. [Az Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) biztosít egyszeri bejelentkezés több ezer felhőalapú (SaaS) alkalmazások és webalkalmazások hozzáférést a helyszíni futtatása.

Az Azure Active Directory (Azure AD) biztonsági funkcióit is tartalmazzák:

- Hozzon létre és kezelheti az egyes felhasználók egyetlen identitással a hibrid vállalati, a felhasználók, csoportok és eszközök szinkronban tartja.

- Adja meg az egyszeri bejelentkezéses hozzáférést több ezer előre integrált SaaS-alkalmazások, beleértve az alkalmazások.

- Alkalmazás-hozzáférés biztonságának engedélyezése által a helyszínen – szabályalapú többtényezős hitelesítés kényszerítése és a felhőbeli alkalmazásokhoz.

- Építhet ki biztonságos távoli hozzáférést a helyszíni webalkalmazásokhoz az Azure AD-alkalmazásproxyn keresztül.

A [Azure Active Directory portálon](http://aad.portal.azure.com/) érhető el az Azure Portalon egy részét. Ezen az irányítópulton áttekintheti a szervezet állapotát, és könnyedén ismerje meg, a könyvtárban, a felhasználók vagy az alkalmazás-hozzáférés kezelése.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Alapvető Azure identitáskezelési funkciói a következők:

- Egyszeri bejelentkezés

- Multi-Factor Authentication

- A biztonság monitorozása, értesítések és gépi tanuláson alapuló jelentésekkel

- A felhasználói identitások és hozzáférés kezelése

- Eszközregisztráció

- Privileged identity management

- Identitásvédelem

#### <a name="single-sign-on"></a>Egyszeri bejelentkezés

[Egyszeri bejelentkezés (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) kell tudni az alkalmazások és az üzleti tevékenységet, jelentkezzen be csak egyszer egyetlen felhasználói fiókkal a szükséges erőforrások eléréséhez. Miután bejelentkezett, elérheti az összes anélkül, hogy van szüksége a kérelmek hitelesítéséhez szükséges (például adjon meg egy jelszót) egy második alkalommal.

Számos szervezet támaszkodik a szoftvert, mint a szoftverszolgáltatások (SaaS) alkalmazások, például Office 365-höz, a Box vagy a Salesforce a végfelhasználói termelékenység. Hagyományosan informatikai munkatársak külön-külön létrehozásához, és az egyes SaaS-alkalmazás felhasználói fiókok frissítése szükséges, és a felhasználóknak kellett telepíteniük azt ne felejtse el a jelszót az egyes SaaS-alkalmazás.

[Az Azure AD a helyszíni Active Directory kiterjeszti a felhőbe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), a tartományhoz csatlakoztatott eszközök és a vállalati erőforrásokhoz való bejelentkezés engedélyezése felhasználók számára, nem csak az elsődleges munkahelyi fiókok használhatók, de szükséges is az összes webes és az SaaS-alkalmazásokhoz a feladat.

Nem csak felhasználó nem rendelkezik több példányban felhasználónevek és jelszavak kezeléséhez, lehet, hogy alkalmazás-hozzáférés alapján automatikusan kiépített vagy eltávolítjuk szervezeti egységek és a egy alkalmazott állapotuk. [Az Azure AD biztonsági és cégirányítási vezérlők vezet be](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) lehetővé teszik a központilag kezelheti a különböző SaaS-alkalmazások felhasználók hozzáférését.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Az Azure multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) van egy hitelesítési mód, amely egynél több ellenőrzési módszer használatát igényli, és a egy kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat. [MFA segít biztonságosabb](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) hozzáférés az adatokhoz és alkalmazásokhoz felhasználói egyszerű bejelentkezési folyamat számára. Szigorú hitelesítést többféle hitelesítési lehetőséget kínál – telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítési vagy ellenőrző kódot és a külső OAuth-jogkivonatok.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>A biztonság monitorozása, értesítések és gépi tanuláson alapuló jelentésekkel

Biztonsági monitoringgal és figyelmeztetésekkel, illetve a gépi tanuláson alapuló jelentésekkel, amelyek felismerő segíthet a üzleti adatai védelméről. Azure Active Directory hozzáférési és használati jelentések segítségével értékes információkhoz juthat az integritásra és a munkahely címtárában biztonságát. Ezekkel az információkkal a directory-rendszergazda megadhatja jobban, hogy ahol lehetséges, hogy egészülnek biztonsági kockázatokat, úgy, hogy azok megfelelően tervezi, hogy ezek a kockázatok csökkentése.

Az Azure Portalon vagy keresztül a [Azure Active Directory portálon](http://aad.portal.azure.com/), [jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) szerint vannak kategóriába sorolva a következő módon:

- Anomáliadetektálási jelentések – bejelentkezési eseményeket, amelyek találtunk rendellenes kell tartalmaznia. Célunk, győződjön meg arról, hogy az ilyen tevékenység tisztában, és engedélyezi, hogy tudni arról, hogy az esemény gyanúsnak dönt.

- Integrált alkalmazás jelentések – hogyan használja a szervezet a felhőalapú alkalmazások betekintést nyújtson. Az Azure Active Directory több ezer felhőalapú alkalmazások integrációt kínál.

- Hibajelentések – adja meg a külső alkalmazások fiókok kiépítése során esetlegesen előforduló hibákat.

- Felhasználó-specifikus jelentések – eszköz/bejelentkezési tevékenység egy adott felhasználó adatainak megjelenítése.

- Tevékenységnaplók – az elmúlt 24 órában, a legutóbbi 7 nap, vagy a elmúlt 30 napban, és a tevékenység módosításainak és a jelszó alaphelyzetbe állítása és regisztrációs tevékenység belül az összes naplózott esemény rekordot tartalmaz.

#### <a name="consumer-identity-and-access-management"></a>A felhasználói identitások és hozzáférés kezelése

[Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású, globális identitáskezelő szolgáltatást nyújt a felhasználók felé néző alkalmazásokhoz, amely több százmillió identitás kezelésére méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy új hitelesítő adatok létrehozásával jelentkezhetnek be minden alkalmazásába.

A korábbi, az alkalmazásfejlesztők számára az [regisztráljon, és jelentkezzen be a fogyasztók](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) alkalmazásaikba lenne írásos engedélye szükséges. a saját kódját. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure Active Directory B2C kínál a szervezet felhasználói identitáskezelést integrálása egy biztonságos, szabványokon alapuló platform és bővíthető házirendek rengeteg segítségével alkalmazások hatékony megoldást.

Azure Active Directory B2C használata esetén a felhasználók regisztrálhatnak az alkalmazások meglévő közösségi fiókjaik használatával (Facebook, Google, Amazon, LinkedIn), vagy új hitelesítő adatok (e-mail cím és jelszó vagy felhasználónév és jelszó) létrehozásával.

#### <a name="device-registration"></a>Eszközregisztráció

[Az Azure AD eszközregisztrációval](https://docs.microsoft.com/azure/active-directory/device-management-introduction) az alapja eszközalapú [feltételes hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) forgatókönyveket. Amikor regisztrál egy eszközt, az Azure AD eszközregisztrációval biztosít az identitás, amellyel az eszköz hitelesítésére, ha a felhasználó bejelentkezik az eszközre. A hitelesített eszköz és az eszköz attribútumai ezután a feltételes hozzáférési házirendek betartatásához használhatók a felhőben és a helyszínen tárolt alkalmazások esetében.

Kombinálva egy [mobileszköz-felügyelet (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) megoldással, például az Intune-ban, az eszköz attribútumait az Azure Active Directoryban frissülnek az eszköz további információival. Ez lehetővé teszi további feltételes hozzáférési szabályok létrehozását, amelyek arra kényszerítik az eszközhozzáféréseket, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak.

#### <a name="privileged-identity-management"></a>Privileged identity management

[Az Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) kezelése, szabályozhatja, és figyelheti az emelt szintű identitások és elérhető erőforrások az Azure ad-ben, valamint egyéb Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune lehetővé teszi.

Egyes esetekben a felhasználóknak kell erőforrásokhoz, Azure vagy Office 365 vagy más SaaS-alkalmazások a privilegizált műveleteket végezni. Ez gyakran azt jelenti, szervezetek számára tegye lehetővé számukra állandó emelt szintű hozzáférés az Azure ad-ben. Egy egyre bővülő biztonsági kockázatot jelent a felhőben üzemeltetett erőforrásokhoz, mert a szervezetek nem eléggé figyelheti, mivel foglalkoznak az ezek a felhasználók a rendszergazdai jogosultságokat. Ha egy emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, ezenkívül egy megsértésének hatással lehet a teljes felhőalapú biztonsági. Az Azure AD Privileged Identity Management segít a kockázat megoldásához.

Az Azure AD Privileged Identity Management lehetővé teszi:

- Tekintse meg a felhasználók Azure AD-rendszergazdái közül

- Igény szerinti, engedélyezze a "csak az időben" rendszergazdai hozzáférést a Microsoft Online szolgáltatásaihoz, például az Office 365 és Intune-ban

- Rendszergazdák hozzáférési műveleteiről és a változások jelentéseket rendszergazdája hozzárendeléseinek beolvasása

- A kiemelt szerepkörű való hozzáféréssel kapcsolatos riasztások beolvasása

#### <a name="identity-protection"></a>Identitásvédelem

[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) egy biztonsági szolgáltatás, amely a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági résekről nézetet. Identity Protection meglévő Azure Active Directory anomáliadetektálási észlelési képességeit (rendellenes tevékenységekre vonatkozó jelentések az Azure AD) használ, és bemutatja az új kockázati események típusai, amelyek valós időben észlelheti a rendellenességeket.

## <a name="secured-resource-access-in-azure"></a>Védett erőforrás-hozzáférés az Azure-ban

Hozzáférés-vezérlés az Azure számlázási szempontból elindul. Azure-fiókkal, megnyitásával tulajdonosa a [Azure Account Center](https://account.windowsazure.com/subscriptions), a fiók rendszergazdája (MT) van. Előfizetések számlázási tárolója, de azok biztonsági határként szolgál: minden előfizetési csomaghoz tartozik egy szolgáltatás rendszergazdai (SA) számára is hozzáadása, törlése és módosítása az Azure-erőforrások, az adott előfizetésben az Azure portal használatával. Új előfizetés az alapértelmezett biztonsági Társítás az AA, de az AA módosíthatja a biztonsági Társítás az Azure Account Center webhelyen.

![Védett erőforrás-hozzáférés az Azure-ban](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Az előfizetések és a címtár társítás is. A könyvtár határozza meg, hogy a felhasználók egy csoportja. Ezek lehetnek a felhasználók a munkahelyi vagy iskolai, hozott létre a címtárban, vagy lehetnek külső felhasználók (vagyis a Microsoft Accounts). Előfizetés szolgáltatás-rendszergazda (SA) vagy a társ-rendszergazdaként (CA); a rendszer rendelt könyvtár felhasználók egy részhalmazát érhetnek el. az egyetlen kivétel, hogy az örökölt okokból Microsoft Accounts (korábban Windows Live ID) rendelhető SA vagy a CA anélkül, hogy a könyvtárban található.

A vállalatok biztonsági jellegű szükségük van a pontos engedélyeket ad az alkalmazottak kell összpontosítania. Túl sok engedély egy fiókot a támadók tehetők közzé. Túl kevés engedélyek jelenti azt, hogy az alkalmazottak nem munkavégzéséhez hatékony. [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) segítségével részletes hozzáférés-vezérlést Azure felajánlásával oldja meg a problémát.

![Védett erőforrás-hozzáférés ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van. Helyett, így mindenki korlátozás nélküli engedélyeket az Azure-előfizetés vagy erőforrásokhoz, engedélyezheti csak bizonyos műveleteket. Ha például az RBAC használatával lehetővé teszik egy alkalmazott egy előfizetésben található virtuális gépek kezelése, miközben egy másik kezelheti az SQL-adatbázisok ugyanazon az előfizetésen belül.

![Védett erőforrások eléréséhez az Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Az Azure Adatbiztonság és titkosítás (védelme)

Az adatvédelem a felhőben, a kulcsok egyikét a számlázást a lehetséges állapotok, amelyben az adatok akkor fordulhat elő, és milyen vezérlők érhetők el az állapotban. Az Azure data biztonsággal és a titkosítással kapcsolatos ajánlott az ajánlások a következő adatokat állapotok merülnének fel.

- At-rest: Ez magában foglalja a tárolási objektum, a tárolók és a fizikai adathordozó statikusan létező típusok kell azt mágneses vagy optikai lemez összes információt.

- Átvitel: Ha az adatok között átvitt összetevők, a helyek vagy a programok, például a hálózaton keresztül, egy service bus (a felhőbe helyszíni, és fordítva, beleértve a hibrid kapcsolatok, például az ExpressRoute) közötti, vagy egy bemeneti/kimeneti folyamat során a rendszer teljes, gondolat a mozgásban lévő.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Titkosítás inaktív állapotban eléréséhez tegye az alábbiakat:

Támogatja az adatok titkosítása az alábbi táblázatban részletes ajánlott titkosítási modellek legalább egyike.

| Titkosítási modellek |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Server Encryption | Server Encryption | Server Encryption | Ügyféloldali titkosítás
| Kiszolgálóoldali titkosítás szolgáltatás kezelt kulcsok használata | Kiszolgálóoldali titkosítás Customer-Managed kulcsok használata az Azure Key Vaultban | Kiszolgálóoldali titkosítás használatával a helyszíni ügyfél által kezelt kulcsok |
| • Azure erőforrás-szolgáltatók a titkosítási és visszafejtési műveleteket végre <br> • A Microsoft kezeli a kulcsok <br>• Teljes felhőalapú funkciókkal | • Azure erőforrás-szolgáltatók a titkosítási és visszafejtési műveleteket végre<br>• Ügyfél szabályozza, kulcsok Azure Key Vault-n keresztül<br>• Teljes felhőalapú funkciókkal | • Azure erőforrás-szolgáltatók a titkosítási és visszafejtési műveleteket végre <br>• Ügyfél szabályozza a helyszíni kulcsok <br> • Teljes felhőalapú funkciókkal| • Azure-szolgáltatások visszafejtett adatait nem látja. <br>• Az ügyfelek kulcsait a helyi környezetben tarthatja, (vagy más biztonságos tárolók). Kulcsok nem érhetők el az Azure-szolgáltatások <br>• Csökkentett felhőalapú funkciókkal|

### <a name="enabling-encryption-at-rest"></a>Titkosítás inaktív állapotban engedélyezése

**Azonosítsa az összes üzletek adatait**

Inaktív adatok titkosítását az a célja, hogy minden adat titkosítása. Ezzel megszünteti a hiányzik a fontos adatok vagy az összes megőrzött hely lehetőségét. Az alkalmazás által tárolt összes adat számbavétele. 

> [!Note] 
> Nem csak "alkalmazásadatok" vagy "személyazonosításra alkalmas adatok", de bármilyen alkalmazást beleértve a vonatkozó adatok fiók metaadatok (előfizetés-leképezések szerződésadatok, PII).

Fontolja meg, milyen tárolók használja adatok tárolására. Példa:

- Külső tároló (például SQL Azure Document DB, Hdinsight, Data Lake, stb.)

- Ideiglenes tároló (bármely helyi gyorsítótár, amely tartalmazza a bérlő adatainak)

- Memórián belüli gyorsítótár (léptethetnek be az oldal fájlja.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Kihasználhatja a meglévő titkosítás a rest-támogatás az Azure-ban

Egyes üzletek használ használhatja a Rest-támogatás a meglévő titkosítása.

- Azure Storage: Lásd: [az Azure Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Lásd: [transzparens adattitkosítás (TDE), az SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Virtuális gép és a helyi lemezes tárolás ([az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

A virtuális gép és a helyi lemezes tárhely használata az Azure Disk Encryption ahol támogatott:

#### <a name="iaas"></a>IaaS

Az IaaS virtuális gépek (Windows vagy Linux) szolgáltatások kell használnia [az Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) titkosíthatja a vásárlói adatokat tartalmazó kötetet.

#### <a name="paas-v2"></a>PaaS v2

Futó szolgáltatások a PaaS v2 a Service Fabric használatával az Azure disk encryption virtuálisgép-méretezési [VMSS] a PaaS v2 virtuális gép titkosítására.

#### <a name="paas-v1"></a>PaaS v1

Az Azure Disk Encryption jelenleg nem támogatott a PaaS v1. Ezért inaktív megőrzött adatok titkosításához alkalmazás a blokkszintű titkosítás kell használnia.  Ez magában foglalja, de nem korlátozódik az alkalmazásadatok, az ideiglenes fájlok, naplók és összeomlási memóriaképek.

A legtöbb szolgáltatás megpróbáljon-használhatja a storage erőforrás-szolgáltató titkosítása. Egyes szolgáltatások kell elvégeznie, explicit titkosítás, például minden megőrzött megosztottkulcs-anyag (tanúsítványok, a legfelső szintű / fő kulcsok) a Key Vaultban kell tárolni.

Ha ügyfél által felügyelt kulcsokkal Szolgáltatásoldali titkosítási támogatja szükség van oly módon, az ügyfél a kulcs lekérése velünk a kapcsolatot. A támogatott és ajánlott módja, hogy integrálja az Azure Key Vault (AKV). Ebben az esetben az ügyfelek veheti fel és kezelheti a saját kulcsok Azure Key vaultban. Egy ügyfél is megtudhatja, hogyan használhatja az AKV-n keresztül [Ismerkedés a Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Integrálható az Azure Key Vault, akkor adja hozzá kódot kulcsot kérni az AKV visszafejtéshez szükség esetén.

- Lásd: [Azure Key Vault – részletes](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) információ az AKV integrálása.

Ha támogatja az ügyfél által felügyelt kulcsok, meg kell adnia egy felhasználói felület, mely a Key Vault (vagy Key Vault URI) használatához adja meg az ügyfél.

Titkosítás inaktív állapotban magában foglalja a gazdagép, infrastruktúra- és bérlői adatok, az adatvesztés a kulcsokat a rendszer hibája miatt a titkosítást, vagy kártékony tevékenységek azt is jelentheti, hogy a titkosított adatok elvész. Ezért fontos, hogy rendelkezik-e egy átfogó vész helyreállítási történetet rendszerhibák és kártékony tevékenységek rugalmas Rest-megoldást a titkosításhoz.

Titkosítás inaktív állapotban megvalósító szolgáltatásoknak általában továbbra is ki van téve a titkosítási kulcsok vagy marad, hogy adatokat nem titkosított a gazdameghajtó (például az a gazdagép operációs Rendszeréhez oldal fájlja.) Ezért szolgáltatások biztosítania kell a gazdakötet szolgáltatásaik titkosítva van. Megkönnyítése érdekében a számítási csoport engedélyezve van a gazdagép-titkosítás, mely használja a központi [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP és -bővítmények a DCM-szolgáltatás és az ügynök a gazdakötet titkosításához.

A legtöbb szolgáltatások standard szintű Azure-beli virtuális gépeken van megvalósítva. Ilyen szolgáltatásokat kell kapnia [gazdagép titkosítási](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automatikusan amikor számítási lehetővé teszi, hogy azt. A futó számítási szolgáltatások felügyelt fürtöket gazdagép titkosítási automatikusan engedélyezve lesz, a Windows Server 2016 bevezetési.

### <a name="encryption-in-transit"></a>Az átvitel közbeni titkosítás

Az átvitt adatok védelme a data protection stratégia nagyon fontos részét kell lennie. Mivel az adatok átvitele számos helyről oda-vissza van, az általános ajánlás az, mindig használt SSL/TLS-protokollok használatával cserélnek adatokat a különböző helyek közötti. Bizonyos esetekben érdemes elkülöníteni a teljes kommunikációs csatornát, a helyszíni és a felhő között infrastruktúra virtuális magánhálózati (VPN) használatával.

Adatok áthelyezése a helyszíni infrastruktúra és az Azure között érdemes lehet megfelelő védelmi módszerek, például a HTTPS- vagy VPN.

Használja a szervezet számára, amelyek biztonságos hozzáférést több munkaállomásokon található a helyszínről az Azure-bA kell [az Azure site-to-site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Használja a szervezet számára, amelyek biztonságos hozzáférést található egy munkaállomás a helyszínről az Azure-bA kell [pont – hely VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Nagyobb adatkészletekhez például egy dedikált nagy sebességű WAN-kapcsolaton keresztül is áthelyezhetők [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Ha az ExpressRoute használatát választja, az adatokat az alkalmazásszintű használata titkosításhoz is [SSL/TLS](https://support.microsoft.com/kb/257591) vagy egyéb protokollok felvett Protection.

Az Azure Storage az Azure Portalon keresztül használja, ha az összes tranzakció fordulhat elő, HTTPS-kapcsolaton keresztül. [Tárolási REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS is használható interakcióba [Azure Storage](https://azure.microsoft.com/services/storage/) és [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Szervezetek számára, amelyek az adatok védelmére átvitel sikertelen jobban ki a [man-in-the-middle támadások](https://technet.microsoft.com/library/gg195821.aspx), [lehallgatást](https://technet.microsoft.com/library/gg195641.aspx), és a munkamenet-eltérítés. Ezeket a támadásokat lehet az első lépés a bizalmas adatok elérésekor.

További tudnivalók az Azure VPN-beállítást a cikk elolvasásával [tervezéssel és kialakítással VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Fájl szintű titkosítás kényszerítéséhez

[Az Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) titkosítási, identitáskezelési és engedélyezési házirendeket használ a fájlok és e-mailek védelmének elősegítésére. Az Azure RMS működésének bemutatása, több eszközre – telefonokon, táblagépeken és számítógépeken a szervezeten belül, mind a szervezeten kívüli ellenőrzését. Ez a lehetőség azért lehetséges, mert az Azure RMS-t biztosítja, ami az adatok maradnak, még akkor is, ha elhagyják a szervezet területét.

Amikor az Azure RMS használatával védeni a fájlokat, és teljes körű támogatással, iparági szabványos kriptográfia használja [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Azure RMS adatvédelmi használja ki, ha rendelkezik, hogy a fájl védett marad akkor is, ha a ellenőrzése alatt nem álló tárolók másolódik informatikai, például egy felhős társzolgáltatás. Az egyetlen különbség, e-mailben megosztott fájlok a fájl védett, lépéseit egy e-mail üzenet mellékleteként nyitni a védett mellékletet.
Az Azure RMS bevezetésének tervezésekor a következőket javasoljuk:

- Telepítse a [RMS-megosztó alkalmazás](https://technet.microsoft.com/library/dn339006.aspx). Ez az alkalmazás integrálja az Office-alkalmazásokat telepít egy Office-bővítmény, hogy a felhasználók egyszerűen védhetik a fájlokat közvetlenül.

- Alkalmazások és szolgáltatások támogatják az Azure RMS konfigurálása

- Hozzon létre [egyéni sablonokat](https://technet.microsoft.com/library/dn642472.aspx) , amely az üzleti követelményeinek megfelelően. Példa: egy sablont, amely a alkalmazni szeretné az összes szigorúan titkos titkos adatkezelési kapcsolatos e-maileket.

Szervezetek számára, amelyek a gyenge [adatbesorolás](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és lehetséges, hogy a fájlok védelmének adatszivárgás érzékenyebb. Megfelelő fájlt nem védett szervezetek nem tudják üzleti információk beszerzése, figyelje a visszaélések és rosszindulatú hozzáférés megakadályozása érdekében a fájlokat.

> [!Note]
> További információ az Azure RMS című cikkben [Ismerkedés az Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Az alkalmazások biztonságossá tételéhez (védelme)
Noha az Azure felelős az infrastruktúra és a platform, amely az alkalmazás fut. biztonságossá tétele, feladata a magát az alkalmazások biztonságossá tételéhez. Más szóval kell fejlesztése, telepítése és kezelheti az alkalmazás kódja és a tartalmak biztonságos módon. E nélkül az alkalmazás kódja vagy a tartalom sebezhetők maradhatnak a fenyegetésekkel szemben.

### <a name="web-application-firewall-waf"></a>Webalkalmazási tűzfal (WAF)
[Webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) szolgáltatása [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , amely a webalkalmazásoknak a gyakori támadások és biztonsági rések központi védelmet nyújt.

A webalkalmazási tűzfal az [alapvető OWASP-szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak szabályai alapján működik. A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

A webalkalmazási tűzfal többek között a következő gyakori internetes biztonsági rések ellen nyújt védelmet:

- SQL-injektálás elleni védelem

- Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

- Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

- HTTP protokoll megsértése elleni védelem

- HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

- Robotprogramok, webbejárók és képolvasók elleni védelem

- Gyakori alkalmazások konfigurációs hibáinak észlelése (vagyis Apache, IIS stb.)

> [!Note]
> Szabályok részletes listáját és a hozzájuk tartozó védelmi megoldások lásd a következő [alapvető szabálykészletek](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Az Azure a bejövő és kimenő forgalmat az alkalmazások biztonságossá tételéhez számos könnyen használható funkciót is biztosít. Az Azure is ügyfelek biztonságos alkalmazás kódját külsőleg biztosításával segít biztosítani funkció vizsgálata a webes alkalmazások, a biztonsági réseket.

- [Az alkalmazás Azure Active Directory-hitelesítés beállítása](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Transport Layer Security (TLS/SSL) – HTTPS engedélyezése az alkalmazás forgalmának biztonságossá tétele](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Minden bejövő forgalom kényszerített HTTPS-kapcsolaton keresztül](http://microsoftazurewebsitescheatsheet.info/)

  - [A szigorú Transport Security (HSTS) engedélyezése](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Ügyfél IP-cím alapján korlátozzák a hozzáférést az alkalmazáshoz](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Ügyfél viselkedés - kérelem gyakoriságát és egyidejűségi alapján korlátozzák a hozzáférést az alkalmazáshoz](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [A webalkalmazás kódját a Tinfoil Security Scanning eszköz használatával biztonsági réseket vizsgálata](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [TLS kölcsönös hitelesítés megkövetelése ügyféltanúsítványok szeretne csatlakozni a webalkalmazás beállítása](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Egy ügyfél-tanúsítványt az alkalmazásból való biztonságos csatlakozáshoz külső erőforrások konfigurálása](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Standard szintű kiszolgáló fejlécek elkerülése érdekében az alkalmazás-ujjlenyomat-eszközök eltávolítása](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Biztonságos csatlakozás egy alkalmazással a pont – hely típusú VPN-kapcsolattal egy magánhálózaton lévő erőforrásokkal](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Biztonságos csatlakozás egy alkalmazással a hibrid kapcsolatok használatával egy magánhálózaton lévő erőforrásokkal](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Az Azure App Service ugyanaz a kártevővédelmi megoldás az Azure Cloud Services és Virtual Machines által használt használ. További információkért lásd: a [kártevőirtó dokumentáció](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Tegye biztonságossá hálózatát (védelme)
A Microsoft Azure egy robusztus hálózati infrastruktúra támogatja az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek tartalmaz. Hálózati kapcsolat a helyszíni között az Azure-ban található erőforrások között lehetőség, és az Azure-ban üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

A [Azure hálózati infrastruktúra](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) lehetővé teszi, hogy biztonságosan csatlakoztathatja egymáshoz az Azure-erőforrások [virtuális hálózatok (Vnetek)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Virtuális hálózat saját felhőbeli hálózatának egy reprezentációja. Virtuális hálózat egy logikai elkülönítése az Ön előfizetéséhez fenntartott Azure-felhő hálózat. Virtuális hálózatok csatlakozhat a helyszíni hálózatok.

![Tegye biztonságossá hálózatát (védelme)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Alapszintű hálózati szintű hozzáférés-vezérlés (IP-cím és a TCP vagy UDP protokoll alapján) van szüksége, akkor használhatja [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Hálózati biztonsági csoport (NSG) egy tűzfal alapszintű csomag, és lehetővé teszi a hozzáférés alapján szabályozhatja a [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple).

Az Azure-hálózatok támogatja a hálózati forgalmat az Azure Virtual Networks az útválasztási viselkedés testre szabhatók. Ezt megteheti konfigurálásával is [felhasználó által megadott útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) az Azure-ban.

[Kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások nem engedélyezett az internethez csatlakozó eszközökről való kapcsolatot kezdeményez.

Az Azure támogatja a dedikált WAN-kapcsolaton kapcsolat a helyszíni hálózat és az Azure Virtual Networkhöz [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Az Azure és a hely közötti kapcsolat, amely nem a nyilvános interneten keresztül halad dedikált kapcsolatot használ. Ha az Azure-alkalmazásokat több adatközpontban fut, akkor használhatja [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) kérelmeket a felhasználóktól nyelvelemző, mind az alkalmazás-példányok között. Nem fut az Azure-ban, ha az internetről elérhető szolgáltatások is irányíthatja a forgalmat.

## <a name="virtual-machine-security-protect"></a>Virtuális gép biztonsági (védelme)

[Az Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/) lehetővé teszi számos számítástechnikai megoldáshoz a legkülönbözőbb. Számítási feladatait a Windows-, Linux-, SQL Server-, Oracle-, IBM-, SAP- és Azure BizTalk Services-támogatás révén mérettől és programnyelvtől függetlenül, szinte bármely operációs rendszerben üzembe helyezheti.

Az Azure-ban használható [kártevőirtó szoftver](https://docs.microsoft.com/azure/security/azure-security-antimalware) származó biztonsági megoldásokkal foglalkozó forgalmazó, például a Microsoft, a Symantec, a Trend Micro és a Kaspersky kártékony fájlokkal, hirdetőprogramokkal és egyéb veszélyforrásokkal szembeni a virtuális gépek.

A Microsoft Antimalware az Azure Cloud Services és Virtual Machines a vírusok, kémprogramok és más kártevők azonosításában és segít a valós idejű védelem funkcióval. Microsoft Antimalware-t biztosít, konfigurálható riasztást, ha ismert kártevő vagy nemkívánatos szoftver megkísérli a telepítést, vagy az Azure rendszeren futtassa.

[Az Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) van egy méretezhető megoldás, amellyel megvédheti alkalmazásadatait nulla tőkebefektetés és minimális üzemeltetési költségek. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védett.

[Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) segítségével koordinálhatja replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások, hogy elérhetők egy másodlagos helyre, ha az elsődleges hely leáll.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Megfelelőség biztosítása: Felhőszolgáltatások legkörültekintőbben ellenőrzőlista miatt (védelme)

A Microsoft kifejlesztett [a Cloud Services esedékes Legkörültekintőbben ellenőrzőlista](https://aka.ms/cloudchecklist.download) segítségével a szervezetek gyakorolja szerkezetkialakításra, akkor fontolja meg a felhőre. Szerkezetet biztosít bármilyen méretű és típusú szervezet – titkos vállalkozás és -közszférában működő szervezetek, beleértve a kormányzati minden szintjének és nonprofit – saját teljesítmény, szolgáltatás, az adatkezelést és cégirányítási célok azonosításához és követelmények. Ez lehetővé teszi őket a különböző felhőszolgáltatók, végső soron az egy felhőalapú szolgáltatási szerződés alapját képező, az ajánlatok összehasonlítása.

A feladatlista olyan keretrendszert kínál, záradék által-záradék és a egy új szabvány nemzetközi felhő-megállapodásokat, ISO/IEC 19086 igazítja. Ez a szabvány szempontok a szervezetek számára, megkönnyíti a felhőre való áttérés döntéseket hozhat a összevetésére a cloud service-ajánlatokról kialakításánál, és egységes készletét kínálja.

A feladatlista elősegíti a felhőbeli, strukturált útmutatás és a egy egységes és megismételhető megközelítés nyújtó felhőszolgáltató kiválasztásakor alaposan ellenőrzött az áthelyezési.

Felhőre való áttérés már nem egyszerűen technológiai döntést. Ellenőrzőlista követelmények érintse meg a szervezet minden szempontból, mivel általuk kiszolgált hívja az összes fő belső-döntéshozók össze – az informatikai és adatvédelmi, valamint jogi, fennáll a veszélye, a beszerzés és megfelelőségi informatikai szakemberek. Ez növeli a döntéshozatal folyamata és hang indoklása csomópontokba előre nem látható akadályát valószínűségét való bevezetését az alapoktól döntések hatékonyságát.

Emellett az Ellenőrzőlista:

- Elérhetővé teszi a fő vitafórum témakörök döntéshozói a cloud bevezetési folyamat kezdetén.

- Alapos üzleti hozzászólások kapcsolatos szabályozásoknak és a szervezet saját célkitűzéseket támogatja az adatok biztonsági, adatvédelmi és személyes azonosításra alkalmas adatokat (PII).

- Segít a szervezeteknek, azonosíthatja a potenciális problémákat, amelyek hatással lehetnek a felhő-projekt.

- Kérdése van, a konzisztens biztosít az azonos feltételeket, definíciók, metrikákat és eszközök segítségével egyszerűsítheti azok összehasonlítja a különböző felhőszolgáltatók ajánlatait, minden szolgáltató számára.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Az Azure infrastruktúra és az alkalmazások biztonsági ellenőrzés céljából (észlelés)

[Az Azure Operational Security](https://docs.microsoft.com/azure/security/azure-operational-security) hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök, Microsoft Azure-ban.

![a biztonsági ellenőrzés (észlelés)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Az Azure Operational Security olyan keretrendszer, amely magában foglalja a különböző képességeket, amelyek a egyedülálló rendszereiből, például a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response központ program keresztül szerzett ismeretek épül , és a kiberbiztonsági fenyegetések világának.

### <a name="microsoft-azure-log-analytics"></a>A Microsoft Azure Naplóelemzés

[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) az IT-felügyeleti megoldást kínál a hibrid felhő áll. Önállóan használva vagy kiterjesztése a System Center-telepítéssel, Log Analytics a legnagyobb rugalmasságot és vezérlést biztosít az infrastruktúra felhőalapú kezelését.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

A Log Analytics-szel a felhőhöz, beleértve a helyszíni, Azure, az AWS, a Windows Server, Linux, VMware és OpenStack, mint az adatbázisszinten megoldások alacsonyabb költséggel lévő bármely példány segítségével kezelheti. A felhő-és felhőközpontú világ számára készült, a Log Analytics új megközelítést kínál az a vállalat, amely a leggyorsabb és leginkább költséghatékony megoldást új üzleti kihívásokat, valamint új terheléseket, alkalmazások és a felhőalapú környezetek kezeléséhez.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics) adatokat gyűjt a felügyelt erőforrások egy központi tárházba figyelési szolgáltatásokat biztosít. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ezzel a módszerrel konszolidálhatja a különféle forrásokból származó adatokat, így kombinálhatja az Azure-szolgáltatásokat a meglévő adatokat a helyszíni környezetben. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.

### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center a potenciális biztonsági hiányosságok azonosítása érdekében elemzi az Azure-erőforrások biztonsági állapotát. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán.

Példák erre vonatkozóan:

- Kártevőirtók kiépítése a kártékony szoftverek azonosításához és eltávolításához

- Hálózati biztonsági csoportok és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok konfigurálása

- Webalkalmazási tűzfalak kiépítése a webalkalmazások ellen irányuló támadások elleni védelemre

- Hiányzó rendszerfrissítések telepítése

- Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és az olyan partnermegoldások, mint például a kártevőirtó-programok és a tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

- Ismert kártékony IP-címmel kommunikáló feltört virtuális gépek

- Windows hibajelentés által észlelt speciális kártevő

- Virtuális gépek elleni találgatásos támadásokkal szemben

- Az integrált kártevőirtó programok és a tűzfalak biztonsági riasztásai

### <a name="azure-monitor"></a>Az Azure monitor

[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) mutatókat tartalmaznak információkat biztosít az adott típusú erőforrásokat. Vizualizáció, lekérdezés, útválasztás, riasztási, automatikus méretezés és mind az Azure-infrastruktúra (tevékenységnapló) és minden egyes Azure-erőforrás (diagnosztikai naplók) származó adatokon automation kínál.

A felhőalapú alkalmazások összetettek a részek. Győződjön meg arról, hogy az alkalmazás mindig elérhető fel az adatokat és kifogástalan állapotban fut figyelést biztosít. Segít, hogy ki a lehetséges problémák stave vagy korábbi kiépítettektől hibaelhárítása.

![Az Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) emellett segítségével monitorozási adatok mélyreható elemzéseket kaphat az alkalmazásról. A Tudásbázis segítségével javíthatja az alkalmazás teljesítménye vagy Karbantarthatóság, vagy, amelyek egyébként manuális beavatkozásra műveletek automatizálása.

A hálózati biztonsági naplózás létfontosságú a hálózati biztonsági rések és az IT-biztonsági és jogszabályi cégirányítási modell betartásáért. Biztonsági csoport nézet kérheti le a konfigurált hálózati biztonsági csoport és a biztonsági szabályokat, valamint az érvényben lévő biztonsági szabályokat. A alkalmazni szabályok listáját, és megadhatja, hogy a nyitott portok irányítását és ss hálózati biztonsági rések.

### <a name="network-watcher"></a>Hálózati figyelő

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet hálózati szinten lévő, a és az Azure-ból. A hálózati diagnosztikai és vizualizációs eszközök a Network Watcherrel elérhető segítenek megérteni, diagnosztizálása és betekintést nyerhet az Azure-ban a hálózati. A szolgáltatás része, csomagrögzítés, következő ugrási, IP-folyamat ellenőrzése, a biztonsági csoport nézet NSG-Folyamatnaplók. Forgatókönyv szintű monitorozása biztosítja a teljes körű képet szakembereket egyes hálózati erőforrások monitorozása a hálózati erőforrásokhoz.

### <a name="storage-analytics"></a>Storage Analytics

[A Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) tárolhatja, összesített és kapacitási adatait tartalmazzák a tárolási szolgáltatáshoz érkező kérések kapcsolatos metrikákat. Tranzakciók jelenti az API művelet szinten, valamint a storage szolgáltatás szintjén, és a storage szolgáltatás szintjén jelentett kapacitás. Metrikaadatok is használható, elemezheti a storage szolgáltatás használatát, a storage szolgáltatás kérelmekre kapcsolatos problémák diagnosztizálása és a egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.

### <a name="application-insights"></a>Application Insights

[Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az élő webalkalmazásának figyelésére használhatja. Automatikusan felismeri a teljesítményanomáliákat. Ez magában foglalja a nagy teljesítményű analitikai eszközök segítségével diagnosztizálhatja a problémákat, és megismerheti, mit a felhasználók az alkalmazását. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Alkalmazásaival együttműködik a helyileg üzemeltetett számos különböző platformokon, beleértve a .NET, Node.js és Java EE-alapú, vagy a felhőben. A devOps folyamattal integrálható, és rendelkezik kapcsolódási ponttal a különböző fejlesztési eszközöket.

A szolgáltatás az alábbiakat figyeli:

- **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó.

- **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.

- **Kivételek** – elemezheti az összesített statisztikákat, vagy konkrét példányok és részletesen a hívásláncot és a kapcsolódó kéréseket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

- **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.

- **AJAX-hívások weboldalakról** -értékek, válaszidők és hibaarányok.

- **Felhasználók és munkamenetek száma.**

- Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat.

- Dockerből vagy Azure-ból származó **gazdadiagnosztika**.

- Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.

- **Egyéni események és mérőszámok** , hogy, amelyeket Ön írt az ügyfél vagy kiszolgáló kódjában, és ezáltal üzleti eseményeket követhet, például eladott tételek, vagy megnyert játékok.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. [Az Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi, hogy az erőforrásokat a megoldás egy csoportként dolgozzon.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Megadhatja, hogy azok a megfelelő sorrendben legyenek telepítve erőforrások közti függőségeket.

- Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modell és a kívánt használta a változásokról, tekintse meg [Understanding Resource Manager-alapú és klasszikus üzembe helyezési](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>További lépések

További információ biztonságával kapcsolatban olvassa el részletes biztonsági témakörök el:

- [Audit és naplózás](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Kiberbűnözés](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Biztonságközpontú tervezés és üzemeltetés](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Titkosítás](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitás és hozzáférés-kezelés](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Hálózati biztonság](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Fenyegetések kezelése](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
