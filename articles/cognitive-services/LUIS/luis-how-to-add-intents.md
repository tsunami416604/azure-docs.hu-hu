---
title: Leképezések hozzáadása – LUIS
titleSuffix: Azure Cognitive Services
description: Leképezések hozzáadása a LUIS-alkalmazás olyan kérdésekre, vagy ugyanazon céljaira rendelkező parancsok azonosítására.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 84799c2866e7b887a7b509280b073814e7653638
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638210"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Leképezések hozzáadása a hosszúságú kimondott szöveg felhasználói szándékának meghatározásához

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

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Szándék-előrejelzési hibák 

A szándékok egyik példájának kimondása a szándék előrejelzési hibája lehet, ha a példa a Kimondás alatt áll, és az előrejelzési szándékot a képzés során határozzák meg. 

A részletes előrejelzési hibák megkereséséhez és a kijavításához használja a **szűrési** lehetőség kiértékelési beállításait a helytelen és a nem egyértelmű beállításhoz a **Részletes nézet** **megtekintési** lehetőségével együtt. 

![A teljes előrejelzési hibák és a kijavításuk megkereséséhez használja a Filter (szűrő) beállítást.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Ha a szűrők és a nézet alkalmazva van, és vannak például hosszúságú kimondott szöveg hibák, a példaként szolgáló lista a hosszúságú kimondott szöveg és a problémákat jeleníti meg.

![! [Ha a szűrők és a nézet alkalmazva van, és vannak például hosszúságú kimondott szöveg hibák, a példában a teljes lista a hosszúságú kimondott szöveg és a problémákat mutatja.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Az egyes sorok az aktuális képzés előrejelzési pontszámát jelenítik meg a példához, a legközelebbi rivális pontszámát, amely a két pontszám különbsége. 

### <a name="fixing-intents"></a>Leképezések kijavítása

Ha meg szeretné tudni, hogyan lehet kijavítani a szándék-előrejelző hibákat, használja az [összegző irányítópultot](luis-how-to-use-dashboard.md). Az összegző irányítópult elemzést nyújt az aktív verzió utolsó betanításáról, és a legjobb javaslatokat kínálja a modell kijavításához.  

## <a name="add-a-custom-entity"></a>Egyéni entitás hozzáadása

Az utterance (kifejezés) megjelölésű ad hozzá, miután kiválaszthatja az utterance (kifejezés) hozhat létre egyéni entitásokat származó szöveg. Egyéni entitás módja a kinyerési, és a megfelelő leképezés címke szövegét. 

További információért tekintse meg az [entitás hozzáadása a teljes](luis-how-to-add-example-utterances.md) információhoz című témakört.

## <a name="entity-prediction-discrepancy-errors"></a>Entitás előrejelzési eltérést észlelt hibák 

Vörös jelzi az entitás aláhúzott egy [entitás előrejelzés eltérés](luis-how-to-add-example-utterances.md#entity-status-predictions). Ez az entitás a első előfordulása, mert nem áll elég nagy magabiztossággal rendelkeznie LUIS példákat, hogy ez a szöveg van megjelölve a megfelelő entitással. Ez az eltérés törlődik, ha az alkalmazás be van tanítva. 

![Képernyőkép a leképezések Részletek lap, kék színnel egyéni entitás neve](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

A szöveg kék színnel, egy entitás jelző van kiemelve.  

## <a name="add-a-prebuilt-entity"></a>Egy előre létrehozott entitás hozzáadása

További információ: [előre összeállított entitások](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>A környezetfüggő eszköztár

Ha a listában egy vagy több példa hosszúságú kimondott szöveg van kiválasztva, akkor a teljes képernyő bal oldalán lévő jelölőnégyzet bejelölésével a teljes lista fölötti eszköztár lehetővé teszi a következő műveletek végrehajtását:

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
* Minta hozzáadása: A minta lehetővé teszi egy közös szöveg-és visszahelyezhető szöveg és a figyelmen kívül hagyható szöveg megjelölését, így csökkentve a szándékot, hogy további hosszúságú kimondott szöveg legyenek. 

A **szándékot feliratú** oszlop lehetővé teszi, hogy az utterance (kifejezés) célját módosítani.

## <a name="train-your-app-after-changing-model-with-intents"></a>Modell, szándék fog vonatkozni módosítása után az alkalmazás betanítása

Miután hozzáadása, szerkesztése vagy eltávolítása a szándék fog vonatkozni, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) az alkalmazás úgy, hogy a módosítások a végpont-lekérdezésekre. 

## <a name="next-steps"></a>További lépések

További adásával kapcsolatos [példa utterances](luis-how-to-add-example-utterances.md) entitásokkal. 
