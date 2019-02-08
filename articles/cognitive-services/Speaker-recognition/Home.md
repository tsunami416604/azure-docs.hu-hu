---
title: Mi az a Speaker Recognition API?
titleSuffix: Azure Cognitive Services
description: A Cognitive Services Speaker Recognition API-jával fejlett algoritmusokat használhat beszélő-ellenőrzéshez és beszélőazonosításhoz.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: dwlin
ms.openlocfilehash: 86162b4d4e752bc11c0d739f6e343c7a357ea0af
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873716"
---
# <a name="speaker-recognition-api"></a>Beszélőfelismerés API

Üdvözlik a Cognitive Services Speaker Recognition API-jai! A Speaker Recognition API-k olyan felhőalapú API-k, amelyek a legfejlettebb algoritmusokat biztosítják a beszélő-ellenőrzéshez és beszélőazonosításhoz. A Speaker Recognition két kategóriára oszlik: beszélő-ellenőrzés és beszélőazonosítás.


## <a name="speaker-verification"></a>Beszélő-ellenőrzés

A hangnak olyan egyedi jellemzői vannak, amelyek segítségével ugyanúgy azonosítható egy személy, mint egy ujjlenyomattal.  A hang jelként való használata a hozzáférés-vezérlési és a hitelesítési forgatókönyvekben új, innovatív eszközként jelent meg – lényegében a biztonság magasabb szintjét kínálja, amely egyszerűsíti a hitelesítési folyamatot az ügyfelek számára.

A beszélő-ellenőrzési API-k automatikusan ellenőrzik és hitelesítik a felhasználókat a hangjuk vagy beszédük alapján.

### <a name="enrollment"></a>Regisztráció

A beszélő-ellenőrzésre történő regisztráció szövegfüggő, ami azt jelenti, hogy a beszélőknek választaniuk kell egy jelszóként használt kifejezést, amelyet a regisztráció és az ellenőrzés fázisában használnak.

A regisztráció során a rendszer rögzíti a beszélő hangját, miközben kimondja a választott kifejezést, majd számos jellemzőt kinyer, és felismeri a választott kifejezést. A kinyert jellemzők és a választott kifejezés együttesen alkot egy egyedi hangaláírást.

### <a name="verification"></a>Ellenőrzés

Az ellenőrzés során a rendszer egy bemeneti hangot és kifejezést hasonlít össze a regisztrált hangaláírással és kifejezéssel, majd ellenőrzi, hogy ugyanattól a személytől származnak-e, és hogy a megfelelő kifejezés hangzott-e el.

Hangalapú azonosítás kapcsolatos további részletekért tekintse meg az API-t [beszélő - ellenőrzés](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Beszélőazonosítás

A beszélőazonosítási API-k automatikusan azonosítják a hangfájlban beszélő személyt, ha rendelkezésre áll a leendő beszélők egy csoportja. A rendszer a bemeneti hangot veti össze a megadott beszélők csoportjával, és egyezés esetén visszaadja a beszélő személyazonosságát.

Először az összes beszélőnek át kell esnie a regisztrációs folyamaton a hangjuknak a rendszerben történő regisztrálásához, és a hanglenyomat létrehozásához.


### <a name="enrollment"></a>Regisztráció

A beszélőazonosításba történő regisztráció szövegfüggetlen, ami azt jelenti, hogy nincsenek korlátozások arra vonatkozóan, mit mond a beszélő a hanganyagban. A rendszer rögzíti a beszélő hangját, és több jellemzőt kinyer egy egyedi hangaláírás létrehozása céljából.


### <a name="recognition"></a>Felismerés

Az ismeretlen beszélő hangjának biztosítása a leendő beszélők csoportjával együtt történik a felismerés során. A rendszer összehasonlítja a bemeneti hangot minden beszélőével, hogy meghatározza, kinek a hangjáról van szó, és ha van egyezés, visszaadja a beszélő személyazonosságát.

Beszélőfelismerés kapcsolatos további részletekért tekintse meg az API-t [beszélő - azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
