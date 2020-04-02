---
title: Az Azure Marketplace SaaS-alkalmazások műszaki közzétételi útmutatója
description: Részletes útmutató és közzétételi ellenőrzőlisták a SaaS-alkalmazások Azure Piactéren való közzétételéhez
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 641297ea74a08dea163cf768b8e9b245348824a4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544280"
---
# <a name="saas-applications-offer-publishing-guide"></a>Az SaaS-alkalmazások ajánlat-közzétételi útmutatója

A SaaS-alkalmazások három különböző cselekvésre szólító felhívással tehetők közzé a piacon: "Kapcsolatfelvétel", "Próbálja ki most" és "Most kapja el". Ez az útmutató ismerteti ezt a három lehetőséget, beleértve az egyes követelmények. 

## <a name="offer-overview"></a>Ajánlat áttekintése  

Az SaaS-alkalmazások mindkét Azure Storefrontban elérhetők Az alábbi táblázat az aktuálisan elérhető lehetőségeket ismerteti:

| Kirakati lehetőség | Listázási | Próba/tranzakció |  
| --- | --- | --- |  
| AppSource | Igen | Igen |
| Azure piactér | Nem | Igen |   

**Lista:**  A Listing közzétételi lehetőség egy Kapcsolat felvételi ajánlattípusból áll, és akkor használatos, ha a próba- vagy tranzakciószintű részvétel nem valósítható meg. Ennek a megközelítésnek az az előnye, hogy lehetővé teszi a piacon lévő megoldással rendelkező megjelenítők számára, hogy azonnal elkezdjék az érdeklődőket, amelyek et üzletté alakíthatnak, hogy növeljék a vállalkozását.  
**Próba/tranzakció:**  Az ügyfélnek lehetősége van arra, hogy közvetlenül megvásárolja vagy próbaverziót kérjen az Ön megoldásához. A próbaverzió sokkal nagyobb mértékben növeli az ügyfelek számára kínált elköteleződési szintet, és lehetővé teszi az ügyfelek számára, hogy vásárlás előtt feltárják a megoldást. A próbaverziós élménynek nagyobb esélye idái lesznek a kirakatokban való promócióra, és több és gazdagabb érdeklődőt kell várnia az ügyfelek elköteleződésétől. A próbaverzióknak tartalmazniuk kell legalább a próbaidőszak alatt nyújtott ingyenes támogatást.  

| SaaS alkalmazások ajánlata | Üzleti követelmények | Műszaki követelmények |  
| --- | --- | --- |  
| **Kapcsolat** | Igen | Nem |  
| **PowerBI / Dinamika** | Igen | Igen (Azure AD-integráció) |  
| **SaaS-alkalmazások**| Igen | Igen (Azure AD-integráció) |     

## <a name="saas-list"></a>SaaS-lista

A próba- és számlázási funkciók nélküli SaaS-adatlapok rakoncáttéba való felhívása a "Kapcsolatfelvétel". 

Nem kell konfigurálnia az Azure Active Directoryt egy SaaS-alkalmazás listázásához. 

|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás egy SaaS-ajánlat  |   A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket kínál.      |


## <a name="saas-trial"></a>SaaS próba

Olyan megoldást vagy alkalmazást biztosít, amely ingyenesen kipróbálhatja a szoftverszolgáltatásként (SaaS) alapú próbaverziót. Az ingyenes próbaajánlatok korlátozott használatú vagy korlátozott időtartamú próbafiókként is megjelenhetnek. 


|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás egy SaaS-ajánlat  |   A megoldás egy SaaS-ajánlat, és több-bérlős SaaS-terméket kínál.      |
|Az alkalmazás AAD-kompatibilis     |   Az ügyfél átirányítja az Ön domain, és akkor a tranzakció az ügyfél közvetlenül       |


## <a name="saas-trial-technical-requirements"></a>SaaS próbatechnikai követelmények

A SaaS-alkalmazások műszaki követelményei egyszerűek. A közzétevők csak akkor kell integrálni az Azure Active Directory (Azure AD) közzé kell tenni. Az Azure AD-integráció az alkalmazásokkal jól dokumentált, és a Microsoft több SDK-t és erőforrást biztosít ennek elvégzéséhez.  

Először is azt javasoljuk, hogy az Azure Marketplace-közzétételhez dedikált előfizetéssel kell rendelkeznie, amely lehetővé teszi a munka elkülönítését más kezdeményezésektől. Miután ez megtörtént, megkezdheti a SaaS-alkalmazás üzembe helyezését ebben az előfizetésben a fejlesztési munka megkezdéséhez.  

A legjobb Azure Active Directory dokumentáció, minták és útmutatások találhatók a következő helyeken: 

