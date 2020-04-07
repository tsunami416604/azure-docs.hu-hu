---
title: 'Rövid útmutató: Tudásbázis tesztelése kötegelt kérdésekkel'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 3bc095d8949f177ccb6c4cc111ba4b272027904e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756696"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Rövid útmutató: Tudásbázis tesztelése kötegelt kérdésekkel és várt válaszokkal

A QnA Maker kötegelt tesztelési eszközzel tesztelheti a QnA Maker erőforrás tudásalapjait a várt válaszok, megbízhatósági pontszámok és többfordulatos kérések szempontjából.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Hozzon létre egy QnA Maker szolgáltatást,](create-publish-knowledge-base.md) vagy használjon egy meglévő szolgáltatást, amely az angol nyelvet használja.
* A [többfordulatos `.docx` mintafájl letöltése](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Töltse le a [kötegelt tesztelési eszközt,](https://aka.ms/qnamakerbatchtestingtool)és bontsa ki a végrehajtható fájlt a `.zip` fájlból.

## <a name="sign-into-qna-maker-portal"></a>Bejelentkezés a QnA Maker portálra

[Jelentkezzen be](https://www.qnamaker.ai/) a QnA Maker portálra.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Új tudásbázis létrehozása a többfordulatos sample.docx fájlból

1. Válassza **a Tudásbázis létrehozása** az eszköztáron lehetőséget.
1. **Lépés** kihagyása, mert már rendelkeznie kell egy QnA Maker erőforrással, majd a **2.**
    * Az Azure Active Directory azonosítója
    * Az Azure-előfizetés neve
    * Az Azure QnA szolgáltatás neve
    * Nyelv - az angol nyelv
1. Írja be `Multi-turn batch test quickstart` a nevét a tudásbázis neveként.

1. **A 4.**

    |Beállítás|Érték|
    |--|--|
    |**Többfordulatos kinyerés engedélyezése URL-címekből, .pdf vagy .docx fájlokból.**|Bejelölve|
    |**Alapértelmezett válaszszöveg**| `Batch test - default answer not found.`|
    |**+ Fájl hozzáadása**|Válassza ki `.docx` a letöltött fájl listát az előfeltételek között.|
    |**Chit-chat**|Válassza a **Professional** lehetőséget|

1. **Az 5.** **Create your KB**

    Amikor a létrehozási folyamat befejeződik, a portál megjeleníti a szerkeszthető tudásbázist.

## <a name="save-train-and-publish-knowledge-base"></a>Tudásbázis mentése, betanítása és közzététele

1. A tudásbázis mentéséhez válassza a **Mentés és betanítás** lehetőséget az eszköztáron.
1. Válassza a **Közzététel** lehetőséget az eszköztáron, majd a tudásbázis közzétételéhez kattintson ismét a **Közzététel** gombra. A közzététel elérhetővé teszi a tudásbázist egy nyilvános URL-végpontlekérdezéseihez. Ha a közzététel befejeződött, mentse a gazdaURL-cím- és végpontkulcs-adatokat a **Közzététel** lapon.

    |Kötelező adatok| Példa|
    |--|--|
    |Közzétett állomás|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Közzétett kulcs|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 karaktersorozat `Endpoint` után )|
    |Alkalmazásazonosító|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 karakteres karakterlánc `POST`a) részeként |

## <a name="create-batch-test-file-with-question-ids"></a>Kötegelt tesztfájl létrehozása kérdésazonosítókkal

A kötegteszteszköz használatához hozzon létre `batch-test-data-1.tsv` egy szövegszerkesztővel megnevezett fájlt. A fájlnak a következő oszlopokat egy lappal kell elválasztania.

|TSV bemeneti fájl mezők|Megjegyzések|Példa|
|--|--|--|
|Tudásbázis azonosítója|A tudásbázis-azonosító a Közzététel lapon található. Egyszerre több tudásbázist teszteljen ugyanabban a szolgáltatásban egyetlen fájlban, különböző tudásbázisazonosítók használatával egyetlen fájlban.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 karakteres karakterlánc `POST`a) részeként |
|Kérdés|A felhasználó által beírt kérdésszöveg. Legfeljebb 1000 karakter.|`How do I sign out?`|
|Metaadatcímkék|választható|`topic:power`a _kulcs:érték_ formátumot használja|
|Felső paraméter|választható|`25`|
|Várt válaszazonosító|választható|`13`|

Ehhez a tudásbázishoz adjon hozzá 3 sort a fájlhoz a 2 szükséges oszlopból. Az első oszlop a tudásbázis-azonosító, a második oszlop pedig a következő kérdéslista:

