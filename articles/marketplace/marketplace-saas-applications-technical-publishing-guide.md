---
title: Az Azure piactér SaaS alkalmazások technikai útmutató közzététele
description: Részletes útmutató és a közzétételi ellenőrzőlisták közzétételéhez SaaS-alkalmazásokhoz az Azure piactéren
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809471"
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS alkalmazások műszaki közzétételi útmutató

Üdvözli az Azure piactér SaaS alkalmazások technikai útmutató közzététele. Ez az útmutató célja jelölt és a meglévő közzétevők az alkalmazásaikat és az Azure piactéren ajánlat SaaS-alkalmazások használata a szolgáltatások elemre. 

Jobb megértése érdekében olyan SaaS ajánlat közzététele, ez az útmutató a következő szakaszok oszlik:
* Az ajánlat áttekintése
* Üzleti feltételek
* Technikai követelmények
* Közzétételi folyamata
* Az Azure Active Directoryval kísérletek engedélyezése
* Az Azure AD-integrációs a piactér igazoló

## <a name="offer-overview"></a>Az ajánlat áttekintése  

Mindkét Azure kirakatokkal SaaS-alkalmazások elérhetők a következő táblázat ismerteti az aktuális elérhető lehetőségek:

| Storefront beállítás | Listaelem | Próbaverzió/Transact |  
| --- | --- | --- |  
| AppSource | Igen (megkereshet) | Igen (Power bi/Dynamics) |
| Azure Piactér | Nem | Igen (SaaS-alkalmazásokhoz) |   

**Listaelem:** a listaelem közzétételi beállítás áll egy ügyfél Me ajánlattípus és használatos, ha egy próba - vagy tranzakciószintű részvétel esetén nem valósítható meg. Ennek a megközelítésnek az az előnye, hogy azonnali megkezdéséhez fogadása érdeklődőket, növelje az üzleti üzletek be lehet kapcsolni a megoldás a piaci a közzétevők lehetővé teszi.  
**Próbaverzió/Transact:** az ügyfél közvetlenül vásárolni, vagy kérje meg a megoldást egy próbaidőszakot lehetősége van. A próbaidőszak felhasználói élmény biztosítása növeli az ügyfél számára biztosított engagement szintjét, és lehetővé teszi az ügyfelek a megoldás felfedezése megvásárlása előtt. Próbaverzió nyújthassunk hogy jobban veszélyét annak, hogy az előléptetés a kirakatokkal a, és az ügyfél kapcsolattartás során több és több funkcióval érdeklődők kell látnia. Próbaverzió tartalmaznia kell szabad támogatási legalább a próbaidőszak alatt.  

| SaaS-alkalmazások ajánlat | Üzleti feltételek | Technikai követelmények |  
| --- | --- | --- |  
| **Kapcsolatfelvétel** | Igen | Nem |  
| **Power bi / Dynamics** | Igen | Igen (az Azure AD-integrációs) |  
| **SaaS-alkalmazásokhoz**| Igen | Igen (az Azure AD-integrációs) |     

