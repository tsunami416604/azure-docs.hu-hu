---
title: "Folyamat ismertetése OAuth2 implicit adja meg az Azure AD |} Microsoft Docs"
description: "Ismerje meg, további információk az Azure Active Directory végrehajtásának OAuth2 implicit biztosítani folyamata, és hogy-e megfelelő az alkalmazás."
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7b3ed6edb0b770e8b57bb5bfde4c183d435335ce
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Az OAuth2 implicit grant flow az Azure Active Directory (AD) ismertetése
Az OAuth2 implicit grant ahhoz, hogy a támogatás a leghosszabb listáját az OAuth2-specifikációt a biztonsági aggályokon meg notorious. És még, amely az ADAL JS és az ajánlott SPA alkalmazások írásához által megvalósított módszerrel. Mi ad? Mellékhatásokkal összes kérdése:, és akkor kapcsolja, mert a implicit grant böngészőből egy webes API a JavaScript keresztül használó alkalmazások is végezze el a legjobb módszer.

## <a name="what-is-the-oauth2-implicit-grant"></a>Mi az az OAuth2 implicit támogatás?
A quintessential [OAuth2 engedélyezési code grant](https://tools.ietf.org/html/rfc6749#section-1.3.1) a két külön végpontok használó hitelesítésengedélyezési van. A felhasználói beavatkozás fázis, így ez az engedélyezési kód engedélyezési végpont használható. A jogkivonat végpontjához majd használja az ügyfél olyan hozzáférési jogkivonatot, és gyakran egy frissítési, valamint a kód cseréjét. Webalkalmazások szükségesek a saját alkalmazás hitelesítő adatokat a jogkivonat végpontjához van, úgy, hogy a hitelesítési kiszolgáló képes hitelesíteni az ügyfelet.

A [OAuth2 implicit grant](https://tools.ietf.org/html/rfc6749#section-1.3.2) egy változata, más engedélyt biztosít. Lehetővé teszi egy ügyfelet, hogy olyan hozzáférési jogkivonatot (és id_token, használatakor [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) közvetlenül a az engedélyezési végpont, lépjen kapcsolatba a jogkivonat végpontjához, sem az ügyfél hitelesítése nélkül. A variant kifejezetten készült JavaScript-alapú böngészőben futtatott alkalmazások: az eredeti OAuth2 specifikációjában tokenek vissza az URI-töredéket. Amely a token bits elérhetővé teszi a JavaScript-kódot az ügyfél, de azt biztosítja, hogy azok a kiszolgáló felé átirányítások nem fog szerepelni. Jogkivonatok visszaadó keresztül böngésző átirányítja az engedélyezési végpont-ről. Az előnyt, így kiküszöböli közötti eredetű hívások indítását, szükséges, ha a JavaScript-alkalmazását szükséges lépjen kapcsolatba a jogkivonat végpontjához vonatkozó követelmények is rendelkezik.

Az OAuth2 implicit grant fontos jellemzője a tényt, hogy az ilyen zajlik az ügyfél soha nem visszatérési frissítési jogkivonatokat. Mivel jelenleg a következő szakaszban láthatja, amely nem valóban szükség, és ténylegesen lenne a biztonsági problémákból.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Az OAuth2 implicit grant megfelelő forgatókönyvei
Deklarálja az OAuth2-specifikációt magát, mint a implicit grant dolgozott lett felhasználói ügynök alkalmazások – azaz, a böngésző végrehajtott JavaScript alkalmazások engedélyezéséhez. A definiáló ilyen alkalmazások jellemzője, hogy (általában egy webes API) kiszolgáló-erőforrások eléréséhez és az alkalmazás UX ennek megfelelően frissítéséhez használja-e a JavaScript-kódot. Az alkalmazások, például a Gmailes vagy Outlook Web Access gondoljon: a beérkezett üzenet kiválasztásakor csak az üzenet a képi megjelenítés panel vált, jelenítse meg az új beállítást, a lap a további változatlan marad. Ez a ellentétben hagyományos átirányításon alapuló webalkalmazások, ahol a teljes lap visszaküldés és az új kiszolgáló válasza a teljes lap megjelenítésének eredményez-e a minden felhasználói beavatkozást.

Alkalmazások, amelyek a JavaScript-alapú módszer annak extreme egyetlen oldal alkalmazásokat, vagy gyógyfürdők nevezzük: a lényege, hogy ezeket az alkalmazásokat csak egy kezdeti HTML-lapok és a kapcsolódó JavaScript szolgálnak, JavaScripttel végre kritikus előfeltételei webes API-hívásokban: az összes későbbi kapcsolati. Azonban hibrid megoldások, ha az alkalmazás többnyire visszaküldés adatvezérelt, de alkalmi JS-hívást hajt végre, amelyek nem ritka, – a vitafórum implicit engedélyezési folyamat használatával kapcsolatos fontos azok is.

Átirányításon alapuló alkalmazások általában biztonságos megközelítés JavaScript-alkalmazások esetében nem működik, valamint a kérelmek cookie-kat, azonban keresztül. Cookie-kat csak a tartományhoz, azok hozták létre, amíg JavaScript-hívásokat előfordulhat, hogy rendszer felé más tartományok dolgozhat. Valójában gyakran fogja tárolni az eset: Microsoft Graph API-t Office API-t Azure API – kívül a tartományhoz, ahol az alkalmazás kiszolgált az összes várt meghívja alkalmazások gondoljon. JavaScript-alkalmazásokhoz az egyre növekvő tendenciát, hogy nincs háttér, függő 100 %-a 3. fél webes API-k, az üzleti függvény végrehajtásához.

A preferált hívásokat egy webes API védelme jelenleg használatára az OAuth2-tulajdonosi jogkivonat módszer, ahol minden hívás együtt egy OAuth2-jogkivonatot. A Web API megvizsgálja a bejövő jogkivonatot, és ha talál, a szükséges hatókörök, azt engedélyezi a hozzáférést a kért művelet. Az implicit engedélyezési folyamat lehetővé teszi a kényelmes hozzáférési tokenek beszerzése érdekében egy webes API-alkalmazások JavaScript kínál a cookie-k tekintetben számos előnye:

* Jogkivonatok megbízhatóan kapott nélkül közötti eredetű hívások indítását – az átirányítási URI azonosítója, amelyhez jogkivonatok visszatérési garantálja, hogy a rendszer nem kiszorított jogkivonatok kötelező regisztrációja
* JavaScript alkalmazások szerezhet be van szükségük, a lehető legtöbb webes API-k célként – tartományok korlátozás nélkül annyi hozzáférési jogkivonatok
* HTML5-szolgáltatások, például munkamenet vagy egy helyi tárterület teljes hozzáférés token-gyorsítótárazási és életciklusának kezelését, mivel nem átlátszó, az alkalmazás a cookie-k kezelése
* Hozzáférési jogkivonatok nem ki vannak téve a többhelyes kérelem (CSRF) hamisítási támadások

Az implicit grant flow frissítési jogkivonatokat, főleg biztonsági okokból nem ad ki. Egy frissítési jogkivonat szűken, mint a hozzáférési jogkivonatok, ezért anyagi sokkal több kárt, abban az esetben, ha kiszivárgott sokkal nagyobb teljesítmény biztosítása nem hatókörét. Az implicit folyamatában jogkivonatok érkeznek, az URL-cím, ezért az hozzáférés veszélye magasabb, mint az engedélyezési kód megadását.

Vegye figyelembe azonban, hogy rendelkezik-e a JavaScript-alkalmazását egy másik mechanizmus a rendelkezésére a hozzáférési jogkivonatok megújítása ismételten a hitelesítő adatokat a felhasználó értesítése nélkül. Az alkalmazás egy rejtett iframe segítségével végezhet el az új jogkivonat-kérelmeket az Azure AD a hitelesítési végpont elleni: mindaddig, amíg a böngésző még aktív munkamenet (olvassa el: egy munkamenetcookie tartozik) az Azure AD-tartomány, szemben a hitelesítési kérelem sikeresen fordulhat elő, felhasználói beavatkozás nélkül.

Ez a modell a JavaScript-alkalmazását számára lehetőséget ad a egymástól függetlenül újítsa meg a jogkivonatot, és még szerezzen be egy új API-újakat (feltéve, hogy a felhasználó korábban átadni kívánt hozzájárult e számukra. Ezzel elkerülhető az beszerzése, karbantartása és védelme a nagy értékű összetevő, például egy frissítési jogkivonat hozzáadott okozta terheket. Az összetevő a beavatkozás nélküli megújítási lehetséges, így az Azure AD munkamenetcookie-t, az alkalmazáson kívül felügyelt. Ezt a módszert használja egy másik előnye, egy felhasználó bejelentkezhessen az Azure AD használatával az alkalmazások az Azure AD, sem a böngészőlapokon fut be van jelentkezve. Ennek eredményeképp az Azure AD-munkamenet cookie-k törlését, és a JavaScript-alkalmazását újítsa meg az aláírt kimenő felhasználó jogkivonatok képes automatikusan elvesznek.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Az implicit grant megfelelő-e az alkalmazásom?
Az implicit grant megadja további kockázatok, mint más biztosít, és a figyelmet fordítani kell területek is szerepelnek. For example [visszaélés a hozzáférési jogkivonat erőforrás tulajdonosa megszemélyesíteni az Implicit Flow] [ OAuth2-Spec-Implicit-Misuse] és [OAuth 2.0 fenyegetések modellezése és biztonsági megfontolások][OAuth2-Threat-Model-And-Security-Implications]). A magasabb kockázatú profil azonban nagy mértékben oka az oka, hogy azt jelenti, hogy engedélyezze az alkalmazásokat, amelyek aktív kódot, a távoli erőforrás böngészőbe szolgálja ki. Ha azt tervezi, az SPA architektúrát nem háttér-összetevők vagy a JavaScripttel egy webes API meghívására, a jogkivonat beszerzése az implicit engedélyezési folyamat használata ajánlott.

Ha az alkalmazás egy natív ügyfél, a implicit engedélyezési folyamat nem kiváló méretezése. Az Azure AD munkamenetcookie-t natív ügyfelek környezetében hiányában megfosztja hosszú élettartamú munkamenet fenntartásának az azt jelenti, hogy az alkalmazás. Ami azt jelenti, hogy az alkalmazást ismételten megkérdezi a felhasználót az új erőforrások hozzáférési jogkivonatok beszerzésekor.

Ha egy webalkalmazást, beleértve a háttérkiszolgálón és felhasználása az API-k, a háttér kódból fejleszt, a implicit engedélyezési folyamat is nem remekül beválik. Más biztosít Önnek sokkal nagyobb teljesítmény. Például az OAuth2 ügyfél hitelesítő adatok megadását teszi lehetővé szerezze be a jogkivonatokat, amelyek a saját magát, az alkalmazás felhasználói delegálásokat figyelésekor rendelt engedélyek tükrözik. Ez azt jelenti, hogy az ügyfél képes a fenntartásához erőforrások programozott hozzáférést még akkor is, ha a felhasználó nem aktívan folytat egy munkamenetet, és így tovább. Nem csak az, hogy, de az ilyen adjon magasabb biztonsági garanciákat. Például hozzáférési jogkivonatok soha nem tranzit keresztül a felhasználó böngészőben, nem kockáztatja a böngészési előzményeket menti a program, és így tovább. Az ügyfélalkalmazás is elvégezheti erős hitelesítés, amikor a jogkivonat kérésével.

## <a name="next-steps"></a>Következő lépések
* Fejlesztői erőforrások teljes listáját, beleértve a protokollok és OAuth2 engedélyezési adatfolyamok támogatás az Azure ad nyújtására referencia jellegű információt tekintse meg a [Azure AD fejlesztői útmutató][AAD-Developers-Guide]
* Lásd: [alkalmazás integrálása az Azure AD] [ ACOM-How-To-Integrate] további mélység alkalmazás integrációs folyamat.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
