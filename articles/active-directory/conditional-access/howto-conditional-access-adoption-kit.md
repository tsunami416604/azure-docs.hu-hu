---
title: Feltételes hozzáférés bevezetési készlete – Azure Active Directory
description: Az Azure AD feltételes hozzáférésének elfogadása az erőforrásokhoz való hozzáféréshez
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
ms.openlocfilehash: c75ec0ec212c99e474d13892178acbdc14e5f386
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075274"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Azure AD feltételes hozzáférés bevezetése

A mobil-első, felhőalapú világában a felhasználók bárhonnan hozzáférhetnek a szervezet erőforrásaihoz a különböző típusú eszközök és alkalmazások használatával. Ennek eredményeképpen csupán arra összpontosít, hogy ki férhet hozzá egy erőforráshoz, már nem elég. Megadhatja, hogy ki férhet hozzá, és azonosítsa, hogy a felhasználó hol van, és milyen eszköz használatban van, és még sok más.

Ennek a vezérlőnek a megadásához **Azure Active Directory (ad) feltételes hozzáférés** lehetővé teszi, hogy megadja azokat a feltételeket, amelyeknek a felhasználónak meg kell felelnie az alkalmazásokhoz való hozzáféréshez, például multi-Factor Authentication (MFA). A feltételes hozzáférési szabályzatok segítségével szabályozható, hogy a jogosult felhasználók (a felhőalapú alkalmazásokhoz hozzáférést kapott felhasználók) milyen feltételek mellett férhessenek hozzá a felhőalapú alkalmazásokhoz. További információkért tekintse meg a [Azure Active Directory feltételes hozzáférését](overview.md) ismertető témakört.

## <a name="key-benefits"></a>Főbb előnyök

Az Azure AD feltételes hozzáférés használatának legfontosabb előnyei a következők:

* **Növelje a termelékenységet:** A feltételes hozzáférési (CA) házirendek lehetővé teszik, hogy megcélozza azt a pontot, amelyen a felhasználók az MFA használatát kérik, hozzáférése letiltva, vagy megbízható eszköz használatára van szükség. Például olyan házirendeket állíthat be, mint például a felhasználók számára, hogy az MFA-t egy alkalmazásban, a vállalati hálózaton kívülre kelljen beállítani. Az MFA-kérelmek csökkentése nagyobb hatékonyságot biztosít a felhasználók számára, mint ha minden bejelentkezéskor MFA-ra van szükségük. Emellett az Azure AD feltételes hozzáférés lehetővé teszi, hogy felhasználói alapon adja meg a szabályzatokat, és az alkalmazásra vonatkozó házirendeket is létrehoz.
* **Kockázat kezelése:** A feltételes hozzáférési szabályzatok lehetővé teszik a felhőalapú identitásvédelem, a kockázatalapú hozzáférés-vezérlési képességek és a natív multi-Factor Authentication támogatását. A feltételes hozzáférés az Identity Protection szolgáltatással való összekapcsolása lehetővé teszi, hogy az alkalmazáshoz való hozzáférés letiltása vagy lehívása megtörténjen.
* **Címek megfelelősége és szabályozása:** Az alkalmazáshoz kapcsolódó hozzáférési kérelmek és jóváhagyások naplózása, valamint az alkalmazások általános használatának megismerése egyszerűbb az Azure AD-ben, mivel a natív naplókat támogat minden egyes alkalmazás-hozzáférési kérelemhez. A naplózás magában foglalja a kérelmező identitását, a kért dátumot, az üzleti indoklást, a jóváhagyási állapotot és a jóváhagyó identitását. Ezek az adatok egy API-ból is elérhetők, amely lehetővé teszi az adatok importálását egy olyan biztonsági incidensre és esemény-figyelési (SIEM) rendszerre, amely választható.
* **Kezelés díja:** A hozzáférési szabályzatok az Azure AD-be való áthelyezésével csökkenthető a feltételes hozzáférésre vonatkozó egyéni vagy helyszíni megoldások, például a Active Directory összevonási szolgáltatások (AD FS) (ADFS), ami csökkenti az infrastruktúra futtatásának költségeit.

## <a name="customer-case-studies"></a>Ügyféleset-tanulmányok

