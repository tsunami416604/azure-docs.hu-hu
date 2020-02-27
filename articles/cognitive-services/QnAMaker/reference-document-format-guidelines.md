---
title: A dokumentum formátumára vonatkozó irányelvek importálása – QnA Maker
description: Ismerje meg, hogy az URL-típusok hogyan használhatók az QnA-készletek importálásához és létrehozásához.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651838"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Az importált dokumentumokra és URL-címekre vonatkozó irányelvek formázása

Tekintse át ezeket a formázási irányelveket, hogy a lehető legjobb eredményeket kapja a tartalomhoz.

## <a name="formatting-considerations"></a>Formázási kapcsolatos szempontok

Fájl vagy URL importálása után QnA Maker átalakítja és tárolja a tartalmat a [Markdown formátumban](https://en.wikipedia.org/wiki/Markdown). Az átalakítási folyamat új sorokat helyez el a szövegben, például `\n\n`. A Markdown formátum ismerete segít megérteni a konvertált tartalmat, és felügyelni a Tudásbázis tartalmát.

Ha közvetlenül a Tudásbázisban adja hozzá vagy szerkeszti a tartalmat, a **Markdown formázás** használatával Rich Text tartalmat hozhat létre, vagy módosíthatja a Markdown formátum tartalmát, amely már szerepel a válaszban. QnA Maker támogatja a Markdown-formátum nagy részét, hogy Rich Text képességeket biztosítson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például a csevegési robot nem támogatja ugyanazt a Markdown-formátumot. Fontos, hogy tesztelje az ügyfélalkalmazás válaszait.

## <a name="basic-document-formatting"></a>Alapszintű dokumentum formázása

A QnA Maker a fájl szakaszait és alszakaszait és kapcsolatait azonosítja a vizualizációs jelek alapján, például:

* betűméret
* betűstílus
* számozás
* színek

|Dokumentum-példák|
|--|
||



## <a name="product-manuals"></a>Kézikönyvek

Manuális művelet rendszerint útmutatást anyagot, amely a termékek dokumentációiból tájékozódhat. Állítsa be, használja, karbantartása és a termék hibaelhárítása a felhasználó számára nyújt segítséget. A QnA Maker manuális dolgozza fel, ha a fejlécek és alszámok, kérdések és válaszok az azt követő tartalom bontja ki. [Itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)talál példát.

Alább egy példát egy index lapot, és a hierarchikus tartalom manuális van

 ![A termék manuális példa a Tudásbázis](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Kibontási optimális, amelyek a tábla tartalmát és/vagy a index lapot és a egy egyértelmű struktúra hierarchikus fejlécekkel kézikönyvek.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brosúrák, irányelvek, tanulmányok és egyéb fájlokat

Dokumentumok sok más típusú is lehet feldolgozni QA párok, létrehozásához megadott egy egyértelmű elrendezése és rendelkeznek. Ezek többek között a következők: prospektusok, irányelvek, jelentések, tanulmányok, tudományos dokumentumok, szabályzatok, könyvek stb. [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)talál példát.

Az alábbi, egy félig strukturált doc, anélkül, hogy az index egy példát:

 ![Az Azure Blob storage szolgáltatásban tárolt részben strukturált Doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturált QnA dokumentum

A formátum a strukturált kérdés-válaszokat DOC fájlok, váltakozó kérdések formájában, és minden sorában, soronként egy kérdésre választ követ a válasz a következő sort az alább látható módon:

```text
Question1

Answer1

Question2

Answer2
```

Alább egy példát egy strukturált QnA word-dokumentumot a következő:

 ![Tudásbázis strukturált QnA dokumentum példa](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *txt*-, *TSV* -és *xls* -fájlok

A QnAs strukturált *. txt*, *. TSV* vagy *. xls* kiterjesztésű fájlok formájában is feltölthető QnA Maker a Tudásbázis létrehozásához vagy bővítéséhez.  Ezek lehet egyszerű szöveges formátumban, vagy RTF vagy HTML-tartalmat is rendelkezhet.

| Kérdés  | Válasz  | Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopokat a rendszer figyelmen kívül hagyja.

### <a name="example-of-structured-excel-file"></a>Példa strukturált Excel-fájlra

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be HTML-tartalommal:

 ![Strukturált QnA excel-példa a Tudásbázis](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be, amely több alternatív kérdést is biztosít egyetlen válaszhoz:

 ![Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

A fájl importálása után a kérdés-válasz pár az alábbi Tudásbázisban látható:

 ![A tudásbázisba importált egyetlen válaszra vonatkozó alternatív kérdések képernyőképe](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturált adatok formátumát importálása

Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Importálás a strukturált .tsv adatforrás adatait tartalmazó fájl szükséges. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Több-turn dokumentum formázása

* Fejlécek és alfejlécek használatával jelezheti a hierarchiát. Például a H1 paraméterrel jelezheti, hogy a szülő QnA és a H2 jelzi a QnA, amelyet a rendszer kérni fog. A kisebb fejlécek méretének használata a későbbi hierarchia jelölésére. Ne használjon stílust, színt vagy valamilyen más mechanizmust a dokumentum struktúrájának megjelenítéséhez, QnA Maker nem fogja kibontani a többszörös bekapcsolási utasításokat.
* A fejléc első karakterének tőkésített kell lennie.
* Ne fejezze be a fejlécet kérdőjeltel, `?`.


|Dokumentum-példák|
|--|
||