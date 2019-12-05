---
title: Támogatott adatforrások – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker automatikusan kivonja a kérdés-válasz párokat weblapként, PDF-fájlként vagy MS Word Doc-fájlként, vagy strukturált QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: dc948629784254c9153f7f48ead7ff253e5f4453
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806388"
---
# <a name="data-sources-for-qna-maker-content"></a>Adatforrások QnA Maker tartalomhoz

QnA Maker automatikusan Kinyeri a kérdés-válasz párokat a félig strukturált tartalomból, például a GYIK, a kézikönyvek, az irányelvek, a támogatási dokumentumok és a weblapokon, PDF-fájlokban vagy MS Word Doc-fájlokban tárolt szabályzatok alapján. A tartalmak strukturált QnA is hozzáadhatók a tudásbázishoz. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Fájl-és URL-adattípusok

Az alábbi táblázat összefoglalja a QnA Maker által támogatott tartalom-és fájlformátumok típusait.

|Source Type (Forrás típusa)|Tartalom típusa| Példák|
|--|--|--|
|URL-cím|Gyakori kérdések<br> (Lapos, szakaszok vagy témakörök kezdőlapja)<br>Támogatási lapok <br> (Egyoldalas útmutató cikkek, hibaelhárítási cikkek stb.)|[Egyszerű gyakori kérdések](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Gyakori kérdések a hivatkozásokkal kapcsolatban](https://www.microsoft.com/en-us/software-download/faq)<br> [GYIK a témakörök kezdőlapján](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Támogatási cikk](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|GYIK<br> Termék kézi,<br> Brosúrák<br> Tanulmány<br> Szórólap-szabályzat,<br> Támogatási útmutató,<br> Strukturált QnA,<br> stb.|[Strukturált QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Minta termék kézi. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Példa: Semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Minta: tanulmány. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Példa: Multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Strukturált QnA-fájl<br> (többek között az RTF, a HTML-támogatás)|[Minta QnA – GYIK. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturált QnA-fájl|[Példa: Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Tudásbázis importálása és exportálása

Az exportált tudásbázisokból származó **TSV-és xls-fájlok**csak a QnA Maker portál **Settings (beállítások** ) lapján található fájlok importálásával használhatók. A Tudásbázis létrehozásakor nem használhatók adatforrásként, illetve a **Beállítások** lapon a **+ fájl hozzáadása** vagy a **+ URL** hozzáadása funkció. 

## <a name="data-source-locations"></a>Adatforrás helyei

Az adatforrás helyei **nyilvános URL-címek vagy fájlok**, amelyek nem igényelnek hitelesítést. 

Ha hitelesítésre van szüksége az adatforráshoz, vegye figyelembe a következő módszereket az adatQnA Makerba való beolvasásához:

* [Fájl manuális letöltése](#download-file-from-authenticated-data-source-location) és importálása QnA Maker
* Fájl importálása a hitelesített [SharePoint-helyhez](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Fájl letöltése a hitelesített adatforrás helyéről

Ha hitelesített fájllal (nem a hitelesített SharePoint-helytel) vagy URL-címmel rendelkezik, egy másik lehetőségként töltse le a fájlt a hitelesített helyről a helyi számítógépre, majd adja hozzá a fájlt a helyi számítógépről a tudásbázishoz.

### <a name="import-file-from-authenticated-sharepoint"></a>Fájl importálása a hitelesített Sharepointból 

A [SharePoint-adatforrás helyei](../How-to/add-sharepoint-datasources.md) számára engedélyezett a hitelesített **fájlok**biztosítása. A SharePoint-erőforrásoknak fájlokat, nem weblapokat kell tartalmazniuk. Ha az URL-cím egy webes bővítménnyel végződik, például: **. ASPX**, a rendszer nem importálja QnA Maker a sharepointból.


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

## <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálással

A Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Az importáláshoz egy strukturált. TSV fájl szükséges, amely az adatforrás adatait tartalmazza. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői besorolás|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Szerkesztői Hozzáadás a tudásbázishoz

Ha nem rendelkezik már meglévő tartalommal a Tudásbázis feltöltéséhez, a QnA Maker Tudásbázisban is hozzáadhat QnAs. Megtudhatja, hogyan frissítheti a tudásbázist [itt](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Formázási megfontolások

Fájl vagy URL importálása után QnA Maker átalakítja és tárolja a tartalmat a [Markdown formátumban](https://en.wikipedia.org/wiki/Markdown). Az átalakítási folyamat új sorokat helyez el a szövegben, például `\n\n`. A Markdown formátum ismerete segít megérteni a konvertált tartalmat, és felügyelni a Tudásbázis tartalmát. 

Ha közvetlenül a Tudásbázisban adja hozzá vagy szerkeszti a tartalmat, a **Markdown formázás** használatával Rich Text tartalmat hozhat létre, vagy módosíthatja a Markdown formátum tartalmát, amely már szerepel a válaszban. QnA Maker támogatja a Markdown-formátum nagy részét, hogy Rich Text képességeket biztosítson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például a csevegési robot nem támogatja ugyanazt a Markdown-formátumot. Fontos, hogy tesztelje az ügyfélalkalmazás válaszait. 

Az alábbiakban a QnA Maker használható Markdown-formátumok listája látható: 

|Rendeltetés|Formátum|Markdown-példa|Renderelés<br>a csevegési robotban megjelenő módon|
|--|--|--|--|
Egy új sor két mondat között.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![új sor formázása két mondat között](../media/qnamaker-concepts-datasources/format-newline.png)|
|A H1-től a H6-ig terjedő fejlécek száma `#` jelzi, hogy melyik fejlécet jelöli. 1 `#` a H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátum Markdown-fejlécekkel](../media/qnamaker-concepts-datasources/format-headers.png)<br>![formátum Markdown fejlécekkel H1 – H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Dőlt |`*text*`|`How do I create a bot with *QnA Maker*?`|![formázás dőlt betűvel](../media/qnamaker-concepts-datasources/format-italics.png)|
|Karakterlánc (félkövér)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formázás erős jelöléssel félkövérre](../media/qnamaker-concepts-datasources/format-strong.png)|
|Hivatkozás URL-címe|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL-cím (hiperhivatkozás) formátuma](../media/qnamaker-concepts-datasources/format-url.png)|
|\* Nyilvános rendszerkép URL-címe|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![nyilvános rendszerkép URL-címének formátuma ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Áthúzott|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![áthúzás formátuma](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Félkövér és dőlt|`***text***`|`How can I create a ***QnA Maker*** bot?`|![félkövér és dőlt formázás](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Hivatkozás félkövér URL-címe|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![félkövér URL-cím formázása](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|Dőlt betűs URL a hivatkozáshoz|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![dőlt betűs URL formátuma](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape Markdown szimbólumok|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![dőlt betűs URL formátuma](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Rendezett lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Az előző példában a Markdown beépített automatikus számozást használ.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Az előző példa explicit számozást használ.|![rendezett lista formátuma](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Rendezetlen listák|`\n * item1 \n * item2`<br>vagy<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![Rendezetlen lista formátuma](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Beágyazott listák|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>A rendezett és rendezetlen listák együtt is ágyazhatók egymásba. A `\t`lapon a gyermek elem behúzási szintje látható.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![beágyazott Rendezetlen lista formátuma](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![beágyazott rendezett lista formátuma](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker semmilyen módon nem dolgozza fel a rendszerképet. Ez az ügyfélalkalmazás szerepe a rendszerkép megjelenítéséhez. 

Ha az Update/replace Tudásbázis API-k használatával szeretne tartalmat felvenni, és a tartalom/fájl HTML-címkéket tartalmaz, megtarthatja a HTML-fájlt a fájlban, hogy a címkék megnyitását és zárását a rendszer a kódolt formátumban konvertálja.

| HTML megőrzése  | Ábrázolás az API-kérelemben  | Megjelenítés a KB-ban |
|-----------|---------|-------------------------|
| Igen | \&lt; br\&gt; | &lt;br&gt; |
| Igen | \&lt; H3\&gt; fejléc\&lt;/H3\&gt; | &lt;H3&gt;header&lt;/H3&gt; |

Emellett a CR LF (\r\n) a KB-ban a \n értékre lett konvertálva. Az LF (\n) a következőképpen van megtartva:. Ha olyan escape-sorozatot szeretne elmenekülni, mint például egy \t vagy \n, használhat fordított perjelet, például: "\\\\r\\\\n" és "\\\\t"

## <a name="editing-your-knowledge-base-locally"></a>A Tudásbázis helyi szerkesztése

A Tudásbázis létrehozása után javasolt a [QnA Maker-portálon](https://qnamaker.ai)lévő Tudásbázis szövegének szerkesztése a helyi fájlok exportálása és újraimportálása helyett. Előfordulhat azonban, hogy a tudásbázist helyileg kell szerkesztenie. 

Exportálja a tudásbázist a **Beállítások** lapról, majd szerkessze a tudásbázist a Microsoft Excelben. Ha úgy dönt, hogy egy másik alkalmazást használ az exportált TSV-fájl szerkesztéséhez, az alkalmazás szintaktikai hibákat eredményezhet, mert az nem teljes TSV-kompatibilis. A Microsoft Excel TSV-fájljai általában nem vezetnek be formázási hibákat. 

Miután végzett a szerkesztéssel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljes mértékben lecseréli az aktuális tudásbázist az importált tudásbázisba. 

## <a name="testing-your-markdown"></a>A Markdown tesztelése

A Markdown ellenőrzéséhez használja a **[CommonMark](https://commonmark.org/help/tutorial/index.html)** oktatóanyagot. Az oktatóanyag egy **kipróbálási** funkcióval rendelkezik a gyors másolási/beillesztési ellenőrzéshez. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>A tudásbázisban található adatok verziószám-vezérlése

Az adatverzió-vezérlést a **Beállítások** lap [Importálás/exportálás funkciójával](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) biztosítjuk. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [QnA Maker szolgáltatás beállítása](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Lásd még: 

[A QnA Maker áttekintése](../Overview/overview.md)
