---
title: Az Azure identity & access ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ez a cikk számos ajánlott eljárások az Identitáskezelés és hozzáférés-vezérlés használatával a beépített Azure-képességek.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2a669f5b46db4d5de7d1d6863b94e6c117667aee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153242"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Az Azure identitáskezelési és hozzáférés-vezérlés ajánlott biztonsági eljárások
Ebben a cikkben bemutatjuk, Azure-Identitáskezelés és hozzáférés vezérlés ajánlott biztonsági eljárások gyűjteménye. Ajánlott eljárások vannak származtatva tapasztalatainkon [Azure ad-ben](../active-directory/fundamentals/active-directory-whatis.md) és az ügyfelek a funkciókat, például saját magának.

A minden egyes ajánlott eljárás az hogy ismertetik:

* Mi az az ajánlott eljárás szerint
* Miért ajánlott eljárás, hogy engedélyezni szeretné
* Mi lehet az eredmény, ha Ön nem engedélyezi az ajánlott eljárás szerint
* Az ajánlott eljárás szerint lehetséges alternatívák
* Hogyan tudhat meg az ajánlott eljárás engedélyezése

Az Azure-Identitáskezelés és hozzáférés-vezérléssel – gyakorlati tanácsok cikk alapján egy konszenzus véleményével és az Azure platform képességeit és szolgáltatáskészleteket, időben léteznek ebben a cikkben íródott. Vélemények és technológiák időbeli változásait, és ez a cikk a változások követése érdekében rendszeresen frissül.

Azure-identitás és hozzáférés vezérlés ajánlott biztonsági eljárások a cikkben leírtak szerint a következők:

* Identitás tekinti az elsődleges biztonsági határ
* Identitáskezelés központosítása
* Csatlakoztatott bérlők kezelése
* Egyszeri bejelentkezés engedélyezése
* Kapcsolja be a feltételes hozzáférés
* Jelszókezelés engedélyezése
* A felhasználók számára a többtényezős ellenőrzési kényszerítése
* Szerepköralapú hozzáférés-vezérlés alkalmazása
* Alacsonyabb kitettség kiemelt jogosultságú fiókok
* Hol találhatók az erőforrások vezérlő helyek
* Storage-hitelesítéshez használt Azure ad-ben

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Identitás tekinti az elsődleges biztonsági határ

