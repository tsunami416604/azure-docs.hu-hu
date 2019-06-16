---
title: Az Azure AD felhőalapú szolgáltatás a helyszíni számítási feladatok – Azure-kezelés
description: Ez a témakör ismerteti a felhőalapú szolgáltatás kezelése a helyszíni számítási feladatok esetében.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109521"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hogyan biztosít az Azure AD felhőalapú szolgáltatás a helyszíni számítási feladatok kezelése

Az Azure Active Directory (Azure AD) egy átfogó identitás több millió szervezetnél, identitás, a hozzáférés-kezelés és a biztonság minden aspektusát kiterjedő által használt szolgáltatási (IDaaS) megoldás. Azure ad-ben több mint egymilliárd felhasználói identitások tárolja, és segít a felhasználóknak, jelentkezzen be és érheti el biztonságosan is:

* Külső erőforrások, például a Microsoft Office 365, az Azure Portalon, és több ezer egyéb szoftver--szolgáltatásként (SaaS) alkalmazások.
* Belső erőforrásokat, például a szervezet vállalati hálózat és az intranetes, a szervezet által fejlesztett, felhőalapú alkalmazások és-alkalmazásokat.

Szervezetek használhatják az Azure AD, ha a "tiszta felhő", vagy egy hibrid, a telepítés, ha rendelkeznek a helyszíni számítási feladatokat. Az Azure AD hibrid telepítés stratégia migrálása a felhőbe az IT-eszközeivel, illetve integrálható a meglévő helyszíni infrastruktúrájával – új felhőalapú szolgáltatások mellett továbbra is egy szervezet része lehet.

"Hibrid" szervezetek korábban látott Azure ad-ben, meglévő kiterjesztése a helyszíni infrastruktúra. Ezeket az üzemelő példányokat, a helyszíni identitás cégirányítási felügyelete a Windows Server Active Directoryval vagy más belső könyvtár rendszerek az ellenőrzési pontok, és felhasználókat és csoportokat a rendszer szinkronizálja ezeket a rendszer egy felhőalapú címtár, például az Azure AD. Miután ezek identitások a felhőben, azok elérhetővé tehetők az Office 365-höz, az Azure és más alkalmazások.