* [Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integráció az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure-ütemterv – Biztonság és identitás](https://azure.microsoft.com/roadmap/?category=security-identity)

Oktatóvideók esetén tekintse át az alábbiakat:

* [Azure Active Directory hitelesítés vittorio Bertoccival](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Az Azure Active Directory-identitás technikai tájékoztatója – 1/2 rész](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Az Azure Active Directory-identitás technikai tájékoztatója – 2/2.](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Alkalmazások létrehozása a Microsoft Azure Active Directoryval](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure-videók az Active Directoryra fókuszálva](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Az ingyenes Azure Active Directory-oktatás elérhető a következő helyen:  
* [Microsoft Azure for IT Pros tartalmi sorozat: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Ezenkívül az Azure Active Directory biztosít egy helyet a szolgáltatásfrissítések kereséséhez   
* [Az Azure AD szolgáltatás frissítései](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>A próbaverziók engedélyezése az Azure Active Directory használatával  

A Microsoft hitelesíti az összes Marketplace-felhasználót az Azure AD-vel, így amikor egy hitelesített felhasználó a Marketplace-en lévő próbaverziós adatlapra kattint, és átirányítja a próbaverziós környezetbe, a felhasználót közvetlenül egy próbaverzióba helyezheti anélkül, hogy további bejelentkezési lépésre lenne szükség. Az alkalmazás által az Azure AD-től a hitelesítés során kapott jogkivonat értékes felhasználói adatokat tartalmaz, amelyek segítségével felhasználói fiókot hozhat létre az alkalmazásban, lehetővé téve a kiépítési élmény automatizálását és a konverzió valószínűségének növelését. A jogkivonatról további információt a [Mintajogtok című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

Az Azure AD használatával egy kattintásos hitelesítést engedélyez az alkalmazáshoz vagy a próbaverzióhoz, tegye a következőket:  
* Egyszerűsíti az ügyfélélményt a Marketplace-ről a próbaverzióra.  
* Fenntartja a "terméken belüli élmény" érzetét, még akkor is, ha a felhasználót átirányítja a Piactérről az Ön domain- vagy próbakörnyezetébe.  
* Csökkenti az átirányítás elhagyása valószínűségét, mert nincs további bejelentkezési lépés.  
* Csökkenti a telepítési akadályokat az Azure AD-felhasználók nagy számú számára.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Az Azure AD-integráció hitelesítése a Marketplace-en  

Az Azure AD-integrációt néhány különböző módon hitelesítheti, attól függően, hogy az alkalmazás egy- vagy több-bérlős, és hogy új-e az Azure AD összevont egyszeri bejelentkezés (SSO), vagy már támogatja azt.  

**Több-bérlős alkalmazások esetén:**  

Ha már támogatja az Azure AD-t, tegye a következőket:
1.  Az alkalmazás regisztrálása az Azure Portalon
2.  Engedélyezze a több-bérlős támogatási funkciót az Azure AD-ben, hogy egy "egy kattintással" próbaverziós élményt kapjon. További információk [itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)találhatók .  

Ha most van az Azure AD összevont SSO, tegye a következőket: 
1.  Az alkalmazás regisztrálása az Azure Portalon
2.  SSO fejlesztése az Azure AD-vel [az OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) vagy [az OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)használatával.
3.  Az AAD több-bérlős támogatási funkciójának engedélyezése az "egy kattintásos" próbaverzióhoz További konkrét információk találhatók [itt.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)  

**Egybérlős alkalmazás esetén használja az alábbi lehetőségek egyikét:**  
* Felhasználók hozzáadása a címtárhoz vendégfelhasználóként az [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) használatával
* Próbaverziók manuális kiépítése az ügyfelek számára a "Kapcsolatfelvétel" használatával
* Ügyfélenkénti "tesztvezetés" kialakítása
* Több-bérlős mintabemutató alkalmazás létrehozása egyszeri felárkal

## <a name="saas-subscriptions"></a>SaaS-előfizetések

A SaaS-alkalmazásajánlat-típussal lehetővé teszi, hogy az ügyfél előfizetésként megvásárolhassa a SaaS-alapú, technikai megoldást. A SaaS-alkalmazáshoz az alábbi követelményeknek kell megfelelni:
- A szolgáltatás díja és számlája egy lakásban (havi vagy éves) vagy felhasználónkénti díj.
- Adjon meg egy módszert a szolgáltatás bármikori frissítéséhez vagy megszakításához.
A Kereskedelmi tranzakciót a Microsoft üzemelteti. A Microsoft az Ön nevében számlázza ki az ügyfelet. SaaS-alkalmazás előfizetésként való ajánlatához integrálnia kell a SaaS teljesítési API-kat.  A szolgáltatásnak támogatnia kell a kiépítést, a frissítést és a visszavonást.

| Követelmény | Részletek |  
|:--- |:--- |  
|Számlázás és mérés | Az ajánlat ára a közzététel előtt kiválasztott díjszabási modell (átalánydíjas vagy felhasználónkénti) alapján történik.  Ha az átalánymodellt használja, akkor tetszés szerint további dimenziókat is megadhat, amelyek a vevőknek az átalánydíjban nem szereplő használatért számítanak fel díjat. |  
|Lemondás | Ajánlatát az ügyfél bármikor lemondhatja. |  
|Tranzakció céloldala | Az Azure-beli társmárkás tranzakciócéloldalt üzemelteti, ahol a felhasználók létrehozhatják és kezelhetik SaaS-szolgáltatásfiókjukat. |   
| Előfizetéses API | Olyan szolgáltatást tesz elérhetővé, amely a SaaS-előfizetéssel együttműködve felhasználói fiókot és szolgáltatási csomagot hozhat létre, frissít het és törölhet. A kritikus API-módosításokat 24 órán belül támogatni kell. A nem kritikus API-módosítások rendszeres időközönként megjelennek. |  

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](./cloud-solution-providers.md) ban talál.

## <a name="next-steps"></a>További lépések
Ha még nem tette meg,

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piacon.

Ha regisztrált, és új ajánlatot hoz létre, vagy egy meglévőn dolgozik,

- [Jelentkezzen be a Cloud Partner Portalszolgáltatásba](https://cloudpartner.azure.com) az ajánlat létrehozásához vagy teljesítéséhez.
- További információkért tekintse meg az [Azure SaaS-alkalmazásajánlatát.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)
