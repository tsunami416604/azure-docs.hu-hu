---
title: Az Azure AD alkalmazásproxy CORS-problémáinak megértése és megoldása
description: A CORS az Azure AD alkalmazásproxy, és hogyan azonosíthatja és oldhatja meg a CORS-problémák.
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
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025791"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Az Azure Active Directory alkalmazásproxy cors-i problémáinak megértése és megoldása

[A több forrásból származó erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/) néha kihívást jelenthet az Azure Active Directory alkalmazásproxyn keresztül közzétett alkalmazások és API-k számára. Ez a cikk ismerteti az Azure AD alkalmazás proxy CORS problémák és megoldások.

A böngésző biztonsága általában megakadályozza, hogy egy weboldal AJAX kéréseket küldjön egy másik domainre. Ezt a korlátozást *azonos eredetű házirendnek*nevezzük, és megakadályozza, hogy egy rosszindulatú webhely bizalmas adatokat olvasson le egy másik webhelyről. Néha azonban érdemes lehet, hogy más webhelyek hívja meg a webes API-t. A CORS egy W3C szabvány, amely lehetővé teszi, hogy a kiszolgáló ellazítsa az azonos eredetű házirendet, és engedélyezze a kereszteredetű kérelmeket, miközben másokat elutasít.

## <a name="understand-and-identify-cors-issues"></a>A CORS-problémák megértése és azonosítása