![Identitás-életciklus](media/cloud-governed-management-for-on-premises//image1.png)

Ahogy a szervezetek több IT-infrastruktúráját és azok alkalmazásait a felhőbe helyezi át, számos keres a nagyobb biztonságot és egyszerűsített kezelési képességeire építkezik identitáskezelést szolgáltatásként. A felhőalapú IDaaS funkciók az Azure ad-ben való szabályozottabbá felhőfelügyelet azáltal, hogy a megoldások és funkciók lehetővé teszik a szervezetek gyorsan fogad el, és helyezze át a további, az Identitáskezelés, a hagyományos helyszíni felgyorsítása az Azure AD, miközben továbbra is támogatja a meglévő, valamint az olyan új alkalmazások rendszerek.

Ez a cikk ismerteti a hibrid IDaaS a Microsoft stratégiáját, és ismerteti, hogyan szervezetek számára az Azure ad-ben meglévő alkalmazásaik számára.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Az Azure ad-ben megközelítés szabályozottabbá Identitáskezelés felhőbe

Szervezetek számára a felhőre való áttérés, mint arról, hogy rendelkezik-e további biztonsági és a tevékenységeket, az automatizálást és a proaktív insights által támogatott további betekintést a teljes környezet – vezérlők biztosítékok van szükségük. "**Cloud management szabályozott**" útmutatás a szervezetek kezelése és szabályozása a felhasználók, alkalmazások, csoportok és a felhőből az eszközökre.

Ez a modern világ szervezetek kell fogja tudni felügyelni gyakorlatilag bármilyen méretekben és SaaS-alkalmazások elterjedése és az együttműködés és a külső identitások növekvő szerepe miatt. A felhő az új kockázati fekvő azt jelenti, hogy a szervezet nagyszámú kell lennie – egy rosszindulatú aktor, akik károsan befolyásolja a felhőbeli felhasználó hatással lehetnek a felhőalapú és helyszíni alkalmazásokban.

Különösen szervezetnek kell delegálni és automatizálhatja hibrid feladatokat, amelyek korábban informatikai manuálisan adta. Feladatok automatizálásához szükséges API-k és folyamatokat, amelyekkel (felhasználók, csoportok, alkalmazások, eszközök), különböző identitáshoz kapcsolódó erőforrások életciklusa, azokat is engedélyezheti – delegálja a napi szintű felügyeleti erőforrások kívül további egyéni felhasználók számára alapvető informatikai személyzetet tart fenn. Az Azure AD-címek felhasználóifiók-kezelés révén ezek a követelmények és hitelesítési anélkül, hogy a felhasználók számára a helyszíni identitás-infrastruktúrát. Nem létrehozni a helyszíni infrastruktúra előnyeit, például az üzleti partnerekkel, amely nem származnak, azok a helyszíni címtár, de amelynek hozzáférés-kezelés, kritikus fontosságú üzleti eredmények eléréséhez a felhasználók új Közösségek rendelkező szervezeteknek.

Emellett felügyeleti még nem fejeződött be, cégirányítási---nélkül és irányítás az ezen új világ egyik bővítménye, hanem az Identitáskezelő rendszerbe integrált része. Identitáskezelést az identitások kezelésére, és az alkalmazottak, üzleti partnerek és szállítók, és a szolgáltatások és alkalmazások keresztül férhetnek hozzá a életciklus lehetővé teszi a szervezetek számára.

Identitáskezelést beépítése megkönnyíti a szervezet számára való áttérés szabályozottabbá felhőfelügyelet engedélyezéséhez, lehetővé teszi, hogy informatikai méretezését, a vendégek új kihívásaira és részletesebb elemzéseket és automation, hogy ügyfeleink az volt, mint a helyszíni infrastruktúrát. Az új világban cégirányítási arra, hogy az átláthatóság, láthatóság és megfelelő szabályozza a hozzáférést a szervezeten belüli erőforrásokhoz a szervezet jelenti. Az Azure AD-biztonsági műveletek és a naplózási csapatok kinek van---és ki kell rendelkeznie – milyen erőforrásokat érhetik a szervezet (milyen eszközöket), mivel foglalkoznak az számukra a hozzáférést, és hogy rendelkezik-e a szervezet és megfelelő Távolítsa el, vagy korlátozza a hozzáférést a vállalati vagy szabályozási házirendek megfelelően szabályozza.

Az új felügyeleti modell számos előnyt biztosít SaaS- és az üzletági (LOB) alkalmazások, a szervezetek számára, azok egyszerűbben tudja kezeléséhez, és ezek az alkalmazások biztonságos elérését. Alkalmazások integrálása az Azure ad-ben, amelyet szervezetek tudnak használható és felügyelhető a hozzáférés mindkét felhőben, és a helyszíni identitások konzisztens módon adja meg. Alkalmazáséletciklus-kezelés több automatikus válik, és az Azure AD, amelynek könnyen elérhető, a helyszíni identitás-kezelés nem az alkalmazás használatának részletes betekintést biztosít. Keresztül az Azure AD-ben az Office 365-csoportok és a csapatok önkiszolgáló funkciókat, szervezetek is könnyedén létrehozhat csoportokat a hozzáférés-kezelés és együttműködési és felhasználók hozzáadása vagy eltávolítása lehetővé teszik az együttműködést, és a hozzáférés a felhőben.

Válassza a jobb az Azure AD képességeket szabályozottabbá felügyeleti attól függ, az alkalmazások felhőbeli, és ezeket az alkalmazásokat az Azure AD-vel integrált hogyan. Az alábbi szakaszok a módszer az AD-val integrált alkalmazásokat és összevonási protokollok (például az SAML, OAuth vagy OpenID Connect) használó alkalmazások szerkezeti.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Szabályozott cloud management Active Directoryba integrált alkalmazásokhoz

Azure ad-ben a szervezet helyszíni Active Directoryba integrált alkalmazások biztonságos távoli hozzáférést és a feltételes hozzáférés révén ezeknek az alkalmazásoknak a felügyeleti javítja. Emellett az Azure AD fiók életciklus-felügyelet és a felhasználó meglévő AD-fiókok, beleértve a hitelesítőadat-kezelés is biztosít:

* **Biztonságos távoli hozzáférést és a feltételes hozzáférés a helyszíni alkalmazások**

A legtöbb szervezet számára, az első lépés a hozzáférés kezelése a helyszíni Active Directoryval integrált webes és a távoli asztal-alapú alkalmazások a felhőből, hogy telepíti a [alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) elé ezek biztosítanak az alkalmazások biztonságossá tétele távoli hozzáférés.

Egy egyszeri bejelentkezés az Azure ad-hez, miután felhasználók hozzáférhessenek felhő- és a helyszíni alkalmazásokhoz egy külső URL-cím vagy egy belső alkalmazás portálon keresztül. Például az alkalmazásproxy biztosít távoli hozzáférést és egyszeri bejelentkezést a távoli asztal, a SharePoint, valamint az alkalmazások, például a Tableau és a Qlik és az üzletági (LOB) alkalmazások. Ezenkívül lehetnek a feltételes hozzáférési házirendek megjelenítése a [használati feltételeket tartalmazó fájl](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) és [hozzájuk biztosítása a felhasználó hozzájárult](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) előtt nem tudnak hozzáférni az alkalmazáshoz.

![Alkalmazásproxy-architektúra](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory-fiókok automatikus életciklus-kezelés**

Identitáskezelést segítségével a szervezetek közötti egyensúly elérése *termelékenység* ---milyen gyorsan egy személy férhet erőforrások szükségük van, például ha a szervezet csatlakoznak? ---és *biztonsági* ---hogyan kell hozzáférésüket időbeli változásait, például amikor az változik, hogy a címzett állapota? Identitás-életciklus-felügyelet identitáskezelést alapját, és hatékony szabályzását nagy méretekben igényel modernizálhatja alkalmazások identitás-életciklus felügyeleti infrastruktúrát.

A legtöbb szervezet számára az alkalmazottak identitás-életciklus kötődik leképezése az, hogy a felhasználó egy Emberierőforrás-kezelési (HCM) rendszerben. A szervezet számára a Workday használatával rendszerében HCM, az Azure AD biztosítható a felhasználói fiók az ad-ben [automatikusan kiépített és az adminisztratív munkatársak számára a Workday – figyelmeztetés megszüntetésről](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Továbbfejlesztett felhasználói termelékenység biztosítása birthright automatizálását, így érdeklődők fiókok és kezeli a kockázati úgy, hogy az alkalmazás hozzáférési automatikusan frissül a felhasználó megváltoztatja a szerepkörök, vagy elhagyja a szervezetet. A felhasználók Workday-központú létrehozásának [telepítési tervének](https://aka.ms/WorkdayDeploymentPlan) egy lépésenkénti útmutató, amely ismerteti, hogyan szervezetek ajánlott eljárások megvalósításával, Workday az Active Directory felhasználók átadásának megoldáshoz egy öt lépésből álló folyamat során.

Az Azure AD Premium is magában foglalja a rekordok importálhat más helyszíni HCM rendszerek, például SAP, a Microsoft Identity Manager Oracle eBusiness, és az Oracle PeopleSoft.

Vállalatközi együttműködés egyre inkább a szervezeten kívüli személyek hozzáférésének engedélyezésére van szükség. [Az Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) együttműködést lehetővé teszi a szervezetek biztonságosan megoszthassák alkalmazásaik és szolgáltatásaik vendég felhasználók és a külső partnerekkel irányítást a saját vállalati adatokat.

Azure ad-ben is [fiókok automatikus létrehozása az ad-ben a vendégfelhasználók számára](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) igény szerint vendégek való hozzáférés engedélyezése a helyszíni AD-val integrált alkalmazásokat anélkül, hogy egy másik jelszót. Szervezetek állíthat be [többtényezős hitelesítés (MFA) szabályzatok esetében a vendégfelhasználók](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s, így a többtényezős hitelesítés ellenőrzi az application proxy hitelesítése során történik. Emellett bármelyik [hozzáférési felülvizsgálatokkal](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) , hogy a felhasználók alkalmazni a helyszíni felhasználók felhőbeli B2B alapján történik. Például ha a felhő felhasználói életciklus-felügyeleti szabályzatokkal törlik, a helyszíni felhasználói is törlődik.

**Hitelesítőadat-kezelése az Active Directory-fiókok** az Azure AD meg, hogy az önkiszolgáló jelszó-visszaállítás lehetővé teszi, hogy a felhasználók, akik elfelejtette a jelszavát kell hitelesíteni, és új jelszót kérjenek, a módosított jelszavak [írása a helyszíni Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). A jelszó-visszállítási folyamatnak is használhatja a helyszíni Active Directory jelszóházirendek: Amikor a felhasználó alaphelyzetbe állítja a jelszavát, a rendszer ellenőrzi annak érdekében, hogy megfelel-e a helyszíni Active Directory-szabályzat, a címtárhoz véglegesítése előtt. Az önkiszolgáló jelszó-visszaállítási [telepítési tervének](https://aka.ms/deploymentplans/sspr) gyakorlati tanácsokat az önkiszolgáló jelszóátállítás bevezetése felhasználók webes és a Windows integrált felületeken keresztül.

![Az Azure AD SSPR architektúra](media/cloud-governed-management-for-on-premises/image3.png)

Végül a szervezet számára, hogy engedélyezheti a felhasználóknak, hogy módosítsák jelszavukat az ad-ben, AD konfigurálható ugyanaz a jelszó szabályzat használatára, mert a szervezet az Azure ad-ben keresztül használja a [Azure AD-jelszó védelmi funkció](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), jelenleg a nyilvános előzetes verziója.

Amikor egy szervezet áthelyezése a felhőbe Active Directoryba integrált alkalmazás áthelyezése az Azure-ba, az alkalmazást üzemeltető operációs rendszer készen áll a [Azure AD tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) biztosít az AD-kompatibilis tartományi szolgáltatások (például a tartományhoz való csatlakozás Csoportházirend, az LDAP és a Kerberos/NTLM-hitelesítés). Az Azure AD tartományi szolgáltatásokkal integrálható a szervezet meglévő Azure AD-bérlőjében teszi a felhasználók vállalati hitelesítői adataikkal. Emellett a meglévő csoportokat és felhasználói fiókok segítségével biztonságos hozzáférés az erőforrásokhoz, egy egyenletesebb "lift-and-shift" a helyszíni erőforrások az Azure infrastruktúra-szolgáltatások biztosítása.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Szabályozott felhőalapú felügyelet a helyszíni összevonási-alapú alkalmazások

Olyan szervezet már használja egy helyszíni identitásszolgáltatót áthelyezi az alkalmazásokat az Azure AD lehetővé teszi a biztonságosabb hozzáférés és egyszerűbb felügyeleti felületet nyújt az összevonás-kezelés. Az Azure AD lehetővé teszi, hogy részletesebb alkalmazásonkénti hozzáférés-vezérlők, többek között az Azure multi-factor Authentication, az Azure AD feltételes hozzáférés konfigurálása. Az Azure AD támogatja a további funkciókat, beleértve az alkalmazás-specifikus jogkivonat-aláíró tanúsítványok és a tanúsítványok konfigurálható lejárati dátumát. Ezen képességek, eszközöket és útmutatást engedélyezze a szervezetek számára, hogy azok a helyszíni identitás-szolgáltatóktól kivonása. A Microsoft saját informatikai, a példában egy át 17,987 alkalmazások a Microsoft belső Active Directory összevonási szolgáltatások (AD FS) az Azure ad-hez.

![Az Azure AD fejlődést szem előtt tartva](media/cloud-governed-management-for-on-premises/image5.png)

Migrálás megkezdéséhez tekintse meg az Azure AD Identitásszolgáltatóként, összevont alkalmazások https://aka.ms/migrateapps mutató hivatkozásokat tartalmaz, amelyek:

* A tanulmány [az alkalmazások áttelepítése az Azure Active Directoryhoz](https://aka.ms/migrateapps/whitepaper), amely megadja az áttelepítés előnyei és egyértelműen vázolt tételéhez négy fázisban áttelepítés tervezéséről: felderítés, besorolás, migrálás, és folyamatos felügyeletét. Gondolja át a folyamat és a projekt könnyen feldolgozható darabokra felosztania rendszer végigvezeti. A dokumentum segítséget nyújt a vizualizáción fontos erőforrásokra mutató hivatkozásokat is.

* A megoldási útmutató [áttelepítése alkalmazás hitelesítése az Active Directory összevonási szolgáltatások az Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) ismerteti részletesebben azonos tervezési és a egy alkalmazás migrálási projekt végrehajtása négy fázisa . Ebben az útmutatóban megismerheti, hogyan ezeket a fázisokat alkalmazni a megadott cél az Active Directory összevonási szolgáltatások (AD FS) az Azure AD-alkalmazások áthelyezését lesz.

* A [Active Directory összevonási szolgáltatások áttelepítési készültségi parancsfájl](https://aka.ms/migrateapps/adfstools) futtatni a meglévő helyszíni Active Directory összevonási szolgáltatások (AD FS) kiszolgálók meghatározásához készen áll-e az alkalmazások Azure ad-ben való migráláshoz.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Folyamatban lévő kezelési felhőalapú és helyszíni alkalmazásokban

Cégeknek szükségük van egy folyamat kezelésére méretezhető. Felhasználók továbbra is hozzáférési jogosultsága összeadódhatnak, és mi kezdetben a számukra kiosztott túli végül. Emellett vállalati szervezetek képeseknek kell lenniük fejlesztéséhez és hozzáférési házirend és a vezérlők folyamatos hatékony méretezést.

Általában az informatikai delegáltak eléréséhez jóváhagyási döntéseket hozhat, üzleti döntéshozók számára. Továbbá informatikai a felhasználók maguk is magában foglalhat. Például a vállalat marketing alkalmazás Európai bizalmas felhasználói adatokhoz hozzáférő felhasználók kell tudni, hogy a cég szabályzatait. Vendég felhasználók is észleli a kezelési követelmények, amelyre ezek meghívót kapott a szervezeten belüli adatok is.

Szervezetek számára a hozzáférés életciklus folyamat technológiákkal például automatizálható [dinamikus csoportok](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), összekapcsolt az való [SaaS-alkalmazások](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), vagy [alkalmazások a rendszer a tartományok közötti Identitáskezeléshez használt integrált (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) standard. Szervezetek is szabályozhatja amely [vendég felhasználók férhetnek hozzá a helyszíni alkalmazások](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Ezek a hozzáférési jogok is, majd rendszeresen ismétlődő használata vizsgálni [az Azure AD hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Jövőbeli irány

Hibrid környezetekben, a Microsoft stratégiáját, hogy központi telepítések engedélyezése, a **felhő az identitás a vezérlősík**, és a helyszíni címtárak és az egyéb identitáskezelési rendszerek, például az Active Directory és más helyi alkalmazások, a cél kialakítási hozzáféréssel rendelkező felhasználók. Ez a stratégia biztosítása a rights, az identitások és hozzáférés az ezen alkalmazások és számítási feladatok, amelyek ezekre épül, továbbra is. A befejezési állapota szervezetek lesz képes a meghajtó a végfelhasználói termelékenység teljes egészében a felhőben.

![Azure AD-architektúra](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>További lépések

További információ az első lépésekről a folyamaton, az Azure AD központi telepítési csomagokat, a helyen található: <https://aka.ms/deploymentplans> . Teljes körű útmutatást üzembe helyezése az Azure Active Directory (Azure AD-) képességeket biztosítanak. Minden csomag azt ismerteti, milyen üzleti értéket, a szempontokat, tervezési és üzemeltetési eljárások sikeres bevezetése az Azure AD-képességekhez szükséges. A Microsoft folyamatosan frissíti a központi telepítési csomagok adatnézetből származó rendszereit és más visszajelzéseit, amikor hozzáadjuk a funkciók felügyelete a felhőből az Azure AD-ajánlott eljárások.
