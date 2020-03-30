---
title: Az OAuth2 implicit támogatási folyamat ának ismertetése az Azure AD-ben | Microsoft dokumentumok
description: További információ az Azure Active Directory által az OAuth2 implicit támogatási folyamat megvalósításáról, valamint arról, hogy megfelelő-e az alkalmazásszámára.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154508"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Az OAuth2 implicit támogatási folyamat ának ismertetése az Azure Active Directoryban (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az OAuth2 implicit támogatás hírhedt arról, hogy a támogatás a leghosszabb listát a biztonsági aggályok az OAuth2 specifikáció. És mégis, ez a megközelítés által végrehajtott ADAL JS és az egyik javasoljuk, ha írásban SPA alkalmazások. Mi a dörgés? Ez mind a kompromisszumokat: és mint kiderült, az implicit támogatás a legjobb megközelítés, amit folytatni alkalmazások fogyasztanak a Webes API JavaScript-en keresztül a böngésző.

## <a name="what-is-the-oauth2-implicit-grant"></a>Mi az OAuth2 implicit támogatás?

A lényegi [OAuth2 engedélyezési kód megadása](https://tools.ietf.org/html/rfc6749#section-1.3.1) az engedélyezési támogatás, amely két különböző végpontot használ. Az engedélyezési végpont a felhasználói beavatkozási fázishoz használatos, ami engedélyezési kódot eredményez. A jogkivonat-végpontot ezután az ügyfél használja a hozzáférési jogkivonat és gyakran egy frissítési jogkivonat kódjának cseréjéhez. A webalkalmazásoknak saját alkalmazáshitelesítő adatokat kell bemutatniuk a jogkivonat-végpontnak, hogy az engedélyezési kiszolgáló hitelesíthesse az ügyfelet.

Az [OAuth2 implicit támogatás](https://tools.ietf.org/html/rfc6749#section-1.3.2) az egyéb engedélyezési támogatások egy változata. Lehetővé teszi, hogy az ügyfél közvetlenül az engedélyezési végpontról szerezzen be egy hozzáférési jogkivonatot (és id_token az [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)használatakor), anélkül, hogy kapcsolatba lépne a token-végponttal, és nem hitelesítené az ügyfelet. Ezt a változatot webböngészőben futó JavaScript-alapú alkalmazásokhoz tervezték: az eredeti OAuth2 specifikációban a tokenek EGY URI-töredékben jelennek meg. Ez teszi a token bitek elérhetővé a JavaScript-kódot az ügyfél, de ez garantálja, hogy nem szerepelnek az átirányítások felé a kiszolgálóra. Az OAuth2 implicit támogatásban az engedélyezési végpont hozzáférési jogkivonatokat ad ki közvetlenül az ügyfélnek egy korábban megadott átirányítási URI használatával. Azt is megvan az az előnye, hogy megszünteti a követelmények több eredetű hívások, amelyek szükségesek, ha a JavaScript-alkalmazás szükséges a kapcsolatot a jogkivonat végpont.

Az OAuth2 implicit támogatás fontos jellemzője az a tény, hogy az ilyen folyamatok soha nem adnak vissza frissítési jogkivonatokat az ügyfélnek. A következő szakasz azt mutatja be, hogy ez nem szükséges, és valójában biztonsági probléma lenne.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Megfelelő forgatókönyvek az OAuth2 implicit támogatáshoz

Az OAuth2 specifikáció kijelenti, hogy az implicit támogatást úgy alakították ki, hogy lehetővé tegye a felhasználói ügynök alkalmazásokat – azaz a böngészőn belül végrehajtó JavaScript-alkalmazásokat. Az ilyen alkalmazások meghatározó jellemzője, hogy a JavaScript-kódot a kiszolgáló erőforrásainak (általában a webes API-k) elérésére és az alkalmazás felhasználói élményének ennek megfelelő frissítésére használják. Gondoljon olyan alkalmazásokra, mint a Gmail vagy az Outlook Web Access: amikor kiválaszt egy üzenetet a beérkezett üzenetek mappából, csak az üzenetvizualizációpanel változik az új kijelölés megjelenítéséhez, míg a lap többi része változatlan marad. Ez a jellemző ellentétben áll a hagyományos átirányítási alapú webalkalmazásokkal, ahol minden felhasználói beavatkozás egy teljes oldal visszaadást és az új kiszolgálóválasz teljes oldalas megjelenítését eredményezi.

Alkalmazások, amelyek a JavaScript-alapú megközelítést a szélsőséges nevezzük egyoldalas alkalmazások, vagy SPA-k. Az ötlet az, hogy ezek az alkalmazások csak egy kezdeti HTML-oldalt és a kapcsolódó JavaScriptet szolgálnak ki, és az összes további interakciót a JavaScript-en keresztül végrehajtott webes API-hívások vezérlik. Azonban a hibrid megközelítések, ahol az alkalmazás többnyire postback-vezérelt, de alkalmi JS-hívásokat hajt végre, nem ritkák – az implicit folyamat használatának megvitatása azok számára is fontos.

Az átirányítási alapú alkalmazások általában cookie-k segítségével biztosítják kéréseiket, azonban ez a megközelítés nem működik olyan jól a JavaScript-alkalmazások esetében. A cookie-k csak azzal a tartománnyal szemben működnek, amelyhez létrehozták őket, míg a JavaScript-hívások más tartományokba irányulhatnak. Valójában ez gyakran előfordul: gondoljon a Microsoft Graph API-t, az Office API-t, az Azure API-t hivatkozó alkalmazásokra – amelyek mindegyike az on kívül található, ahonnan az alkalmazást kiszolgálják. A JavaScript-alkalmazások növekvő tendenciája, hogy egyáltalán nincs háttérprogramjuk, és 100%-ban harmadik féltől származó webes API-kra támaszkodva valósítják meg üzleti funkciójukat.

Jelenleg a webes API-k hívásainak védelmének előnyben részesített módja az OAuth2-tulajdonosi jogkivonat-megközelítés használata, ahol minden hívást egy OAuth2 hozzáférési jogkivonat kísér. A webes API megvizsgálja a bejövő hozzáférési jogkivonatot, és ha megtalálja a szükséges hatóköröket, hozzáférést biztosít a kért művelethez. Az implicit folyamat kényelmes mechanizmust biztosít a JavaScript-alkalmazások számára a webes API-k hozzáférési tokenjeinek megszerzéséhez, számos előnyt kínálva a cookie-k tekintetében:

* A tokenek megbízhatóan beszerezhetők a kereszteredlési hívások szükségessége nélkül – az átirányítási URI kötelező regisztrálása, amelyre a tokenek visszatérési garanciák arra, hogy a tokenek nem kerülnek kiitérésre
* A JavaScript-alkalmazások annyi hozzáférési jogkivonatot szerezhetnek be, amennyire szükségük van, annyi webes API-hoz, amennyit megcéloznak – a tartományok korlátozása nélkül
* A HTML5-funkciók, például a munkamenet vagy a helyi tárolás teljes körű felügyeletet biztosítanak a tokengyorsítótárazás és az élettartam-kezelés felett, míg a cookie-k kezelése nem átlátszatlan az alkalmazás számára
* A hozzáférési jogkivonatok nem érzékenyek a helyek közötti kérelemhamisítási (CSRF) támadásokra

Az implicit támogatási folyamat nem ad ki frissítési jogkivonatokat, főként biztonsági okokból. A frissítési jogkivonat nem olyan szűken, mint a hozzáférési jogkivonatok hatóköre, így sokkal több energiát biztosít, így sokkal több kárt okoz, ha kiszivárogna. Az implicit folyamat jogkivonatok kézbesítése az URL-cím, ezért a lehallgatás kockázata nagyobb, mint az engedélyezési kód támogatás.

Azonban egy JavaScript-alkalmazás rendelkezik egy másik mechanizmus áll a rendelkezésére a hozzáférési jogkivonatok megújítása anélkül, hogy ismételten kéri a felhasználó hitelesítő adatokat. Az alkalmazás egy rejtett iframe használatával új jogkivonat-kérelmeket hajthat végre az Azure AD engedélyezési végpontja ellen: mindaddig, amíg a böngésző még rendelkezik aktív munkamenettel (olvassa el: munkamenet-cookie-val rendelkezik) az Azure AD-tartományban, a hitelesítési kérelem felhasználói beavatkozás nélkül.

Ez a modell lehetővé teszi a JavaScript-alkalmazás számára, hogy önállóan megújítsa a hozzáférési jogkivonatokat, és még újakat is szerezzen be egy új API-hoz (feltéve, hogy a felhasználó korábban hozzájárult hozzájuk). Ezzel elkerülhető a nagy értékű műtermék, például egy frissítési jogkivonat beszerzésének, karbantartásának és védelmének további terhe. A rendszermászolat, amely lehetővé teszi a csendes megújítás, az Azure AD munkamenet cookie-t, az alkalmazáson kívül kezeli. Egy másik előnye ennek a megközelítésnek, hogy a felhasználó kijelentkezhet az Azure AD-ből az Azure AD-be bejelentkezett alkalmazások bármelyikével, amelyek a böngésző lapok bármelyikén futnak. Ennek eredménye az Azure AD munkamenet-cookie törlése, és a JavaScript-alkalmazás automatikusan elveszíti a kijelentkezett felhasználó jogkivonatainak megújításának lehetőségét.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Az implicit támogatás megfelel az alkalmazásomnak?

Az implicit támogatás több kockázatot jelent, mint más támogatások, és a területek, amelyeket figyelembe kell vennie, jól dokumentáltak (például a [hozzáférési jogkivonattal való visszaélés az erőforrás tulajdonosának megszemélyesítésére az Implicit folyamatban][OAuth2-Spec-Implicit-Misuse] és [az OAuth 2.0 fenyegetési modell és biztonsági megfontolások).][OAuth2-Threat-Model-And-Security-Implications] A magasabb kockázati profil azonban nagyrészt annak a ténynek köszönhető, hogy az aktív kódot végrehajtó alkalmazásokat egy távoli erőforrás szolgálja ki a böngészőbe. Ha spa architektúrát tervez, nincs háttér-összetevője, vagy javascripten keresztül webes API-t kíván meghívni, ajánlott az implicit folyamat használata a tokenek beszerzéséhez.

Ha az alkalmazás egy natív ügyfél, az implicit folyamat nem egy nagy roham. Az Azure AD-munkamenet cookie-k hiánya egy natív ügyfél környezetében megfosztja az alkalmazást a hosszú élettartamú munkamenet fenntartásának eszközeitől. Ami azt jelenti, hogy az alkalmazás ismételten kéri a felhasználót az új erőforrások hozzáférési jogkivonatának beszerzésekénte.

Ha olyan webalkalmazást fejleszt, amely háttérrendszert tartalmaz, és egy API-t használ fel a háttérkódból, az implicit folyamat sem megfelelő. Más támogatások sokkal nagyobb hatalmat adnak. Például az OAuth2 ügyfél hitelesítő adatok megadása lehetővé teszi, hogy a jogkivonatok, amelyek tükrözik az alkalmazáshoz rendelt engedélyeket, szemben a felhasználói delegálások. Ez azt jelenti, hogy az ügyfél képes az erőforrásokhoz való programozott hozzáférés fenntartására akkor is, ha a felhasználó nem vesz részt aktívan egy munkamenetben, és így tovább. Nem csak, hogy, de az ilyen támogatások nagyobb biztonsági garanciákat. Például a hozzáférési jogkivonatok soha nem haladnak át a felhasználói böngészőn keresztül, nem kockáztatják a mentést a böngésző előzményeiközött, és így tovább. Az ügyfélalkalmazás is végrehajthat erős hitelesítést, amikor jogkivonatot kér.

## <a name="next-steps"></a>További lépések

* Olvassa [el: Hogyan integrálhat egy alkalmazást az Azure AD-vel][ACOM-How-To-Integrate] az alkalmazásintegrációs folyamat további mélységet.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