Ismerje meg, hogy a legtöbb szervezet hogyan használja az Azure AD feltételes hozzáférést az automatizált hozzáférés-vezérlési döntések definiálásához és megvalósításához a feltételek alapján a felhőalapú alkalmazások eléréséhez. A következő Kiemelt történetek bemutatják, hogyan teljesítik ezeket az ügyfelek igényeit.

* [A **Wipro** a Microsoft Cloud Security eszközeivel fejleszti a mobil termelékenységet az ügyfelek bevonásának javítása érdekében.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Az Azure AD feltételes hozzáférési szabályzatai lehetővé tették a vállalat számára, hogy megosszák a megbízható külső entitásokkal rendelkező dokumentumokat, erőforrásokat és alkalmazásokat,---akik saját hitelesítő adataikat használhatják---a saját vállalati adatok feletti felügyelet fenntartása mellett.
* [A **Accenture** megvédi a felhőbe való áttérést Microsoft Cloud app Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) -Accenture a Cloud app Security [feltételes hozzáférést biztosító alkalmazás-vezérlő](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) funkciójának kiértékelése, amely Azure Active Directory feltételes hozzáférését használja a kapuhoz alkalmazás-hozzáférés bizonyos feltételek alapján. A LePenske azt állítja be, hogy ez a funkció hasznos lehet, például a letöltések letiltásakor a csak olvasási jogosultsággal rendelkező fájlok elérésének engedélyezése.
* [A **Aramex** Delivery Limited – a globális logisztika és a szállítmányozási vállalat felhőalapú, identitás-és hozzáférés-kezelési megoldással rendelkező irodát hoz létre](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). A biztonságos hozzáférés biztosítása különösen nehéz volt a Aramex távoli alkalmazottaival. A vállalat mostantól feltételes hozzáférést alkalmaz arra, hogy ezek a távoli alkalmazottak a hálózaton kívülről férhessenek hozzá SaaS-alkalmazásaihoz. A feltételes hozzáférési szabály eldönti, hogy kikényszeríti-e Multi-Factor Authenticationt, és csak a megfelelő személyeket adja meg a megfelelő hozzáféréssel.

Ha többet szeretne megtudni az Azure AD feltételes hozzáférésével kapcsolatos ügyfelekkel és partneri tapasztalatokkal kapcsolatban, látogasson el ide [: az Azure-ban megjelenő csodálatos dolgok](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Közlemények

Az Azure AD folyamatosan fejlesztései kap. Ha naprakészen szeretne maradni a legújabb fejleményekkel, tekintse meg az Újdonságok [a Azure Active Directory?](../fundamentals/whats-new.md) című témakört.

A legújabb blogok a technikai Közösség és a Microsoft Identity Division:

* Szeptember 24., 2018 [Azure Active Directory feltételes hozzáférés Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018. szeptember 21-én [Az Azure ad feltételes hozzáférés egyéni vezérlői nyilvános előzetes verzióban érhetők el](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* Szeptember 21., 2018, az [Azure ad feltételes hozzáférés támogatása a korlátozott hozzáféréshez Microsoft Cloud app Security mostantól elérhető](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* Szeptember 21., 2018 [, Azure ad feltételes hozzáférés: Az iOS-és Android-platformok felügyelt böngésző általi támogatása mostantól előzetes verzióban](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* Szeptember 21., 2018. az [Azure ad feltételes hozzáférése az ország-kódokhoz nyilvános előzetes](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/) verzióban érhető el
* Szeptember 21., 2018, [Az Azure ad használati feltételei mostantól elérhetők](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Tanulási erőforrások

Az alábbi hivatkozásokat követve megismerheti az Azure AD feltételes hozzáférés funkcióinak áttekintését.

* Ismerje meg[a "mi a feltételes hozzáférés a Azure Active Directory?](overview.md)" című témakört.
* "[Milyen feltételek vonatkoznak Azure Active Directory feltételes hozzáférésre?](conditions.md)"
* Tudnia[kell a "mi a hely feltétele Azure Active Directory feltételes hozzáférés?](location-condition.md)"
* A "[Mik a hozzáférés-vezérlések Azure Active Directory feltételes hozzáféréshez?](controls.md)"
* A mi[a mi a teendő, ha az eszköz a Azure Active Directory feltételes hozzáférés?](what-if-tool.md)
* [A feltételes hozzáférésre vonatkozó ajánlott eljárások](best-practices.md) követése Azure Active Directory

Emellett tekintse meg az alábbi hivatkozásokat, amelyekkel biztosíthatja az Azure Active Directory-hoz integrált összes szolgáltatás elérését.

* [Mi az alapkonfiguráció-védelem (előzetes verzió)?](baseline-protection.md) Az alapkonfiguráció védelme biztosítja, hogy legalább az alapszintű biztonság engedélyezve legyen a Azure Active Directory környezetben.
* [Identitás-és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Ismerteti, hogyan lehet biztonságos hozzáférést beállítani a felhőalapú szolgáltatásokhoz Enterprise Mobility + Security termékeken keresztül egy javasolt környezet és konfiguráció megvalósításával, beleértve a feltételes hozzáférési szabályzatok és a kapcsolódó képességek előírt készletét.
* [Azure Active Directory feltételes hozzáférési beállítások leírása](technical-reference.md). Tanulni
   * Milyen alkalmazások használják a feltételes hozzáférést?
   * Milyen szolgáltatásokat engedélyeznek a feltételes hozzáférés?
* [Engedélyezze Azure Active Directory feltételes hozzáférést a biztonságos felhasználói hozzáféréshez](https://www.youtube.com/watch?v=eLAYBwjCGoA). Tekintse meg ezt a videót, amelyből megtudhatja, hogy a feltételes hozzáférés milyen szerepet játszik a vállalati és a mobilitási csomag munkaterhelésében.

### <a name="training-videos"></a>Oktatóvideók

**Feltételes hozzáférés Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Eszköz alapú feltételes hozzáférés**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**A feltételes hozzáférés Azure Active Directory engedélyezése a biztonságos felhasználói hozzáféréshez**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Online tanfolyamok

Tekintse meg a következő feltételes hozzáférési tanfolyamokat, és még többet a [Pluralsight.com](https://www.pluralsight.com/)-on:

* Pluralsight.com: [Identitáskezelés tervezése Microsoft Azureban](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Ez a tanfolyam végigvezeti az Identitáskezelés az Azure AD-vel való megtervezéséhez szükséges legfontosabb elemeken." Az Azure AD feltételes hozzáférését a "szerepkörök és Access Control az Azure AD-vel" modullal tárgyaljuk.

* Pluralsight.com: [Tervezési hitelesítés Microsoft Azurehoz](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Ez a tanfolyam azt ismerteti, hogyan használható az Azure AD az összes felhőalapú hitelesítési követelmény megoldásához." Az Azure AD feltételes hozzáférése a "különböző forgatókönyvek hitelesítési követelményei" modulra vonatkozik.

* Pluralsight.com: [Microsoft Azure kialakításának engedélyezése](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Ez a kurzus az Azure-ban és az Azure AD-ben elérhető engedélyezési lehetőségeket tanítja meg." Az Azure AD feltételes hozzáférése az "engedélyezés Azure Resource Manager és az Azure AD-vel" modulban található.

### <a name="books"></a>Könyvek

* O'Reilly – [Azure-megoldások megvalósítása – második kiadás.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Az Azure-szolgáltatások üzembe helyezésével és megismerésével megtudhatja, hogyan implementálhatja azokat a szervezetében. Az Azure AD feltételes hozzáférése a [Azure Active Directory üzembe helyezése és szinkronizálása](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)című fejezetben található.

* Wiley- [mastering Microsoft Azure infrastruktúra-szolgáltatások](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "A Microsoft Azuret használó környezetek megismeréséhez, kiértékeléséhez, üzembe helyezéséhez és karbantartásához szükséges minden."

## <a name="white-papers"></a>Tanulmányok

* Közzétett, 2018. december 18., [rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Ez a dokumentum útmutatást nyújt azon stratégiákról, amelyeket a szervezet elfogad, hogy rugalmasságot biztosítson a zárolás kockázatának csökkentése érdekében a váratlan megszakítások során.

* Közzétette: 2018. szeptember 18., [az alkalmazások áttelepítésének forrásai Azure Active Directory](../manage-apps/migration-resources.md)
   * Ez a tanulmány az alkalmazások hozzáférésének és hitelesítésének Azure Active Directory (Azure AD) való áttelepíteni kívánt erőforrások listáját tartalmazza.

* Közzétéve 2018 [. július 12-én Azure Security and Compliance Blueprint: A brit hivatalos számítási feladatokhoz tartozó Péter webalkalmazás-üzemeltetés](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Az Azure-tervrajzok olyan útmutató dokumentumokból és automatizálási sablonokból állnak, amelyek felhőalapú architektúrákat helyeznek üzembe olyan forgatókönyvekhez, amelyek akkreditációs vagy megfelelőségi követelményekkel rendelkeznek.

## <a name="guidance-for-it-administrators"></a>Útmutató rendszergazdáknak

Jelentkezzen be a [Azure Portal](https://portal.azure.com/) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként. Tekintse át a [Azure Active Directory rendszergazdai szerepkör engedélyeit.](../users-groups-roles/directory-assign-admin-roles.md)

Rendszergazdaként az [Azure ad feltételes hozzáférés](overview.md) használatával megkövetelheti, hogy a felhasználók hitelesítése az Azure multi-Factor Authentication használatával történjen, jelentkezzen be egy megbízható hálózatról vagy megbízható eszközről.

Itt találhat hasznos hivatkozásokat az első lépésekhez:

* [Ajánlott eljárások a feltételes hozzáféréshez Azure Active Directory](best-practices.md)
* [Az Azure AD hozzáférési felülvizsgálatok használata a feltételes hozzáférési házirendből kizárt felhasználók felügyeletéhez](../governance/conditional-access-exclusion.md)
* [Útmutató: A feltételes hozzáférési telepítés megtervezése Azure Active Directory](plan-conditional-access.md)
* [Rövid útmutató: MFA megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)
* [Rövid útmutató: Használati feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt](require-tou.md)
* [Rövid útmutató: Hozzáférés letiltása a Azure Active Directory feltételes hozzáféréssel rendelkező munkamenet-kockázat észlelésekor](app-sign-in-risk.md)
* [Azure AD feltételes hozzáférés – gyakori kérdések](faqs.md)
   * További kérdésekért tekintse meg az [MSDN fórumot](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)is.
   * Ha nem találja a választ a problémára, a támogatási csapatunk mindig szívesen segít Önnek. [Vegye fel a kapcsolatot a Microsoft ügyfélszolgálatával](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Oktatóanyagok

* [**QuickStart MFA megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel**](app-based-mfa.md)
   * Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy Azure AD feltételes hozzáférési szabályzatot, amely megköveteli a többtényezős hitelesítést egy kiválasztott felhőalapú alkalmazás számára a környezetben.

* [**QuickStart Használati feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt**](require-tou.md)
   * Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy olyan Azure AD feltételes hozzáférési szabályzatot, amely megköveteli, hogy a környezetében egy kiválasztott felhőalapú alkalmazásnak el kell fogadnia a felhasználási feltételeket.

* [**QuickStart Hozzáférés letiltása a Azure Active Directory feltételes hozzáféréssel rendelkező munkamenet-kockázat észlelésekor**](app-sign-in-risk.md)
   * Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, amely letiltja a bejelentkezést a beállított bejelentkezési kockázati szint észlelésekor.

* [Oktatóanyag: **A többtényezős hitelesítést igénylő klasszikus szabályzat áttelepítését a Azure Portal**](policy-migration-mfa.md)
   * Ez az oktatóanyag bemutatja, hogyan telepíthet át egy olyan klasszikus szabályzatot, amely a többtényezős hitelesítést (MFA) igényli a felhőalapú alkalmazásokhoz.

## <a name="end-user-readiness-and-communication"></a>Végfelhasználói készültség és kommunikáció

A feltételes hozzáférés más Azure AD-funkciókat is használ, amelyek befolyásolhatják a végfelhasználói élményt. Használhatja például az Azure multi-Factor Authenticationt a felhasználók erős hitelesítésének engedélyezéséhez. Ebben az esetben az Azure MFA végfelhasználói sablonjait fogja használni.

## <a name="next-steps"></a>További lépések

* Indítsa el a telepítést a [feltételes hozzáférés telepítési tervezési dokumentációjában](plan-conditional-access.md).
