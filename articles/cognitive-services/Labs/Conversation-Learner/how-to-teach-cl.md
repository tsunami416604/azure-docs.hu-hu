---
title: Hogyan mutatja meg a beszélgetési tanuló - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Útmutató a beszélgetés tanuló mutatja meg.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 639fea64fc8eeb2c1f6e6240c4eb26efc68febbd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348598"
---
# <a name="how-to-teach-with-conversation-learner"></a>Hogyan mutatja meg a beszélgetési tanuló 

Ez a dokumentum elmagyarázza, mit beszélgetés tanuló ismeri a, és ismerteti, hogyan megtanulja jelzi.  

Oktatási bontható két különálló lépésből áll: entitás kinyerési, és a művelet kiválasztása.

## <a name="entity-extraction"></a>Entitás kivonása

A színfalak beszélgetés tanuló használ [LUIS](https://www.luis.ai) entitás kiolvasásához.  Ha ismeri a LUIS, hogy a beszélgetés tanuló az entitás kibontási vonatkozik.

Az entitás kibontási modellek ismerjék a *tartalom* és *környezetben* egy felhasználó utterance belül.  Ha a "Seattle" szó van lett címkézve egy utterance a város, mint például "Mi az a budapesti időjárási", a entitás kibontási képes ismer fel, hogy ugyanazt a tartalmat ("Seattle") "Seattle feltöltési", például egy másik utterance a város, akkor is, ha a utterances nagyon eltérőek.  Ezzel szemben ha "Francis" ismerik egy nevet a "Ütemterv értekezlet Francis", majd egy új korábban nem látható nevet felismeri hasonló környezetben, például a "Set értekezlet multiplexelés a".  Gépi tanulás arra következtet, amikor tartalmat, a környezetben, vagy mindkét, változáskérések példák alapján.

Jelenleg entitás kibontási csak tehát tudatában a jelenlegi utterance tartalmát.  Eltérően művelet kiválasztása (lent) nincs párbeszédpanel előzmények például korábbi rendszer bekapcsolja, ennek a korábbi felhasználói vagy korábban felismert entitások tudomást.  Ennek eredményeképpen entitás kibontási viselkedését által "megosztott" minden utterances.  Például ha a felhasználó utterance "Szeretném Apple" "Apple", "Gyümölcsöt" entitástípus egy felhasználó utterance címkézve, kibontási entitásmodell fog várt, hogy a utterance ("szeretném Apple") mindig meg kell adni "Apple", "Gyümölcsöt" címkével.

Entitás kibontási nem a várt módon viselkedik, ha az alábbiakban lehetséges megoldása:

- A legfontosabb próbálja az, hogy további példák – különösen a tipikus entitás környezet (körülvevő szavak), vagy a kivételek felfedése példák hozzáadása
- Fontolja meg egy "várt" entitástulajdonságra művelet, ha szükséges.  Az oktatóanyag jelenik meg entitások várt további tájékoztatást talál.
- Bár ez lehet manuális feldolgozását a hozzáadni `EntityExtractionCallback` kóddal entitások kinyerési, ennek oka a legalább ajánlott módszer nem előnyösek kapcsolatos fejlesztések a machine learning, a rendszer Miután kiforrottá válik.

## <a name="action-selection"></a>Művelet kiválasztása

Művelet kiválasztása egy ismétlődő Neurális hálózat, amely a beszélgetés előzményei szerint bemeneti összes veszi használja.  Művelet kiválasztása így ismerik az előző felhasználó utterances, az entitás értékek és a rendszer utterances állapot-nyilvántartó folyamatban.  

Néhány jelek természetesen a tanulást által előnyben részesített.  Ez azt jelenti Ha egy művelettel több "elsődleges" jelek használata döntés beszélgetés tanuló azt ismerteti, a következőket hajtja végre; Ha nem, akkor kisebb "elsődleges" jelek fogja használni.

Ez a beszélgetés tanuló, és melyeket művelet kiválasztása által használt összes jelek megjelenítő táblázat.  Vegye figyelembe, hogy a felhasználó utterances rendelés word a rendszer figyelmen kívül hagyja.

Jel | Preferencia (1 = a leginkább előnyben részesített) | Megjegyzések
--- | --- | --- 
Az előző kapcsolja a rendszer művelet | 1 | 
Entitások aktuális kapcsolja szerepelnek. | 1 | 
Hogy ez-e az első bekapcsolása | 1 |
Az aktuális felhasználó utterance szó pontos egyezés | 2 | 
Az aktuális felhasználó utterance hasonló – azaz a szavakat | 3 | 
Kapcsolja be a rendszer műveletek előző előtt | 4 |
Entitások szerepelnek viszont aktuális bekapcsolása előtt | 4 | 
Felhasználói utterances aktuális bekapcsolása előtt | 5 | 

Fontos tudni, hogy művelet kiválasztása rendszer műveletek – a szöveg, kártya tartalmát, vagy API-név vagy viselkedés – a rendszer a művelet csak a identitásának tartalmát.  Ennek eredményeképpen módosítja a tartalmat egy művelet nem módosítja a kijelölési művelet modell viselkedését.

További vegye figyelembe, hogy a tartalom és-értékek entitások nem használt--csak jelenléte vagy hiánya.

Ha a művelet kiválasztása nem a várt módon viselkedik, itt is lehetséges megoldása:

- Adjon hozzá további vonat párbeszédpanelek, különösen a bemutatják, mely művelet kiválasztása kell fizető figyelmet kell fordítani jelek párbeszédpanelek.  Például ha a művelet kiválasztása kell jel egy másikkal, példákat mutatnak be, amely az előnyben részesített jel éppen olyan állapotban van, és az egyéb jelek különböző megjelenítése.  Néhány sorozatok is igénybe vehet néhány olyan további képzési párbeszédpanelek.
- Vegye fel a "Kötelező" és "Kizárásának" művelet definíciók entitásokat.  Ezen korlátok műveletek érhetők el, és az expressz üzleti szabályok és az egyes józan mintázatok számára hasznos lehet. 

## <a name="updates-to-models"></a>Modellek frissítései

Hozzáadásakor vagy szerkesztésekor egy entitás, a művelet, vagy a vonat párbeszédpanel a felhasználói felületen, bármikor újból a kibontási entitásmodell, mind a kijelölési művelet modell betanítása kérelmet generál.  A kérelem egy várólista helyezkedik el, és újra betanítása aszinkron módon történik.  Új modell nem érhető el, használatos ettől kezdve entitás kivonása és művelet kiválasztása.  A újra képzési folyamat gyakran körülbelül 5 másodpercet vesz igénybe, de ha a modell összetett, vagy ha a képzés szolgáltatás magas lehet hosszabb.

Mivel képzési aszinkron módon történik, akkor lehet, hogy a nem azonnal tükröződjön végrehajtott módosításokat.  Ha az entitás kivonása vagy a művelet kiválasztása az utolsó 5-10 másodperc alatt végrehajtott módosításokat a alapján várt módon viselkedik, ez lehet az oka.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alapértelmezett értékeket, és határokat](./cl-values-and-boundaries.md)
