---
title: Azure AD felhőalapú felügyelet helyszíni számítási feladatokhoz – Azure
description: Ez a témakör a helyszíni számítási feladatok felhőalapú felügyeletét ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109521"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hogyan biztosítja az Azure AD a helyszíni számítási feladatok felhőalapú felügyeletét?

Az Azure Active Directory (Azure AD) egy átfogó identitás szolgáltatásként (IDaaS) megoldás, amelyet több millió szervezet használ, amelyek az identitás, a hozzáférés-kezelés és a biztonság minden aspektusára kiterjednek. Az Azure AD több mint egymilliárd felhasználói identitással rendelkezik, és segít a felhasználóknak bejelentkezni és biztonságosan elérni mindkettőt:

* Külső erőforrások, például a Microsoft Office 365, az Azure Portal és több ezer más szoftver-szolgáltatásként (SaaS) alkalmazás.
* Belső erőforrások, például a szervezet vállalati hálózatán és intranetjén lévő alkalmazások, valamint az adott szervezet által fejlesztett felhőalkalmazások.

A szervezetek használhatják az Azure AD-t, ha "tiszta felhő", vagy "hibrid" üzembe helyezésként, ha helyszíni számítási feladatokkal rendelkeznek. Az Azure AD hibrid telepítése része lehet egy stratégia egy szervezet számára, hogy migrálja az informatikai eszközök a felhőbe, vagy továbbra is integrálja a meglévő helyszíni infrastruktúra mellett az új felhőszolgáltatások.

Történelmileg a "hibrid" szervezetek az Azure AD-t a meglévő helyszíni infrastruktúrájuk kiterjesztéseként látták. Ezekben a központi telepítésekben a helyszíni identitásirányítási felügyelet, a Windows Server Active Directory vagy más házon belüli címtárrendszerek a vezérlőpontok, és a felhasználók és csoportok szinkronizálva vannak ezekből a rendszerekből egy felhőalapú címtárba, például az Azure AD. Miután ezek az identitások a felhőben vannak, elérhetővé tehetők az Office 365, az Azure és más alkalmazások számára.

