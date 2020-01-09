---
title: 'Gyors útmutató: Tudásbázis tesztelése batch-kérdésekkel'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/19/2019
ms.author: diberry
ms.openlocfilehash: 9483db2187c05fe8e0f4fa2d41c17b8748ba3db7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474038"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Gyors útmutató: Tudásbázis tesztelése batch-kérdésekkel és várt válaszokkal

A QnA Maker batch Testing Tool eszközzel tesztelheti a QnA Maker erőforrás tudásbázisait a várt válaszok, a megbízhatósági pontszámok és a több bekapcsolási kérések esetében.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Hozzon létre egy QnA Maker szolgáltatást](create-publish-knowledge-base.md#create-a-new-qna-maker-knowledge-base) , vagy használjon egy meglévő szolgáltatást, amely az angol nyelvet használja az ebben a rövid útmutatóban használt minta doc-hoz.
* A [multi-turn minta `.docx` fájl](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) letöltése
* Töltse le a [Batch Testing eszközt](https://aka.ms/qnamakerbatchtestingtool), bontsa ki a végrehajtható fájlt a `.zip` fájlból.

## <a name="sign-into-qna-maker-portal"></a>Bejelentkezés QnA Maker portálra

[Jelentkezzen be](https://www.qnamaker.ai/) a QnA Maker portálra.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Hozzon létre egy új tudásbázist a multi-turn sample. docx fájlból

1. Válassza a **Tudásbázis létrehozása** elemet az eszköztáron.
1. Hagyja ki az **1. lépést** , mert már rendelkeznie kell egy QnA Maker erőforrással, a **2. lépésre** való áttéréssel pedig kiválaszthatja a meglévő erőforrás-információkat:
    * Azure Active Directory azonosítója
    * Azure-előfizetés neve
    * Azure QnA szolgáltatás neve
    * Nyelv – az angol nyelv
1. Adja meg a Tudásbázis neve `Multi-turn batch test quickstart` nevet.
1. A **4. lépésben**jelölje be az **URL-címek,. PDF-vagy. docx-fájlok többszörös kibontásának engedélyezése**lehetőséget.
1. Adja meg `Quickstart - can't find answer`**alapértelmezett válaszának szövegét** . Az éles Tudásbázisban ezeknek az információknak jobban kell megjelenniük a felhasználónak, de ebben a rövid útmutatóban egy egyszerű válasz működik.
1. Továbbra is a **4. lépésben**válassza a **+ fájl hozzáadása** lehetőséget, majd válassza ki a letöltött `.docx` fájl listáját az előfeltételek között.
1. Az **5. lépésben**válassza **a saját kb létrehozása**lehetőséget.

    A létrehozási folyamat befejeződése után a portálon megjelenik a szerkeszthető Tudásbázis.

## <a name="save-train-and-publish-knowledge-base"></a>Tudásbázis mentése, betanítása és közzététele

1. A Tudásbázis mentéséhez válassza a **Mentés és a betanítás** lehetőséget az eszköztárból.
1. Válassza a **Közzététel** lehetőséget az eszköztáron, majd válassza a **Közzététel** újra lehetőséget a Tudásbázis közzétételéhez. A közzététel elérhetővé teszi a tudásbázist egy nyilvános URL-végpontról érkező lekérdezések számára. A közzététel befejezésekor mentse a **közzétételi lapon** megjelenő gazdagép URL-címét és a végponti kulcs információit.

    |Szükséges adatértékek| Példa|
    |--|--|
    |Közzétett gazdagép|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Közzétett kulcs|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (a `Endpoint` után megjelenő 32 karakteres karakterlánc)|
    |Alkalmazásazonosító|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (a `POST`részeként megjelenő 36 karakteres karakterlánc) |

## <a name="create-batch-test-file-with-question-ids"></a>Batch-tesztkörnyezet létrehozása a kérdés-azonosítókkal

A Batch test eszköz használatához hozzon létre egy `batch-test-data-1.tsv` nevű fájlt egy szövegszerkesztővel. A fájlnak a következő oszlopokat kell elválasztania egy lapon.

|TSV bemeneti fájl mezői|Megjegyzések|Példa|
|--|--|--|
|Tudásbázis-azonosító|A Tudásbázis-azonosító a közzétételi oldalon található. Egy adott szolgáltatásban egyszerre több tudásbázist is kipróbálhat egyetlen fájlban, ha különböző Tudásbázis-azonosítókat használ egyetlen fájlban.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (a `POST`részeként megjelenő 36 karakteres karakterlánc) |
|Kérdés|A felhasználó által megadott kérdés szövege. 1 000 karakter max.|`How do I sign out?`|
|Metaadatcímkék|választható|`topic:power` a _kulcsot használja: Value_ Format|
|Felső paraméter|választható|`25`|
|Várt válasz azonosítója|választható|`13`|

Ebben a Tudásbázisban vegyen fel 3 sort csak a 2 szükséges oszlopból a fájlba. Az első oszlop a Tudásbázis-azonosító, a második oszlop pedig a következő kérdéseket tartalmazza:

|2\. oszlop – kérdések|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Ezek a kérdések a Tudásbázis pontos szövege, és a 100 értéket kell visszaadni a megbízhatósági pontszámnak.

Ezután vegyen fel néhány kérdést, amely hasonló a kérdésekhez, de nem pontosan ugyanaz, mint 3 további sorban, ugyanazzal a Tudásbázis-AZONOSÍTÓval:

|2\. oszlop – kérdések|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Győződjön meg arról, hogy az egyes oszlopok csak tabulátorral vannak elválasztva. A kezdő vagy záró szóközök bekerülnek az oszlopba, és a program kivételeket okoz, ha a típus vagy a méret helytelen.

A Batch-tesztüzenet az Excelben való megnyitásakor az alábbi képhez hasonlóan néz ki. A Tudásbázis AZONOSÍTÓját a rendszer a biztonság `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` váltotta fel. Saját batch-teszt esetén győződjön meg arról, hogy az oszlop megjeleníti a Tudásbázis AZONOSÍTÓját.

> [!div class="mx-imgBorder"]
> ![a. TSV fájl első verzióját a Batch-tesztből](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>A batch-fájl tesztelése

Futtassa a Batch Testing programot a parancssorban az alábbi CLI-formátum használatával.

Cserélje le a `YOUR-RESOURCE-NAME` és a `ENDPOINT-KEY`t a szolgáltatás nevére és a végponti kulcsra vonatkozó saját értékekre. Ezek az értékek a QnA Maker portál **Beállítások** lapján találhatók.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
A teszt befejeződött, és létrehozza a `out.tsv` fájlt:

> [!div class="mx-imgBorder"]
> ![a. TSV fájl kimeneti első verzióját a Batch testből](../media/batch-test/batch-test-1-output.png)

A Tudásbázis AZONOSÍTÓját a rendszer a biztonság `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` váltotta fel. A saját batch-teszt esetében az oszlop megjeleníti a Tudásbázis AZONOSÍTÓját.

A megbízhatósági pontszám teszt kimenete, a 4. oszlopban látható, hogy az első 3 kérdés a várt 100 pontszámot adta vissza, mivel minden kérdés pontosan ugyanaz, mint a Tudásbázisban. Az utolsó 3 kérdés, amelynek új szövege a kérdés, a 100 nem ad vissza megbízhatósági pontszámként. Ahhoz, hogy a teszthez és a felhasználókhoz is növelje a pontszámot, további alternatív kérdéseket kell felvennie a tudásbázisba.

## <a name="testing-with-the-optional-fields"></a>Tesztelés a választható mezőkkel

Ha megértette a formátumot és a folyamatot, létrehozhat egy tesztelési fájlt, amellyel a Tudásbázisban futtathat egy adatforrásból, például a csevegési naplókból.

Mivel az adatforrás és a folyamat automatizált, a tesztoldal többször is futtatható különböző beállításokkal a megfelelő értékek meghatározásához.

Ha például van egy csevegési naplója, és meg szeretné határozni, hogy mely csevegési szöveg vonatkozzon a metaadatok mezőire, hozzon létre egy tesztoldalt, és állítsa be az összes sor metaadat-mezőit. Futtassa a tesztet, majd tekintse át a metaadatoknak megfelelő sorokat. Általában a egyezéseknek pozitívnak kell lenniük, de érdemes áttekintenie a hamis pozitív eredményeket. A hamis pozitív érték egy olyan sor, amely megfelel a metaadatoknak, de a szöveg alapján nem egyezik.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Választható mezők használata a bemeneti batch-teszt fájlban

A következő diagram segítségével megismerheti, hogyan keresheti meg a választható adatokhoz tartozó mezőértékeket.

|Oszlop száma|Nem kötelező oszlop|Az adatok helye|
|--|--|--|
|3|metaadatok|Meglévő Tudásbázis exportálása a meglévő _kulcshoz: érték_ párok.|
|4|felül|Az `25` alapértelmezett értéke ajánlott.|
|5|Kérdés-és Levelesláda-készlet azonosítója|A meglévő Tudásbázis exportálása az azonosító értékekhez. Azt is figyelje meg, hogy az azonosítók a kimeneti fájlban lettek visszaadva.|

## <a name="add-metadata-to-the-knowledge-base"></a>Metaadatok hozzáadása a tudásbázishoz

1. A QnA-portál **Szerkesztés** lapján adja hozzá a `topic:power` metaadatokat a következő kérdésekre:

    |Kérdések|
    |--|
    |A Surface Pro 4 feltöltése|
    |Az akkumulátor szintjének ellenõrzése|

    Két QnA-készlet rendelkezik a metaadatok készletével.

    > [!TIP]
    > Az egyes készletekhez tartozó metaadatok és QnA-azonosítók megtekintéséhez exportálja a tudásbázist. Válassza a **Beállítások** lapot, majd válassza az **Exportálás** `.xls` fájlként lehetőséget. Keresse meg ezt a letöltött fájlt, és nyissa meg az Excel-áttekintés a metaadatokhoz és az AZONOSÍTÓhoz.

1. Válassza a **Mentés és a betanítás**lehetőséget, majd válassza ki a **közzétételi** lapot, majd kattintson a **Közzététel** gombra. Ezek a műveletek a Batch-teszt számára elérhetővé teszik a módosítást. Töltse le a tudásbázist a **Beállítások** lapról.

    A letöltött fájl formátuma megfelelő a metaadatokhoz, valamint a helyes kérdés-és válaszfájl-AZONOSÍTÓhoz. Használja ezeket a mezőket a következő szakaszban

    > [!div class="mx-imgBorder"]
    > ![exportált Tudásbázis metaadatokkal](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Második batch-teszt létrehozása

A Batch tesztelésének két fő forgatókönyve van:
* **Csevegési naplófájlok feldolgozása** – a leggyakoribb válasz egy korábban láthatatlan kérdés – a leggyakoribb eset az, amikor a feldolgozáshoz a lekérdezéseket tartalmazó naplófájlt kell használnia, például egy csevegési robot felhasználói kérdéseit. Hozzon létre egy batch-fájl tesztet, amely csak a szükséges oszlopokkal rendelkezik. A teszt az egyes kérdések legfelső szintű válaszát adja vissza. Ez nem jelenti azt, hogy a legjobb válasz a helyes válasz. A teszt befejezése után lépjen be az érvényesítési tesztbe.
* **Ellenőrzési teszt** – a várt válasz ellenőrzése. Ehhez a teszthez meg kell határozni, hogy a Batch-tesztben szereplő összes kérdés és a hozzá tartozó várt válasz érvényesítve lett. Ehhez manuális folyamatra lehet szükség.

A következő eljárás azt feltételezi, hogy az a forgatókönyv a csevegési naplók feldolgozására 

1. Hozzon létre egy új batch-tesztelési fájlt, amely tartalmazza a választható adat`batch-test-data-2.tsv`. Adja hozzá a 6 sort az eredeti batch test bemeneti fájljához, majd adja hozzá a metaadatokat, a Top és a QnA set ID-t az egyes sorokhoz.

    Ha szimulálni szeretné az új szövegnek a tudásbázisból való beolvasása a csevegési naplókból lehetőséget, állítsa az egyes oszlopok metaadatait ugyanarra az értékre: `topic:power`.

    > [!div class="mx-imgBorder"]
    > ![. TSV fájl bemeneti második verziója a Batch test](../media/batch-test/batch-test-2-input.png)

1. Futtassa újra a tesztet, és módosítsa a bemeneti és kimeneti fájlneveket, hogy jelezze a második tesztet.

    > [!div class="mx-imgBorder"]
    > a. TSV fájl kimeneti második verziójának ![a Batch testből](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Tesztelési eredmények és automatizált tesztelési rendszer

Ez a teszt kimeneti fájl egy automatizált folyamatos tesztelési folyamat részeként elemezhető.

Az adott teszt kimenetét a következőként kell értelmezni: az egyes sorok metaadatokkal vannak szűrve, és mivel az egyes sorok nem egyeznek a tudásbázisban található metaadatokkal, az alapértelmezett válasz a nem egyező sorokhoz ("nem található megfelelő egyezés a KB-ban"). Azok a sorok, amelyek megfelelnek a egyezésnek, a QnA AZONOSÍTÓját és pontszámát adja vissza.

Az összes sor helytelen címkét adott vissza, mert egyetlen sor sem felelt meg a válasz AZONOSÍTÓjának.

Ezekkel az eredményekkel láthatja, hogy csevegési naplót szeretne készíteni, és a szöveget használja az egyes sorok lekérdezéséhez. Az eredmények nem ismerik fel az adatmennyiséget, így az eredmények sokat mondanak arról, hogy milyen információkat használhat a mozgó továbbítás:

* meta-adatszolgáltatások
* QnA-azonosító
* pontszám

A teszteléshez jó ötlet volt a meta-adattal való szűrés? Igen és nem. A tesztelési rendszernek létre kell hoznia minden egyes meta-adatpárokhoz tartozó tesztelési fájlt, valamint egy olyan tesztet, amely nem tartalmaz meta-adatpárokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a Tudásbázis tesztelését, törölje a Batch file eszközt és a tesztelési fájlokat.

Ha nem fogja tovább használni ezt a tudásbázist, törölje a tudásbázist a következő lépésekkel:

1. A QnA Maker portálon válassza a **saját Tudásbázis** lehetőséget a felső menüben.
1. A tudásbázisok listájában válassza a **Törlés** ikont a rövid útmutató tudásbázisának sorában.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
