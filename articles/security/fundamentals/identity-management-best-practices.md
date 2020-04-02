---
title: Az Azure-identitás & a biztonsági gyakorlati tanácsokhoz való hozzáféréshez | Microsoft dokumentumok
description: Ez a cikk az Azure-képességek beépített használatával az identitáskezelés és a hozzáférés-vezérlés ajánlott eljárások készletét tartalmazza.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: ffd9919092cdf2481767e58f10ba6525d56ca4a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548457"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Az Azure-beli identitáskezelés és hozzáférés-vezérlés ajánlott biztonsági eljárásai

Ebben a cikkben az Azure-identitáskezelés és a hozzáférés-vezérlés biztonsági gyakorlati tanácsok gyűjteménye. Ezek az ajánlott eljárások az [Azure AD-vel](../../active-directory/fundamentals/active-directory-whatis.md) kapcsolatos tapasztalatainkból és az önhöz hasonló ügyfelek tapasztalataiból származnak.

Minden egyes bevált gyakorlathoz elmagyarázzuk a következőket:

* Mi a legjobb gyakorlat?
* Miért szeretné engedélyezni ezt a bevált gyakorlatot?
* Mi lehet az eredmény, ha nem engedélyezi a legjobb gyakorlatot?
* A legjobb gyakorlat lehetséges alternatívái
* Hogyan tanulhatja meg a legjobb gyakorlat engedélyezését?

Ez az Azure-identitáskezelés és hozzáférés-vezérlés biztonsági gyakorlati tanácsok cikk konszenzusos véleményen és az Azure platform képességek és szolgáltatáskészletek, acikk írásakor.

