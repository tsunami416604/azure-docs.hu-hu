---
title: Tartalomtípusok – QnA Maker
description: A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, mint például a PDF, a DOC és a TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843436"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>A tudásbázisba felvehető dokumentumok tartalomtípusai
A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, mint például a PDF, a DOC és a TXT.

## <a name="file-and-url-data-types"></a>Fájl-és URL-adattípusok

Az alábbi táblázat összefoglalja a QnA Maker által támogatott tartalom-és fájlformátumok típusait.

|Source Type (Forrás típusa)|Tartalom típusa| Példák|
|--|--|--|
|URL-cím|Gyakori kérdések<br> (Lapos, szakaszok vagy témakörök kezdőlapja)<br>Támogatási lapok <br> (Egyoldalas útmutató cikkek, hibaelhárítási cikkek stb.)|[Egyszerű gyakori kérdések](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Gyakori kérdések a hivatkozásokkal kapcsolatban](https://www.microsoft.com/en-us/software-download/faq)<br> [GYIK a témakörök kezdőlapján](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Támogatási cikk](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|GYIK<br> Termék kézi,<br> Brosúrák<br> Tanulmány<br> Szórólap-szabályzat,<br> Támogatási útmutató,<br> Strukturált QnA,<br> stb.|[Strukturált QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Minta termék kézi. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Példa: Semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Minta: tanulmány. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Példa: Multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Strukturált QnA-fájl<br> (többek között az RTF, a HTML-támogatás)|[Minta QnA – GYIK. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturált QnA-fájl|[Példa: Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Ha hitelesítésre van szüksége az adatforráshoz, vegye figyelembe a következő módszereket a tartalom QnA Makerba való beszerzéséhez:

* Fájl manuális letöltése és importálása QnA Maker
* Fájl hozzáadása a hitelesített [SharePoint-helyről](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-tartalom

Két típusú dokumentum importálható QnA Maker URL- **cím** használatával:

* Gyakori kérdések URL-címei
* Támogatási URL-címek

Mindegyik típus egy várt formátumot jelez.

## <a name="file-based-content"></a>Fájl alapú tartalom

A [QnA Maker-portálon](https://www.qnamaker.ai)hozzáadhat fájlokat egy nyilvános forrásból vagy a helyi fájlrendszerből is.

## <a name="content-format-guidelines"></a>A tartalom formátumára vonatkozó irányelvek

QnA Maker hozzáadja a fájl-és URL-tartalmakat, és átalakítja a tartalmat a Markdown (`.md`) néven tárolt QnA-készletekbe. A tartalom olyan tudásbázishoz fér el, amelyben a tartalom strukturált formában van rendszerezve, és jól definiált szakaszokban van megjelenítve. A szakaszok tovább bonthatók alszakaszokra vagy altémakörökre. A kinyerés a legjobb olyan tartalmaknál működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

A QnA Maker a tartalomban található szakaszokat és alszakaszokat és kapcsolatokat azonosítja a vizualizációk, például a betűméret, a betűstílus, a számozás, a színek stb. alapján. A félig strukturált tartalomhoz kézikönyvek, GYIK, irányelvek, szabályzatok, brosúrák, szórólapok és sok más típusú fájl tartozik.

## <a name="faq-urls"></a>Gyakori kérdések URL-címei

A QnA Maker három különböző formában támogathatja a GYIK-weblapokat: egyszerű GYIK-lapok, GYIK-oldalak hivatkozásokkal, GYIK-oldalak a témakörök kezdőlapján.


### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdések oldalai

Ez a leggyakoribb GYIK-oldal, amelyben a válaszok azonnal követik egyazon oldal kérdéseit.

Az alábbi példa egy egyszerű GYIK-oldalra mutat:

![A Tudásbázis egyszerű gyakori kérdések oldalának példája](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>GYIK-lapok hivatkozásokkal

Ebben a gyakori kérdések oldalon a kérdések össze vannak összesítve, és az azonos oldal különböző szakaszaiban vagy különböző lapokon található válaszokhoz vannak társítva.

Az alábbi példa egy olyan GYIK-oldalt mutat be, amely az ugyanazon a lapon található hivatkozásokat tartalmaz:

 ![Szakasz hivatkozás – GYIK oldal, példa tudásbázisra](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>GYIK-lapok a témakörök kezdőlapján

Az ilyen típusú gyakori kérdések egy olyan kezdőlaptal rendelkeznek, amelyben az egyes témakörök egy másik oldalon található kapcsolódó QnAs mutató hivatkozással rendelkeznek. Itt QnA Maker az összes hivatkozott oldalt feltérképezi, hogy kinyerje a megfelelő kérdéseket & válaszokra.

Az alábbi példa egy olyan GYIK-oldalra mutat be, ahol a témakörök kezdőlapja a különböző lapokon található GYIK-szakaszok hivatkozásaira mutat.

 ![Részletes hivatkozás – GYIK oldal, példa a tudásbázisra](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>Támogatási URL-címek

A QnA Maker képes feldolgozni a részben strukturált támogatási weblapokat, például a webcikkeket, amelyek leírják, hogyan hajthat végre egy adott feladatot, hogyan diagnosztizálhatja és elháríthatja az adott problémát, és hogy mi az ajánlott eljárás az adott folyamathoz. A kinyerés a legjobb olyan tartalmaknál működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

> [!NOTE]
> A támogatási cikkek kinyerése egy új szolgáltatás, amely korai fázisban van. A legmegfelelőbb az egyszerű lapokhoz, amelyek jól strukturáltak, és nem tartalmaznak összetett fejléceket/élőlábakat.

![QnA Maker támogatja a félig strukturált weblapok kinyerését, ahol a világos struktúra hierarchikus fejlécekkel van megjelenítve](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF-/DOC-fájlok

A QnA Maker a részben strukturált tartalmat PDF-vagy DOC-fájlban is feldolgozhatja, és átalakíthatja a QnAs. Jól kinyerhető jó fájl, amely egy strukturált formában rendezi a tartalmakat, és jól definiált szakaszokban jelenik meg. A szakaszok tovább bonthatók alszakaszokra vagy altémakörökre. A kinyerés a legjobb olyan dokumentumokon működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

A QnA Maker a fájl szakaszait és alszakaszait és kapcsolatait azonosítja a vizualizációs jelek, például a betűméret, a betűstílus, a számozás, a színek stb. alapján. A félig strukturált PDF-vagy DOC-fájlok a következők lehetnek: kézikönyvek, GYIK, irányelvek, szabályzatok, brosúrák, szórólapok és sok más típusú fájl. Az alábbiakban néhány példát láthat a fájlok típusára.

### <a name="product-manuals"></a>Termék-kézikönyvek

A manuális útmutató általában egy termékhez társuló segédanyag. Segítségével a felhasználó beállíthatja, használhatja, karbantarthatja és elháríthatja a terméket. Ha a QnA Maker manuálisan dolgozza fel a folyamatokat, a fejléceket és alfejléceket kérdésként és az azt követő tartalomként is kibontja a válaszként. [Itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)talál példát.

Az alábbi példa egy, az index oldalt tartalmazó manuális és a hierarchikus tartalom

 ![A tudásbázishoz tartozó termék kézi példája](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A kinyerés a legjobban olyan kézikönyvek esetében működik, amelyek tartalomjegyzéket és/vagy index oldalt, valamint egy hierarchikus fejlécekkel rendelkező tiszta struktúrát alkotnak.

### <a name="brochures-guidelines-papers-and-other-files"></a>Prospektusok, útmutatók, dokumentumok és egyéb fájlok

Számos más típusú dokumentum is feldolgozható a QA-párok létrehozásához, amennyiben azok tiszta struktúrával és elrendezéssel rendelkeznek. Ezek többek között a következők: prospektusok, irányelvek, jelentések, tanulmányok, tudományos dokumentumok, szabályzatok, könyvek stb. [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)talál példát.

Az alábbi példa egy félig strukturált doc-ra mutat, index nélkül:

 ![Azure Blob Storage – félig strukturált doc](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Strukturált QnA-dokumentum

A DOC-fájlokban strukturált kérdés-válasz formátuma váltakozó kérdések és válaszok formájában történik, és soronként egy kérdés jelenik meg, amelyet az alábbi sorban talál, ahogy az alább látható:

```text
Question1

Answer1

Question2

Answer2
```

Az alábbi példa egy strukturált QnA Word-dokumentumra mutat:

 ![Strukturált QnA dokumentum – példa tudásbázisra](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *txt*-, *TSV* -és *xls* -fájlok

A QnAs strukturált *. txt*, *. TSV* vagy *. xls* kiterjesztésű fájlok formájában is feltölthető QnA Maker a Tudásbázis létrehozásához vagy bővítéséhez.  Ezek lehetnek egyszerű szövegek, vagy RTF-vagy HTML-tartalommal is rendelkezhetnek.

| Kérdés  | Válasz  | Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopai figyelmen kívül lesznek hagyva.

### <a name="example-of-structured-excel-file"></a>Példa strukturált Excel-fájlra

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be HTML-tartalommal:

 ![Strukturált QnA Excel-példa egy tudásbázisra](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be, amely több alternatív kérdést is biztosít egyetlen válaszhoz:

 ![Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

A fájl importálása után a kérdés-válasz pár az alábbi Tudásbázisban látható:

 ![A tudásbázisba importált egyetlen válaszra vonatkozó alternatív kérdések képernyőképe](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>Formázási megfontolások

Fájl vagy URL importálása után QnA Maker átalakítja és tárolja a tartalmat a [Markdown formátumban](https://en.wikipedia.org/wiki/Markdown). Az átalakítási folyamat új sorokat helyez el a szövegben, például `\n\n`. A Markdown formátum ismerete segít megérteni a konvertált tartalmat, és felügyelni a Tudásbázis tartalmát.

Ha közvetlenül a Tudásbázisban adja hozzá vagy szerkeszti a tartalmat, a **Markdown formázás** használatával Rich Text tartalmat hozhat létre, vagy módosíthatja a Markdown formátum tartalmát, amely már szerepel a válaszban. QnA Maker támogatja a Markdown-formátum nagy részét, hogy Rich Text képességeket biztosítson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például a csevegési robot nem támogatja ugyanazt a Markdown-formátumot. Fontos, hogy tesztelje az ügyfélalkalmazás válaszait.

További információt a [QnA Maker Markdown dokumentációjában talál](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>A Markdown tesztelése

A Markdown ellenőrzéséhez használja a **[CommonMark](https://commonmark.org/help/tutorial/index.html)** oktatóanyagot. Az oktatóanyag egy **kipróbálási** funkcióval rendelkezik a gyors másolási/beillesztési ellenőrzéshez.

## <a name="next-steps"></a>Következő lépések

* A [kérdések és válaszok (QnA)-készletek](question-answer-set.md) tervezésének és kezelésének ismertetése