Számos fontolja meg kell az elsődleges szegélyhálózat-alapú biztonsági identitás. Ez az a shift a hálózati biztonság hagyományos koncentrálhat. Régebben a hálózat több elválasztó továbbra is megkapja, és a külső védelem nem lehet a felbontására előtti hatásos [BYOD](https://aka.ms/byodcg) eszközök és a felhőbeli alkalmazásokhoz.

[Az Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) identitás és hozzáférés-kezelés az Azure-megoldás. Az Azure AD szolgáltatás több-bérlős felhőalapú címtár és Identitáskezelés felügyeleti szolgáltatás a Microsoft. Amely ötvözi az alapvető címtárszolgáltatásokat, a alkalmazáshozzáférés-felügyeletet és az identity protection egyetlen megoldás.

Az alábbi szakaszok az Azure AD identitás- és hozzáférés biztonsági védelmének bevált gyakorlata listája.

## <a name="centralize-identity-management"></a>Identitáskezelés központosítása

Az egy [hibrid identitás](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) a forgatókönyvben javasoljuk, hogy integrálja a helyszíni és felhőbeli címtárakban. Integrációja lehetővé teszi az informatikai csapat fiókokat szeretne kezelni egy helyről, függetlenül attól, amelyben egy új fiókot létrehozza. Integráció a felhasználók hatékonyságának növelése felhőalapú és a helyszíni erőforrások eléréséhez közös identitás megadásával is segít.

**Ajánlott eljárás**: Egyetlen létesíteni az Azure AD-példányt. Konzisztencia és a egy egyetlen mérvadó források növelje az egyértelműség érdekében, és az emberi hibák és a konfigurációs feladatok összetettsége a biztonsági kockázat csökkentésének.
**Részletes**: Egyetlen Azure AD által kijelölt könyvtár vállalati és a szervezeti fiókok hiteles adatforrásként.

**Ajánlott eljárás**: A helyszíni címtárak integrálása az Azure ad-ben.  
**Részletes**: Használat [az Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) szinkronizálni szeretné a helyszíni címtárát a felhőalapú címtárral.

> [!Note]
> Nincsenek [az Azure AD Connect teljesítményét befolyásoló tényezőket](../active-directory/hybrid/plan-connect-performance-factors.md). Győződjön meg arról, az Azure AD Connect tartani az alulteljesítő akadályozó biztonság és hatékonyság rendszerek elegendő kapacitással rendelkezik. Nagy vagy összetett szervezetek (a szervezetek több mint 100 000 objektumra kiépítése) érdemes követnie a [javaslatok](../active-directory/hybrid/whatis-hybrid-identity.md) optimalizálása az Azure AD Connect végrehajtás.

**Ajánlott eljárás**: Ne legyen szinkronizálás az Azure AD-fiókok, amelyek magas szintű jogosultságokkal rendelkeznek a meglévő Active Directory-példányában.
**Részletes**: Ne módosítsa az alapértelmezett [Azure AD Connect konfiguráció](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) , amely kiszűri a ezeket a fiókokat. Ez a konfiguráció csökkenti a támadók a felhőből (amely sikerült jelentős incidens létrehozása) a helyszíni tartalmakhoz való frissítésének kockázatát.

**Ajánlott eljárás**: Kapcsolja be a Jelszókivonat-szinkronizálást.  
**Részletes**: A Jelszókivonat-szinkronizálás funkciója synch felhasználói jelszókivonatokat egy helyszíni Active Directory-példányból egy felhőbeli Azure-ban használt AD-példányt. A szinkronizálás segít a kiszivárgott hitelesítő adatok alatt játssza vissza az előző támadások elleni védelem érdekében.

Akkor is, ha úgy dönt, hogy összevonási használata az Active Directory összevonási szolgáltatások (AD FS) vagy az egyéb identitás-szolgáltatóktól, igény szerint állíthat be a Jelszókivonat-szinkronizálás biztonsági abban az esetben a helyszíni kiszolgálók sikertelen, vagy ideiglenesen elérhetetlenné válnak. A szinkronizálás lehetővé teszi a felhasználók számára, hogy jelentkezzen be a szolgáltatásba ugyanazzal a jelszóval jelentkezzen be a helyszíni Active Directory-példányból használnak. Lehetővé teszi továbbá a hitelesítő adatokkal való visszaélés észlelése szinkronizált jelszavak kivonatait összehasonlításával jelszavak ismert, hogy sérült, ha a felhasználó rendelkezik az e-mail címet és jelszót más szolgáltatásokban használt, amelyek nem csatlakoznak az Azure AD Identity Protection.

További információkért lásd: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Ajánlott eljárás**: Az új alkalmazásfejlesztéshez Azure AD-hitelesítés használata.
**Részletes**: Használja a megfelelő képességeket hitelesítés támogatásához:

  - Az Azure AD-alkalmazottak számára
  - [Az Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) vendég felhasználók és a külső partnerek számára
  - [Az Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) szabályozhatja, hogy az ügyfelek regisztrálni, jelentkezzen be, és profiljuk kezelését, az alkalmazások használata

Szervezetek számára, amelyek a helyszíni identitás nem integrálható a felhőbeli identitás rendelkezhet több terhelést jelenthet a fiókok kezelése. Ez a terhelés hozzáadódik növeli az elágazás hibákkal és biztonsági résekkel szemben.

> [!Note]
> Kell választania, melyik kritikus fiókok fogja befolyásolni a, és feldolgozza a felügyeleti munkaállomásról használt-e új felhőalapú szolgáltatások által felügyelt vagy a meglévő címtárakban. Meglévő felügyeleti és identitáskialakítási folyamatok használatával csökkentheti a kockázatai, de a támadók veszélyeztetése egy helyi fiókot, és a felhőbe való frissítésének kockázatát is létrehozhat. Előfordulhat, hogy szeretné használni egy másik stratégia más szerepkörök (például informatikai rendszergazdák és az üzleti egység rendszergazdák összehasonlítása). Két lehetősége van. Első lehetőség, ha az Azure AD-fiókok, amelyek nincsenek szinkronizálva a helyszíni Active Directory-példányból. Csatlakozzon a felügyeleti munkaállomásról az Azure AD, amely kezelheti és javítani a Microsoft Intune-nal. Második lehetőség egy létező felügyeleti fiókok használata által szinkronizálja a helyszíni Active Directory-példányból. Az Active Directory-tartományban lévő meglévő munkaállomások használata felügyelet és biztonság.

## <a name="manage-connected-tenants"></a>Csatlakoztatott bérlők kezelése
Biztonsági szervezetének szüksége van, hogy látható-e kockázatfelmérés és teljesültek-e a házirendek és a szervezet bármely szabályozási követelmények meghatározása. Győződjön meg arról, hogy a biztonsági szervezet rendelkezik-e csatlakoztatva a termelési környezetben és a hálózat összes előfizetés betekintést (keresztül [Azure ExpressRoute](../expressroute/expressroute-introduction.md) vagy [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). A [globális rendszergazdai vagy vállalati rendszergazdai](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) az Azure ad-ben való hozzáférésüket megszemélyesítve a [felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkört, és tekintse meg az összes előfizetés és csatlakozik a környezeti kezelt csoportok.

Lásd: [kezelhető az összes Azure-előfizetések és a felügyeleti csoportok a hozzáférési szintjének emelése](../role-based-access-control/elevate-access-global-admin.md) , győződjön meg arról, hogy Ön és a biztonsági csoport tekintheti meg az összes előfizetést vagy felügyeleti csoportok csatlakoztatva a környezethez. Miután értékelte a kockázatokat, el kell távolítania az emelt szintű hozzáférés.

## <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

Mobileszközök és a felhő-és felhőközpontú világában a szeretné engedélyezni, egyszeri bejelentkezés (SSO) az eszközök, alkalmazások és szolgáltatások bárhonnan, a felhasználók számára hatékony munkavégzést bárhol is legyenek, és minden alkalommal, amikor. Ha több identitáskezelési megoldások kezelése, ez lesz a nem csupán a felügyeleti probléma informatikai is a felhasználók számára meg kell jegyezniük több jelszóra.

Az azonos identitáskezelési megoldás az alkalmazások és erőforrások használatával érheti el egyszeri Bejelentkezést. És a felhasználók ugyanazokat a hitelesítő adatok segítségével jelentkezzen be, és elérhessék az erőforrásokat, hogy kell, hogy az erőforrásokat a helyszínen legyenek-e, vagy a felhőben.

**Ajánlott eljárás**: Engedélyezze az egyszeri Bejelentkezést.  
**Részletes**: Az Azure AD [kiterjeszti a helyszíni Active Directory](../active-directory/connect/active-directory-aadconnect.md) a felhőbe. A tartományhoz csatlakoztatott eszközök a vállalati erőforrásokhoz és a web- és SaaS-alkalmazásokhoz, amelyek a munkájuk elvégzéséhez szükséges összes felhasználó az elsődleges munkahelyi vagy iskolai fiókkal használható. Felhasználóknak nem kell észben felhasználónevei és jelszavai között több példányban is, és az alkalmazás-hozzáférés is automatikusan üzembe helyezett (vagy eltávolítása) szervezet csoporttagságok és a egy alkalmazott állapotuk alapján. A katalógusban szereplő alkalmazások, illetve az [Azure AD-alkalmazásproxy](../active-directory/active-directory-application-proxy-get-started.md) használatával kifejlesztett és közzétett saját helyszíni alkalmazások hozzáférése is szabályozható.

Egyszeri bejelentkezés segítségével a felhasználók számára elérhetővé a [SaaS-alkalmazások](../active-directory/active-directory-appssoaccess-whatis.md) alapján a munkahelyi vagy iskolai fiókjával az Azure ad-ben. Ez akkor nem csupán a Microsoft SaaS-alkalmazások, hanem más alkalmazásoknál, mint például [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) és [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Beállíthatja, hogy az alkalmazás használhatja az Azure ad-hez mint egy [SAML-alapú identitás](../active-directory/fundamentals-identity.md) szolgáltató. Az Azure AD biztonsági vezérlőként nem bocsát ki egy jogkivonatot, amely lehetővé teszi a felhasználóknak az alkalmazás bejelentkezni, kivéve, ha azok még kapott hozzáférést az Azure AD-n keresztül. Lehet hozzáférést biztosítani közvetlenül, vagy egy csoportot, hogy a felhasználó a tagja.

Szervezetek számára, amelyek egy közös identitást létrehozni egyszeri Bejelentkezést a felhasználók és alkalmazások ne hozzon létre több jelennek meg a forgatókönyvek, ahol a felhasználók rendelkeznek több jelszóra. Ezekben az esetekben a felhasználók a jelszavak újbóli használata és gyenge jelszót valószínűségének növelése.

## <a name="turn-on-conditional-access"></a>Kapcsolja be a feltételes hozzáférés

Felhasználók a szervezet erőforrásaihoz férhetnek hozzá az eszközök és alkalmazások számos segítségével bárhonnan. INFORMATIKAI rendszergazdaként szeretné győződjön meg arról, hogy ezek az eszközök megfelelnek-e a biztonsági és megfelelőségi szabványoknak. Csak összpontosító férhet hozzá egy erőforrás már nem elegendő.

Elosztja a biztonság és hatékonyság, gondolja át hogyan erőforrás érhető el, mielőtt egy hozzáférés-vezérléssel kapcsolatos döntést kell. Az Azure AD feltételes hozzáférésével megfelelhet ennek a követelménynek. A feltételes hozzáférés automatikus döntést hozhasson a hozzáférésről a felhőalkalmazásokhoz hozzáférő feltételei alapján teheti meg.

**Ajánlott eljárás**: Kezelheti, és a vállalati erőforrásokhoz való hozzáférés szabályozása.  
**Részletes**: Konfigurálja az Azure AD- [feltételes hozzáférési](../active-directory/active-directory-conditional-access-azure-portal.md) egy csoport, a hely és a SaaS-alkalmazások és az Azure AD-hez csatlakozó alkalmazásokat az alkalmazás bizalmassági szintje alapján.

**Ajánlott eljárás**: Az örökölt hitelesítési protokollok blokkolására.
**Részletes**: A támadók kiaknázhatóak a régebbi protokollok minden nap, különösen a jelszó megfelelő támadásokkal szemben. Konfigurálja a feltételes hozzáférést az örökölt protokollok blokkolására. Tekintse meg a videó [Azure ad-ben: Javaslatok és nem ajánlott műveletek](https://www.youtube.com/watch?v=wGk0J4z90GI) további információt.

## <a name="enable-password-management"></a>Jelszókezelés engedélyezése

Ha több bérlő vagy felhasználók számára engedélyezni kívánja [alaphelyzetbe állíthatja saját jelszavait](../active-directory/active-directory-passwords-update-your-own-password.md), fontos, hogy a megfelelő biztonsági házirendek visszaélés megakadályozására használja.

**Ajánlott eljárás**: Állítsa be a felhasználók önkiszolgáló jelszó-visszaállítás (SSPR).  
**Részletes**: Az Azure AD használata [önkiszolgáló jelszó-visszaállítási](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funkció.

**Ajánlott eljárás**: A figyelő hogyan, vagy ha az SSPR igazán használatban van.  
**Részletes**: Figyelheti a felhasználókat, akik az Azure AD-vel regisztrálja [jelszó alaphelyzetbe állítása regisztrációs Tevékenységjelentés](../active-directory/active-directory-passwords-get-insights.md). Az Azure AD által nyújtott jelentéskészítési szolgáltatás segít előre elkészített jelentéseket a kérdésre. Ha a megfelelően licencelt, is létrehozhat egyéni lekérdezéseket.

**Ajánlott eljárás**: Jelszóházirendek felhőalapú és a helyszíni infrastruktúra kiterjesztése.
**Részletes**: Növelheti a szervezet jelszóházirendek azonos ellenőrzések végrehajtása a következő helyi jelszó módosítására is felhőalapú jelszó módosítására. Telepítés [az Azure AD jelszóvédelem](../active-directory/authentication/concept-password-ban-bad.md) Windows Server Active Directory ügynökök a helyszíni letiltott jelszavak listáit, a meglévő infrastruktúra kiterjesztésére. Felhasználók és rendszergazdák, akik számára megváltoztatja, állítsa be, vagy csak felhőalapú felhasználói is ugyanaz a jelszó szabályzat ahhoz, hogy szükség-e a helyszíni jelszavak alaphelyzetbe állítása.

## <a name="enforce-multi-factor-verification-for-users"></a>A felhasználók számára a többtényezős ellenőrzési kényszerítése

Azt javasoljuk, hogy minden felhasználó számára szükséges a kétlépéses ellenőrzés. Ez magában foglalja a rendszergazdák és más szervezetben, akik jelentős hatással lehet, ha a fiók biztonsága sérül (például a pénzügyi igazgatók).

A kétlépéses ellenőrzést igénylő több lehetőség van. Az Ön számára legmegfelelőbb a céljai elérése érdekében futtatja az Azure AD-kiadástól és a licencelési program függ. Lásd: [igénylése a kétlépéses ellenőrzés egy felhasználó](../active-directory/authentication/howto-mfa-userstates.md) meghatározni a legmegfelelőbb az Ön számára. Tekintse meg a [Azure ad-ben](https://azure.microsoft.com/pricing/details/active-directory/) és [Azure multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási lapjait licencekkel kapcsolatos további részletekért és a díjszabás.

Az alábbiakban lehetőségeket és előnyöket a kétlépéses ellenőrzés engedélyezése:

**1. lehetőség**: [Többtényezős hitelesítés engedélyezése felhasználói állapot módosításával](../active-directory/authentication/howto-mfa-userstates.md).   
**Értékelemek**: Ez az a kétlépéses ellenőrzést igénylő hagyományos módszere. Mindkét együttműködik [Azure multi-factor Authentication a felhőben és az Azure multi-factor Authentication-kiszolgáló](../active-directory/authentication/concept-mfa-whichversion.md). Ezzel a módszerrel megköveteli a felhasználóktól a kétlépéses hitelesítés végrehajtására, minden alkalommal, amikor bejelentkeznek, és felülírja a feltételes hozzáférési szabályzatokat.

Annak meghatározásához, ahol a multi-factor Authentication szolgáltatás engedélyeznie kell, lásd: [Azure MFA melyik verziója a legmegfelelőbb a szervezetem számára?](../active-directory/authentication/concept-mfa-whichversion.md).

**2. lehetőség**: [Többtényezős hitelesítés engedélyezése a feltételes hozzáférési szabályzattal együtt](../active-directory/authentication/howto-mfa-getstarted.md).
**Értékelemek**: Ez a beállítás lehetővé teszi a kétlépéses ellenőrzéshez meghatározott feltételek használatával kéri [feltételes hozzáférési](../active-directory/active-directory-conditional-access-azure-portal.md). Az egyes esetekben a felhasználó jelentkezzen be a különböző helyeken, nem megbízható eszközök vagy alkalmazások, amelyeket kockázatosnak lehet. Az egyes esetekben, ahol szükséges a kétlépéses ellenőrzés meghatározása lehetővé teszi, hogy ne állandó kérő üzenet, a felhasználók számára, amely lehet kellemetlen felhasználói felületet.

Ez az a leginkább rugalmas módon engedélyezze a kétlépéses ellenőrzést, a felhasználók számára. Egy feltételes hozzáférési szabályzat engedélyezése csak az Azure multi-factor Authentication a felhőben működik, és az Azure AD prémium szintű funkció. Ez a módszer a további tájékoztatást talál [üzembe helyezése a felhőalapú Azure multi-factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

**3 lehetőség**: Multi-factor Authentication szolgáltatás engedélyezése a feltételes hozzáférési szabályzatokat felhasználói és bejelentkezési kockázatát kiértékelésével [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Értékelemek**: Ez a beállítás teszi lehetővé:

- A szervezet identitásait érintő lehetséges biztonsági résekről észleli.
- Konfigurálja az automatikus válaszok észlelt gyanús tevékenységeket, amelyek kapcsolódnak a szervezet identitásait.
- Gyanús incidensek kivizsgálásában, és megteheti a szükséges lépéseket a problémák megoldásához.

A módszert használ, az Azure AD Identity Protection kockázat kiértékelésekor a kétlépéses ellenőrzéshez szükséges, hogy a felhasználó és minden felhőalapú alkalmazásra bejelentkezési kockázat alapján. Ez a metódus szükséges, Azure Active Directory P2 licencelési. Ez a módszer a további tájékoztatást talál [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> 1. lehetőség – a multi-factor Authentication szolgáltatás engedélyezése a felhasználói állapot módosításával felülbírálja a feltételes hozzáférési szabályzatokat. 2. és 3 beállítások feltételes hozzáférési szabályzatokat használ, mert azokat az 1. lehetőség nem használható.

A szervezetek, ne adjon hozzá további védelmi rétegeket, identitás, például a kétlépéses ellenőrzést, jobban ki a hitelesítő adatok ellopását alkalmazó támadásokkal. A hitelesítő adatok ellopását alkalmazó támadásokkal a biztonsági adatok vezethet.

## <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés alkalmazása
A felhőbeli erőforrások hozzáférés-kezelés, kritikus fontosságú, hogy a felhő minden szervezet számára. [Szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) segítségével kezelheti az Azure-erőforrásokhoz hozzáférő, mit tehet a erőforrásokkal és milyen területeken hozzáféréssel rendelkeznek.

Csoportok vagy egyéni szerepkörök az Azure-ban meghatározott funkciók felelős kijelölő elkerülhetők az zavart okozhat, vezethet, emberi és automatizálási a hibákat, hozzon létre biztonsági kockázatokat. A hozzáférés korlátozása alapján a [ismernie kell](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági alapelveket elengedhetetlen, szervezetek által adatok elérésére vonatkozó biztonsági szabályzatok kikényszerítéséhez.

A biztonsági csapat által igényelt az Azure-erőforrások felmérése és kockázatkezelésre láthatósága. Ha a biztonsági csoport műveleti feladatokra, további engedélyeket a feladataik elvégzéséhez szükségük van.

Használhat [RBAC](../role-based-access-control/overview.md) engedélyek hozzárendelése a felhasználók, csoportok és alkalmazások egy bizonyos hatókörben. Szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet.

**Ajánlott eljárás**: Feladatköröket a csapaton belül, és csak olyan mértékű hozzáférést biztosítson a felhasználók számára, amelyek a feladataik elvégzéséhez szükségük van. Az Azure-előfizetés vagy erőforrások, nem korlátozott engedélyekkel helyett, így mindenki lehetővé teszik a csak bizonyos műveleteket egy adott hatókörben.
**Részletes**: Használat [beépített RBAC-szerepkör](../role-based-access-control/built-in-roles.md) jogosultságok hozzárendelése a felhasználók számára az Azure-ban.

> [!Note]
> Konkrét engedélyeket szükségtelenül bonyolult és félreértések gyűlik "örökölt" konfigurációban nehezen elront valamit, félelem nélkül hozzon létre. Kerülje az erőforrás-specifikus engedélyek. Ehelyett használja a felügyeleti csoportok a nagyvállalati szintű engedélyeket és előfizetések engedélyekkel az erőforráscsoportok. Kerülje a felhasználó-specifikus engedélyek. Ehelyett hozzáférés hozzárendelése csoportokhoz az Azure ad-ben.

**Ajánlott eljárás**: Tekintse meg az Azure-erőforrások, így felmérheti és kockázatkezelésre feladataikat az Azure-hozzáféréssel rendelkező biztonsági csoportok megadása.
**Részletes**: Biztonsági csapat adja meg az RBAC [biztonsági olvasó](../role-based-access-control/built-in-roles.md#security-reader) szerepkör. Használhatja a legfelső szintű felügyeleti csoportot vagy a szegmens felügyeleti csoport feladatkörök függően:

- **Gyökérszintű felügyeleti csoport** összes vállalati erőforráshoz felelős csapatok számára
- **Felügyeleti csoport szegmens** (gyakran miatt szabályozási és más szervezet határain) korlátozott hatókörű fejlesztőcsapatoknak

**Ajánlott eljárás**: Adja meg a megfelelő engedélyeket a biztonsági csoportok számára, amelyek közvetlen működési feladatai.
**Részletes**: Tekintse át a beépített RBAC-szerepkörök, a megfelelő szerepkör-hozzárendelés. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat [egyéni szerepkörök az Azure-erőforrások](../role-based-access-control/custom-roles.md). Mivel a beépített szerepkörök, felhasználók, csoportok és szolgáltatásnevek előfizetés, erőforráscsoport és erőforrás-hatókörök is egyéni szerepkörök hozzárendelése.

**Ajánlott eljárások**: Hozzáférés biztosítása az Azure Security Center biztonsági szerepköröket, amelyek a szükséges. A Security Center lehetővé teszi, hogy a biztonsági csapat gyorsan azonosíthatja és kockázatok.
**Részletes**: Ezek igényekkel rendelkező biztonsági csoportok hozzáadása az RBAC [biztonsági rendszergazda](../role-based-access-control/built-in-roles.md#security-admin) szerepkör, így azok biztonsági szabályzatok megtekintéséhez, biztonsági állapotot, szerkessze a biztonsági szabályzatok, riasztások megtekintése és javaslatok és riasztások és javaslatok elvetése. Ehhez a gyökér felügyeleti csoport vagy a szegmens felügyeleti csoport használatával feladatkörök függően.

Olyan szervezeteknek, amelyek nem kényszerítenek adatok hozzáférés-vezérlése RBAC előfordulhat, hogy így további jogosultságok szükségesek, hogy a felhasználók, mint például a képességek használatával. Ez vezethet a biztonsági adatok felhasználók típusú adatok (például a nagy üzletmenetre gyakorolt hatás), amelyek nem rendelkeznek az eléréséhez.

## <a name="lower-exposure-of-privileged-accounts"></a>Alacsonyabb kitettség kiemelt jogosultságú fiókok

Biztonságossá tétele a privileged access üzleti eszközök védelmének első kritikus fontosságú lépés. Minimalizálja a biztonságos adatokat és erőforrásokat hozzáféréssel rendelkező személyek számát csökkenti az esélyét, hogy egy rosszindulatú felhasználó hozzáférhetne, vagy egy jogosult felhasználó véletlenül érintő az erőforrás-és nagybetűket.

Kiemelt jogosultságú fiókok olyan fiókok, felügyelheti és kezelheti az IT-rendszerekkel. Internetes támadók ezeket a fiókokat a szervezet adatok és rendszerek eléréséhez célozhat meg. Emelt szintű hozzáférés biztonságossá tételéhez, a fiókok és a kockázat, hogy egy rosszindulatú felhasználó rendszert kell elkülöníteni.

Azt javasoljuk, hogy fejleszthet, és hajtsa végre az internetes támadók emelt szintű hozzáférés biztonságossá tételéhez egy ütemtervet. A biztonságos identitás és hozzáférés, vagy az Azure ad-ben jelentette részletes ütemterv létrehozásáról további információt a Microsoft Azure, Office 365-höz és más cloud services áttekintése [Securing privileged hozzáférni a felhőbeli és hibrid környezetekben Az Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

A következő található az ajánlott eljárásokat foglalja össze [az Azure AD felhőalapú és hibrid környezetekben eléréséhez Securing privileged](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Ajánlott eljárás**: Kezelheti, irányíthatja, és figyelheti a kiemelt jogosultságú fiókok hozzáférést.   
**Részletes**: Kapcsolja be a [az Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Privileged Identity Management bekapcsolása után értesítést kap e-mailek a privilegizált hozzáférés érdekében szerepkörök változása. Ezek az értesítések magas jogosultsági szintű szerepkörökhöz a címtárban további felhasználók hozzáadásakor adja meg a korai figyelmeztetéseket.

**Ajánlott eljárás**: Győződjön meg, hogy az összes kritikus rendszergazdai fiók felügyelt Azure AD-fiókokat.
**Részletes**: Olyan fogyasztói fiókok eltávolítása kritikus rendszergazdai szerepkörök (például Microsoft-fiókok például hotmail.com, live.com és outlook.com).

**Ajánlott eljárás**: Győződjön meg arról, minden kritikus rendszergazdai szerepkörök annak érdekében, hogy adathalászattal és más támadásoknak veszélyeztetheti a rendszergazdai jogosultságokkal rendelkezik a felügyeleti feladatok egy külön fiókot.
**Részletes**: Hozzon létre egy külön rendszergazdai fiókot, amely hozzá van rendelve a a felügyeleti feladatok végrehajtásához szükséges jogosultságokkal. A napi hatékonyságnövelő eszközök, mint például a Microsoft Office 365 e-mailben vagy tetszőleges webböngészés az ilyen rendszergazdai fiókok használatának letiltása.

**Ajánlott eljárás**: Azonosítsa és kategorizálhatja a fiókok, amelyek magas szintű jogosultsággal rendelkező szerepkörök.   
**Részletes**: Az Azure AD Privileged Identity Management bekapcsolása, után a felhasználók, akik a globális rendszergazda, a kiemelt szerepkörű rendszergazda és az egyéb magas jogosultsági szintű szerepkörök megtekintése. Távolítsa el a fiókokat, amelyek már nincs szükség az ezeket a szerepköröket, és kategorizálja a megmaradt fiókokat hozzárendelt rendszergazdai szerepkörök:

- Külön-külön rendszergazdai jogosultságokkal rendelkező felhasználókhoz rendelt, és használhatja nem felügyeleti célú (például személyes e-mail-cím)
- Külön-külön hozzárendelt rendszergazdai jogosultságokkal rendelkező felhasználókhoz és a kijelölt csak felügyeleti célokra
- Több felhasználó között megosztott
- Vészhelyzeti hozzáférés-forgatókönyvek esetén
- Automatizált szkriptek esetében
- Külső felhasználók számára

**Ajánlott eljárás**: "Csak az időben" megvalósítása (szerinti JIT) hozzáférési tovább a jogosultságok expozíciós idejének, és jobb áttekinthetősége a rendszerjogosultságú fiókok használatát.   
**Részletes**: Az Azure AD Privileged Identity Management lehetővé teszi:

- Korlátozza a felhasználók számára csak az igény szerinti jogosultságai véve.
- Szerepkörök hozzárendelése egy rövidített időtartamának magabiztosan a jogosultságok automatikusan visszavonódnak.

**Ajánlott eljárás**: Adjon meg legalább két vészelérési fiókok.   
**Részletes**: A vészelérési fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy már létező Azure Active Directory-környezetben. Ezek a fiókok magas szintű jogosultságokkal a rendelkeznek, és nincsenek hozzárendelve a megadott személyeknek. A vészelérési fiókok korlátozva, ahol a szokásos rendszergazdai fiókok nem használható forgatókönyvek. Szervezetek csak a szükséges időn használatot a vészelérési fiókhoz kell korlátozni.

Értékelje ki a fiókokat, amelyek hozzárendelt vagy jogosult a globális rendszergazdai szerepkörhöz. Ha nem lát olyan csak felhőalapú fiókok használatával a `*.onmicrosoft.com` tartomány (vészelérési szánt), hozza létre őket. További információkért lásd: [rendszergazdai fiókok vészhelyzeti hozzáférésének kezelése az Azure ad-ben](../active-directory/users-groups-roles/directory-emergency-access.md).

**Ajánlott eljárás**: Rendelkezik egy "vészhelyzeti" folyamat helyen vészhelyzet esetén.
**Részletes**: Kövesse a [az Azure AD felhőalapú és hibrid környezetekben eléréséhez Securing privileged](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Ajánlott eljárás**: Megkövetelheti a jelszó nélküli minden kritikus rendszergazdai fiókoknak (preferált) vagy többtényezős hitelesítés megkövetelése.
**Részletes**: Használja a [Microsoft Authenticator alkalmazás](../active-directory/authentication/howto-authentication-phone-sign-in.md) bejelentkezni minden olyan Azure AD-fiókot jelszó használata nélkül. Például [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), a Microsoft Authenticator-alapú hitelesítést használ a felhasználói hitelesítő adatokat, amelyek egy eszközhöz kötődik, és használja a biometrikus hitelesítést, vagy egy PIN-kód engedélyezése.

Az Azure multi-factor Authentication megkövetelése bejelentkezéskor véglegesen egy vagy több Azure AD felügyeleti szerepkörök rendelt minden egyes felhasználók számára: Globális rendszergazda, a kiemelt szerepkörű rendszergazda, az Exchange Online rendszergazdai és a SharePoint Online-rendszergazda. Engedélyezése [multi-factor Authentication a rendszergazdai fiókok](../active-directory/authentication/howto-mfa-userstates.md) , és győződjön meg arról, hogy regisztrálták-e rendszergazdai fiókot használó felhasználók.

**Ajánlott eljárás**: Kritikus fontosságú rendszergazdai fiókok egy felügyeleti munkaállomásról, ahol nem engedélyezett az éles feladatok (a példában tallózása és e-mailben) rendelkezik. Ez a rendszergazdai fiókok elleni támadási vektorok használata, webböngészés és az e-mailt, és jelentősen csökkentheti a jelentős esemény kockázatát.
**Részletes**: Használja a felügyeleti munkaállomásról. Válassza ki a munkaállomás biztonság szintje:

- Rendkívül biztonságos hatékonyságnövelő eszközök történő tallózásának tartalombeállításait fejlett biztonsági és egyéb üzleti feladatok adja meg.
- [Az emelt szintű hozzáféréssel rendelkező munkaállomások (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) adja meg az internetről érkező támadások és a bizalmas feladatokhoz fenyegetési vektoroknak védett dedikált operációs rendszert.

**Ajánlott eljárás**: Rendszergazdai fiókok megszüntetése, ha az alkalmazottak elhagyják a szervezet.
**Részletes**: Egy olyan folyamat elkészítése, amely csak letiltja, vagy törli a rendszergazdai fiókokat, ha az alkalmazottak elhagyják a szervezet.

**Ajánlott eljárás**: Rendszergazdai fiókok rendszeresen tesztelje a jelenlegi támadási taktikák kockázatának használatával.
**Részletes**: Az Office 365 támadási szimulátor vagy egy külső ajánlat reális a támadási forgatókönyvek futtatása a szervezetben. Ez segíthet veszélyeztetett felhasználók keresése egy valós támadástól bekövetkezte előtt.

**Ajánlott eljárás**: Tegyen lépéseket a leggyakrabban használt megtámadott taktikák kockázatának csökkentése érdekében.  
**Részletes**: [Microsoft-fiókokat kíván állítani a munkahelyi vagy iskolai fiókok rendszergazdai szerepkör azonosítása](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Győződjön meg, hogy külön felhasználói fiókokat és a globális rendszergazdai fiókok levéltovábbítási](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Győződjön meg arról, hogy a rendszergazdai fiókok jelszava nemrég módosította](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Kapcsolja be a Jelszókivonat-szinkronizálás](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Többtényezős hitelesítés megkövetelése az összes kiemelt szerepkörű felhasználók, valamint a kitett felhasználók](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Szerezze be az Office 365-biztonságos pontszám (Ha használja az Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Olvassa el az Office 365 biztonsági és megfelelőségi útmutatást (Ha használja az Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurálja az Office 365 figyelését (Ha használja az Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Incidens/válságkezelési válasz terv tulajdonosok létrehozása](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Biztonságos helyszíni kiemelt jogosultságú rendszergazdai fiókok](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Ha az emelt szintű hozzáférés nem biztonságos, előfordulhat, hogy túl sok felhasználó magas jogosultsági szintű szerepkört és a támadásokkal szemben sebezhetőbbé. Rosszindulatú actors, beleértve a kibertámadások támadók, gyakran a cél felügyeleti fiókok és emelt szintű hozzáférés más elemeit elérhetők a bizalmas adatok és rendszerek hitelesítő adatok ellopásának használatával.

## <a name="control-locations-where-resources-are-created"></a>Szabályozhatja a helyek, ahol erőforrás jön létre

Engedélyezi a felhő üzemeltetői megakadályozza, hogy tönkretegye egyezmények a szervezet erőforrásaihoz kezeléséhez szükséges feladatok végrehajtásához nagyon fontos. A szervezetek kívánják vezérelni a helyeken, ahol erőforrások jönnek létre, keményen kell kód ezeket a helyeket.

Használhat [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) hozhat létre biztonsági házirendeket, amelyeknek a definícióit műveletek vagy kifejezetten elutasított erőforrás leírása. Ezen szabályzat-definíciókat a kívánt hatókörben, például az előfizetés, az erőforráscsoport vagy egyedi erőforrásokat rendelhet hozzá.

> [!NOTE]
> Biztonsági szabályzatok nem azonosak az RBAC-t. Azok ténylegesen az RBAC használatával engedélyezze a felhasználók számára ezen erőforrások létrehozásához.
>
>

Szervezetek számára, amelyek nem elsődlegesek hogyan jönnek létre az erőforrások érzékenyebbek a felhasználók számára a szolgáltatás további erőforrásokat kell létrehozásával előfordulhat, hogy való visszaélés. Az erőforrás-létrehozási folyamata korlátozására egy több-bérlős forgatókönyvben védelme fontos lépés.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktív monitorozása a gyanús tevékenységek esetén

Az aktív identitást, megfigyelési rendszere gyorsan gyanús viselkedés észlelésében és egy riasztást, további vizsgálat. Az alábbi táblázatban a két Azure AD-képességekhez, amely megkönnyíti a szervezetek számára a személyes adataik figyelése:

**Ajánlott eljárás**: Áll rendelkezésére módszer azonosításához:

- Megpróbál bejelentkezni [nélkül követett](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Találgatásos támadás](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) egy bizonyos fiók elleni támadások.
- Megpróbál bejelentkezni több helyről.
- Történő bejelentkezések [fertőzött eszközök](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Gyanús IP-címeket.

**Részletes**: Használja az Azure AD Premium [anomáliadetektálási jelentések](../active-directory/active-directory-view-access-usage-reports.md). Folyamatok és eljárások vannak bevezetve rendszergazdáknak, hogy ezek a jelentések futtatása, naponta, vagy igény szerint (általában az incidensmegoldási forgatókönyvhöz).

**Ajánlott eljárás**: Van egy aktív monitorozási rendszer értesítést küld a kockázatot és kockázati szintet (magas, közepes vagy alacsony) a vállalati igényeknek megfelelően módosíthatja.   
**Részletes**: Használat [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), amely észleli, ha az aktuális kockázatok, a saját irányítópulton és napi összefoglaló értesítéseket e-mailen keresztül küld. Segítséget nyújt a szervezet identitásait, konfigurálhatja a kockázatalapú szabályzatok, amelyek az automatikusan észlelt problémák reagálnak adott kockázati szint elérésekor.

Szervezetek számára, amelyek nem aktívan figyeljük az identitáskezelési rendszerek számára, hogy a felhasználói hitelesítő adatok biztonsága sérült veszélyt. Anélkül, hogy gyanús tevékenységek feldolgozása zajlik, ezeket a hitelesítő adatokat keresztül ismeretek szervezetek nem csökkentheti a fenyegetés típusát.

## <a name="use-azure-ad-for-storage-authentication"></a>Storage-hitelesítéshez használt Azure ad-ben
[Az Azure Storage](../storage/common/storage-auth-aad.md) Blob storage és Queue storage támogatja a hitelesítés és engedélyezés az Azure ad-ben. Az Azure AD-hitelesítés az Azure szerepköralapú hozzáférés-vezérlés használatával adott engedélyt biztosíthat a felhasználók, csoportok és alkalmazások a hatókör csak egy egyedi blob-tárolóba vagy a várakozási sor.

Javasoljuk, hogy használjon [storage-hozzáférés hitelesítéséhez az Azure AD](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Következő lépés

Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.
