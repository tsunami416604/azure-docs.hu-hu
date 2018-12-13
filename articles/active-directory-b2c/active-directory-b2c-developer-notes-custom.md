---
title: Megjegyzések az Azure Active Directory B2C-vel egyéni szabályzatok segítségével a fejlesztők számára |} A Microsoft Docs
description: Megjegyzések konfigurálását és karbantartását az Azure AD B2C-vel egyéni szabályzatok a fejlesztők számára készült.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ea884f6fecc1e8a0de1f6a0f8a4daafcdf612e9f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272702"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Az Azure Active Directory B2C-vel egyéni házirendet nyilvános előzetes verzió kibocsátási megjegyzései
Egyéni szabályzatkészlet funkció már elérhető az értékeléshez tartozó összes Azure Active Directory B2C nyilvános előzetes verzióként érhető el (Azure AD B2C-vel) ügyfelek. E szolgáltatáskészlet a fejlesztők a fejlett identitáskezelési a legösszetettebb identitáskezelési megoldások célja.  

E szolgáltatáskészlet még ma, a fejlesztők számára, hogy az identitás-kezelőfelületi keretrendszer használatával közvetlenül XML-fájl szerkesztése konfigurálása szükséges. A konfigurációs módszer a nagy teljesítményű és összetett. Fejlett identitás, az identitás-kezelőfelületi keretrendszer használatával a fejlesztők támogatásán triggerhez kapcsolódó befejezése és segédanyagok olvasási időt kell terveznie. 

## <a name="features-included-in-this-public-preview"></a>A nyilvános előzetes verzióban foglalt funkciók
A nyilvános előzetes verzióban bevezetett új funkciókkal a fejlesztők a következő feladatokat végezheti el:<br>

* Hozhat létre, és töltse fel az egyéni hitelesítési felhasználói utak egyéni szabályzatok használatával. 
   * Felhasználói utak cseréje, részletes közötti Jogcímszolgáltatók ismertetik. 
   * Adja meg a felhasználói utak feltételes elágaztatás. * Az egyéni hitelesítési felhasználói utak szolgáltatások REST API-t integrálja.  
* Adja hozzá a megfelelő szabványos OpenIDConnect identitásszolgáltatókkal összevonási. <br>
* Adja hozzá az összevonás az Identitásszolgáltatók, amelyek igazodnak a SAML 2.0 protokoll. 

## <a name="terms-of-the-public-preview"></a>A nyilvános előzetes feltételeinek

* Azt javasoljuk, hogy az új funkciók csak tesztelési célokra használhatók.<br>
* Az új szolgáltatások éles környezetben való használatra nem alkalmasak.<br>
* Az új szolgáltatások szolgáltatásiszint-szerződések (SLA) nem vonatkoznak. <br>
* Támogatási kérések rendszeres támogatási csatornákon keresztül lehet benyújtani. <br>
* Nincs megadva Ígért dátum általános rendelkezésre állás érdekében.<br>
* A saját belátásunk szerint, és bármilyen okból a Microsoft is jelzőt és elutasítása vagy forgatókönyvek és a felhasználói utak keretet túllépő része egy ügyfél identitás- és hozzáférés-kezelő (CIAM) platform, mely az Azure AD B2C-vel termék bérleti hatókörének korlátozása.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Egyéni szabályzat szolgáltatáskészletet fejlesztők feladatai
Manuális szabályzatkonfiguráció alacsonyabb szintű hozzáférést biztosít az Azure AD B2C az alapul szolgáló platform és egy egyedi, teljes mértékben testre szabható a bizalmi keretrendszer létrehozását eredményezi. Ezek lehetséges kombinációinak száma egyéni Identitásszolgáltatók, megbízhatósági kapcsolatokat, az integrációt a külső szolgáltatások és a munkafolyamatok részletes fel őket a speciális fejlesztők nagyobb a növekvő igények szerint helyezze el.

Teljes körűen kihasználhatják a nyilvános előzetes verzióban, javasoljuk, hogy egyéni szabályzatkészlet funkciót használó fejlesztők formátumhoz a következő irányelveket:
* Megismerkedhet az identitás-kezelőfelületi keretrendszer és a kulcs vagy titkos kódokkal felügyeleti konfiguráció nyelven.
* Saját tulajdonba vétel forgatókönyveket és egyéni Integrációk.
* A forgatókönyv módszeres teszt végrehajtása.
* Hajtsa végre a szoftverfejlesztést és átmeneti legalább egy fejlesztési és tesztelési környezetet az ajánlott eljárásokról és a egy éles környezetben.
* Azonnali tájékozódás az új fejlesztések az identitás-szolgáltatóktól és szolgáltatásokat integrálja. Ha például nyomon követheti, változások a titkos kódok és az ütemezett és nem tervezett módosításokat a szolgáltatásban.
* Aktív figyelés beállítása, és figyelheti az alkalmazások válaszkészségét éles környezetben.
* Az Azure-előfizetésében naprakészen kapcsolattartási e-mail-címeket, és továbbra is a Microsoft-webhelyek működés közbeni csapatával e-mailekre válaszkész maradhat.
* Amikor javasoljuk, hogy a webhelyek működés közbeni csoportja. Ehhez kellő időben művelet végrehajtása. 

