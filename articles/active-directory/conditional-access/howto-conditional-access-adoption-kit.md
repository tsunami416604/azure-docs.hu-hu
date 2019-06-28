---
title: Feltételes hozzáférés bevezetési kit – Azure Active Directory
description: Azure AD feltételes hozzáférés az erőforrásokhoz való hozzáférés bevezetése
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387572"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Az Azure ad-beli feltételes hozzáférés bevezetése

Mobileszközök és a felhő-és felhőközpontú világában a felhasználók elérhetik a szervezeti erőforrásokhoz bárhol használatával különféle eszközök és alkalmazások. Ennek eredményeképpen csak összpontosító férhet hozzá egy erőforrás már nem elegendő. Szabályozhatja, aki hozzáféréssel rendelkezik, és azonosítása, ahol a felhasználó, és melyik eszközt használja, és még sok más.

Ez a vezérlő biztosít **Azure Active Directory (AD) feltételes hozzáférés** lehetővé teszi, hogy adja meg a feltételeket, bármely felhasználó számára a hozzáférést egy alkalmazáshoz, például a multi-factor Authentication (MFA) meg kell felelnie. Feltételes hozzáférési szabályzatok segítségével azt szabályozza, hogy engedéllyel rendelkező felhasználók (olyan felhasználók, hogy kapott hozzáférést egy felhőalkalmazás) adott feltételek mellett a felhőalkalmazások eléréséhez. Tekintse meg [Mi a feltételes hozzáférés az Azure Active Directory](overview.md#conditional-access-policies) további információt.

## <a name="key-benefits"></a>Főbb előnyök

Az Azure AD feltételes hozzáférés használatának előnyei a következők:

* **Termelékenység növelése:** Feltételes hozzáféréssel (CA) házirendek lehetővé teszik, hogy a cél a pont, amelyen rendszer kéri a felhasználóktól többtényezős hitelesítés használatát, a hozzáférés blokkolva van, vagy megbízható eszköz használata szükséges. Például beállíthatja a házirendek, például csak egy alkalmazásba, a vállalati hálózat ki a felhasználók többtényezős hitelesítés megkövetelése. MFA kérelmek csökkentése tartja a felhasználók hatékonyabbak, mint akkor, ha van a többtényezős hitelesítés minden alkalommal, amikor bejelentkeznek. Továbbá az Azure AD feltételes hozzáférési szabályzatok felhasználónként megadhatja, és is létrehoz az alkalmazás-specifikus szabályzatokat.
* **Kapcsolódó kockázatok kezelésében:** Engedélyezi a feltételes hozzáférési szabályzatokat biztosít felhőméretű identity protection, a kockázat-alapú hozzáférés-vezérlési funkciókat és a natív többtényezős hitelesítés támogatását. Kapcsolási feltételes hozzáférés az identity protection lehetővé teszi, meghatározhatja, amikor az alkalmazáshoz való hozzáférés blokkolva van, vagy engedi át.
* **Cím megfelelőségi és szabályozási:** Hozzáférési kérelmek és az alkalmazás jóváhagyási naplózás, és összességében az alkalmazás használatának megértéséhez azért egyszerűbb, mivel az Azure AD támogatja a natív auditnaplók minden végrehajtott alkalmazás-hozzáférési kérés. Naplózás magában foglalja a kérelmező azonosságát, kért dátum, üzleti indoklás, jóváhagyás állapota és jóváhagyó identitás. Ezeket az adatokat is a egy API-t, amely lehetővé teszi az adatok importálását egy biztonsági incidensek és a választott esemény figyelése (SIEM) rendszer érhető el.
* **Költségek kezelése:** Hozzáférési szabályzatok áthelyezése az Azure AD csökkenti a licencen alapuló egyéni vagy a helyszíni megoldások, például az Active Directory összevonási szolgáltatások (ADFS) feltételes hozzáférés, az infrastruktúrát futtató költségei csökkennek.

## <a name="customer-case-studies"></a>Ügyféleset-tanulmányok

Ismerje meg, hogyan a legtöbb szervezet Azure AD feltételes hozzáférés használata a kellene kidolgoznia és megvalósítania automatikus döntést hozhasson a hozzáférésről feltételek alapján a felhőalkalmazások eléréséhez. Az alábbi kiemelt történetek bemutatják, hogyan ezek ügyféligények teljesítésének.

* [**A wipro** meghajtók mobilos irodai alkalmazások, a Microsoft cloud security eszközeivel az ügyfélesetekből javítása érdekében.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Az Azure AD feltételes hozzáférési szabályzatok engedélyezve van a dokumentumok, erőforrások és alkalmazások megosztását megbízható külső entitások---a vállalat, akik a saját hitelesítő---az irányítást a saját vállalati adatokat.
* [**Accenture** megvédi az áthelyezés a felhőbe a Microsoft Cloud App securityvel](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture az a célja a [feltételes hozzáférést biztosító alkalmazás-vezérlő](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) funkciója a Cloud App Security, amely használja az Azure Active Directory feltételes hozzáférés a kapu alkalmazás-hozzáférés meghatározott feltételek alapján. LePenske arról tájékoztat, hogy ez a funkció hasznos lehet, tegyük fel, a letöltött fájl nem írásvédett hozzáférés engedélyezése.
* [**Aramex** kézbesítés korlátozott – globális logisztikai és szállítás vállalati felhőhöz csatlakozó office hoz létre identitás és hozzáférés-kezelési megoldás](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Biztonságos hozzáférés biztosítása a távoli alkalmazottak Aramex a különösen nehezen volt. A vállalat most alkalmazza a feltételes hozzáférést, hogy a távoli dolgozók a hálózaton kívülről az SaaS-alkalmazások eléréséhez. A feltételes hozzáférési szabállyal döntsön fogja kényszeríteni a többtényezős hitelesítést, így csak a megfelelő emberek számára a megfelelő hozzáférési jogosultsággal.

További információ az Azure AD feltételes hozzáférés az ügyfelek és partnerek élményt, a Microsoft - [megtudhatja, mivel az Azure hihetetlen dolgokra teszi](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Bejelentések

Az Azure AD folyamatosan fejlesztései kap. A legújabb fejlemények az naprakész maradhat, lásd: [What's new in Azure Active Directory?](../fundamentals/whats-new.md)

Legutóbbi blogok a Tech Közösség és a Microsoft azonosító osztályának:

* 2018. szeptember 24., [az Azure Active Directory feltételes hozzáférés az Azure Databricksben](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018. szeptember 21., [egyéni vezérlők olyan nyilvános előzetes verzióban elérhető Azure AD feltételes hozzáférés](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018. szeptember 21., [már elérhető az Azure AD feltételes hozzáférés támogatása korlátozott hozzáférés a Microsoft Cloud App Securityvel](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018. szeptember 21., [az Azure ad-beli feltételes hozzáférés: Felügyelt böngésző támogatása jelenleg előzetes verzióban elérhető iOS és Android-platformok](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018. szeptember 21., [országkódok az Azure AD feltételes hozzáférése jelenleg nyilvános előzetes verzióban](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018. szeptember 21., [az Azure AD-– használati feltételeket már elérhető](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Tanulási források

Kövesse az alábbi hivatkozásokat, az Azure AD feltételes hozzáférési funkciók áttekintése.

* Ismerje meg, "[Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)"
* Ismert "[Mik azok a feltételek az Azure Active Directory feltételes hozzáférés?](conditions.md)"
* Ismert "[Mi az a hely feltétel, az Azure Active Directory feltételes hozzáférés?](location-condition.md)"
* Ismert "[azt szabályozza, Mik azok a hozzáférés az Azure Active Directory feltételes hozzáférés?](controls.md)"
* Keresse meg "[Mi a mi Ha eszközének Azure Active Directory feltételes hozzáférés?"](what-if-tool.md)
* Hajtsa végre a [gyakorlati tanácsok az Azure Active Directory feltételes hozzáférés](best-practices.md)

Ezenkívül tekintse meg a következő hivatkozások útmutatást az Azure Active Directoryval integrált összes szolgáltatásokhoz való hozzáférés védelme.

* [Mi az alapvető védelmet (előzetes verzió)?](baseline-protection.md) Alapvető védelmet biztosítja, hogy legalább engedélyezve van az Azure Active Directory-környezetet a biztonsági alapkonfiguráció szintjét.
* [Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). A javasolt környezet és konfigurálására, beleértve a kapcsolódó képességek és a feltételes hozzáférési szabályzatok által előírt készletét alkalmazásával biztonságos hozzáférés a felhőszolgáltatásokhoz keresztül az Enterprise Mobility + Security termékek beállításának módját ismerteti.
* [Az Azure Active Directory feltételes hozzáférés beállításaihoz](technical-reference.md). További információ:
   * Mely alkalmazások használnak feltételes hozzáférést?
   * Mely szolgáltatásoknál van engedélyezve a feltételes hozzáférés?
* [Az Azure Active Directory feltételes hozzáférésének engedélyezésére vonatkozó biztonságos hozzáférés](https://www.youtube.com/watch?v=eLAYBwjCGoA). A videóból megtudhatja, hogyan feltételes hozzáférési szerepet játszik más vállalati és mobilitási csomag számítási feladatokhoz.

### <a name="training-videos"></a>Oktatóvideók

**Az Enterprise Mobility + Security feltételes hozzáférés**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Eszközalapú feltételes hozzáférés**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Biztonságos hozzáférés engedélyezése az Azure Active Directory feltételes hozzáférés**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Online tanfolyamok

Tekintse meg a következő feltételes hozzáférési tanfolyamok és egyéb a [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Tervezési Identitáskezelést a Microsoft Azure-ban](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Ez a tanfolyam végigvezeti a kulcsfontosságú dologra kell tudni az identitáskezelési megoldás az Azure ad-vel tervezhet." "A szerepkörök és hozzáférés-vezérlés az Azure ad-vel" foglalkozik az Azure AD feltételes hozzáférés modul.

* Pluralsight.com: [Tervezési hitelesítést a Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Ez a tanfolyam azt ismerteti, hogyan oldja meg a felhőalapú hitelesítési követelmények az Azure AD használatával." Az Azure AD feltételes hozzáférés "Hitelesítési követelmények a különböző forgatókönyvek" modul foglalkozik.

* Pluralsight.com: [Tervezési hitelesítést biztosít a Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Ez a tanfolyam bemutatja az engedélyezési beállítások elérhető az Azure és az Azure AD." Az Azure AD feltételes hozzáférés "Az Azure Resource Manager és az Azure AD engedélyezési" modul foglalkozik.

### <a name="books"></a>Könyvek

* O'Reilly - [megvalósítása az Azure-megoldások – második kiadás.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Első lépésekhez az Azure-szolgáltatásokkal, és ismerje meg, hogyan valósíthat meg őket a szervezet. A fejezet foglalkozik az Azure AD feltételes hozzáférés [üzembe helyezése és az Azure Active Directory szinkronizálása](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [szakértő kezelése a Microsoft Azure infrastruktúra-szolgáltatások](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "A következő ismertetése, kiértékelése, üzembe helyezése és karbantartása használhatják az Microsoft Azure-környezetek tudnivalót."

## <a name="white-papers"></a>Tanulmányok

* 2018. December 18., közzétett [egy rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása az Azure Active Directoryval](../authentication/concept-resilient-controls.md)
   * Ez a dokumentum biztosít egy szervezet stratégiák útmutatást biztosít hibatűrést során váratlan megszakadását fiókzárolási kockázatának csökkentése érdekében előfordulhat, hogy elfogadja.

* 2018. szeptember 18., közzétett [erőforrások áttelepítése az Azure Active Directory-alkalmazások](../manage-apps/migration-resources.md)
   * Ez a tanulmány az alkalmazás-hozzáférési és hitelesítési áttelepítése az Azure Active Directory (Azure AD) segítségével erőforrások listáját tartalmazza.

* 2018. július 12 közzétett [Azure biztonsági és megfelelőségi terv: Egyesült Királyság hivatalos számítási feladatokhoz üzemeltető PaaS webes alkalmazás](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Az Azure tervezetek útmutató dokumentumok és architektúrák felhőalapú megoldásokat kínálnak, amelyek akkreditáció vagy megfelelőségi követelményekre rendelkező forgatókönyvek az automation sablonokból állnak.

## <a name="guidance-for-it-administrators"></a>Útmutató informatikai rendszergazdák számára

Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda. Tekintse meg [rendszergazdája szerepkör engedélyei az Azure Active Directoryban.](../users-groups-roles/directory-assign-admin-roles.md)

Az informatikai rendszergazdaként [Azure AD feltételes hozzáférési](overview.md) szeretné a felhasználók Azure multi-factor Authentication hitelesítéssel való hitelesítése, jelentkezzen be a megbízható hálózathoz, vagy a megbízható eszköz.

Hasznos hivatkozások segítenek az első lépések a következők:

* [Ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md)
* [A feltételes hozzáférési szabályzatok kizárt felhasználók kezelése az Azure AD hozzáférési felülvizsgálatok](../governance/conditional-access-exclusion.md)
* [Útmutató: Az Azure Active Directoryban a feltételes hozzáférés üzembe helyezésének megtervezése](plan-conditional-access.md)
* [Rövid útmutató: Többtényezős hitelesítés konkrét alkalmazások esetén az Azure Active Directory feltételes hozzáférés](app-based-mfa.md)
* [Rövid útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl megkövetelése](require-tou.md)
* [Rövid útmutató: Letiltja a hozzáférést az Azure Active Directory feltételes hozzáférés a munkamenet kockázata észlelésekor](app-sign-in-risk.md)
* [Az Azure ad-beli feltételes hozzáférés – gyakori kérdések](faqs.md)
   * Ha további kérdései is megtekintheti a [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Ha nem találja a választ egy problémára, a támogatási csapatuk mindig elérhetők, további segítséget. Használat [forduljon a Microsoft ügyfélszolgálatához](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Oktatóanyagok

* [**Gyors útmutató: Többtényezős hitelesítés konkrét alkalmazások esetén az Azure Active Directory feltételes hozzáférés**](app-based-mfa.md)
   * Ez a rövid útmutató bemutatja, hogyan van szükség a multi-factor authentication szolgáltatás egy kijelölt felhőalkalmazás a környezetében az Azure AD feltételes hozzáférési szabályzat konfigurálása.

* [**Gyors útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl megkövetelése**](require-tou.md)
   * Ez a rövid útmutató bemutatja, hogyan kijelölt felhőalapú alkalmazások a környezetben a használati feltételek elfogadását igénylő Azure AD feltételes hozzáférési szabályzat konfigurálása.

* [**Gyors útmutató: Letiltja a hozzáférést az Azure Active Directory feltételes hozzáférés a munkamenet kockázata észlelésekor**](app-sign-in-risk.md)
   * Ez a rövid útmutató ismerteti, hogyan konfigurálható a feltételes hozzáférési szabályzatot, amely blokkolja egy bejelentkezési beállított bejelentkezési kockázati szint észlelésekor.

* [Oktatóanyag: **Áttelepíteni a klasszikus szabályzat megköveteli a multi-factor Authentication hitelesítés az Azure Portalon**](policy-migration-mfa.md)
   * Ez az oktatóanyag bemutatja, hogyan telepíthet át egy klasszikus szabályzat megköveteli a multi-factor authentication (MFA) felhőalapú alkalmazás esetében.

## <a name="end-user-readiness-and-communication"></a>A végfelhasználói készültségi és kommunikáció

Feltételes hozzáférés a más Azure AD-képességekhez, amelyek hatással lehetnek a felhasználói élmény használ. Például az Azure multi-factor authentication segítségével felhasználók erős hitelesítés engedélyezése. Ebben az esetben a végfelhasználónak sablonok, az Azure MFA fogja használni.

## <a name="next-steps"></a>További lépések

* Indítsa el az üzembe helyezés a a [feltételes hozzáférés üzembe helyezési, tervezési dokumentáció](plan-conditional-access.md).
