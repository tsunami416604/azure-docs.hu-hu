---
title: Hogyan tanítsa meg Beszélgetéstanuló – a Microsoft Cognitive Services-|} A Microsoft Docs
titleSuffix: Azure
description: Útmutató a Beszélgetéstanuló tanít.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220263"
---
# <a name="how-to-teach-with-conversation-learner"></a>Tanítás a Beszélgetéstanulóval 

Ez a dokumentum ismerteti, milyen Beszélgetéstanuló érzékeli, és ismerteti, hogyan képes megtanulni jelzi.  

Pedagógiai bontható két különálló lépésre: entitások kinyeréséhez és a művelet kiválasztása.

## <a name="entity-extraction"></a>Entitások kinyeréséhez

Valójában Beszélgetéstanuló használ [LUIS](https://www.luis.ai) az entitások kinyeréséhez.  Ha ismeri a LUIS, hogy a entitások kinyeréséhez a Beszélgetéstanuló vonatkozik.

Az entitás kinyerési modellek tisztában a *tartalom* és *környezet* egy felhasználó utterance (kifejezés) belül.  Például, ha a "Seattle" szót lett feliratú egy utterance (kifejezés) a város, például a "Mi az az időjárás, a Seattle", entitások kinyeréséhez a következőkre képes FELISMERVE tartalmat ("Seattle") egy másik utterance "Population Seattle", például a város, akkor is, ha a beszédmódok amelyek nagyon eltérőek.  Ezzel szemben ha "Francis" ismeri el a nevét a "Ütemezés egy értekezlet Francis", hasonló környezetben, például a "Beállítása egy értekezlet-Robin" felismeri az új korábban látott nevet, majd.  Machine learning vegyen részt a tartalmat, helyi vagy mindkettőt, hogy mikor kikövetkezteti a példák alapján.

Jelenleg entitások kinyeréséhez csak tisztában a jelenlegi utterance (kifejezés) tartalmát.  Ellentétben a művelet kiválasztása (alább) azt még nem ismeri, például a korábbi rendszer bekapcsolja, ennek a felhasználó korábbi vagy korábban felismert entitások párbeszédpanel előzmények.  Ennek eredményeképpen az entitások kinyeréséhez viselkedését "" között megoszlik az összes kimondott szöveg.  Például ha a felhasználó utterance (kifejezés) "Szeretnék Apple" a "Apple" entitástípus "Gyümölcs" az egy felhasználó utterance (kifejezés) címkével rendelkezik, az entitás kinyerési modell fog a várt, hogy ("szeretnék Apple") az utterance (kifejezés) mindig rendelkeznie kell a következő címkét "Gyümölcs": "Apple".

Ha az entitások kinyeréséhez nem a várt módon viselkedik, az alábbiakban lehetséges megoldása:

- Próbálja ki a legfontosabb, hogy adjon hozzá további példák – különösen a tipikus entitás környezet (körülvevő szavak), vagy a kivételek felfedése példák
- Fontolja meg, egy "Várt entitás" tulajdonság ad hozzá egy műveletet, ha szükséges.  További részletekért tekintse meg a oktatóanyag várt entitásokra.
- Bár a manuális feldolgozás hozzáadandó `EntityExtractionCallback` kóddal entitások kinyeréséhez, ennek oka a legkevésbé ajánlott módszer nem élvezi az a machine learning, a rendszer kiforrottá fejlesztései.

## <a name="action-selection"></a>Művelet kiválasztása

Művelet kiválasztása használ egy ütemezhetők Neurális hálózat, amely veszi fel a bemeneti összes a beszélgetések előzményeit.  Így a művelet kiválasztása az állapot-nyilvántartó, amely a korábbi felhasználói utterances entitásértékek és rendszer utterances érzékeli.  

Bizonyos jelek természetes módon előnyben részesített a tanulási folyamatot.  Más szóval ha Beszélgetéstanuló ismertetik egy művelet használatával több "elsődleges" jelek döntés, akkor; Ha nem érhető el, majd fogja használni kevesebb "elsődleges" jeleket.

Itt látható az összes jel Beszélgetéstanuló, és melyeket művelet kiválasztása által használt megjelenítő táblázat.  Vegye figyelembe, hogy a rendelés word-felhasználó utterances a rendszer figyelmen kívül hagyja.

Jel | Szabályozó (1 = leginkább előnyben részesített) | Megjegyzések
--- | --- | --- 
Előző kapcsolja be a rendszer művelet | 1 | 
Entitások aktuális kapcsolja szerepelnek. | 1 | 
Hogy ez-e az első bekapcsolása | 1 |
Az aktuális felhasználó utterance (kifejezés) szó pontos egyezés | 2 | 
Hasonló – azaz a szavakat az aktuális felhasználó utterance (kifejezés) | 3 | 
Előző bekapcsolása előtt a rendszer műveletek | 4 |
Ennek a jelenlegi bekapcsolása előtt található entitások | 4 | 
Aktuális bekapcsolása előtt a felhasználó kimondott szöveg | 5 | 

> [!NOTE]
> Művelet kiválasztása nem veszi a tartalom rendszer műveletek – a szöveget, kártya tartalma, vagy API-név vagy viselkedés – csak a rendszer művelet identitását.  Ennek eredményeképpen a művelet tartalmának módosítása nem változtatja meg a művelet kiválasztása modell viselkedését.
>
> Továbbá, hogy a tartalom és az entitások értékei nem használt – csak jelenléte vagy hiánya.

Ha a művelet kiválasztása nem a várt módon viselkedik, az alábbiakban lehetséges megoldása:

- Adjon hozzá további train párbeszédpanelek, különösen olyan párbeszédpanelek, amelyek bemutatják, milyen művelet kiválasztása kell lennie és fordítson különös figyelmet jelek.  Például ha művelet kiválasztása érdemes inkább egy jel egy másikkal szemben, példákkal szemlélteti azt mutatják be az előnyben részesített jel ugyanazt az állapotot folyamatban van, és az egyéb jelekkel változó.  Egyes sorozatok néhány további betanítási párbeszédpanelek is igénybe vehet.
- Adja hozzá a "Kötelező" és "Kizárásának" művelet definíciók entitásokat.  A korlátok műveletek érhetők el, és expressz üzleti szabályok és a egy hétköznapi minták hasznos lehet. 

## <a name="updates-to-models"></a>A modellek frissítések

Bármikor hozzáadásakor vagy szerkesztésekor egy entitást, művelet vagy train párbeszédpanel a felhasználói felületen Ez létrehoz egy kérelmet újra is az entitás kinyerési modell betanítására a művelet kiválasztása modell.  Ezt a kérést egy üzenetsorba el van helyezve, és újbóli betanítása aszinkron módon történik.  Új modell érhető el, ha használatos ettől a ponttól kezdődően entitás kivonása és a művelet kiválasztása.  Ez a folyamat újbóli képzésekre gyakran körülbelül 5 másodperc alatt, de tovább is lehet, ha a modell összetett, vagy ha a betanítási szolgáltatás terhelése nagy.

Képzési aszinkron módon történik, mivel, lehetséges, hogy végrehajtott módosítások azonnal nem érvényesülnek.  Entitás kivonása vagy a művelet kiválasztása az elmúlt 5 – 10 másodpercben módosításainak alapján várt módon viselkedik, ha az OK lehet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alapértelmezett értékeket, és határokat](./cl-values-and-boundaries.md)