## <a name="features-by-stage-and-known-issues"></a>Funkciók szakasz és ismert problémák
Egyéni szabályzat/identitás-kezelőfelületi keretrendszer képességek folyamatos és gyors fejlesztés alatt áll.  Ez a tábla indexe szolgáltatások/összetevő rendelkezésre állása alapján.

Kérdéseit felteheti a Stack overflow-n: [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Identitás-szolgáltatók jogkivonatok, protokollok
A külső összetevők és a felületek

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | Ha például a Google + |
| IDP-OAUTH2 |  | x |  | Ha például a Facebookon  |
| IDP-OAUTH1 |  | x |  | Ha például a Twitteren |
| IDP-SAML |  | x |  | Ha például Salesforce-ban, ADFS |
| IDP-WSFED | x |  |  |  |
| Függő entitás OAUTH |  | x |  |  |
| Függő entitás OIDC |  | x |  |  |
| Függő entitás SAML | x |  |  |  |
| Függő entitás WSFED | x |  |  |  |
| Az alapszintű és a tanúsítványváltás auth REST API-val |  | x |  | Ha például az Azure Functions |


### <a name="component-support"></a>Összetevő-támogatás


| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi Factor Authentication |  | x |  |  |
| Az Azure Active Directory helyi könyvtárként |  | x |  |  |
| Az Azure e-mailek alrendszer 2FA |  | x |  |  |
| Több nyelv támogatása|  | x |  |  |
| Jelszó bonyolultsága | x |  |  |  |


### <a name="content-definition"></a>Tartalomdefiníció

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Hibalap, api.error |  | x |  |  |
|   Identitásszolgáltató kiválasztása oldal, api.idpselections |  | x |  |  |
|   Identitásszolgáltató kiválasztása a regisztrációt, api.idpselections.signup |  | x |  |  |
|   Forgot Password, api.localaccountpasswordreset |  | x |  |  |
|   Helyi fiók bejelentkezési, api.localaccountsignin |  | x |  |  |
|   Helyi fiók regisztrálási, api.localaccountsignup |  | x |  |  |
|   Többtényezős hitelesítés lap, api.phonefactor |  | x |  |  |
|   Önálló kiszolgáló által megerősített – például közösségi fiók regisztrációs, api.selfasserted |  | x |  |  |
|   Önálló kiszolgáló által megerősített profil frissítése, api.selfasserted.profileupdate |  | x |  |  |
|   Egyesített regisztrációs vagy bejelentkezési oldal, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Alkalmazás-IEF integráció
| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Lekérdezési karakterlánc paraméter id_token_hint | x |  |  |  |
| Lekérdezési karakterlánc paraméter domain_hint |  | x |  | valamint a jogcímek, az Identitásszolgáltató adható át érhető el |
| Lekérdezési karakterlánc paraméter login_hint |  | x |  | valamint a jogcímek, az Identitásszolgáltató adható át érhető el |
| JSON beillesztése UserJourney client_assertion keresztül | x |  |  | elavulttá válik |
| UserJourney id_token_hint, JSON beillesztése | x |  |  | go-továbbító megközelítés át, JSON |


### <a name="session-management"></a>Munkamenet-kezelés

| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|---------------------------------|-------------|---------|----|-------|
| Egyszeri bejelentkezés munkamenet-szolgáltató |  | x |  |  |
| Külső bejelentkezés munkamenet-szolgáltató |  | x |  |  |
| SAML egyszeri bejelentkezés munkamenet-szolgáltató |  | x |  |  |


### <a name="security"></a>Biztonság
| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|---------------------------------------------|-------------|---------|----|-------|
| A házirend kulcsok létrehozása, manuális feltöltése |  | x |  |  |
| A házirend kulcsok – RSA/Cert, titkos kulcsok |  | x |  |  |


### <a name="developer-interface"></a>Fejlesztői felület
| Szolgáltatás | Fejlesztés | Előzetes verzió | FE | Megjegyzések |
|---------------------------------------------|-------------|---------|----|-------|
| Az Azure Portal-IEF UX |  | x |  |  |
| Application Insights UserJourney naplók  |  | x |  |  |
| Application Insights-eseménynaplók |x|  |  |  |



## <a name="next-steps"></a>További lépések
[Egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).
