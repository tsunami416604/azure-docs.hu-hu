---
title: 'Oktatóanyag: a Batch tesztelése a hibák kereséséhez – LUIS'
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag azt mutatja be, hogyan használhatók a Batch-tesztek az alkalmazásban felmerülő előrejelzési problémák megkereséséhez és a kijavításához.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 68a0016e034f4642c4e4ff166a1456f7ecf1ee3c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904235"
---
# <a name="tutorial-batch-test-data-sets"></a>Oktatóanyag: batch test adatkészletek

Ez az oktatóanyag azt mutatja be, hogyan használhatók a Batch-tesztek az alkalmazásban felmerülő előrejelzési problémák megkereséséhez és a kijavításához.  

A Batch Testing lehetővé teszi az aktív, betanított modell állapotának érvényesítését a címkézett hosszúságú kimondott szöveg és entitások ismert készletével. A JSON-formátumú batch-fájlban adja hozzá a hosszúságú kimondott szöveg, és állítsa be az entitások feliratait, amelyeknek a teljes kifejezésen belül kell lenniük. 

A Batch-tesztelésre vonatkozó követelmények:

* Legfeljebb 1000 hosszúságú kimondott szöveg. 
* Nincsenek ismétlődések. 
* Engedélyezett entitások típusai: az egyszerű és az összetett elemek csak a géppel megtanult entitások. A Batch-tesztelés csak a gépi megszerzett szándékok és entitások esetén hasznos.

Ha az oktatóanyagtól eltérő *alkalmazást használ, ne használja a* hosszúságú kimondott szöveg már hozzáadott példát. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Batch-teszt fájl létrehozása 
> * Batch-teszt futtatása
> * Tesztek eredményeinek áttekintése
> * Hibák javítása 
> * A köteg újratesztelése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Alkalmazás importálása – példa

Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Ehhez a következő lépések szükségesek:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `batchtest` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes. 

4. Tanítsa be az alkalmazást.

## <a name="batch-file"></a>Batch-fájl

1. Hozzon létre `HumanResources-jobs-batch.json` egy szövegszerkesztőben, vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) . 

2. A JSON-formátumú batch-fájlban adja hozzá a hosszúságú kimondott szöveg a tesztben előre jelezni kívánt **szándékkal** . 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>A köteg futtatása

1. Válassza a **teszt** lehetőséget a felső navigációs sávon. 