További információ a piactér kirakatokkal és az egyes közzétételi lehetőségek adati a következők szerint: a [piactér Publisher útmutató](https://aka.ms/sellerguide) és [közzétételi beállítások](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Üzleti feltételek
A Szolgáltatottszoftver kínál az üzleti követelmények és a technikai követelmények párhuzamosan is elvégezhető. Legtöbb üzleti követelmények és információk gyűjtése a Cloud Partner portálra a SaaS ajánlat létrehozásakor. Az üzleti követelmények a következők: 
* A programban való részvétellel házirendek elfogadását jelző mezőt
* Integráció a Microsoft 
* Az ajánlat célközönség azonosítása
* Adja meg, és határozza meg a használandó átfutási felügyeleti
* Adatvédelmi szabályzatát, illetve a használati feltételek beállítása
* A támogatási kapcsolattartó meghatározása  

További információt, akkor itt található: a témakör [piactér közzététel előfeltételei](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Technikai követelmények

A műszaki által támasztott követelmények a SaaS-alkalmazások egyszerű. Közzétevők csak az Azure Active Directoryval (Azure AD) közzé kell tenni integrációját van szükség. Alkalmazások integrálása az Azure AD megfelelően legyen dokumentálva, és a Microsoft több SDK-k és erőforrások ehhez biztosít.  

Indítsa el, azt javasoljuk, hogy rendelkezik előfizetéssel az Azure piactér a közzététel dedikált lehetővé téve a munkát a más kezdeményezések elkülönítése. A közzététel után megkezdheti a SaaS-alkalmazáshoz ebben az előfizetésben a fejlesztéseiben elindításához telepítését.  

A legjobb Azure Active Directory dokumentációjának, mintákat és útmutatást a következő helyeken találhatók meg: 

* [Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Az Azure Active Directory integrálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure menetrend - biztonság és identitás](https://azure.microsoft.com/roadmap/?category=security-identity)

A videó oktatóanyagokat tekintse meg a következőket:

* [Az Azure Active Directory-hitelesítés az Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Az Azure Active Directory identitás műszaki tájékoztató - 2 / 1. rész](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Az Azure Active Directory identitás műszaki tájékoztató - 2 2. rész](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Épület-alkalmazások a Microsoft Azure Active Directoryban](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [A Microsoft Azure-videók arra irányul, hogy az Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Ingyenes Azure Active Directory-képzési érhető el:  
* [A Microsoft Azure informatikai szakemberek számára a tartalom adatsorozathoz: az Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Azure Active Directory emellett egy helyet, hogy a szolgáltatás frissítéseinek keresése   
* [Az Azure AD szolgáltatás frissítése](https://azure.microsoft.com/updates/?product=active-directory)

Támogatási használhatja a következőket:
* [A Microsoft Developer Network fórumai](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Közzétételi folyamata

A Szolgáltatottszoftver-közzétételi folyamat műszaki és üzleti lépéseket rendelkezik.  Az ajánlat üzleti követelmények teljesítéséhez szükséges munka párhuzamosan teheti meg, amely a fejlesztés és Azure Active Directory integrálása a munka nagyobb része. A tömeges az üzleti igények SaaS-alkalmazás ajánlat konfigurációját a Cloud Partner portálra részét képezik.  
Az alábbi ábrán látható, a fő közzétételi lépései a próbaverzió/Transact kínál:  

![SaaS-közzétételi lépései](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

A következő táblázat ismerteti az egyes fő közzétételi lépéseket:  

| Közzététel | Leírás |   
| --- | --- |  
| **Az SaaS-alkalmazás létrehozása** | Jelentkezzen be a Cloud Partner portálra, válassza ki **új**, majd válassza ki a **SaaS-alkalmazások** kínálnak. |  
| **Az integráció létrehozása az Azure ad szolgáltatással** | Kövesse az előző szakaszban leírt műszaki követelményeitől való integrálása a Szolgáltatottszoftver-kínál az Azure ad-val. |  
| **Az ajánlat beállításainak megadása**| Adja meg az összes SaaS ajánlat kezdeti adatokat. Ajánlat Azonosítóját és nevét kínálnak szeretné használni. |     
| **A műszaki adatainak beállításához** | Adja meg az ajánlat technikai információkat. Az SaaS-alkalmazásokhoz a megoldás URI és ajánlat beszerzési gomb (ingyenes, eljárást, vagy forduljon Me) kell megadni. |  
| **Drive(Optional) tesztelése** | Ez az egy nem kötelező típus vizsgálat szükséges többnyire más típusok a piactér kínál. Ez lehetővé teszi, hogy a próbaidőszak, a közzétevő előfizetések és a záró ügyfél telepítve van. |  
| **Az ajánlat Storefront anyagot beállítása**| Ebben a szakaszban a közzétevő és töltse fel az emblémát, marketingcélú, jogi dokumentumok és összekapcsolja a érdeklődők rendszer konfigurálja. |
| **Az ajánlat partnerek beállítása** | Adja meg a mérnöki csapathoz névjegyeket és a támogatási kapcsolattartási adatokat a Szolgáltatottszoftver-szolgáltatásokat. |  
| **SaaS-alkalmazások Azure AD-integrációs ellenőrzése** | Történő elküldésekor az SaaS-alkalmazás közzététele előtt ellenőriznie kell, hogy az alkalmazás az Azure ad-vel integrált |  
| **Az ajánlat közzététele**| Az ajánlat elküldheti a ajánlatot és a műszaki eszközök elvégzése után. Ez a közzétételi folyamat, amelyben a megoldássablon tesztelik, érvényesítve, hitelesített és közzétételi jóváhagyott indul el. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Az Azure Active Directoryval kísérletek engedélyezése  

Microsoft összes piactér felhasználóit hitelesíti az Azure ad-val, így amikor a hitelesített felhasználók a próbaverzió listázása a piactéren keresztül kattint, és átirányítja a próbaverzió környezet, létesíthet a felhasználó közvetlenül a próbaverzió anélkül, hogy egy további bejelentkezési lépést. A jogkivonatot, amely az alkalmazás fogad az Azure AD-hitelesítés során használhatja egy felhasználói fiókot létrehozni az alkalmazás, így a telepítési élmény automatizálásához és átalakítás érdekében értékes felhasználói adatokat tartalmaz. A token kapcsolatos további információkért lásd: [minta jogkivonatok](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Az alkalmazás vagy a próbaidőszak 1 kattintással hitelesítés engedélyezése az Azure AD használatával hajtja végre a következő:  
* Leegyszerűsíti a felhasználói élmény a próba a piactér.  
* Kezeli a érzetéhez a terméken belüli számára, még ha a felhasználót a rendszer átirányítja piactérről a tartományhoz vagy a próbaidőszak környezetben.  
* Csökkenti a átirányítási elhagyása valószínűségét, mert nincs egy további bejelentkezési lépést.  
* Csökkenti a nagy feltöltése az Azure Active Directory-felhasználók központi telepítési lépést.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Az Azure AD-integrációs a piactér igazoló  

Is az Azure AD-integrációs tanúsítsa, számos különböző módja, attól függően, hogy az alkalmazás bérlői egy vagy több-bérlős, és ha még nem ismeri az Azure AD összevont egyszeri bejelentkezést (SSO), vagy már támogatják a forgatókönyvet.  

**Több-bérlős alkalmazásokhoz:**  

Ha már támogatja az Azure AD, tegye a következőket:
1.  Az alkalmazás regisztrálása az Azure-portálon
2.  A több-bérlős támogatás funkció engedélyezése az Azure AD-be a "Tallózás" próbaverziója beolvasása. További információk találhatók [Itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Ha most ismerkedik az Azure AD összevont egyszeri Bejelentkezést, tegye a következőket: 
1.  Az alkalmazás regisztrálása az Azure-portálon
2.  Egyszeri bejelentkezés fejlesztése az Azure AD használatával [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) vagy [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Több vállalat kiszolgálása támogatásának engedélyezése a "Tallózás" próbaverziója további információk eléréséhez az AAD szolgáltatás található [Itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Single-bérlőt az alkalmazáshoz használja a következő beállításokat:**  
* Felhasználók hozzáadása a címtárhoz Vendég felhasználóként [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Manuálisan hozzon létre, az ügyfelek kísérletek "Me forduljon" használatával
* /-Ügyfél "Tesztelése" fejlesztése
* Egy több-bérlős bemutató mintaalkalmazás SSO összeállítása

