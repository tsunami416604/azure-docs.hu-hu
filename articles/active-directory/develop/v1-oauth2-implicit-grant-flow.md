---
title: OAuth2 implicit ismertetése adja meg a folyamat az Azure ad-ben |} A Microsoft Docs
description: Ismerje meg, további információt az Azure Active Directory megvalósítása az OAuth2 implicit adja meg a folyamat, és hogy-e az alkalmazáshoz szükséges.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 90c636d57189518cb95291510f3e83ef8e7a8a75
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422031"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Az Azure Active Directory (AD) OAuth2 típusú implicit engedélyezés folyamat ismertetése

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Az OAuth2 típusú implicit engedélyezés notorious a támogatás a leghosszabb listáját az OAuth2 specifikációt biztonsági aggályokat is. És még, ez a megközelítés ADAL JS és a egy, azt javasoljuk, hogy amikor az SPA-alkalmazások írására által megvalósított. Mit biztosít? Az összes gyorsan, és kompromisszumot kínál a: és ahogy azt tapasztaltuk, az implicit engedélyezés-e a legjobb módszer szerezni a használó alkalmazások programozása egy webes API a JavaScript használatával egy böngészőből is.

## <a name="what-is-the-oauth2-implicit-grant"></a>Mi az az OAuth2 típusú implicit engedélyezés?

