---
title: Leképezések hozzáadása – LUIS
titleSuffix: Azure Cognitive Services
description: Szándékok hozzáadása a LUIS-alkalmazáshoz az azonos szándékú kérdések vagy parancsok csoportjainak azonosításához.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904329"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Szándékok hozzáadása az utterances felhasználói szándékának meghatározásához

[Szándékok](luis-concept-intent.md) hozzáadása a LUIS-alkalmazáshoz az azonos szándékú kérdések vagy parancsok csoportjainak azonosításához. 

A leképezések kezelése a felső navigációs sáv **Build** szakaszából, majd a bal oldali panel **szándékaiból**áll. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Szándék hozzáadása

1. A [LUIS előzetes verzióportálon](https://preview.luis.ai)válassza **a Build a** leképezések megtekintéséhez. 
1. A **Szándékok** lapon válassza a **+ Create**lehetőséget.
1. Az **Új leképezés létrehozása** párbeszédpanelen adja `ModifyOrder`meg például a szándék nevét, és válassza a **Kész gombot.**

    > [!div class="mx-imgBorder"]
    > ![Leképezés hozzáadása](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A szándék példa utterances igényel példa utterances.

## <a name="add-an-example-utterance"></a>Példa utterance (kifejezés) hozzáadása

Példa utterances szöveges példák a felhasználói kérdések vagy parancsok. A language understanding (LUIS) tanításához, amikor előre ezt a szándékot, példa utterances hozzáadása kell egy szándék. Luis szüksége van a tartományban 15-30 példa utterances a szándék megértéséhez. Ne adjon hozzá példa utterances ömlesztve. Minden utterance (kifejezés) gondosan meg kell választani, hogy miben különbözik a már a szándékban lévő példáktól. 

1. A szándék részletei lapon adja meg a felhasználóktól elvárt `Deliver a large cheese pizza` megfelelő utterance (kifejezés) értéket, például a szándék neve alatti szövegmezőbe, majd nyomja le az Enter billentyűt.
 
    > [!div class="mx-imgBorder"]
    > ![A Szándékok részletei lap képernyőképe, kiemelve az utterance (kifejezés)](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    A LUIS az összes utterances kisbetűssé alakítja, és szóközöket ad a [jogkivonatok,](luis-language-support.md#tokenization) például a kötőjelek körül.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Szándék-előrejelzési hibák 

Egy példa utterance (kifejezés) egy szándék lehet egy szándék előrejelzési hiba a szándék a példa utterance (kifejezés) jelenleg, és a szándék a képzés során meghatározott. 

Az utterance (kifejezés) előrejelzési hibák megkereséséhez és javításához használja a Helytelen és a Nem egyértelmű **szűrő** beállításokat a **Részletes nézet** **Nézet** beállításával kombinálva. 

![Az utterance (kifejezés) előrejelzési hibák megkereséséhez és javításához használja a Szűrő beállítást.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

A szűrők és a nézet alkalmazásakor, és vannak példa kimondott hibák, a példa utterance (kifejezés) lista az utterances és a problémákat.

> [!div class="mx-imgBorder"]
> ![! [A szűrők és a nézet alkalmazásakor, és vannak hibákat tartalmazó példa utterances, a példa utterance (kifejezés) lista az utterances és a problémákat jeleníti meg.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Minden sor az aktuális betanítási pontszám a példa utterance (kifejezés) a példa utterance (kifejezés) a legközelebbi rivális pontszám, amely a különbség a két pontszámok. 

### <a name="fixing-intents"></a>Leképezések rögzítése

A szándékelőrejelzési hibák javításáról az [Összefoglaló irányítópulton](luis-how-to-use-dashboard.md)olvashat. Az összefoglaló irányítópult elemzést nyújt az aktív verzió utolsó betanításához, és a modell javításához a legfontosabb javaslatokat kínálja.  

## <a name="using-the-contextual-toolbar"></a>A környezetfüggő eszköztár használata

A környezet eszköztár a következő műveleteket tartalmazza:

* Példakimondott szöveg szerkesztése vagy törlése
* Példa utterance (kifejezés) hozzárendelése egy másik szándékhoz
* Szűrők és nézetek: csak szűrt entitásokat tartalmazó kimondott szövegeket jelenít meg, vagy nem kötelező részleteket tekinthet meg
* Keresés példakimondott szövegben

## <a name="train-your-app-after-changing-model-with-intents"></a>Az alkalmazás betanítása a modell szándékokkal való módosítása után

A leképezések hozzáadása, szerkesztése vagy eltávolítása után [tanítsa be](luis-how-to-train.md) és [tegye közzé](luis-how-to-publish-app.md) az alkalmazást, hogy a módosítások a végponti lekérdezésekre is vonatkozjanak. Ne eddz minden apró váltás után. Vonat egy csoport módosítás után. 

## <a name="next-steps"></a>További lépések

További információ a [példakimondott szöveg entitások](luis-how-to-add-example-utterances.md) hozzáadásáról. 
