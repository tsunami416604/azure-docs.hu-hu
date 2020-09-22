---
title: Az ügyféloldali függvénytárat hívó Azure kommunikációs szolgáltatások – áttekintés
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt a hívó ügyféloldali függvénytáráról.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9cc00cfa9f44c69a5880d53c0b7ac623f60be16b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947221"
---
# <a name="calling-client-library-overview"></a>Az ügyféloldali kódtár meghívása – áttekintés

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az *ügyfelek* és szolgáltatások esetében két különálló család hívható meg az ügyféloldali kódtárak meghívásával *.* A jelenleg elérhető ügyféloldali kódtárak a végfelhasználói élményekhez készültek: webhelyek és natív alkalmazások.

A szolgáltatás ügyféloldali kódtárai még nem érhetők el, és hozzáférést biztosítanak a robotokkal és más szolgáltatásokkal való integrációhoz alkalmas nyers hang-és video-adatsíkokhoz.

## <a name="calling-client-library-capabilities"></a>Ügyféloldali kódtár képességeinek meghívása

Az alábbi lista azokat a funkciókat mutatja be, amelyek jelenleg elérhetők az Azure kommunikációs szolgáltatásokban az ügyféloldali kódtárak meghívásakor.

| Szolgáltatások csoportja | Képesség                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Alapvető képességek | Egy-az-egyhez hívás elhelyezése két felhasználó között                                                                           | ✔️   | ✔️            | ✔️  
|                   | Csoportos hívás elhelyezése kettőnél több felhasználóval (legfeljebb 350 felhasználó)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Egy-az-egyhez hívás előléptetése két felhasználóval egy csoportos hívásban több mint két felhasználóval                                 | ✔️   | ✔️            | ✔️ 
|                   | Csatlakozás egy csoportos híváshoz az elindítása után                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Egy másik VoIP-résztvevő meghívása egy folyamatos csoportos híváshoz                                                       | ✔️   | ✔️            | ✔️ 
|                   | Mikrofon némítása/feloldása                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Váltás a kamerák között                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Helyi tartás/letartás                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Aktív beszélő                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Válasszon hangszórót a hívásokhoz                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Válassza ki a mikrofont a hívásokhoz                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Résztvevő állapotának megjelenítése<br/>*Tétlen, korai adathordozó, csatlakozás, csatlakoztatott, betartva, a lobbyban, leválasztva*         | ✔️   | ✔️            | ✔️           
|                   | Hívás állapotának megjelenítése<br/>*Korai adathordozó, bejövő, csatlakozás, csengetés, csatlakoztatott, megtartás, leválasztás, leválasztva* | ✔️   | ✔️            | ✔️           
|                   | Annak megjelenítése, hogy a résztvevő el van-e némítva                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Annak az okának a megjelenítése, amiért a résztvevő meghagyott egy hívást                                                                       | ✔️   | ✔️            | ✔️     
| Képernyő megosztása    | A teljes képernyő megosztása az alkalmazáson belülről                                                                 | ✔️   | ❌            | ❌           
|                   | Egy adott alkalmazás megosztása (a futó alkalmazások listájából)                                                | ✔️   | ❌            | ❌           
|                   | Webböngészőt tartalmazó lap megosztása a megnyitott lapok listájából                                                                  | ✔️   | ❌            | ❌           
|                   | A résztvevő megtekintheti a távoli képernyő megosztását                                                                            | ✔️   | ✔️            | ✔️         
| Névsora            | Résztvevők listázása                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Résztvevő eltávolítása                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Egy-az-egyhez hívás elhelyezése PSTN-résztvevővel                                                                     | ✔️   | ✔️            | ✔️   
|                   | Csoportos hívás elhelyezése PSTN-résztvevőkkel                                                                           | ✔️   | ✔️            | ✔️
|                   | Egy-az-egyhez hívás előléptetése PSTN-résztvevővel egy csoportos hívásban                                                 | ✔️   | ✔️            | ✔️
|                   | Tárcsázás egy csoportos hívásból PSTN-résztvevőként                                                                    | ✔️   | ✔️            | ✔️   
| Általános kérdések           | Részvétel egy legfeljebb 350 résztvevőt tartalmazó csoportos hívásban                                                       |  ✔️  | ✔️            | ✔️    
|                   | A mikrofon, a hangszóró és a kamera tesztelése hangtesztelési szolgáltatással (elérhető a 8. hívással: echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="calling-client-library-browser-support"></a>Az ügyféloldali kódtár böngésző támogatásának meghívása

A következő táblázat a jelenleg elérhető támogatott böngészők és verziók készletét tartalmazza.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    |
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ |
| **Ügyféloldali kódtár hívása** | Chrome *, új Edge | Chrome *, Safari** | Chrome  | Chrome | Chrome | Safari * * |


* Vegye figyelembe, hogy a Chrome legújabb verziója az előző két kiadáson felül támogatott.<br/>

* * Vegye figyelembe, hogy a Safari 13.1 + verziója támogatott. A Safari macOS-hez készült kimenő videó még nem támogatott, de iOS rendszeren támogatott. A kimenő képernyő megosztása csak asztali iOS rendszeren támogatott.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hívással](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

További információkért tekintse át a következő cikkeket:
- Ismerkedjen meg az általános [hívási folyamatokkal](../call-flows.md)
- Tudnivalók a [hívási típusokról](../voice-video-calling/about-call-types.md)
- [A PSTN-megoldás megtervezése](../telephony-sms/plan-solution.md)
