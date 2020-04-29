---
title: 'Oktatóanyag: összetett entitások oktatóanyaga – LUIS'
description: Ebben az oktatóanyagban egy összetett entitást ad hozzá, amellyel a különböző típusú kinyert adatmennyiségeket egyetlen tartalmazó entitásba csomagolhatja. Az ügyfélalkalmazások az adatok árukapcsolásával egyszerűen kigyűjthetik a kapcsolódó adatok különböző adattípusokban való kinyerését.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: 5b8185a56c54ec92ce8ceaf1cd029dd31f6e709c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478687"
---
# <a name="tutorial-group-and-extract-related-data"></a>Oktatóanyag: kapcsolódó adatok csoportosítása és kinyerése
Ebben az oktatóanyagban egy összetett entitást ad hozzá, amellyel a különböző típusú kinyert adatmennyiségeket egyetlen tartalmazó entitásba csomagolhatja. Az ügyfélalkalmazások az adatok árukapcsolásával egyszerűen kigyűjthetik a kapcsolódó adatok különböző adattípusokban való kinyerését.

Az összetett entitás célja, hogy a kapcsolódó entitásokat egy szülő kategóriába tartozó entitásba csoportosítsa. Az információk különálló entitásként léteznek az összetett létrehozása előtt.

Az összetett entitás jól illeszkedik az ilyen típusú adattípusokhoz, mert az alábbi adatmennyiségeket adja meg:

* Kapcsolódnak egymáshoz.
* Többféle típusú entitást használhat.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Szándék létrehozása
> * Összetett entitás hozzáadása
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Alkalmazás importálása – példa

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) az entitás listázása oktatóanyagból.

2. Importálja a JSON-t egy új alkalmazásba a [Luis portál](https://www.luis.ai)használatával.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `composite` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="composite-entity"></a>Összetett entitás

Ebben az alkalmazásban a részleg neve a **Department** List entitásban van definiálva, és szinonimákat tartalmaz.

A **TransferEmployeeToDepartment** célja, hogy egy alkalmazott hosszúságú kimondott szöveg egy új részlegbe.

Példa erre a szándékra a következő hosszúságú kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|John W. Smith áthelyezése a nyilvántartási részlegbe|
|Jill Jones átvitele az R&D-re|

Az áthelyezési kérelemnek tartalmaznia kell a részleg nevét és az alkalmazott nevét.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Adja hozzá a PersonName előre összeépített entitást, amely segítséget nyújt a közös adattípusok kinyeréséhez

A LUIS számos előre összeállított entitást biztosít a gyakori adatok kinyeréséhez.

1. Válassza a **Létrehozás** lehetőséget a felső navigációs menüben, majd válassza az **entitások** lehetőséget a bal oldali navigációs menüből.

1. Válassza a **Manage prebuilt entity** (Előre összeállított entitás kezelése) gombot.

1. Válassza a **[PersonName](luis-reference-prebuilt-person.md)** lehetőséget az előre elkészített entitások listájából, majd válassza a **kész**lehetőséget.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ez az entitás segít felvenni a nevek felismerését az ügyfélalkalmazás számára.

## <a name="create-composite-entity-from-example-utterances"></a>Összetett entitás létrehozása példa hosszúságú kimondott szöveg

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza a **TransferEmployeeToDepartment** lehetőséget a cél listából.

1. A részletek `place John Jackson in engineering`területen válassza ki a personName entitást `John Jackson`, majd válassza a **becsomagolás az összetett entitásban** lehetőséget az előugró menüben a következő részletek megjelenítéséhez.

    ![Képernyőkép a wrap Composite kiválasztásáról a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Ezután azonnal válassza ki az utolsó entitást `engineering` a teljes verzióban. Az összetett entitást jelölő kijelölt szavak alatt egy zöld sáv található. Az előugró menüben adja meg az összetett nevet `TransferEmployeeInfo` , majd válassza az ENTER billentyűt.

    ![Képernyőkép az összetett név beírásáról a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. **Milyen típusú entitást kíván létrehozni?** az összes szükséges mező a következő listában szerepel: `personName` és. `Department` Válassza a **Done** (Kész) lehetőséget. Figyelje meg, hogy az előre összeállított entitás (personName) hozzá lett adva az összetett entitáshoz. Ha egy összetett entitás kezdő és záró jogkivonatai között egy előre összeállított entitás látható, az összetett entitásnak tartalmaznia kell az előre elkészített entitásokat. Ha az előre elkészített entitások nem szerepelnek a csomagban, az összetett entitás nem megfelelően van megjósolva, hanem minden egyes elem.

    ![Képernyőkép az összetett név beírásáról a legördülő párbeszédpanelen](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Címke – példa hosszúságú kimondott szöveg összetett entitással

1. Az egyes példákban válassza ki a bal szélső entitást, amely a kompozitban legyen. Ezután válassza **a tördelés az összetett entitásban**lehetőséget.

1. Válassza ki az összetett entitás utolsó szavát, majd az előugró menüből válassza a **TransferEmployeeInfo** lehetőséget.

1. Győződjön meg arról, hogy a szándékban lévő összes hosszúságú kimondott szöveg az összetett entitással van megjelölve.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait

Az alkalmazás betanításához válassza a **betanítás**lehetőséget. A képzés alkalmazza a módosításokat, például az új entitásokat és a címkével ellátott hosszúságú kimondott szöveg az aktív modellre.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Jill Jones to DevOps`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a query.

    Mivel ez a teszt azt ellenőrzi, hogy a kompozit helyesen van-e kibontva, a tesztek tartalmazhatnak egy meglévő minta-kivonást vagy egy új megnyilatkozás. Egy jó teszt az összetett entitásban lévő összes alárendelt entitás belefoglalása.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Ez a Kimondás egy összetett entitások tömbjét adja vissza. Minden entitás típust és értéket kap. Az egyes alárendelt entitások pontosabb megkereséséhez használja az összetett tömb elemében a típus és az érték kombinációját, hogy megtalálja a megfelelő elemet az entitások tömbben.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások listázása – oktatóanyag](luis-quickstart-intents-only.md)
* [Összetett entitás](luis-concept-entity-types.md) fogalmi információi
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag egy összetett entitást hozott létre a meglévő entitások beágyazásához. Ez lehetővé teszi az ügyfélalkalmazás számára, hogy a beszélgetés folytatásához különböző adattípusokban lévő kapcsolódó adat csoportját keresse. Az emberi erőforrások alkalmazáshoz tartozó ügyfélalkalmazás megkérdezheti, hogy milyen napra és időpontra van szükség a költözés kezdéséhez és befejezéséhez. Azt is megteheti, hogy az áthelyezés más logisztikai adatait, például egy fizikai telefont.

> [!div class="nextstepaction"]
> [A nem biztos előrejelzések kijavítása a végpontok hosszúságú kimondott szöveg áttekintésével](luis-tutorial-review-endpoint-utterances.md)