Két URL azonos eredetű, ha azonos sémákkal, állomásokkal és portokkal rendelkeznek ([RFC 6454](https://tools.ietf.org/html/rfc6454)), mint például:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

A következő URL-ek eredete eltér az előző kettőtől:

-   http:\//contoso.net - Különböző domain
-   http:\//contoso.com:9000/foo.html - Különböző port
-   https:\//contoso.com/foo.html - Más rendszer
-   http:\//www.contoso.com/foo.html - Különböző aldomain

Az azonos eredetű házirend megakadályozza, hogy az alkalmazások más eredetű erőforrásokhoz férjenek hozzá, kivéve, ha a megfelelő hozzáférés-vezérlési fejléceket használják. Ha a CORS-fejlécek hiányoznak vagy helytelenek, a kereszteredetű kérelmek sikertelenek lesznek. 

A CORS-problémákat a böngésző hibakeresési eszközeivel azonosíthatja:

1. Indítsa el a böngészőt, és keresse meg a webalkalmazást.
1. Nyomja le **az F12** billentyűt a hibakeresési konzol felcsipszolásához.
1. Próbálja meg reprodukálni a tranzakciót, és tekintse át a konzolüzenetet. A CORS-megsértés konzolhibát okoz az eredetivel kapcsolatban.

A következő képernyőképen a **Try It** gomb kiválasztásával egy\/CORS-hibaüzenet et jelzett, amely https: /corswebclient-contoso.msappproxy.net nem található az Access-Control-Allow-Origin fejlécben.

![CORS-probléma](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS kihívások alkalmazásproxyval

A következő példa egy tipikus Azure AD alkalmazásproxy CORS-forgatókönyvet mutat be. A belső kiszolgáló egy **CORSWebService** web API-vezérlőt és egy **CORSWebClient programot** üzemeltet, amely **corswebservice-t hív**meg. Van egy AJAX kérés **corswebclient** a **CORSWebService**.

![Helyszíni azonos eredetű kérelem](./media/application-proxy-understand-cors-issues/image1.png)

A CORSWebClient alkalmazás működik, ha a helyszíni üzemeltetése, de vagy nem töltődik be, vagy hibák at, amikor közzétett az Azure AD alkalmazásproxy n keresztül közzétett. Ha a CORSWebClient és a CORSWebService alkalmazásokat külön-külön tette közzé különböző alkalmazásokként az alkalmazásproxyn keresztül, a két alkalmazás különböző tartományokban található. A CORSWebClient által a CORSWebService-nek küldött AJAX-kérelem egy kereszteredetű kérelem, és sikertelen.

![Alkalmazásproxy CORS-kérelem](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Megoldások alkalmazásproxy CORS-problémákhoz

Az előző CORS-problémát többféleképpen is megoldhatja.

### <a name="option-1-set-up-a-custom-domain"></a>1. lehetőség: Egyéni tartomány beállítása

Azure AD alkalmazásproxy [egyéni tartomány](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) használatával közzéteheti az azonos eredetű, anélkül, hogy bármilyen módosítást az alkalmazás eredetét, kódját vagy fejlécét. 

### <a name="option-2-publish-the-parent-directory"></a>2. lehetőség: A szülőkönyvtár közzététele

Tegye közzé mindkét alkalmazás szülőkönyvtárát. Ez a megoldás különösen jól működik, ha csak két alkalmazás van a webkiszolgálón. Ahelyett, hogy az egyes alkalmazásokat külön-külön tenné közzé, közzéteheti a közös szülőkönyvtárat, amely ugyanazt az eredetet eredményezi.

A következő példák bemutatják a corswebclient alkalmazás Azure AD alkalmazásproxylapját.  Ha a **belső URL-cím** *contoso.com/CORSWebClient*van beállítva, az alkalmazás nem tud sikeres kéréseket a *contoso.com/CORSWebService* könyvtárba, mert azok több eredetűek. 

![Alkalmazás egyenkénti közzététele](./media/application-proxy-understand-cors-issues/image4.png)

Ehelyett állítsa be a **belső URL-címet** a szülőkönyvtár közzétételére, amely a *CORSWebClient* és a *CORSWebService* könyvtárakat is tartalmazza:

![Szülőkönyvtár közzététele](./media/application-proxy-understand-cors-issues/image5.png)

Az eredményül kapott alkalmazás URL-címei hatékonyan oldják meg a CORS-problémát:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>3. lehetőség: HTTP-fejlécek frissítése

Adjon hozzá egy egyéni HTTP-válaszfejlécet a webszolgáltatáshoz, hogy megfeleljen az eredetkérelemnek. Az Internet Information Services (IIS) szolgáltatásban futó webhelyek esetében az IIS-kezelő segítségével módosítsa a fejlécet:

![Egyéni válaszfejléc hozzáadása az IIS-kezelőben](./media/application-proxy-understand-cors-issues/image6.png)

Ez a módosítás nem igényel kódmódosítást. Ellenőrizheti a Hegedűs nyomaiban:

**Fej hozzáadása**\
HTTP/1.1 200 OK\
Gyorsítótár-vezérlés: nincs gyorsítótár\
Pragma: nincs gyorsítótár\
Tartalom-típus: szöveg/sima; charset=utf-8\
Lejár: -1\
Módosítás: Elfogadás-kódolás\
Kiszolgáló: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Hozzáférés-vezérlés-allow-origin:\:https //corswebclient-contoso.msappproxy.net**\
X-AspNet-verzió: 4.0.30319\
X-Powered-By: ASP.NET\
Tartalom hossza: 17

### <a name="option-4-modify-the-app"></a>4. lehetőség: Az alkalmazás módosítása

Módosíthatja az alkalmazást, hogy támogassa a CORS-t az Access-Control-Allow-Origin fejléc megfelelő értékekkel való hozzáadásával. A fejléc hozzáadásának módja az alkalmazás kódnyelvétől függ. A kód módosítása a legkevésbé ajánlott lehetőség, mert a legtöbb erőfeszítést igényli.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>5. lehetőség: A hozzáférési jogkivonat élettartamának meghosszabbítása

Egyes CORS-problémák nem oldhatók meg, például amikor az alkalmazás átirányítja *login.microsoftonline.com* hitelesítéshez, és a hozzáférési jogkivonat lejár. A CORS-hívás ezután sikertelen lesz. Ebben a forgatókönyvben a megoldás a hozzáférési jogkivonat élettartamának meghosszabbítása, hogy megakadályozza, hogy a felhasználó munkamenete során lejárjon. Ennek módjáról a [Konfigurálható jogkivonat-élettartamok az Azure AD-ben](../develop/active-directory-configurable-token-lifetimes.md)című témakörben talál további információt.

## <a name="see-also"></a>Lásd még
- [Oktatóanyag: Az Azure Active Directory alkalmazásproxyn keresztüli távoli eléréshez helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md) 
- [Azure AD alkalmazásproxy-telepítés megtervezése](application-proxy-deployment-plan.md) 
- [Távoli hozzáférés helyszíni alkalmazásokhoz az Azure Active Directory alkalmazásproxyn keresztül](application-proxy.md) 