A szándék írásban ezt a cikket, hogy egy általános ütemtervet, hogy egy robusztusabb biztonsági testtartás telepítése után által vezetett["5 lépést, hogy biztosítsa a személyazonossági infrastruktúra](steps-secure-identity.md)" ellenőrzőlista, amely végigvezeti Önt néhány alapvető funkciók és szolgáltatások.

A vélemények és a technológiák idővel változnak, és ezt a cikket rendszeresen frissítik, hogy tükrözzék ezeket a változásokat.

Az Azure-identitáskezelés és a hozzáférés-vezérlés biztonsági gyakorlati tanácsok ebben a cikkben tárgyalt a következők:

* Azonosidentitás kezelése elsődleges biztonsági kerületként
* Identitáskezelés központosítása
* Csatlakoztatott bérlők kezelése
* Egyszeri bejelentkezés engedélyezése
* Feltételes hozzáférés bekapcsolása
* A rendszeres biztonsági fejlesztések megtervezése
* Jelszókezelés engedélyezése
* Többtényezős ellenőrzés kényszerítése a felhasználók számára
* Szerepköralapú hozzáférés-vezérlés alkalmazása
* A kiemelt jogosultságú fiókok alacsonyabb kitettsége
* Az erőforrások helyét kezelő helyek ellenőrzése
* Az Azure AD használata a tárolási hitelesítéshez

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Azonosidentitás kezelése elsődleges biztonsági kerületként

Sokan úgy vélik, hogy az identitás a biztonság elsődleges határa. Ez a váltás a hagyományos hangsúly a hálózati biztonság. A hálózati peremek egyre porózusabbak, és ez a peremvédelem nem lehet olyan hatékony, mint a [BYOD-eszközök](https://aka.ms/byodcg) és a felhőalapú alkalmazások robbanása előtt volt.

[Az Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) az Azure-megoldás az identitás- és hozzáférés-kezeléshez. Az Azure AD egy több-bérlős, felhőalapú címtár- és identitáskezelési szolgáltatás a Microsofttól. Egyetlen megoldásban egyesíti az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-kezelést és az identitásvédelmet.

Az alábbi szakaszok az Azure AD használatával az identitás- és hozzáférés-biztonsággal kapcsolatos gyakorlati tanácsokat sorolják fel.

**Ajánlott eljárás:** A biztonsági vezérlők és észlelések középpontba kerülése a felhasználói és szolgáltatásidentitások körül.
**Részlet:** Az Azure AD használatával a vezérlők és identitások közös en.

## <a name="centralize-identity-management"></a>Identitáskezelés központosítása

Hibrid [identitásesetén](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) azt javasoljuk, hogy integrálja a helyszíni és a felhőbeli könyvtárakat. Az integráció lehetővé teszi, hogy az informatikai csapat egy helyről kezelje a fiókokat, függetlenül attól, hogy hol jön létre egy fiók. Az integráció a felhőbeli és a helyszíni erőforrások eléréséhez közös identitás biztosításával is segíti a felhasználókat abban, hogy hatékonyabbak legyenek.

**Ajánlott eljárás:** Hozzon létre egy Azure AD-példányt. A konzisztencia és az egységes mérvadó források növelik az egyértelműséget, és csökkentik az emberi hibákból és a konfiguráció összetettségéből eredő biztonsági kockázatokat.
**Részlet:** Jelöljön ki egyetlen Azure AD-címt a vállalati és szervezeti fiókok mérvadó forrásaként.

**Ajánlott eljárás:** Integrálja a helyszíni könyvtárakat az Azure AD-vel.  
**Részlet:** Az [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) segítségével szinkronizálja a helyszíni címtár a felhőkönyvtárat.

> [!Note]
> Vannak olyan [tényezők, amelyek befolyásolják az Azure AD Connect teljesítményét.](../../active-directory/hybrid/plan-connect-performance-factors.md) Győződjön meg arról, hogy az Azure AD Connect elegendő kapacitással rendelkezik ahhoz, hogy megakadályozza az alulteljesítő rendszerek biztonságát és termelékenységét. Nagy vagy összetett szervezetek (több mint 100 000 objektumot létesítő szervezetek) követniük kell a [javaslatokat](../../active-directory/hybrid/whatis-hybrid-identity.md) az Azure AD Connect-megvalósítás optimalizálásához.

**Ajánlott eljárás:** Ne szinkronizálja a fiókokat az Azure AD,amely magas jogosultságokkal rendelkezik a meglévő Active Directory-példányban.
**Részlet:** Ne módosítsa az [alapértelmezett Azure AD Connect-konfigurációt,](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) amely kiszűri ezeket a fiókokat. Ez a konfiguráció csökkenti annak kockázatát, hogy az ellenfelek a felhőből a helyszíni eszközökre fordulnak (ami súlyos incidenst okozhat).

**Ajánlott eljárás:** Kapcsolja be a jelszókivonat-szinkronizálást.  
**Részlet:** A jelszókivonat-szinkronizálás olyan szolgáltatás, amely a felhasználói jelszókivét szinkronizálására szolgál egy helyszíni Active Directory-példányból egy felhőalapú Azure AD-példányba. Ez a szinkronizálás segít a korábbi támadásokból kiszivárgott hitelesítő adatok újbóli lejátszása ellen.

Még akkor is beállíthatja a jelszókivonat-szinkronizálást biztonsági másolatként, ha a helyszíni kiszolgálók meghibásodnak, vagy átmenetileg elérhetetlenné válnak. Ez a szinkronizálás lehetővé teszi a felhasználók számára, hogy jelentkezzen be a szolgáltatásba ugyanazt a jelszót, hogy az általuk használt bejelentkezni a helyszíni Active Directory-példány. Azt is lehetővé teszi, hogy az Identity Protection észlelje a feltört hitelesítő adatokat a szinkronizált jelszókivét és az ismerten feltört jelszavak összehasonlításával, ha a felhasználó ugyanazt az e-mail címet és jelszót használta más szolgáltatásokon, amelyek nem kapcsolódnak az Azure AD-hez.

További információ: [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect-szinkronizálással.](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)

**Ajánlott eljárás:** Az új alkalmazásfejlesztéshez használja az Azure AD-t a hitelesítéshez.
**Részlet**: A megfelelő képességekkel támogatható a hitelesítés:

  - Azure AD az alkalmazottaknak
  - [Azure AD B2B](../../active-directory/b2b/index.yml) vendégfelhasználók nak és külső partnereknek
  - [Az Azure AD B2C](../../active-directory-b2c/index.yml) segítségével szabályozhatja, hogy az ügyfelek hogyan regisztráljanak, jelentkezzenek be és kezeljék a profiljukat az alkalmazások használata során

Azok a szervezetek, amelyek nem integrálják a helyszíni identitásukat a felhőalapú identitásukkal, nagyobb terhelést kaphatnak a fiókok kezelésében. Ez a többletterhelés növeli a hibák és a biztonsági incidensek valószínűségét.

> [!Note]
> Ki kell választania, hogy mely könyvtárakban lesznek kritikus fiókok, és hogy a használt felügyeleti munkaállomást új felhőszolgáltatások vagy meglévő folyamatok kezelik-e. A meglévő felügyeleti és identitás-létesítési folyamatok használata csökkentheti a kockázatokat, de azt is megkockáztathatja, hogy a támadó veszélyezteti a helyszíni fiókot, és a felhőbe fordul. Előfordulhat, hogy különböző szerepkörökhöz (például informatikai rendszergazdákhoz és részleggazdákhoz) szeretne másik stratégiát használni. Itt két lehetősége van. Az első lehetőség az, hogy hozzon létre azure AD-fiókok, amelyek nincsenek szinkronizálva a helyszíni Active Directory-példány. Csatlakozzon a rendszergazdai munkaállomáshoz az Azure AD-hez, amelyet a Microsoft Intune használatával kezelhet és javíthat. A második lehetőség a meglévő rendszergazdai fiókok használata a helyszíni Active Directory-példánysal való szinkronizálással. A felügyelet és a biztonság érdekében használja az Active Directory tartomány meglévő munkaállomásait.

## <a name="manage-connected-tenants"></a>Csatlakoztatott bérlők kezelése
A biztonsági szervezetnek láthatóságra van szüksége a kockázatok felméréséhez és annak meghatározásához, hogy a szervezet szabályzatait és a szabályozási követelményeket betartják-e. Győződjön meg arról, hogy a biztonsági szervezet az éles környezethez és hálózathoz (az [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) vagy a helyek közötti [VPN- en](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)keresztül) kapcsolódó összes előfizetést láthatja. Az Azure AD [globális rendszergazdája/vállalati rendszergazdája](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) magasabb szintre emelheti a [felhasználói hozzáférés-rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkörhöz való hozzáférését, és megtekintheti a környezethez kapcsolódó összes előfizetést és felügyelt csoportot.

Tekintse meg a hozzáférés magasabb szintű kezelését [az összes Azure-előfizetés és felügyeleti csoport kezeléséhez,](../../role-based-access-control/elevate-access-global-admin.md) hogy Ön és a biztonsági csoport megtekinthesse a környezetéhez kapcsolódó összes előfizetést vagy felügyeleti csoportot. A fokozott hozzáférés megszüntetéséhez, miután felmérte a kockázatokat.

## <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

A mobilos, felhőalapú világban bárhonnan engedélyezni szeretné az egyszeri bejelentkezést az eszközökre, alkalmazásokra és szolgáltatásokra, hogy a felhasználók bárhol és bármikor hatékonyan dolgozhassanak. Ha több identitáskezelési megoldást kell kezelnie, ez nemcsak az informatikai, hanem a több jelszóra emlékező felhasználók számára is adminisztratív problémává válik.

Ugyanazt az identitáskezelési megoldást használja az összes alkalmazáshoz és erőforráshoz, így sso érhető el. A felhasználók ugyanazokat a hitelesítő adatokat használhatják a bejelentkezéshez és a szükséges erőforrások eléréséhez, függetlenül attól, hogy az erőforrások a helyszínen vagy a felhőben találhatók.And your users can use the same set of credentials to sign in and access the resources that they need, whether the resources are located on-premises or in the cloud.

**Ajánlott eljárás:** SSO engedélyezése.  
**Részletes:** Az Azure AD [kiterjeszti a helyszíni Active Directoryt](/azure/active-directory/connect/active-directory-aadconnect) a felhőre. A felhasználók használhatják az elsődleges munkahelyi vagy iskolai fiók a tartományhoz csatlakozott eszközök, vállalati erőforrások, valamint az összes webes és SaaS-alkalmazások, amelyek a munkájukelvégzéséhez szükséges. A felhasználóknak nem kell több felhasználónevet és jelszót megjegyezniük, és alkalmazásuk hozzáférése automatikusan kiépíthető (vagy megszüntethető) a szervezeti csoporttagságaik és az alkalmazotti állapotuk alapján. A katalógusban szereplő alkalmazások, illetve az [Azure AD-alkalmazásproxy](/azure/active-directory/active-directory-application-proxy-get-started) használatával kifejlesztett és közzétett saját helyszíni alkalmazások hozzáférése is szabályozható.

Az SSO használatával engedélyezheti a felhasználóknak, hogy az Azure AD-ben a munkahelyi vagy iskolai fiókjuk alapján hozzáférjenek [saas-alkalmazásaikhoz.](/azure/active-directory/active-directory-appssoaccess-whatis) Ez nem csak a Microsoft SaaS-alkalmazásokra vonatkozik, hanem más alkalmazásokra is, például a [Google Apps és](/azure/active-directory/active-directory-saas-google-apps-tutorial) a [Salesforce alkalmazásokra.](/azure/active-directory/active-directory-saas-salesforce-tutorial) Konfigurálhatja az alkalmazást, hogy az Azure [AD-t SAML-alapú identitásszolgáltatóként](/azure/active-directory/fundamentals-identity) használja. Biztonsági vezérlőként az Azure AD nem ad ki olyan jogkivonatot, amely lehetővé teszi a felhasználók számára, hogy jelentkezzen be az alkalmazásba, kivéve, ha az Azure AD-n keresztül kaptak hozzáférést. Közvetlenül vagy olyan csoporton keresztül is engedélyezhet hozzáférést, amelynek a felhasználók tagjai.

Azok a szervezetek, amelyek nem hoznak létre közös identitást a felhasználók és alkalmazások sso létrehozásához, jobban ki vannak téve olyan eseteknek, ahol a felhasználók több jelszóval rendelkeznek. Ezek a forgatókönyvek növelik annak valószínűségét, hogy a felhasználók újra felhasználják a jelszavakat, vagy gyenge jelszavakat használnak.

## <a name="turn-on-conditional-access"></a>Feltételes hozzáférés bekapcsolása

A felhasználók bárhonnan elérhetik a szervezet erőforrásait különböző eszközök és alkalmazások használatával. Informatikai rendszergazdaként győződjön meg arról, hogy ezek az eszközök megfelelnek a biztonsági és megfelelőségi szabványoknak. Már nem elegendő arra összpontosítani, hogy ki férhet hozzá egy erőforráshoz.

A biztonság és a termelékenység egyensúlyának megteremtéséhez át kell gondolnia, hogyan érhető el egy erőforrás, mielőtt döntést hozna a hozzáférés-vezérléssel kapcsolatban. Az Azure AD feltételes hozzáférés, ezt a követelményt. A Feltételes hozzáférés segítségével automatikus hozzáférés-vezérlési döntéseket hozhat a felhőalapú alkalmazások elérésének feltételei alapján.

**Ajánlott eljárás:** A vállalati erőforrásokhoz való hozzáférés kezelése és szabályozása.  
**Részletes:** Konfigurálja az Azure AD [feltételes hozzáférés](/azure/active-directory/active-directory-conditional-access-azure-portal) alapján egy csoport, hely és alkalmazás érzékenysége SaaS-alkalmazások és az Azure AD-hez csatlakoztatott alkalmazások.

**Ajánlott eljárás:** Blokkolja az örökölt hitelesítési protokollokat.
**Részletesen**: A támadók minden nap kihasználják a régebbi protokollok gyengeségeit, különösen a jelszószórásos támadások esetében. Konfigurálja a feltételes hozzáférést az örökölt protokollok blokkolására. Tekintse meg a videót az [Azure AD: Teendők és ne tegye](https://www.youtube.com/watch?v=wGk0J4z90GI) további információkért.

## <a name="plan-for-routine-security-improvements"></a>A rendszeres biztonsági fejlesztések megtervezése

A biztonság folyamatosan fejlődik, és fontos, hogy a felhő- és identitáskezelési keretrendszerbe építsen be egy módot a növekedés rendszeres bemutatására és a környezet védelmének új módjainak felfedezésére.

Az identitásbiztonságos pontszám olyan ajánlott biztonsági vezérlők készlete, amelyeket a Microsoft tesz közzé, és amely numerikus pontszámmal rendelkezik a biztonsági állapot objektív méréséhez és a jövőbeli biztonsági fejlesztések megtervezéséhez. Azt is megtekintheti a pontszám képest más iparágakban, valamint a saját trendek idővel.

**Ajánlott eljárás:** Tervezze meg a rendszeres biztonsági felülvizsgálatokat és fejlesztéseket az iparágban bevált gyakorlatok alapján.
**Részlet:** Használja az identitás biztonságos pontszám funkciót a fejlesztések rangsorolásához az idő múlásával.

## <a name="enable-password-management"></a>Jelszókezelés engedélyezése

Ha több bérlővel rendelkezik, vagy engedélyezni szeretné a felhasználóknak a [saját jelszavuk alaphelyzetbe állítását,](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)fontos, hogy a visszaélések megelőzése érdekében megfelelő biztonsági házirendeket használjon.

**Ajánlott eljárás:** Állítsa be az önkiszolgáló jelszó-visszaállítást (SSPR) a felhasználók számára.  
**Részlet:** Használja az Azure AD [önkiszolgáló jelszó-visszaállítási](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) funkciót.

**Ajánlott eljárás**: Figyelemmel kíséri, hogyan és ha SSPR valóban használják.  
**Részletes :** Az Azure AD [jelszó-visszaállítási regisztrációs tevékenység jelentésével](/azure/active-directory/active-directory-passwords-get-insights)figyelheti a regisztráló felhasználókat. Az Azure AD által biztosított jelentéskészítési funkció előre összeállított jelentések használatával segít a kérdések megválaszolása. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat.

**Ajánlott eljárás:** A felhőalapú jelszóházirendek kiterjesztése a helyszíni infrastruktúrára.
**Részletes:** A szervezetjelszó-házirendek javítása a helyszíni jelszómódosítások ellenőrzésének elvégzésével, mint a felhőalapú jelszómódosítások esetében. Telepítse [az Azure AD jelszavas védelmét](/azure/active-directory/authentication/concept-password-ban-bad) a windows Server Active Directory-ügynökök számára a helyszínen, hogy kiterjessze a tiltott jelszólistákat a meglévő infrastruktúrára. Azoknak a felhasználóknak és rendszergazdáknak, akik a helyszínen módosítják, beállítják vagy alaphelyzetbe állítják a jelszavakat, meg kell felelniük a csak felhőalapú felhasználókkal azonos jelszóházirendnek.

## <a name="enforce-multi-factor-verification-for-users"></a>Többtényezős ellenőrzés kényszerítése a felhasználók számára

Azt javasoljuk, hogy minden felhasználóhoz kétlépéses ellenőrzést igényel. Ez magában foglalja a rendszergazdák és mások a szervezetben, akik jelentős hatással lehet, ha a fiók veszélybe (például a pénzügyi tisztviselők).

A kétlépéses ellenőrzés megkövetelésére több lehetőség is van. A legjobb megoldás a céloktól, a futtatott Azure AD-kiadástól és a licencelési programtól függ. Lásd: [Hogyan követelheti meg a kétlépéses ellenőrzést a felhasználótól,](/azure/active-directory/authentication/howto-mfa-userstates) hogy meghatározza a legmegfelelőbb megoldást. A licencekkel és a díjszabással kapcsolatos további információkért tekintse meg az [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) és az [Azure többtényezős hitelesítés](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási oldalait.

A kétlépéses ellenőrzés engedélyezéséhez az alábbi lehetőségek és előnyök tartoznak:

**1. lehetőség:** Az MFA engedélyezése az összes felhasználó számára, és bejelentkezési módszerek az Azure AD biztonsági alapértelmezései **előny:** Ez a beállítás lehetővé teszi, hogy könnyen és gyorsan érvényesíteni MFA minden felhasználó a környezetben egy szigorú házirend:

* Az adminisztratív fiókok és az adminisztratív bejelentkezési mechanizmusok kihívása
* Többfa-kihívás megkövetelése a Microsoft Hitelesítőn keresztül minden felhasználó számára
* Az örökölt hitelesítési protokollok korlátozása.

Ez a módszer minden licencelési réteg számára elérhető, de nem keverhető a meglévő feltételes hozzáférési házirendekkel. További információkat az Azure AD biztonsági alapbeállításaiban talál.

**2. lehetőség:** [A többtényezős hitelesítés engedélyezése a felhasználói állapot módosításával.](../../active-directory/authentication/howto-mfa-userstates.md)   
**Előny**: Ez a hagyományos módszer a kétlépéses ellenőrzés megkövetelésére. Az Azure [többtényezős hitelesítésével a felhőben és az Azure többtényezős hitelesítési kiszolgálón is](/azure/active-directory/authentication/concept-mfa-whichversion)működik. Ezzel a módszerrel a felhasználóknak kétlépéses ellenőrzést kell végrehajtaniuk minden bejelentkezéskor, és felülbírálják a feltételes hozzáférési házirendeket.

Annak meghatározásához, hogy hol kell engedélyezni a többtényezős hitelesítést, olvassa el [az Azure MFA melyik verziója megfelelő a szervezet számára című témakört.](/azure/active-directory/authentication/concept-mfa-whichversion)

**3. lehetőség:** [Többtényezős hitelesítés engedélyezése feltételes hozzáférési házirenddel.](/azure/active-directory/authentication/howto-mfa-getstarted)
**Előny**: Ez a beállítás lehetővé teszi, hogy meghatározott feltételek mellett kétlépcsős ellenőrzést kérjenek a [feltételes hozzáférés](/azure/active-directory/active-directory-conditional-access-azure-portal)használatával. Adott feltételek lehetnek a különböző helyekről, nem megbízható eszközökről vagy kockázatosnak ítélt alkalmazásokból érkező felhasználók bejelentkezése. Olyan konkrét feltételek meghatározása, ahol kétlépéses ellenőrzésre van szükség, elkerülheti a felhasználók folyamatos rákérdezését, ami kellemetlen felhasználói élményt okozhat.

Ez a legrugalmasabb módja annak, hogy kétlépcsős ellenőrzést engedélyezze a felhasználók számára. A feltételes hozzáférési szabályzat engedélyezése csak az Azure többtényezős hitelesítésa a felhőben, és az Azure AD prémium funkciója. Erről a módszerről további információt a [felhőalapú Azure többtényezős hitelesítés telepítése](/azure/active-directory/authentication/howto-mfa-getstarted)című részben talál.

**4. lehetőség:** A többtényezős hitelesítés engedélyezése feltételes hozzáférési szabályzatokkal az [Azure AD Identity Protection](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)felhasználói és bejelentkezési kockázatának kiértékelésével.   
**Előny**: Ez a beállítás lehetővé teszi, hogy:

* A szervezet identitását érintő potenciális biztonsági rések észlelése.
* Állítsa be az automatikus válaszokat a szervezet identitásaihoz kapcsolódó észlelt gyanús műveletekre.
* Vizsgálja ki a gyanús eseményeket, és tegye meg a megfelelő lépéseket azok megoldásához.

Ez a módszer az Azure AD Identity Protection kockázatkiértékelése segítségével határozza meg, hogy szükség van-e kétlépéses ellenőrzésre az összes felhőalkalmazás felhasználói és bejelentkezési kockázata alapján. Ehhez a módszerhez Azure Active Directory P2 licencelésszükséges. Erről a módszerről az [Azure Active Directory identitásvédelem](/azure/active-directory/identity-protection/overview)című dokumentumban talál további információt.

> [!Note]
> 1. lehetőség, amely a többtényezős hitelesítést a felhasználói állapot módosításával engedélyezi, felülbírálja a feltételes hozzáférési házirendeket. Mivel a 2.

Azok a szervezetek, amelyek nem adnak hozzá további identitásvédelmi rétegeket, például a kétlépéses ellenőrzést, hajlamosabbak a hitelesítő adatok ellopásával kapcsolatos támadásokra. A hitelesítő adatok lopás támadás a data compromise vezethet.

## <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés alkalmazása

A felhőalapú erőforrások hozzáférés-kezelése kritikus fontosságú minden olyan szervezet számára, amely a felhőt használja. [Szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)segítségével kezelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz, mit tehetnek ezekkel az erőforrásokkal, és milyen területekhez férhetnek hozzá.

Az Azure-beli adott függvényekért felelős csoportok vagy egyéni szerepkörök kijelölésével elkerülhetők azok a félreértések, amelyek olyan emberi és automatizálási hibákhoz vezethetnek, amelyek biztonsági kockázatokat okozhatnak. A hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legkevésbé bizalmas biztonsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) elvek alapján elengedhetetlen azon szervezetek számára, amelyek az adathozzáférésbiztonsági házirendjeit szeretnék érvényesíteni.

A biztonsági csapatnak rá látásra van szüksége az Azure-erőforrásokban a kockázatok felmérése és elhárítása érdekében. Ha a biztonsági csoport működési feladatokkal rendelkezik, további engedélyekre van szükségük a munkájuk hoz.

[Az RBAC](/azure/role-based-access-control/overview) segítségével engedélyeket rendelhet a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörön. A szerepkörkiosztás hatóköre előfizetés, erőforráscsoport vagy egyetlen erőforrás is lehet.

**Ajánlott eljárás:** A feladatok elkülönítése a csapaton belül, és csak a felhasználók számára, hogy a feladatok elvégzéséhez szükséges. Ahelyett, hogy mindenkinek korlátlan engedélyeket adna az Azure-előfizetésében vagy az erőforrásokban, csak bizonyos műveleteket engedélyezhet egy adott hatókörben.
**Részlet:** Az Azure [beépített RBAC-szerepkörök](/azure/role-based-access-control/built-in-roles) használatával jogosultságokat rendelhet a felhasználókhoz.

> [!Note]
> A speciális engedélyek szükségtelen bonyolultságot és zavart keltenek, és olyan "örökölt" konfigurációba halmozódnak fel, amelyet nehéz kijavítani anélkül, hogy félnének valamit megtörni. Kerülje az erőforrás-specifikus engedélyeket. Ehelyett használjon felügyeleti csoportokat vállalati szintű engedélyekhez és erőforráscsoportokat az előfizetéseken belüli engedélyekhez. Kerülje a felhasználóspecifikus engedélyeket. Inkább rendeljen hozzáférést csoportokhoz az Azure AD-ben.

**Ajánlott eljárás:** Adjon hozzáférést az Azure-erőforrások megtekintéséhez az Azure-erőforrásokat biztosító biztonsági csapatoknak, hogy felmérhessék és orvosolhassák a kockázatokat.
**Részlet:** Adja meg a biztonsági csapatoknak az RBAC [biztonsági olvasó](/azure/role-based-access-control/built-in-roles#security-reader) szerepkört. A felelősségi köröktől függően használhatja a gyökérfelügyeleti csoportot vagy a szegmenskezelési csoportot:

* Az összes vállalati erőforrásért felelős csoportok **gyökérfelügyeleti csoportja**
* **Szegmenskezelési csoport** korlátozott hatókörű csapatok számára (általában szabályozási vagy egyéb szervezeti határok miatt)

**Ajánlott eljárás:** Adja meg a megfelelő engedélyeket a közvetlen üzemeltetési felelősséggel rendelkező biztonsági csoportoknak.
**Részlet:** Tekintse át az RBAC beépített szerepkörök a megfelelő szerepkör-hozzárendelés. Ha a beépített szerepkörök nem felelnek meg a szervezet egyedi igényeinek, egyéni szerepköröket hozhat létre [az Azure-erőforrásokhoz.](/azure/role-based-access-control/custom-roles) A beépített szerepkörökhöz is egyéni szerepköröket rendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatástagokhoz az előfizetésben, az erőforráscsoportban és az erőforrás-hatókörökben.

**Gyakorlati tanácsok:** Adjon hozzáférést az Azure Security Center-hez a biztonsági szerepkörökhöz, amelyeknek szükségük van rá. A Security Center lehetővé teszi a biztonsági csoportok számára a kockázatok gyors azonosítását és elhárítását.
**Részletes:** Adja hozzá az ezeket az igényeket az RBAC [biztonsági rendszergazdai](/azure/role-based-access-control/built-in-roles#security-admin) szerepkört, hogy megtekinthesse a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkeszthesse a biztonsági házirendeket, megtekintheti a riasztásokat és javaslatokat, és elvethesse a riasztásokat és javaslatokat. Ezt a legfelső szintű felügyeleti csoport vagy a szegmensfelügyeleti csoport használatával teheti meg, a felelősségi köröktől függően.

Szervezetek, amelyek nem kényszerítik ki az adatelérés-vezérlés i képességek használatával, mint az RBAC lehet, hogy a szükségesnél több jogosultságot a felhasználók számára. Ez adatsérüléshez vezethet azáltal, hogy lehetővé teszi a felhasználók számára, hogy olyan típusú adatokhoz (például nagy üzleti hatás), amelyeket nem kellett volna.

## <a name="lower-exposure-of-privileged-accounts"></a>A kiemelt jogosultságú fiókok alacsonyabb kitettsége

A kiemelt hozzáférés biztonságossá tétele kritikus első lépés az üzleti eszközök védelme érdekében. A biztonságos adatokhoz vagy erőforrásokhoz hozzáféréssel rendelkező személyek számának minimalizálása csökkenti annak az esélyét, hogy egy rosszindulatú felhasználó hozzáférjen, vagy ha egy jogosult felhasználó véletlenül egy bizalmas erőforrást érint.

A kiemelt jogosultságú fiókok informatikai rendszerekfelügyeletére és kezelésére szolgáló fiókok. A kibertámadók ezeket a fiókokat célozzák meg, hogy hozzáférjenek a szervezet adataihoz és rendszereihez. A kiemelt hozzáférés biztonságossá tétele érdekében el kell különítenie a fiókokat és a rendszereket a rosszindulatú felhasználók általi kitett kockázatoktól.

Azt javasoljuk, hogy dolgozzon ki és kövesse az ütemtervet a kiemelt hozzáférés a kibertámadók ellen. Az Azure AD, a Microsoft Azure, az Office 365 és más felhőszolgáltatások által felügyelt vagy jelentett identitások és hozzáférések biztonságossá tétele részletes ütemterv létrehozásáról az Azure AD-ben a kiemelt hozzáférés biztosítása az [Azure AD-ben](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)című részben talál tájékoztatást.

Az alábbiakban összefoglaljuk az Azure [AD hibrid és felhőalapú telepítésekhez való kiemelt hozzáférés ek védelmében](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)található ajánlott eljárásokat:

**Ajánlott eljárás:** A kiemelt fiókokhoz való hozzáférés kezelése, vezérlése és figyelése.   
**Részlet**: Kapcsolja be az [Azure AD kiemelt identitáskezelést.](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) Miután bekapcsolta a kiemelt identitáskezelés, értesítést kap e-mail üzeneteket a kiemelt hozzáférési szerepkör módosításai. Ezek az értesítések korai figyelmeztetést biztosítanak, ha további felhasználókat adnak hozzá a címtár magas szintű jogosultsággal rendelkező szerepköreihez.

**Ajánlott eljárás:** Győződjön meg arról, hogy minden kritikus rendszergazdai fiók felügyelt Azure AD-fiókok.
**Részlet:** Távolítsa el a fogyasztói fiókokat a kritikus rendszergazdai szerepkörökből (például a Microsoft-fiókokból, például a hotmail.com, a live.com és a outlook.com).

**Ajánlott eljárás:** Győződjön meg arról, hogy minden kritikus rendszergazdai szerepkör külön fiókkal rendelkezik a felügyeleti feladatokhoz, hogy elkerülje az adathalászatot és más támadásokat a rendszergazdai jogosultságok veszélyeztetése érdekében.
**Részlet:** Hozzon létre egy külön rendszergazdai fiókot, amely a felügyeleti feladatok végrehajtásához szükséges jogosultságokkal van elrendelve. Tiltsa le ezeknek a felügyeleti fiókoknak a használatát a napi hatékonyságnövelő eszközökhöz, például a Microsoft Office 365-ös levelezéshez vagy az tetszőleges webböngészéshez.

**Ajánlott eljárás:** Azonosítsa és kategorizálja a magas jogosultsági szintű szerepkörökben lévő fiókokat.   
**Részletes:** Az Azure AD kiemelt identitáskezelés bekapcsolása után tekintse meg a felhasználókat, akik a globális rendszergazda, a kiemelt szereprendszergazda és más magas szintű kiemelt jogosultságú szerepkörök. Távolítsa el azokat a fiókokat, amelyekre már nincs szükség ezekben a szerepkörökben, és kategorizálja a rendszergazdai szerepkörökhöz rendelt fennmaradó fiókokat:

* Egyénileg hozzárendelve a rendszergazdai felhasználókhoz, és nem adminisztratív célokra használható (például személyes e-mail)
* Egyénileg hozzárendelt adminisztratív felhasználók és kijelölt adminisztratív célokra csak
* Több felhasználó között megosztva
* Vészhelyzeti hozzáférési forgatókönyvekhez
* Automatikus parancsfájlok esetén
* Külső felhasználók számára

**Ajánlott eljárás:** Valósítsa meg a "just in time" (JIT) hozzáférést a jogosultságok expozíciós idejének további csökkentése és a kiemelt jogosultságú fiókok használatának láthatóságának növelése érdekében.   
**Részletes :** Az Azure AD kiemelt identitáskezelés lehetővé teszi, hogy:

* Korlátozza a felhasználókat, hogy csak a jogosultságok JIT.
* Rendeljen szerepköröket egy rövidebb időtartamhoz, bízva abban, hogy a jogosultságok automatikusan visszavonásra kerülnek.

**Ajánlott eljárás:** Adjon meg legalább két vészelérési fiókot.   
**Részletes:** A segélyhívó fiókok segítségével a szervezetek korlátozhatják a kiemelt hozzáféréseket egy meglévő Azure Active Directory-környezetben. Ezek a fiókok magas szintű jogosultsággal rendelkeznek, és nem vannak hozzárendelve bizonyos személyekhez. A vészelérési fiókok olyan esetekre korlátozódnak, ahol a normál rendszergazdai fiókok nem használhatók. A szervezeteknek a segélyhívási fiók használatát csak a szükséges időre kell korlátozniuk.

Értékelje ki a globális rendszergazdai szerepkörhöz rendelt vagy arra jogosult fiókokat. Ha nem lát csak felhőalapú fiókokat `*.onmicrosoft.com` a (vészelérési hozzáférésre szánt) tartomány használatával, hozza létre őket. További információ: [A segélyhívó rendszergazdai fiókok kezelése az Azure AD-ben.](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Legjobb gyakorlat:** Vészhelyzet esetén "üvegtörési" eljárást kell bevezetni.
**Részletes :** Kövesse a [hibrid és felhőalapú telepítések kiemelt hozzáférésének védelme az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)című lépéseket.

**Ajánlott eljárás:** Az összes kritikus rendszergazdai fiók jelszó nélküli (előnyben részesített) vagy többtényezős hitelesítés megkövetelése.
**Részlet:** A [Microsoft Authenticator alkalmazással](/azure/active-directory/authentication/howto-authentication-phone-sign-in) jelentkezzen be bármely Azure AD-fiókba jelszó használata nélkül. A [Windows Hello for Business szolgáltatáshoz](/windows/security/identity-protection/hello-for-business/hello-identity-verification)hasonlóan a Microsoft Authenticator is kulcsalapú hitelesítést használ az eszközhöz kapcsolt és biometrikus hitelesítést vagy PIN-kódot használó felhasználói hitelesítő adatok engedélyezéséhez.

Az Azure többtényezős hitelesítés megkövetelése bejelentkezéskor minden olyan egyéni felhasználó számára, aki tartósan hozzá van rendelve egy vagy több Azure AD-rendszergazdai szerepkörhöz: globális rendszergazda, kiemelt szereprendszergazda, Exchange Online-rendszergazda és SharePoint Online-rendszergazda. Engedélyezze [a többtényezős hitelesítést a rendszergazdai fiókokszámára,](/azure/active-directory/authentication/howto-mfa-userstates) és győződjön meg arról, hogy a rendszergazdai fiók felhasználói regisztráltak.

**Ajánlott eljárás:** A kritikus rendszergazdai fiókok, van egy rendszergazdai munkaállomás, ahol az éles feladatok nem engedélyezettek (például a böngészés és az e-mail). Ez megvédi a rendszergazdai fiókokat a böngészést és e-maileket használó támadási vektoroktól, és jelentősen csökkenti a súlyos események kockázatát.
**Részlet**: Használjon rendszergazdai munkaállomást. Válassza ki a munkaállomás-biztonság szintjét:

- A rendkívül biztonságos hatékonyságnövelő eszközök fejlett biztonságot nyújtanak a böngészéshez és más hatékonyságnövelő feladatokhoz.
- [A kiemelt hozzáférésű munkaállomások (EMELT hozzáférési szintű munkaállomások)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) olyan dedikált operációs rendszert biztosítanak, amely védett az internetes támadásokkal és a bizalmas feladatok fenyegetésvektorival szemben.

**Ajánlott eljárás:** Rendszergazdai fiókok kiépítése, amikor az alkalmazottak elhagyják a szervezetet.
**Részletes:** Olyan folyamattal rendelkezik, amely letiltja vagy törli a rendszergazdai fiókokat, amikor az alkalmazottak elhagyják a szervezetet.

**Ajánlott eljárás:** Rendszeresen tesztelje a rendszergazdai fiókokat az aktuális támadási technikák használatával.
**Részletes:** Az Office 365 Támadásszimulátor vagy egy harmadik fél től származó ajánlat segítségével valósághű támadási forgatókönyveket futtathat a szervezetben. Ez segíthet megtalálni a sebezhető felhasználókat, mielőtt valódi támadás történne.

**Ajánlott eljárás:** Tegyen lépéseket a leggyakrabban használt támadási technikák enyhítésére.  
**Részlet**: [Azonosítsa azokat a Microsoft-fiókokat a munkahelyi vagy iskolai fiókokra átváltandó felügyeleti szerepkörökben](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Külön felhasználói fiókok és levéltovábbítás biztosítása globális rendszergazdai fiókok hoz](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Annak ellenőrzése, hogy a felügyeleti fiókok jelszavai a közelmúltban megváltoztak-e](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[A jelszókivonat-szinkronizálás bekapcsolása](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Többtényezős hitelesítés megkövetelése az összes kiemelt szerepkörben lévő felhasználók, valamint a kitett felhasználók számára](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Az Office 365 biztonságos pontszámának beszerzése (az Office 365 használata esetén)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Tekintse át az Office 365 biztonsági és megfelelőségi útmutatóját (az Office 365 használata esetén)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Az Office 365 tevékenységfigyelésének konfigurálása (az Office 365 használata esetén)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Esemény- és veszélyhelyzet-reagálási terv tulajdonosok létrehozása](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Helyszíni kiemelt rendszergazdai fiókok biztonságossá tétele](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Ha nem biztonságos a kiemelt hozzáférés, előfordulhat, hogy túl sok felhasználó magas szintű jogosultsággal rendelkező szerepkörökben, és sebezhetőbb a támadások. A rosszindulatú szereplők, beleértve a kibertámadókat is, gyakran rendszergazdai fiókokat és a kiemelt hozzáférés egyéb elemeit célozzák meg a bizalmas adatokhoz és rendszerekhez való hozzáféréshez hitelesítő adatok ellopásával.

## <a name="control-locations-where-resources-are-created"></a>Az erőforrások létrehozásának helyei

Nagyon fontos, hogy lehetővé tegye a felhőszolgáltatók számára a feladatok elvégzését, miközben megakadályozza, hogy megszegjék a szervezet erőforrásainak kezeléséhez szükséges konvenciókat. Szervezetek, amelyek szeretnék szabályozni a helyeket, ahol az erőforrások jönnek létre kell kemény kódot ezeket a helyeket.

Az [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) segítségével biztonsági szabályzatokat hozhat létre, amelyek definíciói a kifejezetten megtagadott műveleteket vagy erőforrásokat írják le. Ezeket a szabályzatdefiníciókat a kívánt hatókörhöz rendeli hozzá, például az előfizetéshez, az erőforráscsoporthoz vagy egy adott erőforráshoz.

> [!NOTE]
> A biztonsági házirendek nem egyeznek meg az RBAC-kal. Ők valójában használ RBAC -hoz engedélyez használók -hoz teremt azok anyagi javak.
>
>

Azok a szervezetek, amelyek nem szabályozzák az erőforrások létrehozásának módját, érzékenyebbek azokra a felhasználókra, akik a szükségesnél több erőforrás létrehozásával visszaélhetnek a szolgáltatással. Az erőforrás-létrehozási folyamat megerősítésével fontos lépés a több-bérlős forgatókönyv biztonságossá tétele.

## <a name="actively-monitor-for-suspicious-activities"></a>A gyanús tevékenységek aktív figyelése

Az aktív identitásfigyelő rendszer gyorsan észlelheti a gyanús viselkedést, és riasztást indíthat el további vizsgálatra. Az alábbi táblázat két Azure AD-képességet sorol fel, amelyek segítségével a szervezetek figyelhetik identitásukat:

**Ajánlott eljárás**: Van egy módszer, amely azonosítja:

- A bejelentkezési kísérletek [nyomon követés nélkül.](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)
- [Brute force](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) támadások ellen egy adott számla.
- Több helyről próbál bejelentkezni.
- Bejelentkezések fertőzött [eszközökről](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Gyanús IP-címek.

**Részletek:** Azure AD Premium [anomáliajelentések](/azure/active-directory/active-directory-view-access-usage-reports)használata . Olyan folyamatokkal és eljárásokkal kell rendelkeznie, amelyek alapján az informatikai rendszergazdák naponta vagy igény szerint futtatják ezeket a jelentéseket (általában incidensválasz-forgatókönyv esetén).

**Ajánlott eljárás:** Rendelkezik egy aktív felügyeleti rendszerrel, amely értesíti Önt a kockázatokról, és a kockázati szintet (magas, közepes vagy alacsony) az üzleti követelményekhez igazíthatja.   
**Részlet:** Használja [az Azure AD Identity Protection,](/azure/active-directory/active-directory-identityprotection)amely megjelöli az aktuális kockázatokat a saját irányítópultján, és e-mailben küld napi összefoglaló értesítéseket. A szervezet identitásainak védelme érdekében konfigurálhat olyan kockázatalapú házirendeket, amelyek automatikusan reagálnak az észlelt problémákra, amikor egy adott kockázati szintet elér.

Azok a szervezetek, amelyek nem figyelik aktívan identitásrendszereiket, a felhasználói hitelesítő adatok feltörésének kockázatával vannak kitéve. Anélkül, hogy tudná, hogy gyanús tevékenységek zajlanak ezeken a hitelesítő adatokon keresztül, a szervezetek nem tudják enyhíteni az ilyen típusú fenyegetést.

## <a name="use-azure-ad-for-storage-authentication"></a>Az Azure AD használata a tárolási hitelesítéshez
[Az Azure Storage](/azure/storage/common/storage-auth-aad) támogatja a hitelesítést és az engedélyezést az Azure AD blob-tároló és várólista-tároló használatával. Az Azure AD-hitelesítéssel az Azure szerepköralapú hozzáférés-vezérlés használatával adott engedélyeket adhat a felhasználóknak, csoportoknak és alkalmazásoknak egy adott blobtároló vagy -várólista hatókörig.

Azt javasoljuk, hogy az Azure AD használatával [hitelesítse a hozzáférést a tárterülethez.](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>Következő lépés

Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.
