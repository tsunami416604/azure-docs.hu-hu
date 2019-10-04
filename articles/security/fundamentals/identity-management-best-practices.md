---
title: Azure Identity & hozzáférési biztonsági eljárások | Microsoft Docs
description: Ez a cikk az Identitáskezelés és a hozzáférés-vezérlés ajánlott eljárásait ismerteti az Azure beépített képességeinek használatával.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 093c5878cd2f7df63502a7aff686824af3c88078
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195088"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Az Azure Identity Management és a hozzáférés-vezérlés biztonsága – ajánlott eljárások
Ebben a cikkben az Azure Identity Management és a hozzáférés-vezérléssel kapcsolatos ajánlott biztonsági eljárások gyűjteményét tárgyaljuk. Ezek az ajánlott eljárások az [Azure ad](../../active-directory/fundamentals/active-directory-whatis.md) -vel és az ügyfelek, például saját felhasználói élményekkel kapcsolatos tapasztalataiból származnak.

Minden ajánlott eljáráshoz a következőket magyarázza el:

* Az ajánlott eljárás
* Miért érdemes engedélyezni az ajánlott eljárásokat
* Mi lehet az eredmény, ha nem sikerül engedélyezni az ajánlott eljárást
* Az ajánlott eljárás lehetséges alternatívái
* Hogyan sajátíthatja el az ajánlott eljárásokat

Az Azure Identity Management és a hozzáférés-vezérlés biztonsági eljárásairól szóló cikk konszenzusos véleményt és az Azure platform képességeit és funkcióit ismerteti, amelyek a cikk írásának időpontjában szerepelnek. A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

Az Azure Identity Management és a hozzáférés-vezérléssel kapcsolatos ajánlott biztonsági eljárások a jelen cikkben tárgyalják a következőket:

* Identitás kezelése elsődleges biztonsági peremhálózatként
* Az Identitáskezelés központosított kezelése
* Csatlakoztatott bérlők kezelése
* Egyszeri bejelentkezés engedélyezése
* Feltételes hozzáférés bekapcsolása
* Jelszókezelés engedélyezése
* A multi-Factor-ellenőrzés érvényesítése a felhasználók számára
* Szerepköralapú hozzáférés-vezérlés alkalmazása
* A Kiemelt jogosultságú fiókok alacsonyabb kitettsége
* Az erőforrások helyének vezérlése
* Az Azure AD használata tárolási hitelesítéshez

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Identitás kezelése elsődleges biztonsági peremhálózatként

