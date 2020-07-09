---
title: SaaS-alkalmazások – közzétételi útmutató – Microsoft kereskedelmi piactér
description: Az SaaS-alkalmazások Microsoft AppSource és az Azure Marketplace-en való közzétételéhez szükséges követelmények és erőforrások.
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: c19799265679eeead96bf95943f274aa32c75ff2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121571"
---
# <a name="saas-applications-offer-publishing-guide"></a>Az SaaS-alkalmazások ajánlat-közzétételi útmutatója

Az SaaS-alkalmazásokat a kereskedelmi piactéren teheti közzé három különböző hívással: "Kapcsolatfelvétel", "kipróbálás most" és "Letöltés most". Ez a cikk ezt a három lehetőséget ismerteti, beleértve az egyes követelmények követelményeit is. 

## <a name="offer-overview"></a>Ajánlat áttekintése  

Az SaaS-alkalmazások Microsoft AppSource és az Azure Marketplace-en érhetők el.  A kirakatok támogatják a listát, a próbaverziót és a Transact ajánlatokat is.

**Lista:**  A tőzsdei közzétételi lehetőség egy kapcsolatfelvételi ajánlat típusát tartalmazza, és akkor használható, ha a próbaverziós vagy tranzakciós szintű részvétel nem valósítható meg. Ennek a megközelítésnek az az előnye, hogy lehetővé teszi a kiadók számára, hogy a piacon elérhető megoldással azonnal elkezdhetik a vállalkozása növekedéséhez szükséges érdeklődők fogadását.  
**Próbaverzió/tranzakció:**  Az ügyfél lehetősége van arra, hogy közvetlenül vásároljon vagy kérjen próbaverziót a megoldásához. A próbaverziós szolgáltatás megadásával növelheti az ügyfelek számára felkínált engagement szintet, és a vásárlás előtt lehetővé teszi az ügyfeleknek a megoldás megismerését. A próbaverziós szolgáltatással jobb eséllyel népszerűsítheti a kirakatokat, és több és gazdagabb érdeklődőket kell várnia az ügyfelek bevonásával. A próbaverzióknak legalább a próbaidőszak időtartama alatt ingyenes támogatást kell tartalmazniuk.  

| SaaS-alkalmazások ajánlata | Üzleti követelmények | Technikai követelmények |  
| --- | --- | --- |  
| **Kapcsolatfelvétel** | Igen | Nem |  
| **Power BI/Dynamics** | Igen | Igen (Azure AD-integráció) |  
| **SaaS-alkalmazások**| Igen | Igen (Azure AD-integráció) |     

## <a name="saas-list"></a>SaaS-lista

Ha egy SaaS-listához próbaverziót szeretne felvenni, és nincs számlázási funkciója, akkor a "Kapcsolatfelvétel" lehetőséggel. 

Az SaaS-alkalmazások listázásához nem kell Azure Active Directory konfigurálnia. 

|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás egy SaaS-ajánlat  |   A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket is kínál.      |


## <a name="saas-trial"></a>SaaS-próbaverzió

A megoldás vagy az alkalmazás egy ingyenes, szoftveres (SaaS-) alapú próbaverzió használatával biztosítható. Az ingyenes próbaverziós ajánlatokat korlátozott használati vagy korlátozott időtartamú próbaverziós fiókként lehet megjeleníteni. 


|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás egy SaaS-ajánlat  |   A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket is kínál.      |
|Az alkalmazás HRE engedélyezve van     |   A rendszer átirányítja az ügyfelet a tartományhoz, és közvetlenül az ügyféllel fog tranzakciót kötni       |


## <a name="saas-trial-technical-requirements"></a>SaaS-próbaverzió műszaki követelményei

Az SaaS-alkalmazások technikai követelményei egyszerűek. A közzétevőket csak a közzétételhez szükséges Azure Active Directory (Azure AD) integrációra kell integrálni. Az Azure AD-integráció az alkalmazásokkal jól dokumentált, és a Microsoft több SDK-t és erőforrást biztosít ennek megvalósításához.  

A kezdéshez javasoljuk, hogy az Azure Marketplace Publishing szolgáltatáshoz dedikált előfizetéssel elkülönítse a munkát más kezdeményezésekkel. Ha ez megtörtént, megkezdheti az SaaS-alkalmazás üzembe helyezését ebben az előfizetésben a fejlesztési munka elindításához.  

A legjobb Azure Active Directory dokumentáció, minták és útmutatás a következő helyeken található: 

* [Azure Active Directory fejlesztői útmutató](../active-directory/develop/index.yml)

* [Integráció az Azure Active Directoryval](../active-directory/develop/active-directory-how-to-integrate.md)