|2. oszlop - kérdések|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Ezek a kérdések a tudásbázis pontos megfogalmazásai, és a megbízhatósági pontszámként 100-at kell visszaadniuk.

Ezután adjunk hozzá néhány kérdést, hasonlóan ezekhez a kérdésekhez, de nem pontosan ugyanaz a 3 további sorok, ugyanazt a tudásbázis-azonosítót használva:

|2. oszlop - kérdések|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Győződjön meg arról, hogy minden oszlopot csak tabulátorral választanak el. A program kezdő vagy záró szóközöket ad az oszlopadatokhoz, és a program kivételeket okoz, ha a típus vagy a méret helytelen.

A kötegelt tesztfájl az Excel programban való megnyitásakor az alábbi képhez hasonlóan néz ki. A tudásbázis-azonosítót biztonsági `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` okokból kicserélték. A saját kötegteszthez győződjön meg arról, hogy az oszlop megjeleníti a tudásbázis-azonosítót.

> [!div class="mx-imgBorder"]
> ![A .tsv fájl első verziójának bevitele kötegelt tesztből](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>A kötegfájl tesztelése

Futtassa a kötegelt tesztelési programot a következő CLI formátumban a parancssorból.

Cserélje `YOUR-RESOURCE-NAME` `ENDPOINT-KEY` le, és a saját értékeit a szolgáltatás nevét és végpontkulcs. Ezek az értékek a QnA Maker portál **Beállítások** lapján találhatók.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
A teszt befejeződik, `out.tsv` és létrehozza a fájlt:

> [!div class="mx-imgBorder"]
> ![A .tsv fájl első verziójának kimenete kötegelt tesztből](../media/batch-test/batch-test-1-output.png)

A tudásbázis-azonosítót biztonsági `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` okokból kicserélték. A saját kötegteszthez az oszlop a tudásbázis-azonosítót jeleníti meg.

Oszlopban a megbízhatósági pontszám tesztkimenete a vártnak megfelelően 100-as pontszámot adott vissza az első 3 kérdésre, mert minden kérdés pontosan ugyanaz, mint a tudásbázisban. Az utolsó 3 kérdés, az új megfogalmazás a kérdés, nem tér vissza 100, mint a bizalom pontszámot. Annak érdekében, hogy növelje a pontszám mind a teszt, és a felhasználók, hozzá kell adnia további alternatív kérdéseket a tudásbázishoz.

## <a name="testing-with-the-optional-fields"></a>Tesztelés az opcionális mezőkkel

Miután megértette a formátumot és a folyamatot, létrehozhat egy tesztfájlt, amely a tudásbázison keresztül fut egy adatforrásból, például a csevegési naplókból.

Mivel az adatforrás és a folyamat automatizált, a tesztfájl többször is futtatható különböző beállításokkal a helyes értékek meghatározásához.

Ha például rendelkezik csevegőnaplóval, és meg szeretné határozni, hogy melyik csevegőnapló szövege melyik metaadatmezőkre vonatkozik, hozzon létre egy tesztfájlt, és állítsa be a metaadatmezőket minden sorhoz. Futtassa a tesztet, majd tekintse át a metaadatoknak megfelelő sorokat. Általában a mérkőzések pozitívnak kell lenniük, de felül kell vizsgálnia az eredményeket a hamis pozitív. A hamis pozitív egy sor, amely megfelel a metaadatoknak, de a szöveg alapján nem egyezhet.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Választható mezők használata a bemeneti kötegtesztfájlban

Az alábbi diagram segítségével megtudhatja, hogyan keresheti meg a választható adatok mezőértékeit.

|Oszlop száma|Választható oszlop|Az adatok helye|
|--|--|--|
|3|metaadatok|Meglévő tudásbázis exportálása meglévő _kulcs:értékpárokhoz._|
|4|felül|Az alapértelmezett `25` érték ajánlott.|
|5|Kérdés-válasz készlet azonosítója|Meglévő tudásbázis exportálása azonosítóértékekhez. Azt is figyelje meg, hogy az azonosítók visszakerültek a kimeneti fájlba.|

## <a name="add-metadata-to-the-knowledge-base"></a>Metaadatok hozzáadása a tudásbázishoz

1. A QnA portál **Szerkesztés** lapján adja hozzá a `topic:power` következő kérdések metaadatait:

    |Kérdések|
    |--|
    |A Surface Pro 4 töltése|
    |Az akkumulátor töltöttségi szintjének ellenőrzése|

    Két QnA-pár metaadat-készlete van.

    > [!TIP]
    > Az egyes csoportok metaadatait és QnA-azonosítóit exportálni a tudásbázist. Jelölje ki a **Beállítások** lapot, `.xls` majd az **Exportálás** fájlként lehetőséget. Keresse meg ezt a letöltött fájlt, és nyissa meg az Excel metaadatokat és -azonosítót.

1. Válassza a **Mentés és betanítás**lehetőséget, majd a **Közzététel** lapot, majd a **Közzététel** gombot. Ezek a műveletek elérhetővé teszik a módosítást a kötegteszt számára. Töltse le a tudásbázist a **Beállítások** lapról.

    A letöltött fájl megfelelő formátumú a metaadatokhoz, és a helyes kérdés-válasz azonosító. A következő szakaszban lévő mezők használata

    > [!div class="mx-imgBorder"]
    > ![Exportált tudásbázis metaadatokkal](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Második kötegteszt létrehozása

A kötegelt tesztelésnek két fő forgatókönyve van:
* **Folyamat chat log fájlok -** Határozza meg a felső válasz egy korábban nem látott kérdés - a leggyakoribb helyzet az, amikor fel kell dolgozni a naplófájl a lekérdezések, mint például a chat bot felhasználói kérdéseire. Kötegfájl-teszt létrehozása, csak a szükséges oszlopokkal. A teszt minden kérdésre a legjobb választ adja vissza. Ez nem jelenti azt, hogy a legjobb válasz a helyes válasz. Miután befejezte ezt a tesztet, lépjen tovább az érvényesítési tesztre.
* **Érvényesítési teszt** – A várt válasz ellenőrzése. Ez a teszt megköveteli, hogy a kötegtesztben a várt összes kérdés és egyeztetési válasz érvényesítve legyen. Ehhez szükség lehet némi kézi folyamatra.

A következő eljárás feltételezi, hogy a forgatókönyv a csevegési naplók

1. Hozzon létre egy új kötegtesztfájlt, amely nem kötelező adatokat tartalmaz, `batch-test-data-2.tsv`. Adja hozzá az eredeti kötegteszt bemeneti fájljának 6 sorát, majd adja hozzá a metaadatokat, a felső és a QnA-párazonosítót minden sorhoz.

    A csevegőnaplókból származó új szövegek tudásbázissal való ellenőrzésének automatikus szimulálásához állítsa az `topic:power`egyes oszlopok metaadatait ugyanarra az értékre: .

    > [!div class="mx-imgBorder"]
    > ![A .tsv fájl második verziójának bevitele kötegelt tesztből](../media/batch-test/batch-test-2-input.png)

1. Futtassa újra a tesztet, és módosítsa a bemeneti és kimeneti fájlneveket, jelezve, hogy ez a második teszt.

    > [!div class="mx-imgBorder"]
    > ![A .tsv fájl második verziójának kimenete kötegelt tesztből](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Vizsgálati eredmények és automatizált vizsgálati rendszer

Ez a tesztkimeneti fájl egy automatizált folyamatos tesztfolyamat részeként elemezhető.

Ezt a konkrét tesztkimenetet a következőképpen kell értelmezni: minden sor metaadatokkal lett szűrve, és mivel minden sor nem felelt meg a tudásbázis metaadatainak, a visszaadott nem egyező sorokra vonatkozó alapértelmezett válasz ("nem található megfelelő egyezés a kb-ban"). Azok közül a sorok közül, amelyek megegyeztek, a QnA-azonosítót és a pontszámot visszaadták.

Minden sor helytelen címkét adott vissza, mert egyetlen sor sem felelt meg a várt válaszazonosítónak.

Ezekkel az eredményekkel látnia kell, hogy csevegőnaplót vehet fel, és a szöveget használhatja az egyes sorok lekérdezéseként. Anélkül, hogy bármit is tudnánk az adatokról, az eredmények sokat elárulnak azokról az adatokról, amelyeket aztán használhat az előrelépéshez:

* metaadatok
* QnA azonosító
* pontszám

Jó ötlet volt a metaadatokkal történő szűrés a teszthez? Igen és nem. A tesztrendszernek tesztfájlokat kell létrehoznia minden egyes metaadat-párhoz, valamint egy metaadat-pár nélküli tesztet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a tudásbázis tesztelését, törölje a kötegfájl-eszközt és a tesztfájlokat.

Ha nem fogja tovább használni ezt a tudásbázist, törölje a tudásbázist a következő lépésekkel:

1. A QnA Maker portálon válassza a Legfelső menü **Saját tudásbázisok** lehetőséget.
1. A tudásbázisok listájában válassza a **Törlés** ikont a rövid útmutató tudásbázisának sorában.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
