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
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS alkalmazások műszaki közzétételi útmutató

Üdvözli az Azure piactér SaaS alkalmazások technikai útmutató közzététele. Ez az útmutató célja jelölt és a meglévő közzétevők az alkalmazásaikat és az Azure piactéren ajánlat SaaS-alkalmazások használata a szolgáltatások elemre.  
Használandó SaaS-alkalmazásokhoz ajánlat lesz telepítve, a megoldás a saját Azure-előfizetés és az ügyfelek fog jelentkezzen be egy felületen, tervezése és kezelheti az alkalmazás teszteléséhez. Ennek érdekében használatával [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) kihasználhatják a meglévő próba-környezettel. Ez azt jelenti akkor egy ügyfél által vezetett, szolgáltatott ingyenes próbaverziót. Alapvető fontosságú a megoldás úgy, hogy lehetőséget felhő vásárlói élmény a megoldás egymástól függetlenül kell fizetni vagy díj nem teszi közzé, és így a ajánlattípus próba élményt nyújt, amely megfelel az ügyfelek hogyan megoldások keresése.  

A más piactér-ajánlatokhoz áttekintéséhez tekintse meg a [piactér Publisher útmutató](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>SaaS-alkalmazás műszaki útmutató
A műszaki által támasztott követelmények a SaaS-alkalmazások egyszerű. Közzétevők csak kell meg tudjanak jelenni az Azure AD integrálva van szükség.  Alkalmazások integrálása az Azure AD megfelelően legyen dokumentálva, és a Microsoft több SDK-k és erőforrások ehhez biztosít.  

Indítsa el, azt javasoljuk, hogy rendelkezik előfizetéssel az Azure piactér a közzététel dedikált lehetővé téve a munkát a más kezdeményezések elkülönítése. Továbbá, ha még nem telepítette, javasoljuk, hogy rendelkezik-e a következő eszközök a fejlesztési környezet részeként: 
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Az Azure powerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Az Azure Fejlesztőeszközök (áttekintheti az elérhető)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>További források
Az alábbiakban adja meg a használatuk megkönnyítése érdekében ajánlott az Azure AD forrásokra mutató hivatkozásokat: 

**Dokumentáció**

- [Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [Az Azure Active Directory integrálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Azure menetrend - biztonság és identitás](https://azure.microsoft.com/roadmap/?category=security-identity)

**Videók**

- [Az Azure Active Directory-hitelesítés az Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Az Azure Active Directory identitás műszaki tájékoztató - 2 / 1. rész](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Az Azure Active Directory identitás műszaki tájékoztató - 2 2. rész](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Épület-alkalmazások a Microsoft Azure Active Directoryban](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [A Microsoft Azure-videók arra irányul, hogy az Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Képzés**  
- [A Microsoft Azure informatikai szakemberek számára a tartalom adatsorozathoz: az Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Az Azure Active Directory szolgáltatás hogyan frissíti**  
- [Az Azure AD szolgáltatás frissítése](https://azure.microsoft.com/updates/?product=active-directory)

Támogatási használhatja a következőket:
- [A Microsoft Developer Network fórumai](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Az Azure Active Directory-katalógus
Az alkalmazás az Azure piactér/AppSource a felsorolt mellett, az alkalmazás az Azure AD alkalmazás-katalógus, amely része az Azure piactér App Store áruházból felsorolt is lehet. Az ügyfelek, akik az Azure AD használja az identitásszolgáltató megtalálhatja a különböző SaaS-alkalmazás összekötők itt közzé. A rendszergazdák összekötők hozzáadása az app-galériából és konfigurálása, majd az összekötők használata az egyszeri bejelentkezés (SSO) és üzembe helyezését. Az Azure AD az egyszeri bejelentkezés, beleértve a SAML 2.0, az OpenID Connect, a OAuth és a WS-Fed összes fő összevonási protokollt támogatja.  

Miután tesztelte, hogy működik-e az alkalmazások integrálása az Azure ad-vel, küldje el a kérést, hozzáférésre a az alkalmazás hálózati Portal. Ha rendelkezik Office 365-fiókkal, amelyek segítségével jelentkezzen be a portálra. Ha nem rendelkezik Office 365-fiókkal, jelentkezzen be Microsoft-fiókját (például az Outlook vagy a Hotmail) segítségével.

## <a name="program-benefits"></a>A program előnyei
- A legjobb lehetséges egyszeri bejelentkezést biztosít az ügyfelek
- Az alkalmazás egyszerű és minimális konfiguráció
- Ügyfelek keresse meg az alkalmazás és megtalálható a gyűjteményben
- Minden ügyfél használhat ez az integráció függetlenül attól, milyen Azure AD-Termékváltozat (ingyenes, alapszintű vagy prémium) használata
- A kölcsönös ügyfelek részletes konfigurációs útmutató
- Lehetővé teszi, hogy a felhasználók átadása ugyanahhoz az alkalmazáshoz tartozó SCIM használata

## <a name="prerequisites"></a>Előfeltételek
Egy alkalmazás az Azure AD-katalógus listázásához, az alkalmazás első implementálnia kell az Azure AD által támogatott összevonási protokollok egyikét. Olvassa el a használati feltételek a helyen az Azure AD alkalmazás-tár [Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal/).  

Az alábbiak azt ismertetik, attól függően, hogy melyik protokollt használ, a további előfeltételekre vonatkozó információk:

**OpenID Connect** – a több-bérlős alkalmazás létrehozása az Azure ad-ben, és valósítja meg a hozzájárulási keretrendszere, amely az alkalmazás. Így minden ügyfél biztosítani tudja az alkalmazás hozzájárul a bejelentkezési kérelem elküldése a közös végpontot. Azt is szabályozhatja a felhasználói hozzáférést a felhasználónak a bérlő azonosítója és a felhasználó egyszerű megkapta a jogkivonatot a alapján.  
**SAML 2.0-s vagy a WS-Fed** – az alkalmazás egy olyan képességet, ehhez az SAML-alapú vagy a WS-Fed SSO integráció SP vagy IdP módban kell rendelkeznie.
