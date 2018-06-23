---
title: Beszélőfelismerési API-nevéből felismert |} Microsoft Docs
description: Speciális algoritmusok használata hangalapú ellenőrzési és hangalapú azonosítási kognitív szolgáltatásokban felismerés Beszélőfelismerési API-val.
services: cognitive-services
author: dwlin
manager: zhang
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: article
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 6d5e4e4bbe0cb5e57d2556f680ffcf8d16ee1818
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347395"
---
# <a name="speaker-recognition-api"></a>Beszélőfelismerés API

Üdvözli a Microsoft hangalapú felismerés API-k. Hangalapú felismerés API-k, amelyek a legtöbb speciális algoritmusok hangalapú ellenőrzési és hangalapú azonosítási API felhőalapú. Beszélőfelismerési felismerés két kategóriába oszthatók: hangalapú ellenőrzési és hangalapú azonosítási.


## <a name="speaker-verification"></a>Hangalapú azonosítás


Hang jellemzői egyedi személy, mint például egy ujjlenyomat azonosítására használható.  Hangalámondás használatával, mint a hozzáférés-vezérléshez és a hitelesítési forgatókönyvek esetén jel mutatkozott új innovatív eszközként – tulajdonképpen ajánlat, amely egyszerűbbé teszi az ügyfelek felhasználói hitelesítés biztonsági egy szint.

Hangalapú azonosítási API-k automatikusan győződjön meg arról, és hitelesíti a felhasználókat a hang- vagy beszéd használatával.

### <a name="enrollment"></a>Regisztráció

Beszélőfelismerési ellenőrzés igénylés ennek szöveg függő, ami azt jelenti, hangszórók kell választania a regisztráció és a ellenőrzési fázis során használandó különleges hozzáférési kódot. 

Beléptetését a beszélőre hang rögzítése közli, hogy egy adott kifejezés, akkor számos szolgáltatást ki kell olvasni, és a rendszer felismeri a kiválasztott kifejezés. Együtt kibontott funkciókat, mind a kiválasztott kifejezés űrlap egyedi hang aláírás.

### <a name="verification"></a>Ellenőrzés
###
Ellenőrzés, egy bemeneti hang- és kifejezés összehasonlítja a beléptetési hang aláírás és kódot – ugyanazon személy vannak-e, és ha azok a helyes kódot véleményét ellenőrzése érdekében.

A hangalapú ellenőrzési kapcsolatos további tudnivalókért tekintse meg az API-t [hangalapú - ellenőrzési](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Beszélőfelismerés

Hangalapú azonosítási API-k automatikusan azonosíthatja a személy, és beszéljen hangfájl, a megadott potenciális hangszórók csoportja. A bemeneti hang párosítva van a megadott csoport hangszóró ellen, és abban az esetben, amely található egyezés, a beszélőre identitás ad vissza.

Minden hangszórók kell egy regisztrációs folyamat használatának alapjaival a hang, a rendszer regisztrálva, és létrehozott egy hang nyomtatása rendelkezik.


### <a name="enrollment"></a>Regisztráció

Hangalapú azonosítási regisztrálásának szöveg független, ami azt jelenti, hogy nincsenek-e meg, hogy mi a beszélőre a hallható szerint korlátozások. A beszélőre hang rögzítése, és számos szolgáltatást és egyedi hang aláírás ki kell olvasni. 


### <a name="recognition"></a>Elismerés

Az ismeretlen hangalapú beszélők többsége potenciális csoportja és a hang felismerés során megadott. A bemeneti hang össze lesz hasonlítva annak meghatározására, amelynek hang összes hangszórók szemben, és ha egyezés található, a beszélőre identitásának adja vissza.


Hangalapú azonosítási kapcsolatos további részletekért tekintse meg az API-t [hangalapú - azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