* [Azure-ütemterv – biztonság és identitás](https://azure.microsoft.com/roadmap/?category=security-identity)

Videós oktatóanyagok esetében tekintse át a következőket:

* [Hitelesítés Azure Active Directory Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity műszaki tájékoztató – 1. rész, 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory Identity műszaki tájékoztató – 2. rész](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Alkalmazások kiépítése Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Active Directoryre összpontosító Microsoft Azure videók](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Az ingyenes Azure Active Directory képzés a következő címen érhető el:  
* [Microsoft Azure informatikai szakembereknek szóló tartalmi sorozatban: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Emellett Azure Active Directory biztosít a szolgáltatás frissítéseinek keresésére szolgáló helyet.   
* [Azure AD-szolgáltatások frissítései](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>A Azure Active Directory használata a próbaverziók engedélyezéséhez  

A Microsoft hitelesíti az összes piactér-felhasználót az Azure AD-vel, így amikor egy hitelesített felhasználó rákattint a piactéren a próbaverzióra, és a rendszer átirányítja a próbaverziós környezetbe, a felhasználót közvetlenül is kiépítheti a próbaverzióba anélkül, hogy további bejelentkezési lépésre lenne szükség. A hitelesítés során az alkalmazás által az Azure AD-től kapott token értékes felhasználói adatokat tartalmaz, amelyekkel felhasználói fiókot hozhat létre az alkalmazásban, lehetővé téve a kiépítési élmény automatizálását és az átalakítás valószínűségének növelését. A jogkivonattal kapcsolatos további információkért lásd: [minta tokenek](../active-directory/develop/active-directory-token-and-claims.md) .

Az Azure AD használatával engedélyezheti az alkalmazásra vagy próbaverzióra vonatkozó 1 kattintásos hitelesítést a következő műveleteket:  
* A piactéren a próbaverzióra egyszerűsítheti a felhasználói élményt.  
* A "terméken belüli felhasználói élmény" megtartása akkor is, ha a felhasználó a piactérről a tartományba vagy a próbaverziós környezetbe van átirányítva.  
* Csökkenti annak a valószínűségét, hogy az átirányítás lemondható, mert nincs további bejelentkezési lépés.  
* Csökkenti az Azure AD-felhasználók nagy sokaságának üzembe helyezési korlátait.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Az Azure AD-integráció tanúsítása a piactéren  

Az Azure AD-integrációt néhány különböző módon tanúsíthatja, attól függően, hogy az alkalmazása egybérlős vagy több bérlős-e, és hogy új-e az Azure AD összevont egyszeri bejelentkezés (SSO), vagy már támogatja azt.  

**Több-bérlős alkalmazások esetén:**  

Ha már támogatja az Azure AD-t, tegye a következőket:
1.    Az alkalmazás regisztrálása a Azure Portal
2.    Engedélyezze az Azure AD több-bérlős támogatási szolgáltatását, és kérjen le egy kattintással elérhető próbaverziót. Részletesebb információkat [itt](../active-directory/develop/active-directory-integrating-applications.md)találhat.  

Ha még nem ismeri az Azure AD összevont egyszeri bejelentkezést, tegye a következőket: 
1.  Az alkalmazás regisztrálása a Azure Portal
2.  Az Azure AD-val történő egyszeri bejelentkezést az [OpenID Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) vagy a [OAuth 2,0](../active-directory/develop/active-directory-protocols-oauth-code.md)használatával fejlesztheti.
3.  A HRE több-bérlős támogatási funkciója lehetővé teszi, hogy a "One-Click" próbaverzióval kapcsolatos további információkat [itt](../active-directory/develop/active-directory-devhowto-appsource-certified.md)találja.  

**Egybérlős alkalmazások esetén használja az alábbi lehetőségek egyikét:**  
* Felhasználók hozzáadása a címtárhoz vendégként az [Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) használatával
* Próbaverziók manuális kiépítése az ügyfelek számára a "Kapcsolatfelvétel" használatával
* Felhasználónkénti "tesztelési meghajtó" fejlesztése
* Több-bérlős minta bemutató alkalmazás létrehozása SSO-val

## <a name="saas-subscriptions"></a>SaaS-előfizetések

Az SaaS-alkalmazások ajánlatának típusa lehetővé teszi, hogy az ügyfél a SaaS-alapú, technikai megoldását előfizetésként vásárolja meg. A következő követelményeknek kell teljesülniük az SaaS-alkalmazáshoz:
- Díj és számlázás a szolgáltatással (havonta vagy évente), vagy felhasználónkénti díjszabással.
- Adjon meg egy módszert a szolgáltatás frissítésére vagy megszakítására.
A Microsoft üzemelteti a kereskedelmi tranzakciót. A Microsoft az Ön nevében számlázza az ügyfelet. Az SaaS-alkalmazások előfizetésként való ellátásához integrálni kell a SaaS-megvalósítási API-kkal.  A szolgáltatásnak támogatnia kell a kiépítés, a verziófrissítés és a megszakítást.

| Követelmény | Részletek |  
|:--- |:--- |  
|Számlázás és mérés | Az ajánlat díjszabása a közzététel előtt kiválasztott árképzési modell alapján történik (átalány vagy felhasználónként).  Az átalány-modell használata esetén igény szerint további dimenziókat is megadhat, amelyeket a rendszer az átalányban nem szereplő használati díjak megadására használ fel. |  
|Lemondás | Ajánlatát bármikor megszakíthatja az ügyfél. |  
|Tranzakció kezdőlapja | Egy Azure-beli közös védjeggyel ellátott tranzakció-kezdőlapot üzemeltet, ahol a felhasználók létrehozhatják és kezelhetik az SaaS-szolgáltatásfiókot. |   
| Előfizetés API | Olyan szolgáltatást tesz elérhetővé, amely képes kommunikálni az SaaS-előfizetéssel egy felhasználói fiók és szolgáltatáscsomag létrehozásához, frissítéséhez és törléséhez. A kritikus API-módosításokat 24 órán belül támogatni kell. A nem kritikus API-változások rendszeresen jelennek majd meg. |  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md) című témakört.

## <a name="next-steps"></a>Következő lépések
Ha még nem tette meg,

* [További](https://azuremarketplace.microsoft.com/sell) információ a Piactérről.

A partner Centerben való regisztrációhoz hozzon létre egy új ajánlatot, vagy dolgozzon egy meglévőn:

* Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
* További információért lásd: [SaaS-alkalmazás létrehozási ajánlata](./partner-center-portal/create-new-saas-offer.md) .
