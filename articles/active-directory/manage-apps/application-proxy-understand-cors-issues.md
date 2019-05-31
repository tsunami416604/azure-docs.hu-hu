---
title: Megismerheti, és az Azure AD Application Proxy CORS hiba elhárításához nyújthatnak segítséget
description: Itt a CORS-t az Azure AD-alkalmazásproxy és CORS problémák azonosítását és megoldását annak megértését.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399341"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Megismerheti, és az Azure Active Directory Application Proxy CORS hiba elhárításához nyújthatnak segítséget

[Eltérő eredetű erőforrások megosztása (CORS)](http://www.w3.org/TR/cors/) néha kihívást az alkalmazások és API-k az Azure Active Directory Application Proxy-n keresztül közzétett. Ez a cikk ismerteti az Azure AD Application Proxy CORS problémákat és megoldásokat.

A böngésző biztonsági általában megakadályozható, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományhoz. Ez a korlátozás nevezzük a *azonoseredet-* , és megakadályozza, hogy egy rosszindulatú webhely érzékeny adatok olvasása a másik helyről. Előfordulhat azonban, hogy ahhoz, hogy a webes API hívása más helyekre. A CORS egy W3C szabvány, amely lehetővé teszi, hogy egy kiszolgáló enyhíteni az azonos eredethez házirendet, és néhány eltérő eredetű kérések engedélyezése elutasítása mellett más.

## <a name="understand-and-identify-cors-issues"></a>Megismerheti és azonosíthatja azokat a CORS-problémák

Két URL-címet azonos származási rendelkezik, ha rendelkeznek azonos sémák, a gazdagépek és a portok ([RFC 6454](https://tools.ietf.org/html/rfc6454)), például:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

A következő URL-címekkel rendelkezik a különböző források, mint az előző két:

-   http:\//contoso.net - másik tartományba
-   http:\//contoso.com:9000/foo.html - másik portot
-   https:\//contoso.com/foo.html - különböző séma
-   http:\//www.contoso.com/foo.html – különböző altartomány

Azonoseredet-letiltja az alkalmazások erőforrásokhoz való hozzáférés a más források egyedül a megfelelő hozzáférés-vezérlő fejléceket. Ha a CORS fejlécek hiányzik vagy hibás, eltérő eredetű kérelmek sikertelenek. 

CORS-problémák böngésző hibakereső eszközeivel határozza meg:

1. Indítsa el a böngészőt, és keresse meg a webalkalmazást.
1. Nyomja meg **F12** viszi, megjelenik a hibakeresési konzolt.
1. Próbálja meg reprodukálni a tranzakciót, és tekintse át a konzol üzenetet. A CORS megsértése forrás egy konzol hibaüzenetet eredményez.

Az alábbi képernyőképen kiválasztása a **Kipróbálom** gomb okozza a CORS hibaüzenet, hogy https:\//corswebclient-contoso.msappproxy.net az Access-Control-Allow-Origin fejléc nem található.

![CORS-probléma](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Az alkalmazásproxy használatával a CORS kihívásai

A következő példa mutatja egy jellemző az Azure AD Application Proxy CORS forgatókönyv. A belső gazdagépek egy **CORSWebService** webes API-vezérlő, és a egy **CORSWebClient** meghív **CORSWebService**. Van egy AJAX kérelme **CORSWebClient** való **CORSWebService**.

![A helyszíni azonos eredethez kérelem](./media/application-proxy-understand-cors-issues/image1.png)

A CORSWebClient alkalmazás üzemeltetése, a helyszínen, de betöltése sikertelen, vagy hibák, amikor az Azure AD-alkalmazásproxyn keresztül közzétett működik. Ha közzé a CORSWebClient és CORSWebService alkalmazások külön-külön proxyn keresztül történő alkalmazás különböző alkalmazásokat, a két alkalmazás üzemeltetett eltérő tartományokban. Az AJAX kérelem CORSWebClient CORSWebService eltérő eredetű kérelem, és ez nem sikerül.

![Application Proxy CORS kérése](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Application Proxy CORS problémák megoldásait

Számos módon bármelyike az előző CORS probléma is fel tudja oldani.

### <a name="option-1-set-up-a-custom-domain"></a>Option 1: Egyéni tartomány beállítása

Használja az Azure AD-alkalmazásproxy [egyéni tartomány](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) közzététele az azonos forrásból semmilyen módosítást alkalmazás források, kódok vagy fejlécek nélkül. 

### <a name="option-2-publish-the-parent-directory"></a>Option 2: A szülőkönyvtárhoz közzététele

Tegye közzé szülőkönyvtára mindkét alkalmazást. A megoldás együttműködik különösen jól, ha csak két alkalmazásokat a webkiszolgálón. Ahelyett, hogy minden alkalmazáshoz külön-külön közzététel, a közös szülőkönyvtárában, ami azonos származási teheti közzé.

Az alábbi példák bemutatják a portálon az CORSWebClient alkalmazás Azure AD-alkalmazásproxy lapját.  Ha a **belső URL-cím** értékre van állítva *contoso.com/CORSWebClient*, az alkalmazás nem hajtható végre, a sikeres kérések a *contoso.com/CORSWebService* könyvtár, mert eltérő eredetű zajlik. 

![Egyenként tegye közzé az alkalmazást](./media/application-proxy-understand-cors-issues/image4.png)

Ehelyett állítsa be a **belső URL-cím** közzététele a szülő könyvtár, amely egyaránt tartalmazza a *CORSWebClient* és *CORSWebService* könyvtárak:

![Közzététel a szülőkönyvtárhoz](./media/application-proxy-understand-cors-issues/image5.png)

Az eredményül kapott alkalmazás URL-címeket hatékonyan feloldani a CORS-hiba:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>3. lehetőség: HTTP-fejlécek frissítése

Egyéni HTTP-válaszfejléc hozzáadása az eltérő eredetű kérés megfelelően a web service. Webhely Internet Information Services (IIS), az IIS-kezelő használatával módosítsa a fejléc:

![Egyéni válaszfejléc hozzáadása az IIS-kezelőben](./media/application-proxy-understand-cors-issues/image6.png)

Ez a módosítás nem szükséges kódot módosítania. A Fiddler-nyomkövetés ellenőrizheti:

**POST fejléc hozzáadása**\
HTTP/1.1 200 OK\
A Cache-Control: nem-cache\
Direktiva Pragma: nem-cache\
Content-Type: text/plain; charset = utf-8\
Lejárat:-1\
Eltérőek: Fogadja el Encoding\
Kiszolgáló: A Microsoft-az IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>4. lehetőség: Az alkalmazás módosítása

Módosíthatja az alkalmazások a CORS támogatására az Access-Control-Allow-Origin fejléc, a megfelelő értékekre hozzáadásával. A fejléc hozzáadása módja függ az alkalmazás nyelv. A kód megváltoztatása lehetőség legalább ajánlott, mivel a legtöbb beavatkozást igényel.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>5. lehetőség: A hozzáférési jogkivonat élettartama kiterjesztése

CORS-problémák nem oldható fel, például ha az alkalmazás átirányítja a felhasználókat az *login.microsoftonline.com* hitelesítést, és a hozzáférési jogkivonat lejár. A CORS hívja, akkor sikertelen lesz. Ebben a forgatókönyvben a megoldás az, nehogy azt a felhasználói munkamenet során lejár, a hozzáférési jogkivonat élettartama kiterjesztése. Ezzel kapcsolatos további információkért lásd: [konfigurálható jogkivonatok élettartamának, az Azure ad-ben](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Lásd még
- [Oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban](application-proxy-add-on-premises-application.md) 
- [Az Azure AD-alkalmazásproxy központi telepítésének megtervezése](application-proxy-deployment-plan.md) 
- [Az Azure Active Directory Application Proxy keresztül a helyszíni alkalmazások távoli elérését](application-proxy.md) 