A quintessential [OAuth2 engedélyezési kód](https://tools.ietf.org/html/rfc6749#section-1.3.1) van két külön végpontot használó az engedélyezést. Az engedélyezési végpont az engedélyezési kódot eredményez felhasználói interakció fázis használható. A jogkivonat-végpont majd használja az ügyfél által cserélt a kódot egy hozzáférési jogkivonatot, és gyakran, valamint a frissítési jogkivonatot. Webes alkalmazások szükségesek a saját alkalmazás hitelesítő adatokat a jogkivonat végpontra, hogy az engedélyezési kiszolgáló hitelesíthető az ügyfél.

A [OAuth2 típusú implicit engedélyezés](https://tools.ietf.org/html/rfc6749#section-1.3.2) egy változata, más engedélyezések. Ez lehetővé teszi, hogy egy ügyfél-hozzáférési jogkivonat beszerzése (és id_token, használatakor [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) közvetlenül a az engedélyezési végponton, és vegye fel a kapcsolatot a jogkivonat-végpont, nem az ügyfél hitelesítése nélkül. Ez a változó úgy lett kialakítva, JavaScript-alapú egy webes böngésző a futó alkalmazások: az eredeti OAuth2 specifikációt a jogkivonatok tért fragment identifikátoru URI. Amely elérhetővé teszi a token bits a JavaScript-kódot az ügyfél, de biztosítja azokat a kiszolgáló felé átirányítások nem fog szerepelni. Közvetlenül az engedélyezési végponton a jogkivonatok visszaadó böngészőből irányítja át. Azt az előnyt, így kiküszöböli a forrás-hívás, amely van szükség, ha a JavaScript-alkalmazását a jogkivonat-végpont kapcsolatba kell közötti kapcsolatos követelmények is rendelkezik.

Az OAuth2 típusú implicit engedélyezés fontos jellemzője az a tény, hogy olyan folyamatok, az ügyfél soha nem visszatérési frissítési jogkivonatokat. Ez a szakasz bemutatja, hogy ez nem szükséges, és valójában lenne a biztonsági.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Az OAuth2 típusú implicit engedélyezés megfelelő forgatókönyvei

Az OAuth2 specifikációt deklarálja, hogy az az implicit engedélyezés engedélyezése a felhasználói ügynök alkalmazások – azaz, egy böngészőből végrehajtása JavaScript-alkalmazások kidolgozni-e. A meghatározó az ilyen alkalmazások jellemzője, hogy a kiszolgáló erőforrások (általában egy webes API) eléréséhez, és ennek megfelelően frissíti az alkalmazás felhasználói élmény a JavaScript-kódot használatos. Képzelje úgy, mint például a Gmail vagy Outlook Web Access alkalmazások: Amikor kiválaszt egy üzenetet a postaládából, az üzenet képi megjelenítések panelen átvált jelenítse meg az új beállítást az oldal többi változatlan marad. Ez a jellemző van ellentétben a hagyományos átirányításon alapuló webalkalmazások, ahol a teljes lap postback és a egy teljes oldalmegjelenítések az új kiszolgáló válasz eredményez-e a minden felhasználói interakció.

Alkalmazások, amelyek a JavaScript-alapú módszer annak extreme egyoldalas alkalmazás, illetve gyógyfürdők nevezzük. A cél pedig az, hogy ezeket az alkalmazásokat csak szolgálja ki egy kezdeti HTML-oldalt, és a kapcsolódó JavaScript, a kritikus előfeltételei: webes API-hívások keresztül történik. a JavaScript összes ezt követő műveletet. Azonban a hibrid megközelítés, ahol az alkalmazás csak a postback adatvezérelt, de alkalmanként JS-hívást hajt végre, amelyek nem ritka, – implicit folyamat használatáról a hozzászólás megfelelő, azok számára is.

Átirányítási-alapú alkalmazások általában biztonságos a módszer nem működik, valamint a JavaScript-alkalmazások kérelmek cookie-kat, azonban keresztül. A cookie-k csak a tartományhoz, akkor jött létre, bár előfordulhat, hogy legyen átirányítva a JavaScript-hívásokat, más tartományok felé esetében működik. Tulajdonképpen, amelyek gyakran az eset áll fenn: gondoljon alkalmazások erőforráshasználatát Microsoft Graph API, API-t az Office, az Azure API – az összes hozzá tartozó, ahol az alkalmazás biztosítja a tartományon kívüli. JavaScript-alkalmazások növekedési trendje, hogy háttérrendszer nélküli minden függő 100 %-a külső webes API-kkal saját üzleti funkció megvalósítását.

Az előnyben részesített módja egy webes API-hívások védelmét jelenleg használatához az OAuth2-tulajdonosi jogkivonat módszer, ha minden hívás OAuth2 hozzáférési jogkivonat csatolni. A webes API megvizsgálja a bejövő jogkivonatot, és ha talál, a szükséges hatókörökhöz, azt hozzáférést biztosít a kért művelet. Az implicit folyamatot a tekintetben a cookie-k számos olyan előnyöket kínál a JavaScript-alkalmazások hozzáférési tokenek beszerzése a webes API-hoz, kényelmes módot biztosít:

* Jogkivonatok nem kell a forrás-hívásai – közötti az átirányítási URI-t, amelyhez a jogkivonatok olyan visszatérési garantálja, hogy a rendszer nem kiírja jogkivonatok kötelező regisztrációs megbízhatóan szerezhető
* JavaScript-alkalmazások szerezheti be szükségük van, a legtöbb webes API-k céloznak – tartományok korlátozás nélkül annyi hozzáférési jogkivonatok
* HTML5-alapú szolgáltatások például munkamenet vagy a helyi tároló token-gyorsítótárazási és életciklusának kezelését, teljes körű vezérlést biztosítanak, mivel a cookie-k kezelése az alkalmazás nem átlátszó
* Hozzáférési jogkivonatok nem ki van téve a többhelyes-kérések hamisítása (CSRF)

Az implicit engedélyezési folyamat frissítési biztonsági jogkivonat, főleg biztonsági okokból nem ad ki. A frissítési jogkivonatok szűken, mint a hozzáférési tokenek megadása ezért anyagi sokkal több kárt, abban az esetben azt kiszivárgott jóval több power nem hatókörét. Az implicit folyamatot a jogkivonatok kézbesítése az URL-cím, ezért az hozzáférés veszélye magasabb, mint az engedélyezési kód.

Azonban egy JavaScript-alkalmazását egy másik mechanizmussal rendelkezik a rendelkezésére a hozzáférési jogkivonatok megújítása ismételten a hitelesítő adatokat a felhasználó értesítése nélkül. Az alkalmazás egy rejtett IFRAME-keret segítségével végezhet el az új jogkivonat-kérelmeket az engedélyezési végponton, az Azure AD: mindaddig, amíg a böngésző még aktív munkamenet (olvasása: rendelkezik egy munkamenetcookie-t) ellen az Azure AD-tartományhoz, a hitelesítési kérelmet is sikeresen nem kell a felhasználói beavatkozás nélkül történik.

Ezt a modellt biztosít a JavaScript-alkalmazás lehetővé teszi az egymástól függetlenül újítsa meg a jogkivonatot, és még beszerezni az újakat új API-hoz (feltéve, hogy a felhasználó korábban által jóváhagyott, számukra. Ezzel elkerülhető a hozzáadott nehezedő terheket, beszerzése, fenntartása és a egy nagy értékű összetevő, például a frissítési jogkivonatok védelme. Összetevő, amely lehetővé teszi a beavatkozás nélküli megújításához, az Azure ad-ben munkamenetcookie-t, az alkalmazás-en kívül kezeli. Ez a megközelítés egy másik előnye, a felhasználó is jelentkezzen ki az Azure AD használata az alkalmazások Azure AD-ben futó bármelyik böngésző lapon be van jelentkezve. Ez az Azure ad-ben munkameneti cookie törlését eredményezi, és a JavaScript-alkalmazás automatikusan megszűnik újítsa meg az aláírt meg felhasználó tokenek lehetővé teszi.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Ideális választás az implicit engedélyezés az alkalmazásomhoz?

Az implicit engedélyezés mutat be, mint a többi biztosít további kockázatok, és a kell odafigyelni területeken is szerepelnek. Ha például [való visszaélés a hozzáférési tokent az erőforrás tulajdonosa megszemélyesíteni az Implicit folyamat] [ OAuth2-Spec-Implicit-Misuse] és [OAuth 2.0-s fenyegetések modellezése és biztonsági megfontolások] [ OAuth2-Threat-Model-And-Security-Implications]). Azonban a magasabb kockázatú profil, nagymértékben oka, hogy az azt jelenti, hogy lehetővé teszik az alkalmazások, amelyek aktív programkód kiszolgálása az internetszolgáltatójuk által távoli erőforráshoz böngészőbe. Ha tervezi az SPA-architektúra nem háttér-összetevők vagy szándékoznak JavaScripttel egy webes API meghívása, a token beszerzéséhez az implicit folyamat használata ajánlott.

Ha az alkalmazás egy natív ügyfél, az implicit folyamatot nem kiválóan alkalmas. Az Azure ad-ben munkamenetcookie-t egy natív ügyfél környezetében hiányában megfosztja, hosszú élettartamú munkamenetek fenntartását azt jelenti, hogy az alkalmazás. Ami azt jelenti, hogy az alkalmazás ismételten a felhasználóktól az új erőforrások hozzáférési tokenek beszerzése során.

Ha egy webalkalmazást, amely tartalmaz egy háttérbeli és a egy API-jával a háttéralkalmazás kódjához felhasználni fejleszt, az implicit folyamat akkor sem jó megoldás. Más biztosít a jóval több teljesítményt biztosítanak. Például az OAuth2 ügyfélhitelesítő adatok teszi lehetővé, amelyek tükrözik az engedélyek hozzárendelve a magát, az alkalmazás felhasználói delegálásokat figyelésekor tokenek beszerzése érdekében. Ez azt jelenti, hogy az ügyfél rendelkezik az erőforrások programozás alapú hozzáférést még akkor is, fenntartása, amikor a felhasználó nem aktívan folytat egy munkamenetet, és így tovább. Nem csak az, hogy, de az ilyen magasabb biztonsági garanciákat. Például hozzáférési jogkivonatok soha nem a felhasználó böngészőn keresztül átvitel, azok nem nem menti a böngészési előzményeket a kockázati, és így tovább. Az ügyfélalkalmazás is végrehajthat erős hitelesítési jogkivonat kérése során.

## <a name="next-steps"></a>További lépések

* Fejlesztői erőforrások teljes listáját a protokollok és OAuth2 engedélyezési folyamatok támogatása az Azure AD által biztosítása, beleértve a kapcsolódó információk tekintse meg a [Azure AD fejlesztői útmutató][AAD-Developers-Guide]
* Lásd: [alkalmazás integrálása az Azure AD] [ ACOM-How-To-Integrate] további tudni a az alkalmazás-integráció folyamatának.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
