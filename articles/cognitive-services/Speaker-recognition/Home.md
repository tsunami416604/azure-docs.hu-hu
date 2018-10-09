---
title: Mi a Speaker Recognition?
titlesuffix: Azure Cognitive Services
description: A Speaker Recognition API-val fejlett algoritmusokat használhat beszélő-ellenőrzéshez és beszélőazonosításhoz.
services: cognitive-services
author: dwlin
manager: cgronlun
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: overview
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 13a95aff8b2b0d5dad0574e6107958a20576702a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227333"
---
# <a name="speaker-recognition-api"></a>Beszélőfelismerés API

Üdvözlik a Speaker Recognition API-k! A Speaker Recognition API-k olyan felhőalapú API-k, amelyek a legfejlettebb algoritmusokat biztosítják a beszélő-ellenőrzéshez és beszélőazonosításhoz. A Speaker Recognition két kategóriára oszlik: beszélő-ellenőrzés és beszélőazonosítás.


## <a name="speaker-verification"></a>Beszélő-ellenőrzés


A hangnak olyan egyedi jellemzői vannak, amelyek segítségével ugyanúgy azonosítható egy személy, mint egy ujjlenyomattal.  A hang jelként való használata a hozzáférés-vezérlési és a hitelesítési forgatókönyvekben új, innovatív eszközként jelent meg – lényegében a biztonság magasabb szintjét kínálja, amely egyszerűsíti a hitelesítési folyamatot az ügyfelek számára.

A beszélő-ellenőrzési API-k automatikusan ellenőrzik és hitelesítik a felhasználókat a hangjuk vagy beszédük alapján.

### <a name="enrollment"></a>Regisztráció

A beszélő-ellenőrzésre történő regisztráció szövegfüggő, ami azt jelenti, hogy a beszélőknek választaniuk kell egy jelszóként használt kifejezést, amelyet a regisztráció és az ellenőrzés fázisában használnak. 

A regisztráció során a rendszer rögzíti a beszélő hangját, miközben kimondja a választott kifejezést, majd számos jellemzőt kinyer, és felismeri a választott kifejezést. A kinyert jellemzők és a választott kifejezés együttesen alkot egy egyedi hangaláírást.

### <a name="verification"></a>Ellenőrzés
###
Az ellenőrzés során a rendszer egy bemeneti hangot és kifejezést hasonlít össze a regisztrált hangaláírással és kifejezéssel, majd ellenőrzi, hogy ugyanattól a személytől származnak-e, és hogy a megfelelő kifejezés hangzott-e el.

A beszélő-ellenőrzéssel kapcsolatos további információért tekintse meg a [beszélő-ellenőrzési](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) API dokumentációját.

## <a name="speaker-identification"></a>Beszélőazonosítás

A beszélőazonosítási API-k automatikusan azonosítják a hangfájlban beszélő személyt, ha rendelkezésre áll a leendő beszélők egy csoportja. A rendszer a bemeneti hangot veti össze a megadott beszélők csoportjával, és egyezés esetén visszaadja a beszélő személyazonosságát.

Először az összes beszélőnek át kell esnie a regisztrációs folyamaton a hangjuknak a rendszerben történő regisztrálásához, és a hanglenyomat létrehozásához.


### <a name="enrollment"></a>Regisztráció

A beszélőazonosításba történő regisztráció szövegfüggetlen, ami azt jelenti, hogy nincsenek korlátozások arra vonatkozóan, mit mond a beszélő a hanganyagban. A rendszer rögzíti a beszélő hangját, és több jellemzőt kinyer egy egyedi hangaláírás létrehozása céljából. 


### <a name="recognition"></a>Felismerés

Az ismeretlen beszélő hangjának biztosítása a leendő beszélők csoportjával együtt történik a felismerés során. A rendszer összehasonlítja a bemeneti hangot minden beszélőével, hogy meghatározza, kinek a hangjáról van szó, és ha van egyezés, visszaadja a beszélő személyazonosságát.


A beszélőazonosítással kapcsolatos további információért tekintse meg a [beszélőazonosítási](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) API dokumentációját.
