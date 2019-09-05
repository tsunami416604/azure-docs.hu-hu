---
title: Mikor kell használni az ENUMERÁLÁSi entitásokat, és be kell állítani az ENTITÁSok műveleteit egy Conversation Learner modellel – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogy megfelelő-e az ENUMERÁLÁSi entitások használata, valamint az ENTITÁSok műveleteinek Conversation Learner modellel történő beállítása.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 429570d81f7e15758d8ea60951bb4d01b96f8f2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256485"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Mikor kell használni az ENUMERÁLÁSi entitásokat és beállítani az ENTITÁSok műveleteit

Ez az oktatóanyag azt ismerteti, hogy mikor kell használni az ENUMERÁLÁSi (enumerálási) entitásokat és a SET_ENTITY műveleteket.

## <a name="video"></a>Videó

[![Entitás-oktatóanyag előzetes verziójának beállítása](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Mit takar

Ez az oktatóanyag két új funkciót fog bevezetni. Egy új, ENUM (short for enumerálás) típusú entitás és egy új, SET_ENTITY nevű művelet, amely hivatkozhat ezen enumerálási értékek egyikére, és ahogy a neve is jelzi, az entitást erre az értékre állítja be. Ahogy az alábbiakban is megismerheti, ezeket az új funkciókat együttesen használják, és elmagyarázjuk, hogy mi és hogyan használhatók. A részletek beszerzése előtt fontos tisztában lennie azzal, hogy milyen problémát jelent ezek a funkciók.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Probléma

Vannak olyan esetek a beszélgetésekben, amikor a szavak jelentése a kontextustól függ.  A címkével ellátott kulcsfontosságú szavakat általában a Language Understanding Service használatával lehet megtanulni és kinyerni, de ezekben az esetekben előfordulhat, hogy ezek a rendszerek nem tudnak a címkével ellátott példákat használni.

Képzelje el, hogy a közeli személyek közötti beszélgetés egy részét meghallgatja, és csak az "igen" szót hallja. Nem tudhatja, mi az "igen", és nem is erősíti meg, mert nem hallotta a kérdést. Az előtt feltett kérdés a kontextus, amely a válasz jelentését adja. Hasonlóképpen, mivel az "igen" olyan gyakori válasz, amely sok különböző kérdésre vonatkozik, nem lehet megtanulni, ha olyan példákat ad meg, mint például az [Egyéni képzettséggel](04-introduction-to-entities.md) rendelkező entitások esetében, mivel ez azt is megtudhatja, hogy az összes "igen" címkét adja meg.

### <a name="example"></a>Példa

Pontosítsa a következő példát:

Bot Tetszik az Azure Cognitive Services?
Felhasználói Igen bot: Szeret fagylaltot?
Felhasználói Igen

Az előző oktatóanyagokban megvizsgáltuk az [Egyéni betanított](04-introduction-to-entities.md) entitásokat, és az első gondolat, hogy létrehoz egy "likesCogServices" nevű entitást, és az első "igen" címkét adja meg az entitásként.  A System azonban a második "igen" címkét is felcímkézi. Amikor megpróbálta kijavítani a második "igen" címkét a "likesIceCream" értékre, akkor két azonos bemenetből álló "igen" típusú ütközést hozunk létre, ami különböző dolgokat jelent, és beragadhat.

Ezekben az esetekben az ENUMERÁLÁSi entitásokat és a SET_ENTITY műveleteket kell használnia.

## <a name="when-to-use-enums-or-set_entity-actions"></a>Mikor használjon ENUMERÁLÁSokat vagy SET_ENTITY műveleteket

Az alábbi szabályok segítségével tájékozódhat az ENUMERÁLÁSi entitások és a SET_ENTITY műveletek használatáról:

- Az entitás észlelése vagy beállítása a kontextustól függ
- A lehetséges értékek száma rögzített (igen, és nem lenne két érték)

Más szóval ezeket minden olyan lezárult kérdéshez használhatja, mint például a megerősítő kérdések, amelyek mindig az igen vagy a nem értéket eredményezik.

> [!NOTE]
> Az enumerálási entitások esetében jelenleg legfeljebb 5 érték van korlátozva. Minden érték az aktuális 64-as korlát egyik tárolóhelyét használja. Lásd: [CL-Values-and-határok](../cl-values-and-boundaries.md)

Példa: Bot Helyes a megrendelés?
Felhasználói Igen

Ha az entitás lehetséges értékei nyitottak és nem rögzítettek, alternatív szolgáltatást kell használni, például a [várt entitást](05-expected-entity.md).

Példa: Bot hogy hívnak?
Felhasználói Matt bot: Mi a kedvenc színe?
Felhasználói Silver

Ezek a kérések nyílt végűek, mert tetszőleges értékekkel megválaszolható.

## <a name="what"></a>Mi:

### <a name="enum-entities"></a>Entitások ENUMERÁLÁSa

Az ENUMERÁLÁSi entitások ugyanúgy jönnek létre, mint a többi entitás. A "programozott" entitásokhoz hasonlóan a szavakat nem címkézheti az entitások alapján. Ehelyett kódokat vagy SET_ENTITY műveleteket kell beállítania.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Entitás-műveletek beállítása

Ahogy fent említettük, az "entitások beállítása" műveletekhez egyszerűen egy entitást kell beállítani egy ismert enumerálási értékre. Ugyanezeket az eredményeket úgy érheti el, ha létrehoz egy API-visszahívási műveletet, és a Memory Manager használatával beállítja az entitást egy értékre. Például `memory.Set(entityName, entityValue)`. A kód írásához és a műveletek létrehozásához unalmas és nehezen kezelhető lesz, ezért Conversation Learner speciális műveletekkel könnyíti meg ezt a munkát, és automatikusan hozza létre ezeket a műveleteket a használatuk során. Ezek a független műveletek megőrzik ezeket a képességeket anélkül, hogy a robotban más műveletekkel vagy kóddal párosítva lenne.

- Az entitások beállítása művelet csak akkor hozható létre, ha egy enumerálási entitás értékére hivatkozik, így először létre kell hoznia egy enumerálási entitást.
- Az entitások beállítása "nem várt", mivel nem látható kimenettel rendelkeznek, és egy "WAIT" művelettel kell követni, amelyet a felhasználó láthat.
- Az entitások beállítása nem módosítható, mert a létrehozás után nem szerkesztheti őket.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatikus művelet létrehozása

Ha egy enumerálási entitás létezik a modellben, Conversation Learner helyőrző műveleteket hoz létre az egyes lehetséges értékekhez, és elérhetővé teszi azokat a képzés során való kiválasztáshoz. A kiválasztás után a rendszer automatikusan létrehozza a műveletet.

Ha például egy "yes" és "No" értékkel rendelkező enumerálási entitást hoz létre:

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Még az új enumeráláshoz tartozó műveletek explicit létrehozása nélkül is megjelenhet két új, a képzés során elérhető művelet:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Robot létrehozása ezekkel az új funkciókkal

### <a name="requirements"></a>Követelmények

Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

Létre fogunk hozni egy robotot a gyors élelmiszer-rendezés szimulálása érdekében. Az italok és a krumpli mérete (kicsi/közepes/nagy) és a megerősítő kérdések igen/nem választ tartalmaznak. Mindkét entitás megfelel a környezettől függő válaszok és a rögzített értékek közötti két szabálynak.

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felületen kattintson az "Importálás" elemre.
2. Válassza ki a "tutorial-Enum-set-Entity" nevű oktatóanyagot

Ekkor megnyílik a modell-felügyeleti oldal.
Figyelje meg, hogy a modell már tartalmaz néhány enumerálási entitást, és beállította az entitások műveleteit.

### <a name="create-the-first-dialog"></a>Az első párbeszédpanel létrehozása

1. A bal oldali navigációs panelen kattintson a "betanítási párbeszédpanelek" elemre, majd kattintson az "új vonat" gombra.
2. A felhasználó begépeli a "Hi, szeretném megrendelni a kokszot és a krumplit".
3. Kattintson a "pontszám műveletek" gombra

   > A felhasználó nem adott meg méretet az italhoz vagy a krumplihoz, ezért fel kell kérni őket.

4. Válassza ki a választ tartalmazó műveletet: "Milyen méretű italt szeretne?"
5. A felhasználó típusaként a "nagy"
6. Kattintson a "pontszám műveletek" gombra
7. Válassza ki a műveletet SET_ENTITY-"drinkSize: NAGY
8. Válassza ki a választ tartalmazó műveletet: "Milyen méretű krumplit szeretne?"
9. Használja az "UM" kifejezést, és tegye meg ezeket a médiumokat.
10. Kattintson a "pontszám műveletek" gombra
11. Válassza ki a műveletet SET_ENTITY-"friesSize: KÖZEPES
12. Válassza ki a választ tartalmazó műveletet: "Szeretné, ha bármilyen fűszer?"
13. A használat típusaként az "igen"
14. Kattintson a "pontszám műveletek" gombra
15. Válassza ki a műveletet SET_ENTITY-"condimentsConfirmation: IGEN
16. Válassza ki a választ tartalmazó műveletet: "Ok, megrendelésem egy nagy italra és közepes krumplira. Ez helyes? "
17. A használat típusaként az "igen"
18. Kattintson a "pontszám műveletek" gombra
19. Válassza ki a műveletet SET_ENTITY-"orderConfirmation: IGEN
20. Válassza ki a választ tartalmazó műveletet: "Ok, a megrendelés száma 58. Kis türelmet. "
21. A párbeszédpanel bezárásához kattintson a Mentés gombra.

Most létrehozta az első párbeszédpanelt az ENUMERÁLÁSi entitások és a SET_ENTITY műveletek használatával. A felhasználó több kombinációját is megadhatja részleges információkkal, vagy más típusú, lezárult kérdésekkel kísérletezve.

> [!NOTE]
> A képzés során megjelenik a SET_ENTITY műveletek helyőrzői, például:
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> Ha azonban a naplófájlok létrehozásakor vagy a telepített robotok használatakor a felhasználók nem fogják látni ezeket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alternatív bemenetek](./10-alternative-inputs.md)
