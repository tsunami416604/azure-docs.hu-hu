---
title: Az Azure Marketplace SaaS alkalmazások műszaki közzétételi útmutató
description: Részletes útmutató és a SaaS-alkalmazások Azure Marketplace-en való közzététel közzétételi Ellenőrzőlisták
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: keithcharlie
manager: nunoc
editor: keithcharlie
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: bd6c592f0d3d073ec2a05c8f4636086df01ddb06
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261145"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS-alkalmazások ajánlat közzétételi útmutató

SaaS-alkalmazások tehetők közzé a piactéren, három különböző művelet meghívásával: "Velem a kapcsolatot," "Kipróbálás most" és "Letöltés most." Ez az útmutató azt ismerteti, hogy ezen három lehetőségekről, beleértve az egyes. 

## <a name="offer-overview"></a>Az ajánlat áttekintése  

SaaS-alkalmazások érhetők el mindkét Azure Kirakattípus a következő táblázat ismerteti az aktuális elérhető lehetőségek közül:

| Storefront lehetőség | Listaelem | Próbaverzió/Transact |  
| --- | --- | --- |  
| AppSource | Igen (velem a kapcsolatot) | Igen (a Power bi/Dynamics) |
| Azure Piactér | Nem | Igen (SaaS-alkalmazások) |   

**Lista:**  A lista közzétételi lehetőség áll egy névjegyet ajánlattípus és használatos, ha egy próba - vagy tranzakciószintet részvételi nem megvalósítható. Ez a megközelítés előnye, hogy lehetővé teszi a megoldás a piacra jutási közzétevőket azonnali megkezdéséhez fogadása az érdeklődők, az növelheti az üzleti üzletek kapcsolható.  
**Próbaverzió és a tranzakciós:**  Az ügyfél közvetlenül vásárolni, vagy a megoldás próbaverziója lehetősége van. Próbaverzió biztosítása érdekében növeli az engagement szintjét, az ügyfelek számára érhető el, és lehetővé teszi az ügyfelek számára, hogy a megoldás megismerése megvásárlása előtt. A próbaidőszak felhasználói élményét kell jobb szoftver-és promóciós a kirakattípus a, és több és több funkcióval az ügyfélesetekből származó érdeklődők kell látnia. Kísérletek tartalmaznia kell ingyenes támogatási legalább a próbaidőszak alatt időtartamára.  

| SaaS-alkalmazások ajánlat | Üzleti feltételek | Technikai követelmények |  
| --- | --- | --- |  
| **Kapcsolatfelvétel** | Igen | Nem |  
| **PowerBI / Dynamics** | Igen | Igen (Azure AD-integráció) |  
| **SaaS-alkalmazások**| Igen | Igen (Azure AD-integráció) |     

## <a name="saas-list"></a>SaaS-lista

Nem próbaverzió és a számlázási funkció sem SaaS felsorolását művelet meghívása az "Me Contact" 

Nem kell konfigurálni az Azure Active Directory az SaaS-alkalmazások listázása. 

|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás, egy SaaS-ajánlatok  |   A megoldás egy SaaS-ajánlatok, és egy több-bérlős SaaS-terméket kínál.      |


## <a name="saas-trial"></a>SaaS Próbalehetőség

A megoldás vagy a használatával ingyenes – a – próbálja ki, szoftver--szolgáltatásként (SaaS) alkalmazások adnia-alapú próbaverzió. Ingyenes próbaverziós ajánlat, amely korlátozott használatú vagy korlátozott időtartamú próbaverziós fiók lehet benyújtani. 


|Követelmények  |Részletek  |
|---------|---------|
|Az alkalmazás, egy SaaS-ajánlatok  |   A megoldás egy SaaS-ajánlatok, és egy több-bérlős SaaS-terméket kínál.      |
|Az alkalmazás aad-ben engedélyezve     |   Az ügyfél újbóli irányított tartományához lesz, és meg fogja üzletet az ügyfél közvetlenül       |


## <a name="saas-trial-technical-requirements"></a>SaaS-próbaverzió technikai követelmények

Az SaaS-alkalmazások technikai követelmények egyszerűek. A kiadók csak szükséges integrálhatók az Azure Active Directory (Azure AD) közzé kell tenni. Az Azure AD integrációja az alkalmazások megfelelően legyen dokumentálva, és a Microsoft több SDK-k és erőforrásokat ennek érdekében biztosít.  

Indítsa el, azt javasoljuk, hogy van előfizetése az Azure Marketplace közzétételi dedikált lehetővé teszi, hogy elkülönítse a további kezdeményezésen is a munkát. Ha ezzel végzett megkezdése az SaaS alkalmazás elindításához a fejlesztési munka ebben az előfizetésben üzembe helyezése.  

Az ajánlott az Azure Active Directory-dokumentáció, minták és útmutatók az alábbi helyeken találhatók meg: 