![Identitás életciklusa](media/cloud-governed-management-for-on-premises//image1.png)

Ahogy a szervezetek az alkalmazásaikkal együtt egyre több informatikai infrastruktúrát helyeznek át a felhőbe, sokan az identitáskezelés hatékonyabb biztonsági és egyszerűbb felügyeleti képességeit keresik. Az Azure AD felhőalapú IDaaS-funkciói felgyorsítják a felhőalapú felügyeletre való áttérést azáltal, hogy olyan megoldásokat és képességeket biztosítanak, amelyek lehetővé teszik a szervezetek számára, hogy gyorsan alkalmazzák és áthelyezzék identitáskezelésüket a hagyományos helyszíni környezetből az Azure AD-be, miközben továbbra is támogatja a meglévő és az új alkalmazásokat.

Ez a dokumentum ismerteti a Microsoft hibrid IDaaS-ra vonatkozó stratégiáját, és ismerteti, hogy a szervezetek hogyan használhatják az Azure AD-t a meglévő alkalmazásaikhoz.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Az Azure AD-s megközelítés a felhőalapú identitáskezeléshez

Ahogy a szervezetek áttérnek a felhőre, biztosítékokra van szükségük, hogy a teljes környezetüket szabályozzák – nagyobb biztonságot és nagyobb rálátást a tevékenységekbe, amelyeket az automatizálás és a proaktív elemzések támogatnak. A "**Felhőalapú felügyelet**" azt ismerteti, hogy a szervezetek hogyan kezelik és szabályozzák a felhasználókat, alkalmazásokat, csoportokat és eszközöket a felhőből.

Ebben a modern világban a szervezeteknek képesnek kell lenniük hatékonyan kezelni a saas-alkalmazások elburjánzása, valamint az együttműködés és a külső identitások növekvő szerepe miatt. A felhő új kockázati környezete azt jelenti, hogy a szervezetnek jobban kell reagálnia – egy rosszindulatú szereplő, aki behatol egy felhőalapú felhasználóba, hatással lehet a felhőalapú és a helyszíni alkalmazásokra.

A hibrid szervezeteknek különösen képesnek kell lenniük a feladatok delegálására és automatizálására, amelyeket korábban manuálisan végzett. A feladatok automatizálásához olyan API-kra és folyamatokra van szükségük, amelyek koordinálják a különböző identitással kapcsolatos erőforrások (felhasználók, csoportok, alkalmazások, eszközök) életciklusát, hogy az erőforrások napi kezelését több, az identitáson kívüli személynek delegálhassák. alapvető informatikai személyzet. Az Azure AD a felhasználói fiókok kezelésével és a felhasználók natív hitelesítésével a helyszíni identitás-infrastruktúra megkövetelése nélkül kezeli ezeket a követelményeket. A helyszíni infrastruktúra kiépítésének elnem nem imázsa olyan szervezetek számára is előnyös lehet, amelyek új felhasználói közösségekkel rendelkeznek, például üzleti partnerekkel, amelyek nem a helyszíni címtárból származnak, de amelyek hozzáférés-kezelése kritikus fontosságú az üzleti eredmények eléréséhez.

Ezen túlmenően, a felügyelet nem teljes irányítás nélkül --- és a kormányzás ebben az új világban egy integrált része az identitásrendszer helyett egy add-on. Az identitás-irányítás lehetővé teszi a szervezetek számára, hogy kezeljék az identitást és a hozzáférési életciklust az alkalmazottak, az üzleti partnerek és a szállítók, valamint a szolgáltatások és alkalmazások között.

Az identitáskezelés beépítése megkönnyíti a szervezet felhőalapú felügyeletre való áttérését, lehetővé teszi az informatikai szolgáltatások méretezését, a vendégekkel szembeni új kihívások kezelését, valamint az ügyfeleknél az ügyfeleknél megfogalmazottnál mélyebb betekintést és automatizálást biztosít. helyszíni infrastruktúrát. A vállalatirányítás ebben az új világban azt jelenti, hogy a szervezet nek lehetősége van átláthatóságra, láthatóságra és megfelelő szabályozásra az erőforrásokhoz való hozzáférésben a szervezeten belül. Az Azure AD-vel a biztonsági műveletek és a naplózási csoportok láthatják, hogy ki rendelkezik --- és kinek kell rendelkeznie - hozzáférés a szervezet erőforrásaihoz (milyen eszközökön), milyen felhasználók csinálnak ezzel a hozzáféréssel, és hogy a szervezet rendelkezik-e és használja-e a megfelelő a hozzáférés megszüntetésére vagy korlátozására vonatkozó ellenőrzések a vállalati vagy szabályozási irányelveknek megfelelően.

Az új felügyeleti modell mind az SaaS, mind az üzletági (LOB) alkalmazásokkal rendelkező szervezetek számára előnyös, mivel könnyebben kezelhetik és biztosíthatják az alkalmazásokhoz való hozzáférést. Az alkalmazások Azure AD-vel való integrálásával a szervezetek a felhőbeli és a helyszíni identitások közötti hozzáférést is használhatják és kezelhetik. Az alkalmazások életciklus-kezelése egyre automatizáltabbá válik, és az Azure AD részletes betekintést nyújt az alkalmazáshasználatba, amely nem volt könnyen elérhető a helyszíni identitáskezelésben. Az Azure AD, az Office 365-csoportok és a Teams önkiszolgáló funkcióin keresztül a szervezetek egyszerűen létrehozhatnak csoportokat a hozzáférés-kezeléshez és az együttműködéshez, és felhasználókat vehetnek fel vagy távolíthatnak el a felhőben, hogy lehetővé tegyék az együttműködési és hozzáférés-kezelési követelményeket.

A felhőalapú felügyelethez tartozó azure-ad-képességek kiválasztása a használandó alkalmazásoktól és attól függ, hogy ezek az alkalmazások hogyan lesznek integrálva az Azure AD-vel. A következő szakaszok ismertetik az AD-integrált alkalmazások és összevonási protokollokat (például SAML, OAuth vagy OpenID Connect) használó alkalmazások megközelítéseit.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Felhőalapú felügyelet az AD-vel integrált alkalmazásokhoz

Az Azure AD biztonságos táveléréssel és feltételes hozzáféréssel javítja a szervezet helyszíni Active Directory-integrált alkalmazásai felügyeletét. Emellett az Azure AD fiókéletciklus-kezelést és hitelesítő adatok kezelését is biztosítja a felhasználó meglévő AD-fiókjaihoz, többek között a következőket:

* **Biztonságos távelérés és feltételes hozzáférés a helyszíni alkalmazásokhoz**

Számos szervezet számára az első lépés a helyszíni AD-integrált webes és távoli asztali alkalmazások felhőből való hozzáférésének kezeléséhez az [alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) telepítése az alkalmazások előtt a biztonságos távoli hozzáférés biztosítása érdekében.

Az Azure AD-be való egyszeri bejelentkezés után a felhasználók egy külső URL-címen vagy egy belső alkalmazásportálon keresztül is elérhetik a felhőalapú és a helyszíni alkalmazásokat. Az Alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosít a Távoli asztalhoz, a SharePointhoz, valamint olyan alkalmazásokhoz, mint a Tableau és a Qlik, valamint az üzletági (LOB) alkalmazásokba. Továbbá a feltételes hozzáférés-házirendek közé tartozhat a [használati feltételek](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) megjelenítése, és [annak biztosítása, hogy a felhasználó beleegyezett, mielőtt](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) hozzáférhetne egy alkalmazáshoz.

![Alkalmazásproxy architektúra](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatikus életciklus-kezelés Az Active Directory-fiókokhoz**

Az identitás-szabályozás segít a szervezeteknek egyensúlyt teremteni a *termelékenység* --- milyen gyorsan férhet hozzá egy személy a szükséges erőforrásokhoz, például amikor csatlakozik a szervezethez? --- és *biztonsági* --- hogyan kell a hozzáférésük idővel változnia, például amikor az adott személy foglalkoztatási státusza megváltozik? Az identitáséletciklus-kezelés az identitáskezelés alapja, és a hatékony nagy mértékű szabályozás az alkalmazások identitáséletciklus-kezelési infrastruktúrájának modernizálását igényli.

Számos szervezet esetében az alkalmazottak identitásának életciklusa az adott felhasználó humántőke-kezelési (HCM) rendszerben való megjelenítéséhez kötődik. A Workday-t HCM-rendszerként használó szervezetek számára az Azure AD biztosíthatja, hogy az AD felhasználói fiókjai [automatikusan kilegyenek és a Workday dolgozói számára kilegyenek építve.](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) Ez a születési joghoz képes fiókok automatizálása révén javítja a felhasználói hatékonyságot, és kezeli a kockázatot azáltal, hogy biztosítja az alkalmazáshozzáférés automatikus annektálását, amikor a felhasználó szerepkört módosít, vagy kilép a szervezetből. A Workday által vezérelt felhasználói kiépítési [telepítési terv](https://aka.ms/WorkdayDeploymentPlan) egy lépésről-lépésre útmutató, amely végigvezeti a szervezeteket a Workday ajánlott eljárások megvalósításán keresztül az Active Directory felhasználói kiépítési megoldásához egy öt lépéses folyamat során.

Az Azure AD Premium tartalmazza a Microsoft Identity Managert is, amely más helyszíni HCM-rendszerekből, például az SAP-ból, az Oracle eBusiness-ből és az Oracle PeopleSoft-ból importálhat rekordokat.

A vállalkozások közötti együttműködéshez egyre inkább szükség van a szervezeten kívüli személyek számára való hozzáférés megadására. [Az Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) együttműködés lehetővé teszi a szervezetek számára, hogy biztonságosan megosszák alkalmazásaikat és szolgáltatásaikat a vendégfelhasználókkal és külső partnerekkel, miközben fenntartják a saját vállalati adataik feletti ellenőrzést.

Az Azure AD [szükség szerint automatikusan létrehozhat fiókokat az AD-ben a vendégfelhasználók számára,](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) így az üzleti vendégek egy másik jelszó nélkül férhetnek hozzá a helyszíni AD-be integrált alkalmazásokhoz. A szervezetek [többtényezős hitelesítési (MFA) házirendeket állíthatnak be a vendégfelhasználók számára,](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)így az MFA-ellenőrzések az alkalmazásproxy-hitelesítés során történnek. Emellett a felhőbeli B2B-felhasználókon végzett [hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) a helyszíni felhasználókra vonatkoznak. Ha például a felhőbeli felhasználó törlődik az életciklus-kezelési szabályzatok, a helyszíni felhasználó is törlődik.

**Az Active Directory-fiókok hitelesítő adatainak kezelése** Az Azure AD önkiszolgáló jelszó-visszaállítása lehetővé teszi a jelszavukat elfelejtő felhasználók számára a jelszó újbóli hitelesítését és a jelszavak alaphelyzetbe állítását, a módosított jelszavakat a [helyszíni Active Directoryba írva.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) A jelszó-visszaállítási folyamat is használhatja a helyszíni Active Directory jelszóházirendek: Amikor egy felhasználó alaphelyzetbe állítja a jelszavát, ellenőrzi, hogy megfelel-e a helyszíni Active Directory-házirendnek, mielőtt véglegesíti azt a könyvtárba. Az önkiszolgáló jelszó-visszaállítási [telepítési terv](https://aka.ms/deploymentplans/sspr) ismerteti az önkiszolgáló jelszó-visszaállítás webes és Windows-integrált szolgáltatásokon keresztül történő visszaállításának gyakorlati tanácsait.

![Azure AD SSPR architektúra](media/cloud-governed-management-for-on-premises/image3.png)

Végül a szervezetek, amelyek lehetővé teszik a felhasználók számára, hogy változtassák meg a jelszavukat az AD-ben, az AD konfigurálható, hogy ugyanazt a jelszóházirendet használja, mint a szervezet az Azure AD-ben az [Azure AD jelszavas védelmi szolgáltatáson](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)keresztül, jelenleg nyilvános előzetes verzióban.

Ha egy szervezet készen áll arra, hogy egy AD-be integrált alkalmazást helyezzen át a felhőbe az alkalmazást üzemeltető operációs rendszer Azure-ba való áthelyezésével, az [Azure AD tartományi szolgáltatások AD-kompatibilis](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) tartományi szolgáltatásokat (például tartományhoz való csatlakozást, csoportházirendet, LDAP-t és Kerberos/NTLM-hitelesítést) biztosít. Az Azure AD tartományi szolgáltatások integrálhatók a szervezet meglévő Azure AD-bérlőjével, lehetővé téve a felhasználók számára, hogy vállalati hitelesítő adataikkal jelentkezzenek be. Emellett a meglévő csoportok és felhasználói fiókok az erőforrásokhoz való hozzáférés biztosítására is használhatók, így biztosítva a helyszíni erőforrások zökkenőmentesebb "lift-and-shift" szolgáltatását az Azure-infrastruktúra-szolgáltatásokhoz.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Felhőalapú felügyelet a helyszíni összevonási alapú alkalmazásokhoz

Egy szervezet, amely már használja a helyszíni identitás-szolgáltató, az alkalmazások áthelyezése az Azure AD biztonságosabb hozzáférést és egyszerűbb felügyeleti élményt az összevonási felügyelet. Az Azure AD lehetővé teszi az alkalmazásonkénti részletes hozzáférés-vezérléskonfigurálását, beleértve az Azure Többtényezős hitelesítést is az Azure AD feltételes hozzáférés használatával. Az Azure AD további képességeket támogat, beleértve az alkalmazásspecifikus jogkivonat-aláíró tanúsítványokat és a konfigurálható tanúsítvány lejárati dátumait. Ezek a képességek, eszközök és útmutatások lehetővé teszik a szervezetek számára, hogy kivonják a helyszíni identitásszolgáltatókat. A Microsoft saját informatikai rendszere például 17 987 alkalmazást helyezett át a Microsoft belső Active Directory összevonási szolgáltatásaiból (AD FS) az Azure AD-be.

![Az Azure AD fejlődése](media/cloud-governed-management-for-on-premises/image5.png)

Az összevont alkalmazások azure AD-be identitásszolgáltatóként történő https://aka.ms/migrateapps áttelepítésének megkezdéséhez tekintse meg a következő hivatkozásokat:

* Az [alkalmazások áttelepítése az Azure Active Directoryba](https://aka.ms/migrateapps/whitepaper)című tanulmány, amely bemutatja az áttelepítés előnyeit, és ismerteti, hogyan tervezheti meg az áttelepítést négy világosan körvonalazott fázisban: felderítés, besorolás, áttelepítés és folyamatos felügyelet. Végigvezeti, hogyan gondolkodhat a folyamatról, és hogyan bonthatja le a projektet könnyen fogyasztható darabokra. A dokumentum ban olyan fontos forrásokra mutató hivatkozások találhatók, amelyek segítenek az út során.

* A megoldásútmutató [az alkalmazáshitelesítés áttelepítése az Active Directory összevonási szolgáltatásokból az Azure Active Directoryba](https://aka.ms/migrateapps/adfssolutionguide) részletesebben feltárja az alkalmazásáttelepítési projekt tervezésének és végrehajtásának ugyanazt a négy fázisát. Ebben az útmutatóban megtudhatja, hogyan alkalmazhatja ezeket a fázisokat az active directory összevonási szolgáltatásokból (AD FS) az Azure AD-be való áthelyezésének konkrét céljára.

* Az [Active Directory összevonási szolgáltatások áttelepítési készenléti parancsfájlfuttatható](https://aka.ms/migrateapps/adfstools) a meglévő helyszíni Active Directory összevonási szolgáltatások (AD FS) kiszolgálókon, hogy meghatározza az alkalmazások készen áll az Azure AD-be való áttelepítéshez.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Folyamatos hozzáférés-kezelés felhőbeli és helyszíni alkalmazásokban

A szervezeteknek méretezhető hozzáférés kezeléséhez folyamatra van szükségük. A felhasználók továbbra is felhalmozódnak a hozzáférési jogokat, és a végén túl az eredetileg kiépített számukra. Emellett a vállalati szervezeteknek képesnek kell lenniük a hatékony méretezésre a hozzáférési házirendek és -vezérlők folyamatos fejlesztése és érvényesítése érdekében.

Az informatikai részleg általában hozzáférést biztosít az üzleti döntéshozóknak a jóváhagyási döntésekhez. Továbbá, az it is bevonja a felhasználók magukat. Például azoknak a felhasználóknak, akik egy vállalat európai marketingalkalmazásában férnek hozzá a bizalmas ügyféladatokhoz, ismernie kell a vállalat irányelveit. Előfordulhat, hogy a vendégfelhasználók nincsenek tisztában az olyan szervezet ben lévő adatok kezelési követelményeivel, amelyekhez meghívást kaptak.

A szervezetek automatizálhatják a hozzáférési életciklus folyamatát olyan technológiákkal, mint a [dinamikus csoportok,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)a felhasználók [SaaS-alkalmazásokba](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)való kiépítéssel vagy [a Tartományok közötti identitáskezelés (SCIM) szabványhasználatával integrált alkalmazásokkal.](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) A szervezetek azt is szabályozhatják, hogy mely [vendégfelhasználók férhetnek hozzá a helyszíni alkalmazásokhoz.](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) Ezek a hozzáférési jogok ezután rendszeresen felülvizsgálhatók az ismétlődő [Azure AD-hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)használatával.

## <a name="future-directions"></a>Jövőbeli irányok

Hibrid környezetekben a Microsoft stratégiája az, hogy engedélyezze az olyan központi telepítéseket, ahol a **felhő az identitás vezérlősíkja,** és a helyszíni könyvtárak és más identitáskezelő rendszerek, például az Active Directory és más helyszíni alkalmazások a hozzáféréssel rendelkező felhasználók kiépítésének célpontjai. Ez a stratégia továbbra is biztosítja a jogokat, identitásokat és hozzáférést azokban az alkalmazásokban és számítási feladatokban, amelyek rájuk támaszkodnak. Ebben a végső állapotban a szervezetek teljes mértékben a felhőből tudják előhajtani a végfelhasználók termelékenységét.

![Azure AD-architektúra](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>További lépések

Az utazás megkezdéséről az Azure AD telepítési csomagjai című <https://aka.ms/deploymentplans> témakörben talál további információt. Teljes körű útmutatást nyújtanak az Azure Active Directory (Azure AD) képességeinek üzembe helyezéséhez. Minden terv ismerteti az üzleti értéket, a tervezési szempontokat, a tervezést és az azure-beli AD-képességek sikeres bevezetéséhez szükséges működési eljárásokat. A Microsoft folyamatosan frissíti a telepítési terveket az ügyfél-telepítésekből és más visszajelzésekből származó gyakorlati tanácsokkal, amikor új képességeket adunk hozzá a felhőből való kezeléshez az Azure AD-vel.
