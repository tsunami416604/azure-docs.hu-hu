---
title: Engedélyezési ügynökök és azok engedélyezése | Azure
description: Tudnivalók a különböző engedélyezési ügynökökről a Microsoft Authentication Library (MSAL) lehetővé teszi az Android-alkalmazás használatát és azok engedélyezését.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085329"
---
# <a name="authorization-agents-android"></a>Engedélyezési ügynökök (Android)

Ez a cikk azokat a különböző engedélyezési ügynököket ismerteti, amelyekkel a Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazás használatát és azok engedélyezését.

Az engedélyezési ügynökökhöz tartozó konkrét stratégia kiválasztása nem kötelező, és a további funkcionalitási alkalmazásokat is testreszabhatja. A legtöbb alkalmazás a MSAL alapértelmezett beállításait fogja használni (lásd: az [androidos MSAL konfigurációs fájljának ismertetése](msal-configuration.md) a különböző alapértelmezett beállítások megjelenítéséhez).

A MSAL `WebView`vagy a rendszerböngésző használatával támogatja az engedélyezést.  Az alábbi képen látható, hogyan néz ki a `WebView`vagy a rendszerböngésző a CustomTabs vagy CustomTabs nélkül:

![MSAL bejelentkezési példák](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Egyszeri bejelentkezés következményei

Alapértelmezés szerint a MSAL-ben integrált alkalmazások a rendszerböngésző egyéni lapjait használják az engedélyezéshez. A webnézetekkel ellentétben az egyéni lapok megosztanak egy cookie jar-t az alapértelmezett rendszerböngészővel, így kevesebb bejelentkezést tesznek lehetővé az egyéni lapokkal integrált webes vagy más natív alkalmazásokkal.

Ha az alkalmazás olyan `WebView` stratégiát használ, amely nem integrálja Microsoft Authenticator vagy Céges portál támogatást az alkalmazásba, a felhasználók nem fognak egyszeri bejelentkezéses (SSO) élményt biztosítani az eszközön, illetve a natív alkalmazások és a webalkalmazások között.

Ha az alkalmazás a MSAL-t Microsoft Authenticator vagy Céges portál támogatással használja, akkor a felhasználók az alkalmazásokban egyszeri bejelentkezéses felhasználói felülettel rendelkezhetnek, ha a felhasználó aktív bejelentkezést végez az egyik alkalmazással.

## <a name="webview"></a>WebView

A alkalmazáson belüli webnézet használatához helyezze a következő sort az App Configuration JSON-ba, amelyet a MSAL továbbít:

```json
"authorization_user_agent" : "WEBVIEW"
```

Az alkalmazáson belüli `WebView`használatakor a felhasználó közvetlenül az alkalmazásba jelentkezik be. A tokenek az alkalmazás Homokozójában maradnak, és nem érhetők el az alkalmazás cookie jar-on kívül. Ennek eredményeképpen a felhasználónak nem lehet egyszeri bejelentkezéses felhasználói felülete, kivéve, ha az alkalmazások integrálva vannak a hitelesítő vagy a Céges portál.

`WebView` azonban lehetővé teszi a bejelentkezési felhasználói felület megjelenésének és működésének testreszabását. A testreszabással kapcsolatos további tudnivalókért tekintse meg az [androidos Webnézeteket](https://developer.android.com/reference/android/webkit/WebView) .

## <a name="default-browser-plus-custom-tabs"></a>Alapértelmezett böngésző és egyéni lapok

Alapértelmezés szerint a MSAL a böngészőt és az [Egyéni lapfülek](https://developer.chrome.com/multidevice/android/customtabs) stratégiát használja. Explicit módon jelezheti ezt a stratégiát, hogy megakadályozza a jövőbeli kiadásokban való `DEFAULT` módosítását az egyéni konfigurációs fájl következő JSON-konfigurációjának használatával:

```json
"authorization_user_agent" : "BROWSER"
```

Ezzel a megközelítéssel egyszeri bejelentkezéses felhasználói élményt biztosíthat az eszköz böngészőjében. A MSAL egy megosztott cookie jar-t használ, amely lehetővé teszi más natív alkalmazások vagy webalkalmazások számára az egyszeri bejelentkezést az eszközön a MSAL által beállított állandó munkamenet-cookie használatával.

## <a name="browser-selection-heuristic"></a>Böngésző kiválasztása – heurisztikus

Mivel a MSAL nem tudja megállapítani az androidos telefonok széles skáláján használni kívánt böngésző-csomagot, a MSAL egy olyan böngésző-kiválasztási heurisztikus szolgáltatást valósít meg, amely a legjobb eszközök közötti egyszeri bejelentkezést próbálja megadni.

A MSAL lekéri az eszközön telepített böngészők teljes listáját, hogy kiválassza a használni kívánt böngészőt. A lista a Package Manager által visszaadott sorrendben történik, amely közvetett módon a felhasználó beállításait mutatja. Például az alapértelmezett böngésző, ha be van állítva, a lista első bejegyzése. A lista _első_ böngészője attól függetlenül lesz kiválasztva, hogy támogatja-e az egyéni lapokat. Ha a böngésző támogatja az egyéni lapokat, a MSAL elindítják az egyéni fület. az egyéni lapok egy alkalmazásbeli `WebView`nek, és lehetővé teszik az alapvető felhasználói felület testreszabását. További információért lásd: [Egyéni lapok az Androidban](https://developer.chrome.com/multidevice/android/customtabs) .

Ha nincsenek böngésző-csomagok az eszközön, a MSAL az alkalmazáson belüli `WebView`használja.

A böngésző listájában szereplő böngészők sorrendjét az operációs rendszer határozza meg. A legkevesebb előnyben részesített a legkevésbé. Ha az eszköz alapértelmezett beállítása nem módosul, a bejelentkezéshez ugyanazt a böngészőt kell elindítani az egyszeri bejelentkezéses felhasználói élmény biztosítása érdekében.

> [!NOTE]
> A MSAL már nem mindig a Chrome-ot részesíti előnyben, ha egy másik böngésző alapértelmezettként van beállítva. Például egy olyan eszközön, amelyen a Chrome és egy másik böngésző előre telepítve van, a MSAL a felhasználó által alapértelmezettként beállított böngészőt fogja használni.

### <a name="tested-browsers"></a>Tesztelt böngészők

A következő böngészők tesztelték, hogy megfelelően átirányítva vannak-e a konfigurációs fájlban megadott `"redirect_uri"`ra:

| | Beépített böngésző | Chrome | Opera  | Microsoft Edge | UC böngésző | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| 4\. Nexus (API 17) | Pass | Pass |nem alkalmazható |nem alkalmazható |nem alkalmazható |nem alkalmazható |
| Samsung S7 (API 25) | pass | Pass | Pass | Pass | Sikertelen |Pass |
| Huawei (API 26) |pass * * | Pass | Sikertelen | Pass | Pass |Pass |
| Vivo (API 26) |Pass|Pass|Pass|Pass|Pass|Sikertelen|
| 2\. képpont (API 26) |Pass | Pass | Pass | Pass | Sikertelen |Pass |
| Ellenfél | Pass | nem alkalmazható * * * |nem alkalmazható  |nem alkalmazható |nem alkalmazható | nem alkalmazható|
| OnePlus (25. API) |Pass | Pass | Pass | Pass | Sikertelen |Pass |
| Nexus (API 28) |Pass | Pass | Pass | Pass | Sikertelen |Pass |
|MI | Pass | Pass | Pass | Pass | Sikertelen |Pass |

\* A Samsung beépített böngészője a Samsung Internet.  
\* * A Huawei beépített böngészője a Huawei böngésző.  
Az alapértelmezett böngésző nem módosítható a ellenfél-eszköz beállításán belül.
