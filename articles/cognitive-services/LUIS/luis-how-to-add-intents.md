---
title: Leképezések hozzáadása – LUIS
titleSuffix: Azure Cognitive Services
description: Vegyen fel szándékokat a LUIS-alkalmazásba, hogy azonosítsa az azonos szándékkal rendelkező kérdések vagy parancsok csoportjait.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467545"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Leképezések hozzáadása a hosszúságú kimondott szöveg felhasználói szándékának meghatározásához

Vegyen fel [leképezéseket](luis-concept-intent.md) a Luis-alkalmazásba, hogy azonosítsa az azonos szándékú kérdések vagy parancsok csoportjait. 

A leképezéseket a rendszer a felső navigációs sáv **Build** szakasza, majd a bal oldali panel **szándékai**alapján kezeli. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Szándék hozzáadása

1. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

1. Az **Új leképezés létrehozása** párbeszédpanelen adja meg a leképezés nevét, `GetEmployeeInformation`, majd kattintson a **kész**gombra.

    ![Leképezés hozzáadása](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Példa Kimondás hozzáadása

Például a hosszúságú kimondott szöveg a felhasználói kérdések és parancsok szöveges példái. Language Understanding (LUIS) tanításához hozzá kell adnia egy példa hosszúságú kimondott szöveg egy szándékhoz.

1. A **GetEmployeeInformation** -leképezés részletei lapon adja meg a felhasználóktól várt megfelelő értéket, például `Does John Smith work in Seattle?` a cél neve alatti szövegmezőben, majd nyomja le az ENTER billentyűt.
 
    ![A szándékok részletei oldal képernyőképe, a teljes kiemeléssel](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    A LUIS az összes hosszúságú kimondott szöveg kisbetűsre alakítja, és szóközöket, például kötőjeleket hoz létre.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Szándék-előrejelzési hibák 

A szándékok egyik példájának kimondása a szándék előrejelzési hibája lehet, ha a példa a Kimondás alatt áll, és az előrejelzési szándékot a képzés során határozzák meg. 

A részletes előrejelzési hibák megkereséséhez és a kijavításához használja a **szűrési** lehetőség **kiértékelési** beállításait a helytelen és a nem egyértelmű beállításhoz a **Részletes nézet** **megtekintési** lehetőségével együtt. 

![A teljes előrejelzési hibák és a kijavításuk megkereséséhez használja a Filter (szűrő) beállítást.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Ha a szűrők és a nézet alkalmazva van, és vannak például hosszúságú kimondott szöveg hibák, a példaként szolgáló lista a hosszúságú kimondott szöveg és a problémákat jeleníti meg.

![! [Ha a szűrők és a nézet alkalmazva van, és vannak például hosszúságú kimondott szöveg hibák, a példában a teljes lista a hosszúságú kimondott szöveg és a problémákat mutatja.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Az egyes sorok az aktuális képzés előrejelzési pontszámát jelenítik meg a példához, a legközelebbi rivális pontszámát, amely a két pontszám különbsége. 

### <a name="fixing-intents"></a>Leképezések kijavítása

Ha meg szeretné tudni, hogyan lehet kijavítani a szándék-előrejelző hibákat, használja az [összegző irányítópultot](luis-how-to-use-dashboard.md). Az összegző irányítópult elemzést nyújt az aktív verzió utolsó betanításáról, és a legjobb javaslatokat kínálja a modell kijavításához.  

## <a name="add-a-custom-entity"></a>Egyéni entitás hozzáadása

Ha egy Kimondás bekerül egy szándékba, egy egyéni entitás létrehozásához kiválaszthatja a szöveget a teljes szövegből. Az egyéni entitások lehetővé teszik a szöveg címkézését a kinyeréshez, valamint a megfelelő szándékot. 

További információért tekintse meg az [entitás hozzáadása a teljes](luis-how-to-add-example-utterances.md) információhoz című témakört.

## <a name="entity-prediction-discrepancy-errors"></a>Entitás-előrejelzési eltérési hibák 

Az entitás piros színnel van aláhúzva, hogy jelezze az [entitások előrejelzési eltérését](luis-how-to-add-example-utterances.md#entity-status-predictions). Mivel ez egy entitás első előfordulása, nincs elég példa arra, hogy LUIS nagy megbízhatósággal rendelkezzen, hogy a szöveg a megfelelő entitással legyen címkézve. A rendszer eltávolítja ezt az eltérést az alkalmazás betanításakor. 

![A szándékok részletei lap képernyőképe, amely a kék színnel jelölt egyéni entitás nevét adja meg](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

A szöveg kék színnel van kijelölve, és egy entitást jelez.  

## <a name="add-a-prebuilt-entity"></a>Előre összeépített entitás hozzáadása

További információ: [előre összeépített entitás](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>A környezetfüggő eszköztár használata

Ha a listában egy vagy több példa hosszúságú kimondott szöveg van kiválasztva, akkor a teljes képernyő bal oldalán lévő jelölőnégyzet bejelölésével a teljes lista fölötti eszköztár lehetővé teszi a következő műveletek végrehajtását:

* Hozzárendelés ismételt leképezése: a lemondás (ok) áthelyezése különböző célra
* Kimondás (ok) törlése
* Entitás-szűrők: csak a szűrt entitásokat tartalmazó hosszúságú kimondott szöveg megjelenítése
* Csak az összes/csak hibák megjelenítése: hosszúságú kimondott szöveg megjelenítése előrejelzési hibákkal vagy az összes hosszúságú kimondott szöveg megjelenítése
* Entitások/jogkivonatok nézet: entitások nézetének megjelenítése az entitások nevével vagy a Kimondás nyers szövegének megjelenítése
* Nagyító: adott szöveget tartalmazó hosszúságú kimondott szöveg keresése

## <a name="working-with-an-individual-utterance"></a>Egyéni Kimondás használata

A következő műveletek hajthatók végre a három pontból álló, a kiválasztástól jobbra lévő, egyedi Kimondás esetén:

* Szerkesztés: a Kimondás szövegének módosítása
* Törlés: távolítsa el a kivágást a szándékból. Ha továbbra is szeretné kipróbálni a teljes értéket, a jobb módszer az, ha áthelyezi a **nincs** szándékra. 
* Minta hozzáadása: A minta lehetővé teszi, hogy egy közös és megjelöléssel elválasztható szöveget és figyelmen kívül hagyható szöveget tegyen elérhetővé, így kevesebb hosszúságú kimondott szöveg van szükség a szándékban. 

A **címkézett leképezési** oszlop lehetővé teszi, hogy megváltoztassa a kiválasztás szándékát.

## <a name="train-your-app-after-changing-model-with-intents"></a>Az alkalmazás betanítása a modell leképezésekkel való módosítása után

Miután hozzáadta, szerkeszti vagy eltávolítja a leképezéseket, [betanítja](luis-how-to-train.md) és [közzéteszi](luis-how-to-publish-app.md) az alkalmazást, hogy a módosítások a végponti lekérdezésekre legyenek alkalmazva. 

## <a name="next-steps"></a>További lépések

További információ a [példa hosszúságú kimondott szöveg](luis-how-to-add-example-utterances.md) az entitásokkal való hozzáadásáról. 
