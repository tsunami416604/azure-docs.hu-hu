---
title: Leképezések hozzáadása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Leképezések hozzáadása a LUIS-alkalmazás olyan kérdésekre, vagy ugyanazon céljaira rendelkező parancsok azonosítására.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.component: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: d353f37b89f9932827a07fc8079aa606d425ed38
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882871"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Leképezések meghatározni a felhasználó szándékának beszédmódok hozzáadása

Adjon hozzá [leképezések](luis-concept-intent.md) az olyan kérdések és azonos szándékkal rendelkező parancsok azonosítására a LUIS-alkalmazás. 

Leképezések a felső navigációs sávban felügyelt **hozhat létre** című szakaszában, majd a bal oldali panelen **leképezések**. 

## <a name="add-intent"></a>Szándék hozzáadása

1. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

1. Az a **új leképezésének létrehozása** párbeszédpanelen adja meg a leképezés nevét `GetEmployeeInformation`, és kattintson a **kész**.

    ![Leképezés hozzáadása](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Adjon hozzá egy példa utterance (kifejezés)

Példa utterances Példák szöveges felhasználói kérdések vagy parancsok. Language Understanding (LUIS), akik az példa beszédmódok hozzáadása egy beszédszándék kell.

1. Az a **GetEmployeeInformation** szándék részletei lap, adja meg egy megfelelő utterance (kifejezés) adatraktáraktól a felhasználók számára, mint például `Does John Smith work in Seattle?` alatti leképezési nevét, és nyomja le az ENTER billentyűt a szövegmezőben.
 
    ![Képernyőkép a leképezések Részletek lapján az utterance (kifejezés) kiemelésével](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    A LUIS kisbetűvé alakítja az összes kimondott szöveg, és hozzáadja a jogkivonatok kötőjeleket például szóközt.

## <a name="intent-prediction-discrepancy-errors"></a>Leképezési előrejelzési eltérést észlelt hibák 

Előfordulhat, hogy az utterance (kifejezés) megjelölésű a egy leképezési előrejelzés eltérés van a kiválasztott célt és az előrejelzési pontszám között. LUIS azt jelzi, hogy a piros Keretes ez eltérést észlelt a **szándékot feliratú** a sorban található az a példában utterance (kifejezés). 

![Képernyőkép a leképezések Részletek lapján utterance (kifejezés) előrejelzés eltérés hibákkal](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

Válassza ki a felső navigációs **Train**. Az előrejelzés eltérés van most szűnt.

## <a name="add-a-custom-entity"></a>Egyéni entitás hozzáadása

Az utterance (kifejezés) megjelölésű ad hozzá, miután kiválaszthatja az utterance (kifejezés) hozhat létre egyéni entitásokat származó szöveg. Egyéni entitás módja a kinyerési, és a megfelelő leképezés címke szövegét. 

1. Válassza ki a word-, `Seattle`, az az utterance (kifejezés). Hranaté závorky dolgozzák a szöveget, és megjelenik egy legördülő menü. 

    ![Képernyőkép a leképezések részletező lapján, az egyéni entitások](./media/luis-how-to-add-intents/create-custom-entity.png) 

    Ebben a példában egy entitás megjelölése egyetlen szó választja ki. Egyetlen működik és kifejezések entitásokként jelölheti meg.

1. A felső szöveg-, a menü, adja meg `Location`, majd **új entitás létrehozása**. 

    ![Képernyőkép a leképezések Részletek lapján nevű egyéni entitás létrehozása](./media/luis-how-to-add-intents/create-custom-entity-name.png) 

1. Az a **milyen típusú entitást szeretne létrehozni?** előugró ablak az entitás létrehozását, ellenőrizze, hogy a **entitás neve** van _hely_, és a **entitás típusa**  van _egyszerű_. Válassza a **Done** (Kész) lehetőséget.

## <a name="entity-prediction-discrepancy-errors"></a>Entitás előrejelzési eltérést észlelt hibák 

Vörös jelzi az entitás aláhúzott egy [entitás előrejelzés eltérés](luis-how-to-add-example-utterances.md#entity-status-predictions). Ez az entitás a első előfordulása, mert nem áll elég nagy magabiztossággal rendelkeznie LUIS példákat, hogy ez a szöveg van megjelölve a megfelelő entitással. Ez az eltérés törlődik, ha az alkalmazás be van tanítva. 

![Képernyőkép a leképezések Részletek lap, kék színnel egyéni entitás neve](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

A szöveg kék színnel, egy entitás jelző van kiemelve.  

## <a name="add-a-prebuilt-entity"></a>Egy előre létrehozott entitás hozzáadása

További információ: [előre összeállított entitások](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="using-the-contextual-toolbar"></a>A környezetfüggő eszköztár

Ha a bal oldalán az utterance (kifejezés) négyzet bejelölésével, egy vagy több példa utterances a listában, az eszköztáron az utterance (kifejezés) lista fölött lehetővé teszi hajtsa végre a következő műveleteket:

* Leképezés ismételt hozzárendelése: utterance(s) áthelyezése másik leképezés
* Utterance(s) törlése
* Entitás szűrők: csak a szűrt entitásokat tartalmazó kimondott szöveg megjelenítése
* Az összes megjelenítése / csak a hibák: előrejelzés hibákkal kimondott szöveg vagy az összes kimondott szöveg megjelenítése
* Entitások/tokenek megtekintése: entitás névvel rendelkező entitásokat nézet vagy az utterance (kifejezés) nyers szöveg megjelenítése
* Nagyító: keresse meg a meghatározott szöveget tartalmazó kimondott szöveg

## <a name="working-with-an-individual-utterance"></a>Az egyes utterance (kifejezés) használata

Az alábbi műveleteket az egyes utterance (kifejezés) a három pont menüben jobb oldalán az utterance (kifejezés) hajtható végre:

* Szerkesztés: az utterance (kifejezés) szövegének módosítása
* Törlés: a leképezés eltávolítása az utterance (kifejezés). Ha továbbra is az utterance (kifejezés), célszerűbb áthelyezni, hogy-e a **nincs** szándékot. 
* Adjon hozzá egy minta: A minta egy közös utterance (kifejezés) igénybe vehet, és jelölje be a cserélhető és szöveg figyelmen kívül hagyható, ezáltal csökkentve a további utterances a SZÁNDÉKTÓL teszi lehetővé. 

A **szándékot feliratú** oszlop lehetővé teszi, hogy az utterance (kifejezés) célját módosítani.

## <a name="train-your-app-after-changing-model-with-intents"></a>Modell, szándék fog vonatkozni módosítása után az alkalmazás betanítása

Miután hozzáadása, szerkesztése vagy eltávolítása a szándék fog vonatkozni, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) az alkalmazás úgy, hogy a módosítások a végpont-lekérdezésekre. 

## <a name="next-steps"></a>További lépések

További adásával kapcsolatos [példa utterances](luis-how-to-add-example-utterances.md) entitásokkal. 