2. Válassza a **Batch-tesztelés panelt** a jobb oldali panelen. 

    [![a LUIS alkalmazás képernyőképe a Batch test panel kiemelésével](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Válassza az **adatkészlet importálása**lehetőséget.

    [![a LUIS-alkalmazás képernyőképét az importálási adatkészlet kiemelésével](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Válassza ki a `HumanResources-jobs-batch.json` fájljának helyét.

5. Nevezze el az adathalmazt `intents only` majd válassza a **kész**lehetőséget.

    ![Fájl kiválasztása](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Kattintson a **Futtatás** gombra. 

7. Válassza az **eredmények megtekintése**lehetőséget.

8. Tekintse át az eredményeket a gráfban és a jelmagyarázatban.

    [a LUIS-alkalmazás ![képernyőképe a Batch-tesztek eredményeivel](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>A Batch eredményeinek áttekintése

A Batch-diagram az eredmények négy negyedét jeleníti meg. A diagram jobb oldalán egy szűrő látható. A szűrő leképezéseket és entitásokat tartalmaz. Amikor kijelöl egy [szakaszt](luis-concept-batch-test.md#batch-test-results) a diagramon, vagy egy pontot a diagramon belül, a társított kimaradás (ek) a diagram alatt látható. 

Amikor a diagram fölé viszi a kurzort, az egér kereke megnövelheti vagy csökkentheti a diagram megjelenítését. Ez akkor hasznos, ha a diagramon több pont is van, és szorosan össze van kapcsolva. 

A diagram négy részből áll, és a piros színnel megjelenő két részből áll. **Ezek a csoportok, amelyekre összpontosítani kell**. 

### <a name="getjobinformation-test-results"></a>GetJobInformation-tesztek eredményei

A szűrőben megjelenített **GetJobInformation** -teszt eredményei azt mutatják, hogy a négy előrejelzés közül 2 sikeres volt. A diagram alatti hosszúságú kimondott szöveg megtekintéséhez válassza a **hamis negatív** nevet a bal alsó sarokban. 

A CTRL + E billentyűkombinációval váltson a felirat nézetre, és tekintse meg a felhasználó teljes szövegét. 

A kimaradás `Is there a database position open in Los Colinas?` _GetJobInformation_ van megjelölve, de az aktuális modell a _ApplyForJob_. 

A **ApplyForJob** -nál majdnem háromszor annyi példa van, mint a **GetJobInformation**. Ez a hosszúságú kimondott szöveg a **ApplyForJob** -cél előnyben tart, ami a helytelen előrejelzést eredményezi. 

Figyelje meg, hogy mindkét leképezés azonos számú hibát tartalmaz. Az egyik szándékban helytelen előrejelzés a másik szándékot is érinti. Mindkettő hibákba ütközik, mert a hosszúságú kimondott szöveg helytelenül lettek jelezve egy szándékhoz, és helytelenül nem jósolták meg a másik szándékot. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Az alkalmazás javítása

Ennek a szakasznak a célja, hogy az alkalmazás kijavításával minden hosszúságú kimondott szöveg megfelelően előre megjósolható legyen a **GetJobInformation** számára. 

A látszólagos gyors javítás a megfelelő szándékhoz adja hozzá a batch-fájl hosszúságú kimondott szöveg. Ez nem az, amit szeretne tenni. Azt szeretné, hogy a LUIS helyesen előre megjósolja ezeket a hosszúságú kimondott szöveg, anélkül, hogy példákat adna hozzájuk. 

A hosszúságú kimondott szöveg a **ApplyForJob** -ből való eltávolítására is kíváncsi lehet, amíg a teljes mennyiség nem egyezik meg a **GetJobInformation**. Ez javíthatja a teszt eredményeit, azonban a LUIS-t a következő időpontra való pontos előrejelzés alapján akadályozhatja. 

A javítás célja, hogy további hosszúságú kimondott szöveg adjon hozzá a **GetJobInformation**-hoz. Ne felejtse el, hogy eltér a teljes hossz, a Word Choice és a Word megegyezése _, miközben_ továbbra is a feladatra vonatkozó információk keresésének szándékát célozza meg.

### <a name="add-more-utterances"></a>További hosszúságú kimondott szöveg hozzáadása

1. A felső navigációs panelen kattintson a **teszt** gombra a Batch-teszt panel bezárásához. 

2. Válassza a **GetJobInformation** lehetőséget a cél listából. 

3. Vegyen fel további hosszúságú kimondott szöveg, amelyek a hossz, a Word Choice és a Word-elrendezés esetében eltérőek, és ügyeljen arra, hogy tartalmazza a `resume`, a `c.v.`és a `apply`feltételeit:

    |Példa a **GetJobInformation** szándékának hosszúságú kimondott szöveg|
    |--|
    |Az új feladatot a tárházban egy Stocker megköveteli, hogy egy folytatást alkalmazzon?|
    |Hol vannak jelenleg a tetőfedési feladatok?|
    |Hallottam, hogy egy orvosi kódolási feladatokra van szükség, amely folytatást igényel.|
    |Szeretnék egy feladatot segíteni a kollégium gyermekeinek, hogy megírják a c.v.s. |
    |Itt látható a folytatás, új bejegyzés keresése a közösségi Egyetemen a számítógépek használatával.|
    |Milyen pozíciók érhetők el a gyermek-és otthoni gondozásban?|
    |Van egy Intern Desk az újságban?|
    |Saját azt mutatja, hogy jó vagyok a beszerzések, a költségvetések és az elveszett pénzmennyiség elemzéséhez. Van valami ehhez a típusú munkához?|
    |Hol vannak most a föld fúrási feladatai?|
    |8 évig dolgoztam EMS-illesztőprogramként. Minden új feladat?|
    |Az új élelmiszer-kezelési feladatokhoz alkalmazás szükséges?|
    |Hány új munkafeladat érhető el?|
    |Van új HR-bejegyzés a munkakapcsolatokhoz és a tárgyalásokhoz?|
    |A könyvtár és az Archívum kezelése főkiszolgálóval rendelkezik. Minden új pozíció?|
    |Van olyan gyermekfelügyeleti feladat, amely 13 éves korban van ma a városban?|

    Ne címkézze fel a **feladattípust** a hosszúságú kimondott szöveg. Az oktatóanyag ezen szakasza kizárólag a szándék-előrejelzésre koncentrál.

4. Az alkalmazás betanításához válassza a jobb felső navigációs sávon a **betanítás** lehetőséget.

## <a name="verify-the-new-model"></a>Az új modell ellenőrzése

Annak ellenőrzéséhez, hogy a Batch-teszt hosszúságú kimondott szöveg megfelelően vannak-e előre jelezve, futtassa újra a Batch-tesztet.

1. Válassza a **teszt** lehetőséget a felső navigációs sávon. Ha a Batch-eredmények továbbra is nyitva vannak, válassza a **vissza a listához**lehetőséget.  

1. A Batch neve mellett kattintson a három pont (***..***.) gombra, és válassza a **Futtatás**lehetőséget. Várjon, amíg a Batch-teszt elkészült. Figyelje meg, hogy az **eredmények megtekintése** gomb most zöld. Ez azt jelenti, hogy a teljes köteg sikeresen futott.

1. Válassza az **eredmények megtekintése**lehetőséget. A szándékoknak mind zöld ikonokkal kell rendelkezniük a leképezési nevek bal oldalán. 

## <a name="create-batch-file-with-entities"></a>Batch-fájl létrehozása entitásokkal 

A Batch-tesztben lévő entitások ellenőrzéséhez az entitásokat a Batch JSON-fájljában kell megcímkézni. 

A teljes Word-([token](luis-glossary.md#token)-) darabszám entitások variációja hatással lehet a jóslat minőségére. Győződjön meg arról, hogy a megcímkézett hosszúságú kimondott szöveg megjelenő kiképzési információk számos különböző típusú entitást tartalmaznak. 

A Batch-fájlok első írásakor és tesztelésekor érdemes megkezdeni néhány hosszúságú kimondott szöveg és az Ön által jól ismert entitásokat, valamint néhányat, hogy azt gondolja, hogy nem megfelelően van-e előre jelezve. Ez segítséget nyújt a problémás területek gyors kiépítésében. Miután tesztelte a **GetJobInformation** és a **ApplyForJob** -leképezéseket több különböző feladatnév használatával, amelyek nem lettek előre jelezve, a rendszer kidolgozta ezt a Batch-tesztoldalt, hogy ellenőrizze, van-e előrejelzési probléma a **feladatok** entitásának bizonyos értékeivel. 

Egy, a teszt hosszúságú kimondott szöveg megadott **feladathoz** tartozó entitás értéke általában egy vagy két szó, és néhány példa több szót is tartalmaz. Ha _a saját_ emberi erőforrás-alkalmazásnak általában sok szó van a feladatokhoz, akkor az alkalmazásban a hosszúságú kimondott szöveg címkével **ellátott példa** nem fog megfelelően működni.

1. Hozzon létre `HumanResources-entities-batch.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/) , vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) .

2. A JSON-formátumú batch-fájlban adjon hozzá olyan objektumokat tartalmazó tömböt, amelyek a tesztben előre jelezni kívánt **szándékot** , valamint a teljes hosszúságú kimondott szöveg lévő entitások helyét tartalmazzák. Mivel az entitás jogkivonat-alapú, mindenképpen indítsa el és állítsa le az egyes entitásokat a karaktereken. Ne kezdjen el vagy fejezze be a teljes területet. Ez hibát okoz a batch-fájl importálása során.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>A Batch futtatása entitásokkal

1. Válassza a **teszt** lehetőséget a felső navigációs sávon. 

2. Válassza a **Batch-tesztelés panelt** a jobb oldali panelen. 

3. Válassza az **adatkészlet importálása**lehetőséget.

4. Válassza ki a `HumanResources-entities-batch.json` fájl fájlrendszerének helyét.

5. Nevezze el az adathalmazt `entities` majd válassza a **kész**lehetőséget.

6. Kattintson a **Futtatás** gombra. Várjon, amíg a teszt elkészült.

7. Válassza az **eredmények megtekintése**lehetőséget.

## <a name="review-entity-batch-results"></a>Entitás-köteg eredményeinek áttekintése

Ekkor megnyílik a diagram az összes olyan szándékkal, amely megfelelően előre jelzett. Görgessen lefelé a jobb oldali szűrőben, és keresse meg az entitások előrejelzéseit a hibákkal. 

1. Válassza ki a szűrőben a **feladatok** entitást.

    ![Hiba az entitások előrejelzésében a szűrőben](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    A diagram megváltoztatja az entitás-előrejelzések megjelenítését. 

2. Válassza a **hamis negatív** elemet a diagram alsó, bal oldali negyedében. Ezután a billentyűzet kombináció vezérlő + E használatával váltson át a jogkivonat nézetre. 

    [entitás-előrejelzések ![jogkivonat-nézete](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    A diagram alatti hosszúságú kimondott szöveg áttekintése konzisztens hibát mutat be, ha a feladatnév `SQL`tartalmaz. A példa hosszúságú kimondott szöveg és a feladattípusok listájának áttekintéséhez az SQL csak egyszer használatos, és csak egy nagyobb feladatnév részeként `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Az alkalmazás kijavítása az entitás batch-eredményei alapján

Az alkalmazás kijavításához az szükséges, hogy a LUIS helyesen határozza meg az SQL-feladatok változatait. A javításnak több lehetősége is van. 

* Explicit módon adjon hozzá több példát a hosszúságú kimondott szöveg, amelyek az SQL-t használják, és felcímkézik ezeket a szavakat feladatokként. 
* További SQL-feladatok explicit hozzáadása a kifejezések listájához

Ezeket a feladatokat elvégezheti.

Egy [minta](luis-concept-patterns.md) hozzáadása az entitás megfelelő előrejelzése előtt nem fogja elhárítani a problémát. Ennek az az oka, hogy a minta nem egyezik, amíg a rendszer a minta összes entitását észleli. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag egy batch-tesztet használt az aktuális modellel kapcsolatos problémák kereséséhez. A modell meg lett javítva, és újra lett tesztelve a Batch-fájllal, hogy ellenőrizze a módosítás helyességét.

> [!div class="nextstepaction"]
> [A minták ismertetése](luis-tutorial-pattern.md)