Az identitások többsége elsődleges biztonsági szempontoknak számít. Ez a hálózati biztonság hagyományos fókuszának eltolódása. A hálózati kerületek folyamatosan egyre több porózus, és a szegélyhálózati védelem nem lehet olyan hatékony, mint a [BYOD](https://aka.ms/byodcg) -eszközök és a Felhőbeli alkalmazások robbanása előtt.

A [Azure Active Directory (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md) az identitás-és hozzáférés-kezeléshez használható Azure-megoldás. Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelő szolgáltatás a Microsofttól. Az alapszintű címtárszolgáltatás, az alkalmazás-hozzáférés kezelése és az Identity Protection egyetlen megoldással kombinálható.

Az alábbi fejezetek az identitás-és hozzáférés-biztonságra vonatkozó ajánlott eljárásokat sorolja fel az Azure AD használatával.

## <a name="centralize-identity-management"></a>Az Identitáskezelés központosított kezelése

[Hibrid identitási](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) helyzetekben javasoljuk, hogy integrálja a helyszíni és a Felhőbeli címtárakat. Az integráció lehetővé teszi az IT-csoportnak, hogy a fiókokat egy adott helyről kezelhesse, függetlenül attól, hogy hol hoztak létre. Az integráció a Felhőbeli és a helyszíni erőforrások elérésének közös identitásával is segíti a felhasználókat.

**Ajánlott eljárás**: Hozzon létre egyetlen Azure AD-példányt. A konzisztencia és az egyetlen mérvadó forrás is növeli az átláthatóságot, és csökkenti a biztonsági kockázatokat az emberi hibák és a konfiguráció bonyolultsága miatt.
**Részletek**: Egyetlen Azure AD-címtár kijelölése a vállalati és a szervezeti fiókok mérvadó forrásaként.

**Ajánlott eljárás**: A helyszíni címtárak integrálása az Azure AD-vel.  
**Részletek**: A [Azure ad Connect](/azure/active-directory/connect/active-directory-aadconnect) használatával szinkronizálhatja a helyszíni címtárat a felhő címtárával.

> [!Note]
> Vannak olyan [tényezők, amelyek hatással vannak a Azure ad Connect teljesítményére](../../active-directory/hybrid/plan-connect-performance-factors.md). Győződjön meg arról, Azure AD Connect elegendő kapacitással rendelkezik ahhoz, hogy a rendszerek ne akadályozzák a biztonságot és a termelékenységet. A nagyméretű vagy összetett szervezeteknek (szervezeteknek több mint 100 000 objektumot kell kiépíteniük [](../../active-directory/hybrid/whatis-hybrid-identity.md) ) az ajánlásokat követve optimalizálhatja Azure ad Connect megvalósítását.

**Ajánlott eljárás**: Ne szinkronizálja a fiókokat az Azure AD-be, amelyek magas szintű jogosultságokkal rendelkeznek a meglévő Active Directory-példányban.
**Részletek**: Ne módosítsa az alapértelmezett [Azure ad Connect konfigurációt](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) , amely kiszűri ezeket a fiókokat. Ez a konfiguráció csökkenti a felhőből a helyszíni eszközökre irányuló ellenfelek (ami jelentős incidenst eredményezhet) kockázatát.

**Ajánlott eljárás**: Jelszó-kivonat szinkronizálásának bekapcsolása.  
**Részletek**: A jelszó-kivonatolási szinkronizálás egy helyszíni Active Directory-példányról egy felhőalapú Azure AD-példányra történő felhasználói jelszó-kivonatok szinkronizálására szolgáló szolgáltatás. Ez a szinkronizálás segít megvédeni a korábbi támadásokból visszajátszott kiszivárgott hitelesítő adatokat.

Még ha úgy dönt, hogy összevonást használ Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy más identitás-szolgáltatókkal, beállíthatja a jelszó-kivonat szinkronizálását biztonsági mentésként, ha a helyszíni kiszolgálók meghibásodnak vagy átmenetileg elérhetetlenné válnak. Ez a szinkronizálás lehetővé teszi a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be a szolgáltatásba, amelyet a helyszíni Active Directory-példányba való bejelentkezéshez használnak. Azt is lehetővé teszi, hogy az Identity Protection észlelje a feltört hitelesítő adatokat, összehasonlítva a szinkronizált jelszó-kivonatokat a biztonsággal ismert jelszavakkal, ha a felhasználó ugyanazt az e-mail címet és jelszót használta más olyan szolgáltatásokon, amelyek nem csatlakoznak az Azure AD-hez.

További információ: jelszó- [kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Ajánlott eljárás**: Az új alkalmazások fejlesztéséhez használja az Azure AD-t a hitelesítéshez.
**Részletek**: A hitelesítés támogatásához használja a megfelelő képességeket:

  - Azure AD az alkalmazottak számára
  - [Azure ad B2B](../../active-directory/b2b/index.yml) vendég felhasználók és külső partnerek számára
  - [Azure ad B2C](../../active-directory-b2c/index.yml) annak szabályozásához, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat az alkalmazások használatakor

Azok a szervezetek, amelyek nem integrálják a helyszíni identitást a Felhőbeli identitással, nagyobb terheléssel rendelkezhetnek a fiókok kezelésében. Ez a terhelés növeli a hibák és a biztonsági rések valószínűségét.

> [!Note]
> Ki kell választania, hogy mely címtárakban kritikus fiókok fognak megjelenni, és hogy a használt rendszergazdai munkaállomást az új Cloud Services vagy a meglévő folyamatok felügyelik-e. A meglévő felügyeleti és identitás-kiépítési folyamatok használata csökkentheti a kockázatokat, de a támadók kockázatát is veszélyeztethetik a helyszíni fiókok és a felhőbe való belátások. Érdemes lehet más stratégiát használni a különböző szerepkörökhöz (például rendszergazdák és üzleti egység adminisztrátorai). Két lehetőség közül választhat. Az első lehetőség olyan Azure AD-fiókok létrehozása, amelyek nincsenek szinkronizálva a helyszíni Active Directory-példánnyal. Csatlakoztassa a felügyeleti munkaállomást az Azure AD-hez, amelyet Microsoft Intune használatával kezelhet és javíthat. A második lehetőség a meglévő rendszergazdai fiókok használata a helyszíni Active Directory-példányra való szinkronizálással. A Active Directory tartományban meglévő munkaállomásokat kezelheti felügyeletre és biztonságra.

## <a name="manage-connected-tenants"></a>Csatlakoztatott bérlők kezelése
A biztonsági szervezetnek meg kell vizsgálnia a kockázat felmérését, és meg kell határoznia, hogy a szervezet szabályzatait és az összes szabályozási követelményt követi-e a rendszer. Győződjön meg arról, hogy a biztonsági szervezet az éles környezethez és a hálózathoz (az [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) vagy [a helyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)-en keresztül) kapcsolódó összes előfizetéshez betekintést kapott. Az Azure AD [globális rendszergazdája/vállalati rendszergazdája](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) hozzáférhet a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepköréhez, és megtekintheti a környezethez csatlakoztatott összes előfizetést és felügyelt csoportot.

Lásd: [jogosultságszint-emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md) , hogy Ön és biztonsági csoportja megtekintse a környezetéhez kapcsolódó összes előfizetést vagy felügyeleti csoportot. A kockázatok felmérése után távolítsa el ezt a emelt szintű hozzáférést.

## <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

A mobil-első, Felhőbeli első világban az egyszeri bejelentkezést (SSO) az eszközökre, alkalmazásokra és szolgáltatásokra szeretné engedélyezni bárhonnan, így a felhasználók bárhol és bármikor dolgozhatnak. Ha több identitási megoldással rendelkezik, ez a felügyeleti probléma nem csupán az, hanem a több jelszót megjegyező felhasználók számára is.

Ha ugyanazt az identitás-megoldást használja az összes alkalmazásához és erőforrásához, az SSO-t is elérheti. És a felhasználók ugyanazt a hitelesítő adatokat használhatják a bejelentkezéshez és a szükséges erőforrásokhoz való hozzáféréshez, függetlenül attól, hogy az erőforrások a helyszínen vagy a felhőben találhatók-e.

**Ajánlott eljárás**: Egyszeri bejelentkezés engedélyezése.  
**Részletek**: Az Azure AD [kiterjeszti a helyszíni Active Directory](/azure/active-directory/connect/active-directory-aadconnect) a felhőre. A felhasználók saját elsődleges munkahelyi vagy iskolai fiókját használhatják a tartományhoz csatlakoztatott eszközökhöz, a vállalati erőforrásokhoz, valamint az összes olyan webes és SaaS-alkalmazáshoz, amelyre a munkájuk elvégzéséhez szükségük van. A felhasználóknak nem kell megemlékezniük több Felhasználónév és jelszó megadására, és az alkalmazásuk hozzáférése automatikusan kiépíthető (vagy kiépíthető), a szervezeti csoporttagság és az alkalmazotti állapotuk alapján. A katalógusban szereplő alkalmazások, illetve az [Azure AD-alkalmazásproxy](/azure/active-directory/active-directory-application-proxy-get-started) használatával kifejlesztett és közzétett saját helyszíni alkalmazások hozzáférése is szabályozható.

Az egyszeri bejelentkezés lehetővé teszi, hogy a felhasználók a munkahelyi vagy iskolai fiókjuk alapján hozzáférjenek az [SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) -alkalmazásokhoz az Azure ad-ben. Ez nem csak a Microsoft SaaS-alkalmazások, hanem más alkalmazások, például a [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) és a [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial)esetében is érvényes. Beállíthatja, hogy az alkalmazás az Azure AD-t [SAML-alapú identitás-](/azure/active-directory/fundamentals-identity) szolgáltatóként használja. Biztonsági ellenőrzésként az Azure AD nem ad ki jogkivonatot, amely lehetővé teszi, hogy a felhasználók bejelentkezzenek az alkalmazásba, hacsak nem kaptak hozzáférést az Azure AD-n keresztül. Közvetlenül is megadhat hozzáférést, vagy egy olyan csoporton keresztül, amely a felhasználók tagja.

Azok a szervezetek, amelyek nem hoznak létre közös identitást az SSO létrehozásához a felhasználók és az alkalmazások számára, jobban ki vannak téve olyan forgatókönyvek esetén, ahol a felhasználóknak több jelszava van. Ezek a forgatókönyvek nagyobb valószínűséggel használják a felhasználókat a jelszavak újrafelhasználására vagy a gyenge jelszavak használatára.

## <a name="turn-on-conditional-access"></a>Feltételes hozzáférés bekapcsolása

A felhasználók különböző eszközök és alkalmazások segítségével bárhonnan hozzáférhetnek a szervezet erőforrásaihoz. Rendszergazdaként meg kell győződnie arról, hogy ezek az eszközök megfelelnek a biztonsági és megfelelőségi szabványoknak. Csak arra összpontosíthat, hogy ki férhet hozzá egy erőforráshoz, már nem elegendő.

A biztonság és a termelékenység egyensúlya érdekében meg kell gondolnia, hogyan érhető el egy erőforrás, mielőtt döntést hozna a hozzáférés-vezérlésről. Az Azure AD feltételes hozzáférésével ezt a követelményt kezelheti. A feltételes hozzáférés révén automatizált hozzáférés-vezérlési döntéseket hozhat a felhőalapú alkalmazások elérésére vonatkozó feltételek alapján.

**Ajánlott eljárás**: A vállalati erőforrásokhoz való hozzáférés kezelése és szabályozása.  
**Részletek**: Az Azure AD [feltételes hozzáférés](/azure/active-directory/active-directory-conditional-access-azure-portal) konfigurálása az SaaS-alkalmazások és az Azure ad-hez csatlakoztatott alkalmazások csoport-, hely-és alkalmazás-érzékenysége alapján.

**Ajánlott eljárás**: Örökölt hitelesítési protokollok blokkolása.
**Részletek**: A támadók minden nap kihasználják a régebbi protokollok gyengeségeit, különösen a jelszó-szórásos támadásokhoz. Feltételes hozzáférés konfigurálása az örökölt protokollok blokkolásához. Tekintse meg [a videót az Azure ad-ben: További információért tegye a következőt:.](https://www.youtube.com/watch?v=wGk0J4z90GI)

## <a name="enable-password-management"></a>Jelszókezelés engedélyezése

Ha több Bérlővel rendelkezik, vagy engedélyezni szeretné a felhasználók számára a [saját jelszavának alaphelyzetbe állítását](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), fontos, hogy a megfelelő biztonsági házirendeket használja a visszaélések elkerülése érdekében.

**Ajánlott eljárás**: Az önkiszolgáló jelszó-visszaállítás (SSPR) beállítása a felhasználók számára.  
**Részletek**: Használja az Azure AD önkiszolgáló [jelszó-visszaállítási](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) szolgáltatását.

**Ajánlott eljárás**: Figyelje meg, hogy a SSPR valóban használatban van-e.  
**Részletek**: Figyelje meg, hogy mely felhasználók regisztrálnak az Azure AD [jelszó-visszaállítási regisztrációs tevékenység jelentés](/azure/active-directory/active-directory-passwords-get-insights)használatával. Az Azure AD által biztosított jelentéskészítési funkció segítséget nyújt az előre elkészített jelentések használatával kapcsolatos kérdések megválaszolásához. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat.

**Ajánlott eljárás**: Kiterjesztheti a felhőalapú jelszavas házirendeket a helyszíni infrastruktúrára.
**Részletek**: A jelszó-szabályzatok a szervezeten belüli módosításával megegyező ellenőrzéseket végezhet a helyszíni jelszavak változásaihoz, ahogy a felhőalapú jelszó módosításakor. Telepítse az [Azure ad-jelszavas védelmet](/azure/active-directory/authentication/concept-password-ban-bad) a Windows Server Active Directory-ügynököknek a helyszínen, hogy kiterjessze a tiltott jelszavak listáját a meglévő infrastruktúrára. Azok a felhasználók és rendszergazdák, akik a helyszíni jelszavakat módosítják, beállítják vagy alaphelyzetbe állítják, a csak felhőalapú felhasználókra vonatkozó jelszóházirend megtartásához szükségesek.

## <a name="enforce-multi-factor-verification-for-users"></a>A multi-Factor-ellenőrzés érvényesítése a felhasználók számára

Javasoljuk, hogy az összes felhasználóra vonatkozóan kétlépéses ellenőrzést igényeljen. Ebbe beletartozik a szervezet rendszergazdái és más tagjai, akik jelentős hatással lehetnek a fiókjuk biztonságára (például pénzügyi tisztviselők).

Több lehetőség is van a kétlépéses ellenőrzés megkövetelésére. A legjobb megoldás az Ön által futtatott céloktól, az Azure AD-kiadástól és a licencelési programtól függ. Tekintse meg, hogyan kell kétlépéses ellenőrzést megkövetelni a [felhasználók](/azure/active-directory/authentication/howto-mfa-userstates) számára a legjobb megoldás meghatározásához. A licencekkel és a díjszabással kapcsolatos további információkért tekintse meg az [Azure ad](https://azure.microsoft.com/pricing/details/active-directory/) és az [Azure multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási oldalát.

A kétlépéses ellenőrzés engedélyezésének lehetőségei és előnyei a következők:

**1. lehetőség**: [A multi-Factor Authentication engedélyezése a felhasználói állapot módosításával](../../active-directory/authentication/howto-mfa-userstates.md).   
**Előnyök**: Ez a hagyományos módszer a kétlépéses ellenőrzés megkövetelésére. [Az azure multi-Factor Authentication a felhőben és az azure multi-Factor Authentication-kiszolgáló](/azure/active-directory/authentication/concept-mfa-whichversion)is működik. Ennek a módszernek a használatával a felhasználóknak kétlépéses ellenőrzést kell végezniük minden alkalommal, amikor bejelentkeznek, és felülbírálják a feltételes hozzáférési szabályzatokat.

Annak megállapításához, hogy hol kell engedélyezni a Multi-Factor Authenticationt, tekintse meg a [szervezetem számára legmegfelelőbb Azure MFA-verziót](/azure/active-directory/authentication/concept-mfa-whichversion).

**2. lehetőség**: [Feltételes hozzáférési házirenddel rendelkező multi-Factor Authentication engedélyezése](/azure/active-directory/authentication/howto-mfa-getstarted).
**Előnyök**: Ez a beállítás lehetővé teszi, hogy a [feltételes hozzáférés](/azure/active-directory/active-directory-conditional-access-azure-portal)használatával megkérdezzen a kétlépéses ellenőrzést adott körülmények között. A konkrét feltételek különböző helyekről, nem megbízható eszközökről vagy kockázatos alkalmazásokból is lehetnek felhasználói bejelentkezésre. Meghatározott feltételek meghatározása, amelyekben kétlépéses ellenőrzésre van szükség, így elkerülhető a felhasználók folyamatos rákérdezése, ami kellemetlen felhasználói élmény lehet.

Ez a legrugalmasabb módszer a felhasználók kétlépéses ellenőrzésének engedélyezésére. A feltételes hozzáférési szabályzat engedélyezése csak a felhőben működő Azure-Multi-Factor Authentication működik, és az Azure AD prémium funkciója. Erről a módszerről a [felhőalapú Azure-multi-Factor Authentication üzembe helyezése című](/azure/active-directory/authentication/howto-mfa-getstarted)témakörben talál további információt.

**3. lehetőség**: A Multi-Factor Authentication a feltételes hozzáférési házirendekkel engedélyezheti az [Azure ad Identity Protection](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)felhasználói és bejelentkezési kockázatának kiértékelésével.   
**Előnyök**: Ez a beállítás a következőket teszi lehetővé:

- A szervezet identitásait érintő lehetséges sebezhetőségek észlelése.
- Konfigurálja az automatizált válaszokat a szervezete identitásával kapcsolatos gyanús műveletekre.
- Vizsgálja meg a gyanús incidenseket, és tegye meg a megfelelő lépéseket a megoldásához.

Ez a módszer a Azure AD Identity Protection kockázatértékelés használatával határozza meg, hogy szükséges-e kétlépéses ellenőrzés a felhasználói és bejelentkezési kockázatok alapján minden felhőalapú alkalmazás esetében. Ehhez a módszerhez Azure Active Directory P2 licencelés szükséges. A metódussal kapcsolatban a Azure Active Directory Identity Protectionban talál további [](/azure/active-directory/identity-protection/overview)információt.

> [!Note]
> 1\. lehetőség, a Multi-Factor Authentication engedélyezése a felhasználói állapot módosításával, felülbírálja a feltételes hozzáférési szabályzatokat. Mivel a 2. és a 3. lehetőség feltételes hozzáférési házirendeket használ, nem használhatja az 1. lehetőséget.

Azok a szervezetek, amelyek nem vesznek fel az Identity Protection további rétegeit, például a kétlépéses ellenőrzést, hajlamosabbak a hitelesítő adatok ellopására irányuló támadásokra. A hitelesítő adatok ellopására irányuló támadás adatsérülést eredményezhet.

## <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés alkalmazása
A felhőalapú erőforrásokhoz való hozzáférés kezelése kritikus fontosságú a felhőt használó bármely szervezet számára. A [szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)segítségével felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz, mit tehet ezekkel az erőforrásokkal, és milyen területekhez férhet hozzá.

A csoportok vagy az egyes Azure-funkciókért felelős egyedi szerepkörök kijelölik a félreértéseket, amelyek biztonsági kockázatokat okozó emberi és automatizálási hibákhoz vezethetnek. Az adathozzáférésre vonatkozó biztonsági szabályzatokat kényszerítő szervezetek számára elengedhetetlen a hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legalacsonyabb jogosultsági szintű](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elvek alapján.

A kockázat felmérése és javítása érdekében a biztonsági csapatnak meg kell vizsgálnia az Azure-erőforrásokat. Ha a biztonsági csapat működési felelősséggel rendelkezik, további engedélyekre van szükségük a feladataik elvégzéséhez.

A [RBAC](/azure/role-based-access-control/overview) segítségével engedélyeket rendelhet hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy adott hatókörben. A szerepkörkiosztás hatóköre előfizetés, erőforráscsoport vagy egyetlen erőforrás is lehet.

**Ajánlott eljárás**: Elkülönítheti a feladatokat a csapaton belül, és csak a felhasználók számára biztosíthatja a feladatok elvégzéséhez szükséges hozzáférést. Ahelyett, hogy az Azure-előfizetésben vagy-erőforrásokban mindenki számára korlátlan jogosultságot adna, csak bizonyos műveleteket engedélyezzen egy adott hatókörben.
**Részletek**: Az Azure [-ban beépített RBAC](/azure/role-based-access-control/built-in-roles) -szerepköröket használhat a felhasználókhoz való jogosultságok kiosztásához.

> [!Note]
> A konkrét engedélyek szükségtelen bonyolultságot és félreértéseket hoznak létre, és olyan "örökölt" konfigurációba halmozódnak, amely nehezen orvosolható, és nem kell mást tennie. Kerülje az erőforrás-specifikus engedélyeket. Ehelyett a vállalati szintű engedélyek és erőforráscsoportok felügyeleti csoportjait használhatja az előfizetéseken belüli engedélyekhez. Kerülje a felhasználó-specifikus engedélyeket. Ehelyett rendeljen hozzá hozzáférést a csoportokhoz az Azure AD-ben.

**Ajánlott eljárás**: Az Azure-beli feladatokkal rendelkező biztonsági csapatoknak biztosíthatja az Azure-erőforrások megtekintését és a kockázatok kijavítását.
**Részletek**: Adja meg a biztonsági csapatoknak a RBAC [biztonsági olvasó](/azure/role-based-access-control/built-in-roles#security-reader) szerepkört. A hatáskörök hatókörének függvényében a gyökérszintű felügyeleti csoportot vagy a szegmens felügyeleti csoportot használhatja:

- Az összes vállalati erőforrásért felelős csapatok **gyökérszintű felügyeleti csoportja**
- **Szegmens felügyeleti csoport** korlátozott hatókörű csapatok esetében (általában szabályozási vagy egyéb szervezeti határok miatt)

**Ajánlott eljárás**: Adja meg a megfelelő engedélyeket a közvetlen működési feladatokkal rendelkező biztonsági csapatoknak.
**Részletek**: Tekintse át a RBAC beépített szerepköreit a megfelelő szerepkör-hozzárendeléshez. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, egyéni szerepköröket hozhat létre [Az Azure](/azure/role-based-access-control/custom-roles)-erőforrásokhoz. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket rendelhet hozzá a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz az előfizetés, az erőforráscsoport és az erőforrás-hatókörök alapján.

**Ajánlott eljárások**: Adja meg Azure Security Center hozzáférést a szükséges biztonsági szerepkörökhöz. Security Center lehetővé teszi a biztonsági csapatok számára a kockázatok gyors azonosítását és szervizelését.
**Részletek**: Ezeket a biztonsági csoportokat a RBAC [biztonsági rendszergazdai](/azure/role-based-access-control/built-in-roles#security-admin) szerepkörrel veheti fel, így megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat Ezt a felügyeleti csoport vagy a szegmens felügyeleti csoport használatával végezheti el a felelősségi köröktől függően.

Azok a szervezetek, amelyek nem kényszerítik az adathozzáférés-vezérlést olyan képességek használatával, mint a RBAC, több jogosultságot is biztosítanak a felhasználók számára. Ez adatsérüléshez vezethet azáltal, hogy lehetővé teszi a felhasználók számára az adattípusok (például a nagy üzleti hatás) elérését.

## <a name="lower-exposure-of-privileged-accounts"></a>A Kiemelt jogosultságú fiókok alacsonyabb kitettsége

Az emelt szintű hozzáférés biztonságossá tétele kulcsfontosságú az üzleti eszközök védelmének első lépése. A biztonságos információkhoz vagy erőforrásokhoz hozzáférő személyek számának minimalizálása csökkenti annak az esélyét, hogy egy rosszindulatú felhasználó hozzáférjen az adatokhoz, vagy ha egy jogosult felhasználó véletlenül egy bizalmas erőforrást érint.

A Kiemelt jogosultságú fiókok olyan fiókok, amelyek az informatikai rendszereket felügyelik és kezelik. A Cyber-támadók megcélozzák ezeket a fiókokat, hogy hozzáférjenek a szervezet adateszközeihez és rendszereihez. A rendszerjogosultságú hozzáférés biztonságossá tételéhez el kell különíteni a fiókokat és a rendszereket a rosszindulatú felhasználók számára elérhető kockázattól.

Javasoljuk, hogy fejlesszen és kövessen egy olyan ütemtervet, amely biztonságos hozzáférést biztosít a Cyber-támadók ellen. További információ az Azure AD-ben, az Microsoft Azure-ban, az Office 365-ben és más felhőalapú szolgáltatásokban kezelt vagy jelentett identitások és hozzáférés biztonságossá tételéről, valamint az Azure AD-ben a [hibrid és Felhőbeli üzemelő példányokhoz ](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

A következő összefoglalja az [Azure ad-ben az emelt szintű hozzáférés biztonságossá tételével kapcsolatos](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)ajánlott eljárásokat:

**Ajánlott eljárás**: A Kiemelt fiókok hozzáférésének kezelése, szabályozása és figyelése.   
**Részletek**: [Azure ad Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)bekapcsolása. A Privileged Identity Management bekapcsolását követően értesítési e-mail-üzeneteket fog kapni az emelt szintű hozzáférési szerepkör változásairól. Ezek az értesítések korai figyelmeztetést nyújtanak, ha további felhasználókat adnak hozzá a címtár magas jogosultsági szintű szerepköreihez.

**Ajánlott eljárás**: Győződjön meg arról, hogy az összes kritikus rendszergazdai fiók felügyelt Azure AD-fiókok.
**Részletek**: Távolítsa el a felhasználói fiókokat a kritikus rendszergazdai szerepkörökből (például Microsoft-fiókok, például hotmail.com, live.com és outlook.com).

**Ajánlott eljárás**: Győződjön meg arról, hogy az összes kritikus rendszergazdai szerepkör külön fiókkal rendelkezik a felügyeleti feladatokhoz, hogy elkerülje az adathalászatot és más támadásokat a rendszergazdai jogosultságokkal szemben.
**Részletek**: Hozzon létre egy külön rendszergazdai fiókot, amely a rendszergazdai feladatok végrehajtásához szükséges jogosultságokat rendeli hozzá. Ezen rendszergazdai fiókok használatának letiltása a napi hatékonyságnövelő eszközökhöz, például Microsoft Office 365 e-mailekhez vagy tetszőleges webes böngészéshez.

**Ajánlott eljárás**: A magas jogosultsági szintű szerepkörökben lévő fiókok azonosítása és kategorizálása.   
**Részletek**: Azure AD Privileged Identity Management bekapcsolását követően tekintse meg a globális rendszergazda, a Kiemelt szerepkörű rendszergazda és más magas jogosultsági szintű szerepkörök felhasználóit. Távolítsa el azokat a fiókokat, amelyekre már nincs szükség ezekben a szerepkörökben, és kategorizálja a rendszergazdai szerepkörökhöz rendelt többi fiókot:

- Egyénileg rendelhető hozzá a rendszergazda felhasználókhoz, és nem rendszergazdai célokra használható (például személyes e-mail-cím)
- Egyénileg rendelhető hozzá a rendszergazda felhasználókhoz, és csak felügyeleti célokra van kijelölve
- Több felhasználó között megosztott
- Vészhelyzeti hozzáférési forgatókönyvek esetén
- Automatikus parancsfájlok esetén
- Külső felhasználók számára

**Ajánlott eljárás**: Az "igény szerinti" (JIT) hozzáférés megvalósítása a jogosultságok expozíciós idejének további csökkentéséhez és a Kiemelt fiókok használatának növeléséhez.   
**Részletek**: A Azure AD Privileged Identity Management a következőket teszi lehetővé:

- Korlátozza a felhasználókat arra, hogy csak a jogosultságokat JIT-re vegyék.
- A lerövidített időtartamhoz rendeljen hozzá szerepköröket úgy, hogy a jogosultságok automatikusan visszavonásra kerülnek.

**Ajánlott eljárás**: Legalább két vészhelyzeti hozzáférési fiókot adjon meg.   
**Részletek**: A vészhelyzeti hozzáférési fiókok segítenek a szervezeteknek a rendszerjogosultságú hozzáférés korlátozásában egy meglévő Azure Active Directory környezetben. Ezek a fiókok Kiemelt jogosultságokkal rendelkeznek, és nem adott személyekhez vannak rendelve. A sürgősségi hozzáférési fiókok olyan forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok. A szervezeteknek csak a szükséges időtartamra kell korlátoznia a segélyhívó fiók használatát.

Értékelje ki azokat a fiókokat, amelyek a globális rendszergazdai szerepkörhöz vannak rendelve vagy jogosultak. Ha nem látja a csak felhőalapú fiókokat a `*.onmicrosoft.com` tartomány használatával (vészhelyzeti hozzáférés céljára), hozza létre őket. További információ: [a sürgősségi hozzáférés felügyeleti fiókjainak kezelése az Azure ad-ben](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Ajánlott eljárás**: Vészhelyzet esetén a "break Glass" folyamatra van szükség.
**Részletek**: Kövesse az [Azure ad-ben az emelt szintű hozzáférés biztonságossá tétele a hibrid és](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)a felhőben történő üzembe helyezéshez című témakör lépéseit.

**Ajánlott eljárás**: Az összes kritikus rendszergazdai fiók jelszavának lekérése (előnyben részesített) vagy Multi-Factor Authentication megkövetelése.
**Részletek**: A [Microsoft Authenticator alkalmazással](/azure/active-directory/authentication/howto-authentication-phone-sign-in) jelszó használata nélkül jelentkezhet be bármely Azure ad-fiókba. A [vállalati Windows Hello-hez](/windows/security/identity-protection/hello-for-business/hello-identity-verification)hasonlóan a Microsoft Authenticator a kulcs-alapú hitelesítés használatával engedélyezi az eszközhöz kötött felhasználói hitelesítő adatokat, és biometrikus hitelesítést vagy PIN-kódot használ.

Az Azure-Multi-Factor Authentication bejelentkezéskor minden olyan felhasználóhoz, aki véglegesen hozzá van rendelve egy vagy több Azure AD-rendszergazdai szerepkörhöz, be kell jelentkeznie: Globális rendszergazda, Kiemelt szerepkörű rendszergazda, Exchange Online-rendszergazda és SharePoint Online-rendszergazda. Engedélyezze [a rendszergazdai fiókok multi-Factor Authenticationét](/azure/active-directory/authentication/howto-mfa-userstates) , és győződjön meg arról, hogy a rendszergazdai fiók felhasználóinak regisztrálva vannak.

**Ajánlott eljárás**: A kritikus rendszergazdai fiókok esetében olyan felügyeleti munkaállomás szükséges, amelyben a termelési feladatok nem engedélyezettek (például a böngészés és az e-mail). Ez biztosítja a rendszergazdai fiókokat a böngészést és e-mailt használó támadási vektorokból, és jelentősen csökkenti a jelentős incidensek kockázatát.
**Részletek**: Használjon rendszergazdai munkaállomást. Válassza ki a munkaállomás biztonságának szintjét:

- A fokozottan biztonságos hatékonyságnövelő eszközök fokozott biztonságot nyújtanak a böngészéshez és más hatékonyságnövelő feladatokhoz.
- Az emelt [szintű hozzáférésű munkaállomások (mancsok)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) egy dedikált operációs rendszert biztosítanak, amely az internetes támadásokkal és a bizalmas feladatokhoz kapcsolódó veszélyforrásokkal védett.

**Ajánlott eljárás**: Rendszergazdai fiókok kiépítése, amikor az alkalmazottak elhagyják a szervezetét.
**Részletek**: Olyan folyamattal rendelkezik, amely letiltja vagy törli a rendszergazdai fiókokat, amikor az alkalmazottak elhagyják a szervezetét.

**Ajánlott eljárás**: Az aktuális támadási technikák használatával rendszeresen tesztelheti a rendszergazdai fiókokat.
**Részletek**: Az Office 365 Attack Simulator vagy egy harmadik féltől származó ajánlat használatával reális támadási forgatókönyveket futtathat a szervezetében. Ez segít a veszélyeztetett felhasználók megtalálásában a valódi támadás előtt.

**Ajánlott eljárás**: Végezze el a leggyakoribb megtámadott technikák enyhítésének lépéseit.  
**Részletek**: [Azonosítsa a Microsoft-fiókokat olyan rendszergazdai szerepkörökben, amelyeket munkahelyi vagy iskolai fiókokra kell váltani](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Különálló felhasználói fiókok és a levelezés továbbítása globális rendszergazdai fiókokhoz](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Győződjön meg arról, hogy a rendszergazdai fiókok jelszavai nemrég módosultak](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Jelszó-kivonat szinkronizálásának bekapcsolása](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Multi-Factor Authentication megkövetelése minden Kiemelt szerepkörrel rendelkező és a felhasználók számára elérhető felhasználók számára](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Az Office 365 biztonságos pontszámának beszerzése (az Office 365 használata esetén)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Tekintse át az Office 365 biztonsági és megfelelőségi útmutatóját (ha az Office 365-at használja)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365-tevékenységek figyelése (az Office 365 használata esetén)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Incidens/vészhelyzeti válasz terv tulajdonosainak létrehozása](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Biztonságos helyszíni privilegizált rendszergazdai fiókok](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Ha nem védi a Kiemelt jogosultságú hozzáférést, előfordulhat, hogy túl sok felhasználója van magas jogosultsági szintű szerepkörben, és a támadásokkal szemben sebezhető. Rosszindulatú szereplők, beleértve a Cyber-támadókat, gyakran a rendszergazdai fiókokat és a privilegizált hozzáférés más elemeit a bizalmas adatokhoz és rendszerekhez való hozzáféréshez a hitelesítő adatok ellopásával.

## <a name="control-locations-where-resources-are-created"></a>Az erőforrások létrehozásának vezérlési helyei

A Felhőbeli operátorok számára a feladatok elvégzésének engedélyezése, miközben a szervezet erőforrásainak kezeléséhez szükséges szabályok megszegésének megakadályozása nagyon fontos. Azok a szervezetek, amelyek szabályozni szeretnék, hogy mely helyekről kell létrehozni az erőforrásokat, ezeket a webhelyeket is fel kell használni

A [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) használatával olyan biztonsági házirendeket hozhat létre, amelyek definíciói a kifejezetten megtagadott műveleteket vagy erőforrásokat írják le. Ezeket a házirend-definíciókat a kívánt hatókörhöz, például az előfizetéshez, az erőforráscsoporthoz vagy egy adott erőforráshoz kell rendelni.

> [!NOTE]
> A biztonsági házirendek nem egyeznek meg a RBAC. Valójában a RBAC használatával engedélyezik a felhasználók számára az erőforrások létrehozását.
>
>

Azok a szervezetek, amelyek nem szabályozzák, hogy az erőforrások hogyan jöjjenek létre, érzékenyebbek azokra a felhasználókra, akik a szükségesnél több erőforrást hoznak létre. Az erőforrás-létrehozási folyamat megerősítése fontos lépés egy több-bérlős forgatókönyv biztonságossá tételéhez.

## <a name="actively-monitor-for-suspicious-activities"></a>Gyanús tevékenységek aktív figyelése

Az aktív identitás-figyelési rendszer gyorsan képes észlelni a gyanús viselkedést, és riasztást indít a további vizsgálathoz. Az alábbi táblázat két olyan Azure AD-funkciót sorol fel, amelyek segítségével a szervezetek megtekinthetik az identitásuk körét:

**Ajánlott eljárás**: A következő módszert kell azonosítani:

- Kísérlet történt a bejelentkezés [nyomon követése nélkül](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- [Találgatásos](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) támadás egy adott fiókkal szemben.
- Megpróbál több helyről bejelentkezni.
- [Fertőzött eszközökről](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices)történő bejelentkezések.
- Gyanús IP-címek.

**Részletek**: Prémium szintű Azure AD anomália- [jelentések](/azure/active-directory/active-directory-view-access-usage-reports)használata. Olyan folyamatokkal és eljárásokkal kell rendelkezniük, amelyekkel a rendszergazdák napi vagy igény szerint futtathatják ezeket a jelentéseket (általában incidensek esetén).

**Ajánlott eljárás**: Olyan aktív figyelési rendszerrel kell rendelkeznie, amely értesíti a kockázatokról, és a kockázati szintet (magas, közepes vagy alacsony) módosíthatja üzleti igényei szerint.   
**Részletek**: Használja a [Azure ad Identity Protection](/azure/active-directory/active-directory-identityprotection), amely a saját irányítópultján lévő aktuális kockázatokat és napi összefoglaló értesítéseket küld e-mailben. A szervezete identitásának védelméhez olyan kockázatalapú házirendeket állíthat be, amelyek automatikusan válaszolnak az észlelt problémákra, amikor elérik egy adott kockázati szintet.

Azok a szervezetek, amelyek nem figyelik aktívan a személyazonossági rendszerüket, a felhasználó hitelesítő adatai veszélyben vannak. Ha nem tudja, hogy a gyanús tevékenységek ezen hitelesítő adatokon keresztül zajlanak, a szervezetek nem tudják elhárítani az ilyen típusú fenyegetést.

## <a name="use-azure-ad-for-storage-authentication"></a>Az Azure AD használata tárolási hitelesítéshez
Az [Azure Storage](/azure/storage/common/storage-auth-aad) támogatja a hitelesítést és az engedélyezést az Azure ad-vel a blob Storage és a várólista tárolásához. Az Azure AD-hitelesítéssel az Azure szerepköralapú hozzáférés-vezérlésével konkrét engedélyeket biztosíthat a felhasználóknak, csoportoknak és alkalmazásoknak egy adott blob-tároló vagy-várólista hatóköréhez.

Javasoljuk, hogy [Az Azure ad használatával hitelesítse a tárolóhoz való hozzáférést](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Következő lépés

Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági](best-practices-and-patterns.md) eljárásaival és modelljeivel foglalkozó témakörben talál.
