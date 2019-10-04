---
title: Támogatott – az adatforrásokat a QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker automatikusan kinyeri a kérdés-válasz párt, például – gyakori kérdések, kézikönyvek, irányelvek, támogatási dokumentumok és szabályzatok weblapok, a PDF-fájlokat vagy a Microsoft Word-dokumentum fájlok tárolt részben strukturált tartalomból. Tartalmakat is hozzáadhat a Tudásbázis származó strukturált QnA tartalomfájlok.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: 1a9f3eb0ea79a0cd79850e721d081b00dc582a31
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695279"
---
# <a name="data-sources-for-qna-maker-content"></a>A tartalmat a QnA Maker adatforrások

A QnA Maker automatikusan kinyeri a kérdés-válasz párt, például – gyakori kérdések, kézikönyvek, irányelvek, támogatási dokumentumok és szabályzatok weblapok, a PDF-fájlokat vagy a Microsoft Word-dokumentum fájlok tárolt részben strukturált tartalomból. Tartalmakat is hozzáadhat a Tudásbázis származó strukturált QnA tartalomfájlok. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Fájl-és URL-adattípusok

Az alábbi táblázat foglalja össze a tartalmat és fájlformátumot, QnA Maker által támogatott típusú.

|Source Type (Forrás típusa)|Tartalomtípus| Példák|
|--|--|--|
|URL|Gyakori kérdések<br> (Lapos, szakaszok vagy témakörök kezdőlapja)<br>Támogatási lapok <br> (Egyoldalas útmutató cikkek, hibaelhárítási cikkek stb.)|[Egyszerű gyakori kérdések](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Gyakori kérdések a hivatkozásokkal kapcsolatban](https://www.microsoft.com/software-download/faq)<br> [GYIK a témakörök kezdőlapján](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Támogatási cikk](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|GYIK<br> Termék kézi,<br> brosúrák<br> Tanulmány<br> Szórólap-szabályzat,<br> Támogatási útmutató,<br> Strukturált QnA,<br> Stb.|[Strukturált QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Minta termék kézi. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Példa: Semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Minta: tanulmány. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Példa: Multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Strukturált QnA fájl<br> (többek között az RTF, a HTML-támogatás)|[Minta QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturált QnA fájl|[Minta chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Az exportált tudásbázisokból származó **TSV-és xls-fájlok**csak a QnA Maker portál **Settings (beállítások** ) lapján található fájlok importálásával használhatók. Nem használhatók adatforrásként a Tudásbázis létrehozásakor vagy a fájl vagy URL-cím hozzáadása funkcióval a **Beállítások** lapon. 

További információ a többfunkciós [dokumentumok]()használatáról. 

## <a name="data-source-locations"></a>Adatforrás helye

Az adatforrás helyei **nyilvános URL-címek vagy fájlok**, amelyek nem igényelnek hitelesítést. 

Ha hitelesítésre van szüksége az adatforráshoz, vegye figyelembe a következő módszereket az adatQnA Makerba való beolvasásához:

* [Fájl manuális letöltése](#download-file-from-authenticated-data-source-location) és importálása QnA Maker
* Fájl importálása a hitelesített [SharePoint-helyhez](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Fájl letöltése a hitelesített adatforrás helyéről

Ha hitelesített fájllal (nem a hitelesített SharePoint-helytel) vagy URL-címmel rendelkezik, egy másik lehetőségként töltse le a fájlt a hitelesített helyről a helyi számítógépre, majd adja hozzá a fájlt a helyi számítógépről a tudásbázishoz.

### <a name="import-file-from-authenticated-sharepoint"></a>Fájl importálása a hitelesített Sharepointból 

A [SharePoint-adatforrás helyei](../How-to/add-sharepoint-datasources.md) számára engedélyezett a hitelesített **fájlok**biztosítása. A SharePoint-erőforrásoknak fájlokat, nem weblapokat kell tartalmazniuk. Ha az URL-cím egy webes bővítménnyel végződik, például: **. ASPX**, a rendszer nem importálja QnA Maker a sharepointból.


## <a name="faq-urls"></a>Gyakori kérdések URL-címek

A QnA Maker 3 különböző formában támogathatja a gyakori kérdések weblapjait: Egyszerű GYIK-lapok, GYIK-oldalak hivatkozásokkal, GYIK-oldalak a témakörök kezdőlapján.

### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdéseket tartalmazó oldalak

Ez a gyakori kérdéseket tartalmazó oldal, ahol a válaszok azonnal kövesse ugyanazon az oldalon szereplő kérdések leggyakoribb típusát. 

Az alábbi, egy egyszerű gyakori kérdéseket tartalmazó oldal egy példát:

![Egyszerű – gyakori kérdések lapján példa a Tudásbázis](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Mutató hivatkozásokat tartalmaz a gyakori kérdéseket tartalmazó oldalak 

A gyakori kérdéseket tartalmazó oldal az ilyen típusú kérdéseket együtt vannak összesítve, és választ, amelyek különböző szakaszaiban azonos vagy eltérő lapjain van csatolva.

Alább egyik példája egy gyakori kérdéseket tartalmazó oldal mutató hivatkozásokat tartalmaz, amelyek ugyanazon az oldalon szakaszokban:

 ![A szakasz kapcsolat – gyakori kérdések lapján példa a Tudásbázis](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>A témakörök kezdőlapja a gyakori kérdéseket tartalmazó oldalak

Az ilyen típusú – gyakori kérdések a témakörök honlappal rendelkezik, ahol az egyes témakörök az eltérő oldalán a megfelelő QnA-tudásbázisok mutató hivatkozást. Itt QnA Maker feltérképezi a társított oldalt a megfelelő kérdések és válaszok kinyerése.

Alább egyik példája egy gyakori kérdések lapra, ahol témakörök kezdőlapja már – gyakori kérdések szakaszokban eltérő lapjain hivatkozásokat. 

 ![Tudásbázis lapon példa áruházra mutató mélyhivatkozás – gyakori kérdések](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Támogatási URL-címek

A QnA Maker képes feldolgozni a részben strukturált támogatási weblapokat, például a webcikkeket, amelyek leírják, hogyan hajthat végre egy adott feladatot, hogyan diagnosztizálhatja és elháríthatja az adott problémát, és hogy mi az ajánlott eljárás az adott folyamathoz. A kinyerés a legjobb olyan tartalmaknál működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

> [!NOTE]
> A támogatási cikkek kinyerése egy új szolgáltatás, amely korai fázisban van. A legmegfelelőbb az egyszerű lapokhoz, amelyek jól strukturáltak, és nem tartalmaznak összetett fejléceket/élőlábakat.

![QnA Maker támogatja a félig strukturált weblapok kinyerését, ahol a világos struktúra hierarchikus fejlécekkel van megjelenítve](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / DOC fájlok

A QnA Maker egy PDF- vagy DOC fájlban félig strukturált tartalmakat dolgoznak, és átalakíthatja a QnA-tudásbázisok. Egy jó fájl, amely jól kiolvasható olyan tartalom ahol strukturált valamilyen vannak rendezve, és jól definiált szakaszokban jelölt. A szakaszok további a struktúrát, vagy altémakörei kell megszakadt. Kibontási optimális dokumentumok hierarchikus fejlécekkel egyértelmű struktúrával rendelkezik.

A QnA Maker azonosítja szakaszok és struktúrát és a fájlban, például a betűméret betűstílust, vizuális jelek alapján kapcsolatok számozása színeket, stb. Részben strukturált PDF vagy DOC fájlokat lehet kézikönyvek – gyakori kérdések, irányelvek, szabályzatok, brosúrák, szórólapok és sok más típusú fájlok. Az alábbiakban néhány példa típusú ezeket a fájlokat.

### <a name="product-manuals"></a>Kézikönyvek

Manuális művelet rendszerint útmutatást anyagot, amely a termékek dokumentációiból tájékozódhat. Állítsa be, használja, karbantartása és a termék hibaelhárítása a felhasználó számára nyújt segítséget. A QnA Maker manuális dolgozza fel, ha a fejlécek és alszámok, kérdések és válaszok az azt követő tartalom bontja ki. Látható egy példa [Itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Alább egy példát egy index lapot, és a hierarchikus tartalom manuális van

 ![A termék manuális példa a Tudásbázis](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Kibontási optimális, amelyek a tábla tartalmát és/vagy a index lapot és a egy egyértelmű struktúra hierarchikus fejlécekkel kézikönyvek.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brosúrák, irányelvek, tanulmányok és egyéb fájlokat

Dokumentumok sok más típusú is lehet feldolgozni QA párok, létrehozásához megadott egy egyértelmű elrendezése és rendelkeznek. Ezek a következők: Prospektusok, irányelvek, jelentések, tanulmányok, tudományos dokumentumok, szabályzatok, könyvek stb. Látható egy példa [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Az alábbi, egy félig strukturált doc, anélkül, hogy az index egy példát:

 ![Az Azure Blob storage szolgáltatásban tárolt részben strukturált Doc](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturált QnA dokumentum

A formátum a strukturált kérdés-válaszokat DOC fájlok, váltakozó kérdések formájában, és minden sorában, soronként egy kérdésre választ követ a válasz a következő sort az alább látható módon: 

```text
Question1

Answer1

Question2

Answer2
```

Alább egy példát egy strukturált QnA word-dokumentumot a következő:

 ![Tudásbázis strukturált QnA dokumentum példa](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *TXT*, *TSV* és *XLS* fájlok

QnA-tudásbázisok formájában strukturált *.txt*, *.tsv* vagy *.xls* fájlokat is is feltölthetők a QnA Maker létrehozásához vagy egy Tudásbázis bővítésével.  Ezek lehet egyszerű szöveges formátumban, vagy RTF vagy HTML-tartalmat is rendelkezhet. 

| Kérdés  | Válasz  | Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopokat a rendszer figyelmen kívül hagyja.

### <a name="example-of-structured-excel-file"></a>Példa strukturált Excel-fájlra

Az alábbi példában a strukturált QnA, *.xls* fájlt, HTML-tartalom:

 ![Strukturált QnA excel-példa a Tudásbázis](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be, amely több alternatív kérdést is biztosít egyetlen válaszhoz:

 ![Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

A fájl importálása után a kérdés-válasz pár az alábbi Tudásbázisban látható:

 ![A tudásbázisba importált egyetlen válaszra vonatkozó alternatív kérdések képernyőképe](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturált adatok formátumát importálása

Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Importálás a strukturált .tsv adatforrás adatait tartalmazó fájl szükséges. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Source| Metaadatok (1 kulcs: 1 érték) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Tudásbázis tudásbázishoz hozzáadása

Ha nem rendelkezik már meglévő tartalom feltöltéséhez a Tudásbázis, hozzáadhat QnA-tudásbázisok besorolást a QnA Maker Knowledge base. Ismerje meg, hogyan frissíthető a Tudásbázis [Itt](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Formázási kapcsolatos szempontok

Fájl vagy URL importálása után QnA Maker átalakítja és tárolja a tartalmat a [Markdown formátumban](https://en.wikipedia.org/wiki/Markdown). Az átalakítási folyamat új sorokat szúr be a szövegbe, `\n\n`például:. A Markdown formátum ismerete segít megérteni a konvertált tartalmat, és felügyelni a Tudásbázis tartalmát. 

Ha közvetlenül a Tudásbázisban adja hozzá vagy szerkeszti a tartalmat, a **Markdown formázás** használatával Rich Text tartalmat hozhat létre, vagy módosíthatja a Markdown formátum tartalmát, amely már szerepel a válaszban. QnA Maker támogatja a Markdown-formátum nagy részét, hogy Rich Text képességeket biztosítson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például a csevegési robot nem támogatja ugyanazt a Markdown-formátumot. Fontos, hogy tesztelje az ügyfélalkalmazás válaszait. 

Az alábbiakban a QnA Maker használható Markdown-formátumok listája látható: 

|Cél|Formátum|Példa Markdown|Renderelés<br>a csevegési robotban megjelenő módon|
|--|--|--|--|
Egy új sor két mondat között.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![új sor formázása két mondat között](../media/qnamaker-concepts-datasources/format-newline.png)|
|A H1-től a H6-ig terjedő `#` fejlécek száma, amely a fejlécet jelöli. 1 `#` a H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátum Markdown-fejlécekkel](../media/qnamaker-concepts-datasources/format-headers.png)<br>![formátum Markdown fejlécekkel H1 – H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Dőlt |`*text*`|`How do I create a bot with *QnA Maker*?`|![formázás dőlt betűvel](../media/qnamaker-concepts-datasources/format-italics.png)|
|Karakterlánc (félkövér)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formázás erős jelöléssel félkövérre](../media/qnamaker-concepts-datasources/format-strong.png)|
|Hivatkozás URL-címe|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL-cím (hiperhivatkozás) formátuma](../media/qnamaker-concepts-datasources/format-url.png)|
|\* Nyilvános rendszerkép URL-címe|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![nyilvános rendszerkép URL-címének formátuma ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Áthúzás|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![áthúzás formátuma](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Félkövér és dőlt|`***text***`|`How can I create a ***QnA Maker*** bot?`|![félkövér és dőlt formázás](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Hivatkozás félkövér URL-címe|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![félkövér URL-cím formázása](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|Dőlt betűs URL a hivatkozáshoz|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![dőlt betűs URL formátuma](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape Markdown szimbólumok|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![dőlt betűs URL formátuma](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Rendezett lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Az előző példában a Markdown beépített automatikus számozást használ.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Az előző példa explicit számozást használ.|![rendezett lista formátuma](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Rendezetlen lista|`\n * item1 \n * item2`<br>or<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![Rendezetlen lista formátuma](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Beágyazott felsorolások|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>A rendezett és rendezetlen listák együtt is ágyazhatók egymásba. A lap `\t`a gyermek elem behúzási szintjét jelzi.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![beágyazott Rendezetlen lista formátuma](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![beágyazott rendezett lista formátuma](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker semmilyen módon nem dolgozza fel a rendszerképet. Ez az ügyfélalkalmazás szerepe a rendszerkép megjelenítéséhez. 

Ha az Update/replace Tudásbázis API-k használatával szeretne tartalmat felvenni, és a tartalom/fájl HTML-címkéket tartalmaz, megtarthatja a HTML-fájlt a fájlban, hogy a címkék megnyitását és zárását a rendszer a kódolt formátumban konvertálja.

| HTML megőrzése  | Ábrázolás az API-kérelemben  | Megjelenítés a KB-ban |
|-----------|---------|-------------------------|
| Igen | \&lt; br\&gt; | &lt;br&gt; |
| Igen | \&lt; H3\&gt; fejléc\&lt;/H3\&gt; | &lt;H3&gt;fejléc&lt;/H3&gt; |

Emellett a CR LF (\r\n) a KB-ban a \n értékre lett konvertálva. Az LF (\n) a következőképpen van megtartva:. Ha a \t vagy a \n escape-sorozatot is el szeretné kerülni, használhat fordított perjelet, például: "\\\\r\\\\n" és "\\\\t"

## <a name="editing-your-knowledge-base-locally"></a>A Tudásbázis helyi szerkesztése

A Tudásbázis létrehozása után javasolt a [QnA Maker-portálon](https://qnamaker.ai)lévő Tudásbázis szövegének szerkesztése a helyi fájlok exportálása és újraimportálása helyett. Előfordulhat azonban, hogy a tudásbázist helyileg kell szerkesztenie. 

Exportálja a tudásbázist a **Beállítások** lapról, majd szerkessze a tudásbázist a Microsoft Excelben. Ha úgy dönt, hogy egy másik alkalmazást használ az exportált TSV-fájl szerkesztéséhez, az alkalmazás szintaktikai hibákat eredményezhet, mert az nem teljes TSV-kompatibilis. A Microsoft Excel TSV-fájljai általában nem vezetnek be formázási hibákat. 

Miután végzett a szerkesztéssel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljes mértékben lecseréli az aktuális tudásbázist az importált tudásbázisba. 

## <a name="testing-your-markdown"></a>A Markdown tesztelése

A Markdown ellenőrzéséhez használja a **[CommonMark](https://commonmark.org/help/tutorial/index.html)** oktatóanyagot. Az oktatóanyag egy **kipróbálási** funkcióval rendelkezik a gyors másolási/beillesztési ellenőrzéshez. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>A tudásbázisban található adatok verziószám-vezérlése

Az adatverzió-vezérlést a **Beállítások** lap [Importálás/exportálás funkciójával](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) biztosítjuk. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker szolgáltatás beállítása](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
