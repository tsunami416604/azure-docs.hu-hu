---
title: Mikor érdemes használni az ENUM entitásokat, és ÁLLÍTSA ENTITÁSOKKAL végezhető műveletek a beszélgetés Learner modellel – Azure Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, abban az esetben a beszélgetés Learner modell ENUM entitásokat, és ÁLLÍTSA ENTITÁSOKKAL végezhető műveletek használata.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ms.openlocfilehash: ed18d30a0c3f5d51cb3a07b8948863cdda16c1ae
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845963"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Mikor érdemes használni, ENUM entitásokat, és ÁLLÍTSA ENTITÁSOKKAL végezhető műveletek

Ez az oktatóanyag elmagyarázza mikor ENUM (enumerálási) entitásokat és SET_ENTITY műveletek használja.

## <a name="video"></a>Videó

[![Állítsa be az entitás oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Témák

Ebben az oktatóanyagban két új funkciókat vezet be. Egy entitás új típusú nevű ENUM (rövid a enumerálási) és a egy új típusú művelet SET_ENTITY, amely enum értékek egyikét, olvassa el, és a neve is mutatja, ahogy az entitás állítja ezt az értéket. Alább ismertetjük, új funkciókról együtt használja, és ismertetjük őket, és hogyan használhatja az alábbi. Mielőtt a részleteit, fontos tudni, mely a probléma megoldásához ezeket a funkciókat.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Probléma

Előfordulhatnak olyan esetek témák, ahol a értelmében szavakat a környezettől függ.  Általában a címkével ellátott kulcsot szavakat küszöbértéket, és ki kell olvasni a hangfelismerési szolgáltatás használatával, de ebben az esetben ezek a rendszerek nem lehet címkézett példák segítségével további.

Tegyük fel, overhear személyek közeli hasznos helyek közötti beszélgetés része, és csak hallott szót "Igen" gombra. Nem tudhatja meg, mi az "Igen" elfogadja a, vagy, mielőtt ismételt megerősíti, mert nem érkezett rá válasz a kérdésre. Előtt feltett kérdés a környezetet igényel, amely lehetővé teszi a választ a jelentése. Hasonlóképpen óta az "Igen" megadásával példákat, mint a nem megtanult számos különböző kérdéseket ilyen gyakori válasz [egyéni betanított](04-introduction-to-entities.md) entitások mert szeretne címkével ellátni minden "Yes" entitás, ismerje meg, majd.

### <a name="example"></a>Példa

Most további tisztázása a következő példában:

A robot: Tetszett Önnek az Azure Cognitive Services?
Felhasználó: A robot Igen: Az ice cream tetszett Önnek?
Felhasználó: Igen

Az előző oktatóanyagokban megvizsgáltunk, [egyéni betanított](04-introduction-to-entities.md) entitásokat és a kezdeti gondolat "likesCogServices" nevű entitás létrehozása és az első "Yes" ehhez az entitáshoz, a címke lehet.  Azonban a rendszer akkor is címkét a második "Yes". Kísérel meg "Igen", "likesIceCream" a második címkéjének kijavíthatja, ha azt szeretné majd hozzon létre két azonos bemeneti ütközés "Yes" jelentése különböző dolgok és elakadt a lenne.

Ezekben az esetekben kell használnia, ENUM entitásokat és SET_ENTITY műveletek van.

## <a name="when-to-use-enums-or-setentity-actions"></a>Mikor érdemes használni, enumerálások és SET_ENTITY műveletek

Ezek a szabályok az alábbi használatával, hogy mikor ENUM entitásokat és SET_ENTITY műveletek:

- Észlelési vagy a beállítás entitás nem környezeti változó
- A lehetséges értékek száma rögzített (Igen, és nem lehet két értéket)

Más szóval használja ezeket a Bezárás ért utasításokat, például a megerősítő kérdésekre, amelyek mindig Igen vagy nem értéket.

> [!NOTE]
> Jelenleg legfeljebb 5 értéket, enum entitás korlátozása van. Az egyes értékek használja az egyik tárolóhely a jelenleg legfeljebb 64. See [cl-values-and-boundaries](../cl-values-and-boundaries.md)

Példa: A robot: A helyes rendelését?
Felhasználó: Igen

Ha a lehetséges értékek az entitás nyílt, és nem állandó, például egy másik szolgáltatás használata kell [várt entitás](05-expected-entity.md).

Példa: A robot: hogy hívnak?
Felhasználó: Matt Bot: Mi a kedvenc színét?
Felhasználó: Ezüst

Ezek a felkérések nyílt minősülnek, mert sikerült megválaszolandó tetszőleges értékekkel.

## <a name="what"></a>Mi

### <a name="enum-entities"></a>ENUM entitások

Csakúgy, mint a más szervezetek ENUM entitások jönnek létre. "Programozott" entitásokhoz hasonlóan szavak nem címkét, ezeket az entitásokat. Ehelyett hogy meg kell kód vagy SET_ENTITY műveletek.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Entitásokkal végezhető műveletek beállítása

Amint már említettük, a "Entitás beállítása" műveletek egy entitás egyszerűen egy ismert enumerálási értéket állítsa be. API visszahívási művelet létrehozásával, és a memória-kezelő segítségével értékre az entitást sikerült érhet el ugyanazokat az eredményeket. Például `memory.Set(entityName, entityValue)`. Ez a kód írása, és ezek a műveletek létrehozása válnak fárasztó és nehéz kezelni – így Beszélgetéstanuló elősegítik ezt a munkát, és automatikusan létrehozni ezeket a műveleteket, használatukról speciális műveleteket. Ezeket az adatokat független műveletek kellene megőrzi a compose ezek nélkül folyamatban összefüggő egyéb műveletek vagy a kóddal a robot lehetővé teszi.

- Entitás kapcsolatos műveletek csak ha hivatkozó enum entitás értékét, ezért létre kell hoznia egy enum entitás először hozza létre.
- Entitás kapcsolatos műveletek is "nem-await", mivel azok nem látható kimenet rendelkezik, és szükség szerint a felhasználó számára látható a "wait" művelet nyomon kell követni.
- Entitás kapcsolatos műveletek nem módosíthatók, ami azt jelenti, létrehozás után nem szerkeszthetők.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatikus művelet létrehozása

Ha enum entitás már létezik a modellben, Beszélgetéstanuló helyőrző a lehetséges értékek mindegyike esetén végrehajtandó műveletek létrehozása, és elérhetővé tétele a betanítás során válassza. Kiválasztáskor a művelet automatikusan létrehozott az Ön számára.

Például, ha hozok létre enum entitás az értékek az "Igen" és "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Explicit módon létrehozása az új enum műveletek nélkül is a betanítás során elérhető két új műveletet lenne láthatja:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Hozzon létre egy új funkciók használatával Bot

### <a name="requirements"></a>Követelmények

Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

Gyors food rendezése szimulálása robotprogramok hozunk létre. Méretek ital és fries (kis és közepes vagy nagy) diszkrét értékek fog rendelkezni, és a megerősítő kérdés az Igen / nem válaszokat. Mindkét ezeket az entitásokat felel meg a fenti két szabályt, hogy a környezet-függő válaszokat és a rögzített értékeit.

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületen kattintson a "Importálása"
2. Válassza az "Az oktatóanyag-számbavételi-Set-entitás" nevű oktatóanyag

Ez a modell kezelése lap jelenik meg.
Figyelje meg, hogy a modell már néhány enum entitásokat tartalmaz, és állítsa be az entitásokkal végezhető műveletek.

### <a name="create-the-first-dialog"></a>Az első párbeszédpanel létrehozása

1. A bal oldali navigációs panelen kattintson a "Train-párbeszédpanelekhez", majd kattintson a "Train párbeszédpanel" gombra.
2. A felhasználó típusaként a "Hi, szeretném, ha sorrend egy koksz és fries meg".
3. Kattintson a "Score műveletek" gombra

   > A felhasználó az ital méretei nem megadott vagy fries, ezért ellenőriznünk kell a kérje meg őket.

4. A válasz művelet kiválasztása: "Milyen méretű ital szeretné használni?"
5. A felhasználó írja be a "nagy"
6. Kattintson a "Score műveletek" gombra
7. Válassza ki a következő műveletet: SET_ENTITY – "drinkSize: LARGE"
8. A válasz művelet kiválasztása: "Milyen méretű fries szeretné?"
9. A típus használata, mint "Um, győződjön meg azok közepes.
10. Kattintson a "Score műveletek" gombra
11. Válassza ki a következő műveletet: SET_ENTITY – "friesSize: KÖZEPES"
12. A válasz művelet kiválasztása: "Szeretné bármely fűszerek?"
13. A használat típusa az "Igen"
14. Kattintson a "Score műveletek" gombra
15. Válassza ki a következő műveletet: SET_ENTITY – "condimentsConfirmation: IGEN LEHETŐSÉGET
16. A válasz művelet kiválasztása: "Rendben, van egy olyan NAGYMÉRETŰ, amelyet és közepes méretű fries sorrendje. Az, hogy helyes-e?"
17. A használat típusa az "Igen"
18. Kattintson a "Score műveletek" gombra
19. Válassza ki a következő műveletet: SET_ENTITY – "orderConfirmation: IGEN LEHETŐSÉGET
20. A válasz művelet kiválasztása: "Rendben, az order number 58 is. Várjon, van ott."
21. Kattintson a "Mentés" gombra kattintva zárja be a párbeszédpanelt

Most hozta létre az első párbeszédpanel ENUM entitásokat és SET_ENTITY műveletek használatával. A felhasználó részleges kapcsolatos információk megadásával vagy más típusú kérdések Bezárás véget ért a kísérletezés számos további kombinációit is felvehető.

> [!NOTE]
> Betanítás során látni fogja helyőrzőket SET_ENTITY műveleteihez például
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> de amikor párbeszédpanelek vagy segítségével telepítve létrehozásához jelentkezzen robotokat felhasználók nem látják ezeket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alternatív bemenetek](./10-alternative-inputs.md)
