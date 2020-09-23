---
title: Díjszabás a telefonáláshoz (hang/videó) és csevegéshez
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások díjszabási modelljét.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 94a34937af92eefa6146c7a043b6d5d68cf6b852
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947115"
---
# <a name="pricing-scenarios"></a>Díjszabási forgatókönyvek

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Az Azure kommunikációs szolgáltatások díjai az utólagos elszámolású modelleken alapulnak, előzetes költségek nélkül. Csak a szolgáltatások felhasználására és használatára számítunk fel díjat.

## <a name="voicevideo-calling-and-screen-sharing"></a>Hang/videó hívása és a képernyő megosztása

Az Azure kommunikációs szolgáltatások lehetővé teszik hang-és videohívások hozzáadását az alkalmazásokhoz. A felhasználói élményt a JavaScript, Objective-C (Apple), Java (Android) vagy .NET ügyféloldali kódtárak használatával ágyazhatja be alkalmazásaiba. Tekintse meg az [elérhető ügyféloldali kódtárak teljes listáját](./sdk-options.md).

### <a name="pricing"></a>Díjszabás

A hívási és a képernyő-megosztási szolgáltatásokat felhasználónként percenként $0,004-kor számoljuk el a csoportos hívások esetében. A különböző hívások lehetséges folyamatainak megismeréséhez tekintse meg [ezt a lapot](./call-flows.md).

A hívás minden résztvevője a meghíváshoz kapcsolódó percenkénti számlázással fog számolni. Ez attól függetlenül igaz, hogy a felhasználó videohívás, hanghívás vagy képernyő-megosztás.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Díjszabási példa: hang/videó hívásának csoportosítása JS-és iOS-ügyféloldali kódtárak használatával

Alice csoportos hívást készített a kollégáival, Bob és Charlie-val. Alice és Bob használta a JS ügyféloldali kódtárakat, Charlie iOS ügyféloldali kódtárakat.

- A hívás összesen 60 percig tart.
- Alice és Bob részt vett a teljes hívásban. Alice öt percen belül bekapcsolta a videóját, és 23 percen keresztül megosztotta a képernyőjét. Bob megkapta a videót a teljes híváshoz (60 perc), és 12 percig megosztva a képernyőt.
- Charlie 43 perc elteltével elhagyta a hívást. Charlie használta a hang-és video-használati időt (43 perc).

**Költségszámítások**

- 2 résztvevő x 60 perc x $0,004/résztvevő/perc = $0,48 [a videó és az audió díja azonos a díjszabásban]
- 1 résztvevő x 43 perc x $0,004/résztvevő/perc = $0,172 [a videó és az audió díja azonos a díjszabásban]

**A csoportos hívás teljes költsége**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Csevegés

A kommunikációs szolgáltatások segítségével javíthatja az alkalmazását, hogy 2 vagy több felhasználótól érkező csevegési üzeneteket küldjön és fogadjon. A csevegési ügyféloldali kódtárak a JavaScript, a .NET, a Python és a Java esetében érhetők el. Tekintse meg [ezt a lapot az ügyféloldali kódtárak megismeréséhez](./sdk-options.md)

### <a name="price"></a>Ár

- Minden elküldött Csevegésnél $0,0008 díjat számítunk fel.

### <a name="pricing-example-chat-between-two-users"></a>Díjszabási példa: csevegés két felhasználó között 

A-vel egy olyan csevegési szálat indít el, amely az Emily használatával megoszt egy frissítést, és 5 üzenetet küld. A csevegés 10 percet vesz igénybe, hogy a és az Emily egy másik 15 üzenetet küldjön.

**Költségszámítások** 
- Küldött üzenetek száma (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Díjszabási példa: csoportos csevegés több felhasználóval 

Charlie elindít egy csevegési szálat barátaival Casey & Jasmine-vel, hogy megtervezze a nyaralást. Beszélgetnek egy darabig, amelyben Charlie, Casey & jázmin 20, 30 és 18 üzenetet küld. Tisztában vagyunk azzal, hogy a barát Rose-nak érdekes lehet az utazáshoz való csatlakozás is, így hozzá kell adnia a csevegési szálhoz, és meg kell osztania az összes korábbi üzenetet. 

Rose látja az üzeneteket, és elindítja a csevegést. Az időpontban egy hívást kezdeményeznek, és később úgy dönt, hogy felveszi a beszélgetést. Charlie, jázmin & Rose dönt az utazási dátumokról, és egy újabb 30, 25, 35 üzenetet küld.

**Költségszámítások** 

- Küldött üzenetek száma (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264
