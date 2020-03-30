---
title: Engedélyező ügynökök és azok engedélyezése | Azure
description: Ismerje meg, hogy a Microsoft Authentication Library (MSAL) milyen különböző engedélyezési ügynököket engedélyez az Android-alkalmazás számára, és hogyan engedélyezheti azokat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085329"
---
# <a name="authorization-agents-android"></a>Engedélyezési ügynökök (Android)

Ez a cikk azokat a különböző engedélyezési ügynököket ismerteti, amelyeket a Microsoft hitelesítési tár (MSAL) engedélyez az alkalmazás számára, és amelynek engedélyezését.

Az engedélyezési ügynökök reprezentatiójának kiválasztása nem kötelező, és további funkciókat jelent, amelyeket az alkalmazások testre szabhatnak. A legtöbb alkalmazás az MSAL alapértelmezett beállításait fogja használni [(lásd: Az Android MSAL konfigurációs fájlának megértése](msal-configuration.md) a különböző alapértelmezett értékek megtekintéséhez).

Az MSAL támogatja `WebView`az engedélyezést a , vagy a rendszerböngészővel.  Az alábbi képen látható, `WebView`hogyan néz ki a , vagy a rendszer böngésző CustomTabs vagy anélkül CustomTabs:

![Példák a MSAL bejelentkezési példáira](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Egyszeri bejelentkezési vonatkozások

Alapértelmezés szerint az MSAL-ba integrált alkalmazások a rendszerböngésző egyéni lapjait használják az engedélyezéshez. A webnézetekkel ellentétben az egyéni lapok cookie-tégelyt osztanak meg az alapértelmezett rendszerböngészővel, amely kevesebb bejelentkezést engedélyez a webböngészővel vagy más natív alkalmazásokkal, amelyek integrálva vannak az egyéni lapokkal.

Ha az alkalmazás `WebView` stratégiát használ anélkül, hogy integrálná a Microsoft Authenticator vagy a Vállalati portál támogatását az alkalmazásba, a felhasználók nem rendelkeznek egyszeri bejelentkezési (SSO) felhasználói felülettel az eszközön vagy a natív alkalmazások és a webalkalmazások között.

Ha az alkalmazás az MSAL-t a Microsoft Authenticator vagy a Vállalati portál támogatásával használja, akkor a felhasználók egyszeri bejelentkezéssel élhetnek az alkalmazások között, ha a felhasználó aktív bejelentkezéssel rendelkezik az egyik alkalmazással.

## <a name="webview"></a>WebView nézet

Az alkalmazáson belüli WebView használatához tegye a következő sort az MSAL-nak átadott JSON alkalmazáskonfigurációban:

```json
"authorization_user_agent" : "WEBVIEW"
```

Az alkalmazáson `WebView`belüli használatakor a felhasználó közvetlenül bejelentkezik az alkalmazásba. A tokenek az alkalmazás sandboxán belül maradnak, és nem érhetők el az alkalmazás cookie-jar-ján kívül. Ennek eredményeképpen a felhasználó nem rendelkezhet sso-élménygel az alkalmazások között, kivéve, ha az alkalmazások integrálhatók a hitelesítővel vagy a vállalati portállal.

Azonban `WebView` nem biztosítja a lehetőséget, hogy testre szabhatja a megjelenését és hangulatát a bejelentkezési felhasználói felület. A testreszabás módjáról az [Android WebViews webhelyen](https://developer.android.com/reference/android/webkit/WebView) további tudnivalókat talál.

## <a name="default-browser-plus-custom-tabs"></a>Alapértelmezett böngésző és egyéni lapok

Alapértelmezés szerint az MSAL a böngészőt és az [egyéni lapok stratégiáját](https://developer.chrome.com/multidevice/android/customtabs) használja. Ezt a stratégiát explicit módon jelezheti a `DEFAULT` jövőbeli kiadások ban a következő JSON-konfiguráció használatával az egyéni konfigurációs fájlban:

```json
"authorization_user_agent" : "BROWSER"
```

Ezzel a módszerrel sso-élményt biztosít az eszköz böngészőjében. Az MSAL megosztott cookie-tsót használ, amely lehetővé teszi, hogy más natív alkalmazások vagy webalkalmazások sso-t érjenek el az eszközön az MSAL által beállított munkamenet-cookie-k használatával.

## <a name="browser-selection-heuristic"></a>Böngésző kiválasztása heurisztikus

Mivel lehetetlen, hogy az MSAL pontosan meghatározza az Android telefonok minden egyes széles skáláján használandó böngészőcsomagot, az MSAL egy böngészőkiválasztási heurisztikát valósít meg, amely megpróbálja a legjobb eszközközi SSO-t biztosítani.

Az MSAL lekéri az eszközre telepített böngészők teljes listáját, hogy kiválassza, melyik böngészőt használja. A lista a csomagkezelő által visszaküldött sorrendben van, amely közvetve tükrözi a felhasználó preferenciáit. Ha be van állítva, az alapértelmezett böngésző például a lista első bejegyzése. A lista _első_ böngészője attól függetlenül kerül kiválasztásra, hogy támogatja-e az egyéni lapokat. Ha a böngésző támogatja az egyéni lapokat, az MSAL elindítja az Egyéni lapot. `WebView` További információ: [Egyéni lapok az Android androidos alkalmazásában.](https://developer.chrome.com/multidevice/android/customtabs)

Ha nincsenek böngészőcsomagok az eszközön, az MSAL `WebView`az alkalmazáson belüli .

A böngészők sorrendjét a böngészőlistában az operációs rendszer határozza meg. Ez annak érdekében, hogy a legtöbb preferált a legkevésbé. Ha az eszköz alapértelmezett beállítása nem változik, ugyanazt a böngészőt kell indítani minden bejelentkezéshez az SSO-élmény biztosítása érdekében.

> [!NOTE]
> Az MSAL már nem mindig részesíti előnyben a Chrome-ot, ha egy másik böngésző van beállítva alapértelmezettként. Például egy olyan eszközön, amelyen a Chrome és egy másik böngésző is előre telepítve van, az MSAL a felhasználó által alapértelmezettként beállított böngészőt fogja használni.

### <a name="tested-browsers"></a>Tesztelt böngészők

A következő böngészők tesztelték, hogy azok megfelelően `"redirect_uri"` átirányítsák-e a konfigurációs fájlban megadott akövetkező böngészők:

| | Beépített böngésző | Chrome | Opera  | Microsoft Edge | UC böngésző | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Át | Át |nem alkalmazható |nem alkalmazható |nem alkalmazható |nem alkalmazható |
| Samsung S7 (API 25) | áthaladás* | Át | Át | Át | Nem |Át |
| Huawei (API 26) |át** | Át | Nem | Át | Át |Át |
| Vivo (API 26) |Át|Át|Át|Át|Át|Nem|
| Pixel 2 (API 26) |Át | Át | Át | Át | Nem |Át |
| Ellenfél | Át | nem alkalmazható*** |nem alkalmazható  |nem alkalmazható |nem alkalmazható | nem alkalmazható|
| OnePlus (API 25) |Át | Át | Át | Át | Nem |Át |
| Nexus (API 28) |Át | Át | Át | Át | Nem |Át |
|MI | Át | Át | Át | Át | Nem |Át |

*A Samsung beépített böngészője a Samsung Internet.  
**A Huawei beépített böngészője a Huawei böngésző.  
Az alapértelmezett böngésző nem módosítható az Oppo eszközbeállításán belül.
