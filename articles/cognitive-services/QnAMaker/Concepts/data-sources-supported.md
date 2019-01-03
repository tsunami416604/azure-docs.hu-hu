---
title: Támogatott – az adatforrásokat a QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker automatikusan kinyeri a kérdés-válasz párt, például – gyakori kérdések, kézikönyvek, irányelvek, támogatási dokumentumok és szabályzatok weblapok, a PDF-fájlokat vagy a Microsoft Word-dokumentum fájlok tárolt részben strukturált tartalomból. Tartalmakat is hozzáadhat a Tudásbázis származó strukturált QnA tartalomfájlok.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/27/2018
ms.author: tulasim
ms.openlocfilehash: f732391cc73f0a7cb417409cd6f6f2b3c54f93d6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994130"
---
# <a name="data-sources-for-qna-maker-content"></a>A tartalmat a QnA Maker adatforrások

A QnA Maker automatikusan kinyeri a kérdés-válasz párt, például – gyakori kérdések, kézikönyvek, irányelvek, támogatási dokumentumok és szabályzatok weblapok, a PDF-fájlokat vagy a Microsoft Word-dokumentum fájlok tárolt részben strukturált tartalomból. Tartalmakat is hozzáadhat a Tudásbázis származó strukturált QnA tartalomfájlok. 

Az alábbi táblázat foglalja össze a tartalmat és fájlformátumot, QnA Maker által támogatott típusú.

|Source Type (Forrás típusa)|Tartalomtípus| Példák|
|--|--|--|
|URL-cím|Gyakori kérdések<br> (Átalánydíjjal, szakaszok vagy témaköröket kezdőlapja)<br>Támogatási oldal <br> (Egyoldalas útmutatókat, hibaelhárítási cikkek stb.)|[Egyszerű – gyakori kérdések](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Hivatkozások – gyakori kérdések](https://www.microsoft.com/software-download/faq),<br> [A témakörök kezdőlap – gyakori kérdések](https://support.microsoft.com/products/windows?os=windows-10)<br>[Támogatási cikk](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Gyakori kérdések,<br> A termék kézi,<br> Brosúrák,<br> A papír<br> A házirend szórólap<br> Az útmutató támogatás,<br> A QnA strukturált,<br> Stb.|[Strukturált QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [A termék Manual.pdf minta](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Félig-structured.doc minta](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Példa a fehér paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Strukturált QnA fájl<br> (beleértve a RTF, HTML támogatja)|[Minta QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Strukturált QnA fájl|[Minta chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Adatforrás helye

Csak nyilvános URL-címek érvényesek minden adatforráson. Ne küldjön el a hitelesítést igénylő adatforrások. A hitelesített webhelyről töltheti le a fájlt, majd a fájl feltöltési, amelynek segítségével a kérdések és válaszok kinyerése.

## <a name="faq-urls"></a>Gyakori kérdések URL-címek

A QnA Maker – gyakori kérdések weblapok támogathat 3 különböző képernyőkön: Egyszerű gyakori kérdéseket tartalmazó oldalak, a gyakori kérdéseket tartalmazó oldalak mutató hivatkozásokat tartalmaz, a témakörök kezdőlapja a gyakori kérdéseket tartalmazó oldalak.

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


### <a name="support-urls"></a>Támogatja az URL-címek

A QnA Maker félig strukturált támogatási weblapok, például webes cikkeket, hogy egy adott feladat végrehajtása, diagnosztizálása és a egy adott probléma megoldásához és a egy adott folyamathoz ajánlott eljárások tud feldolgozni. Kibontási hierarchikus fejlécekkel egyértelmű struktúra, amelyik a legjobban használható.

> [!NOTE]
> A támogatással kapcsolatos cikkek kinyerési új szolgáltatása, és korai szakaszában van. Leginkább egyszerű weblapok esetében, amelyek jól strukturált, és nem tartalmaz összetett fejlécek és láblécek.

![A QnA Maker támogatja a részben strukturált weblapok, ahol egy egyértelmű struktúra hierarchikus fejlécekkel egyike jelenik meg a kinyerés](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


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

Dokumentumok sok más típusú is lehet feldolgozni QA párok, létrehozásához megadott egy egyértelmű elrendezése és rendelkeznek. Ezek a következők: Brosúrák, irányelvek, jelentések, tanulmányok, tudományos dolgozatok, szabályzatok, könyveket, stb. Látható egy példa [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

| Kérdés  | Válasz  | Metaadatok                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopokat a rendszer figyelmen kívül hagyja.

Az alábbi példában a strukturált QnA, *.xls* fájlt, HTML-tartalom:

 ![Strukturált QnA excel-példa a Tudásbázis](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Strukturált adatok formátumát importálása

Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Importálás a strukturált .tsv adatforrás adatait tartalmazó fájl szükséges. Ez az információ segít a QnA Maker, kérdés-válasz párt csoportot, és azokat egy adott adatforrást attribútumot.

| Kérdés  | Válasz  | Forrás| Metaadatok                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Tudásbázis tudásbázishoz hozzáadása

Ha nem rendelkezik már meglévő tartalom feltöltéséhez a Tudásbázis, hozzáadhat QnA-tudásbázisok besorolást a QnA Maker Knowledge base. Ismerje meg, hogyan frissíthető a Tudásbázis [Itt](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Formázási kapcsolatos szempontok

Egy fájlba vagy URL-cím az importálás után konvertálva a Markdown és a formátumban tárolja. Ha az átalakítási folyamat nem megfelelően konvertálása a fájl-és URL-címeket hivatkozásokkal, szerkesztenie kell a kérdések és válaszok a a **szerkesztése** lapot. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker szolgáltatás beállítása](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