* [Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Az Azure Active Directory integrálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Az Azure ütemterve – biztonság és identitás](https://azure.microsoft.com/roadmap/?category=security-identity)

Videós oktatóanyagok tekintse át a következőket:

* [Az Azure Active Directory-hitelesítés a Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Az Azure Active Directory Identity műszaki Briefing – 1. 2. rész](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Az Azure Active Directory Identity műszaki Briefing - 2 2. rész](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Alkalmazások fejlesztése a Microsoft Azure Active Directoryval](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Videók a Microsoft Azure Active Directory összpontosítanak.](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Ingyenes Azure Active Directory-képzés érhető el:  
* [Microsoft Azure for IT Pros tartalom sorozat: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Emellett az Azure Active Directory biztosít egy hely szolgáltatás frissítései   
* [Az Azure AD-szolgáltatásfrissítések](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Az Azure Active Directoryval kísérletek engedélyezése  

A Microsoft végzi a hitelesítést minden Marketplace-felhasználót az Azure ad-vel, ezért amikor a hitelesített felhasználók a próbaidőszak listázása a Marketplace-en keresztül kattint, és a rendszer átirányítja az Ön próbaverziós környezetére, helyezheti üzembe a felhasználó közvetlenül hozzárendelhet egy próbaverziós fiókot anélkül, hogy egy további bejelentkezési lépések. A jogkivonatot, amely az alkalmazás fogad a hitelesítés során az Azure AD-ből, amelyek segítségével hozzon létre egy felhasználói fiókot az alkalmazásba, lehetővé téve a automatizálni az üzembe helyezési élmény és átalakítás valószínűségének növelése értékes felhasználói adatokat tartalmazza. A jogkivonat kapcsolatos további információkért lásd: [minta jogkivonatok](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Az alkalmazás vagy a próbaidőszak 1 kattintással hitelesítés engedélyezése az Azure AD használatával a következőket teszi:  
* Leegyszerűsíti a felhasználói élmény a próbaverzió a piactérről.  
* A működését a terméken belüli számára fenntart még ha rendszer átirányítja a felhasználót a piactérről a tartományhoz vagy próbaverziós környezetére.  
* Csökkenti az átirányítási való lemondás valószínűségét, mert nem egy további bejelentkezési lépések.  
* Csökkenti a nagy feltöltése az Azure AD-felhasználók számára üzembe helyezési korlátok.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Igazolja a Marketplace-en Azure AD-integráció  

Is az Azure AD-integrációs tanúsítása több különböző módon, attól függően, hogy az alkalmazás egybérlős vagy több-bérlős,-e, és akár most ismerkedik az Azure AD összevont egyszeri bejelentkezést (SSO), vagy már támogatják a forgatókönyvet.  

**Több-bérlős alkalmazásokhoz:**  

Ha már támogatja az Azure AD, tegye a következőket:
1.  Az alkalmazás regisztrálása az Azure Portalon
2.  A több-bérlős támogatásának engedélyezése az Azure AD-"egykattintásos" kipróbálására. További információk találhatók [Itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Ha most ismerkedik az Azure AD összevont egyszeri Bejelentkezést, tegye a következőket: 
1.  Az alkalmazás regisztrálása az Azure Portalon
2.  Egyszeri fejlesztés az Azure AD-t [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) vagy [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Több-bérlős támogatásának engedélyezése az aad-ben "egykattintásos" próbaverziója további információkat beolvasni a szolgáltatás található [Itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Egybérlős alkalmazás esetében a következő lehetőségek bármelyikét használhatja:**  
* Felhasználók hozzáadása a címtárhoz, használó vendég felhasználók [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Manuálisan üzembe helyezése az ügyfelek kísérletek az "Me Contact"
* Fejlesztés /-ügyfél "Test Drive"
* Az egyszeri bejelentkezés több-bérlős minta bemutató alkalmazás készítése

## <a name="saas-subscriptions"></a>SaaS-előfizetések

SaaS-alkalmazás ajánlattípusra használatával engedélyezhető az ügyfelek vásárolhat előfizetést as SaaS-alapú, műszaki megoldását. Az alábbi követelményeknek kell teljesülniük az SaaS-alkalmazáshoz:
- Ár és a szolgáltatás egyszerű, a havi díj számla.
- Biztosít módszert a frissítése vagy a szolgáltatást bármikor lemondhatja.
A Microsoft a kereskedelmi tranzakciót futtat. A Microsoft az Ön nevében az ügyfél bankkártyáján. Az előfizetés számlázási egy SaaS-alkalmazást használja, engedélyeznie kell, saját előfizetés-management szolgáltatás API-t. Az előfizetés felügyeleti szolgáltatás API-t közvetlenül az Azure Resource Manager API-kat kell kommunikálniuk. Az előfizetés felügyeleti szolgáltatás API támogatnia kell a szolgáltatás kiépítését, frissítését és megszakítása.

| Követelmény | Részletek |  
|:--- |:--- |  
|Számlázással és méréssel | Az ajánlat havonta átalánydíjat díjszabása. Jelenleg nem támogatottak a használatalapú díjszabás és a használat alapú "igaz felfelé" képességeket. |  
|Megszakítás | Az ajánlat bármikor visszavonható az ügyfélnek. |  
|Tranzakció kezdőlapja | Üzemeltetése egy Azure társmárkás tranzakció kezdőlapja, ahol a felhasználók létrehozása és SaaS-szolgáltatás fiókjuk kezeléséhez. |   
| Előfizetés API | Elérhetővé teszi egy szolgáltatás, amely az SaaS-előfizetés létrehozása, frissítése és egy felhasználói fiókot és a service-csomag törlése hatással lehet. Kritikus fontosságú API-módosítás támogatnia kell a 24 órán belül. A nem kritikus API-módosítás rendszeres időközönként kiadjuk. |  

## <a name="next-steps"></a>További lépések
Ha ezt még nem tette meg,

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en.

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik

- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot.
- Lásd: [Azure SaaS-alkalmazás ajánlat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-offer) további információt.
