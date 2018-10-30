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
ms.date: 09/25/2018
ms.author: tulasim
ms.openlocfilehash: 4c96eb5b49c72fe9bb5ab977c773407889c56805
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213304"
---
# <a name="data-sources-for-qna-maker-content"></a>A tartalmat a QnA Maker adatforrások

A QnA Maker automatikusan kinyeri a kérdés-válasz párt, például – gyakori kérdések, kézikönyvek, irányelvek, támogatási dokumentumok és szabályzatok weblapok, a PDF-fájlokat vagy a Microsoft Word-dokumentum fájlok tárolt részben strukturált tartalomból. Tartalmakat is hozzáadhat a Tudásbázis származó strukturált QnA tartalomfájlok. 

Az alábbi táblázat foglalja össze a tartalmat és fájlformátumot, QnA Maker által támogatott típusú.

|Source Type (Forrás típusa)|Tartalomtípus| Példák|
|--|--|--|
|URL-cím|(Egyszerű, szakaszok vagy a témakörök kezdőlapja) – gyakori kérdések|[Egyszerű – gyakori kérdések](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), [– gyakori kérdések hivatkozásokkal](https://www.microsoft.com/software-download/faq), [– gyakori kérdések a témakörök kezdőlapja](https://support.microsoft.com/products/windows?os=windows-10)|
|PDF / DOC|Gyakori kérdések, termék manuális, brosúrák, tanulmány, szórólap házirend és támogatási útmutató, strukturált QnA stb.|[Strukturált QnA.doc](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Bot%20Service%20Sample%20FAQ.docx), [minta termék Manual.pdf](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf), [félig-structured.doc minta](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx), [fehér paper.pdf minta](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-stack-wortmann-bring-the-power-of-the-public-cloud-into-your-data-center/Azure_Stack_Wortmann_Bring_the_Power_of_the_Public_Cloud_into_Your_Data_Center.pdf)|
|Excel|Strukturált QnA fájlt (beleértve a RTF, HTML támogatja)|[Minta QnA FAQ.xls](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Strukturált QnA fájl|[Minta chit-chat.tsv](https://github.com/Microsoft/BotBuilder-PersonalityChat/blob/master/CSharp/Datasets/scenario_responses_the_friend.tsv)|

## <a name="faq-urls"></a>Gyakori kérdések URL-címek

A QnA Maker – gyakori kérdések weblapok támogathat 3 különböző képernyőkön: egyszerű gyakori kérdéseket tartalmazó oldalak, a gyakori kérdéseket tartalmazó oldalak mutató hivatkozásokat tartalmaz, a témakörök kezdőlapja a gyakori kérdéseket tartalmazó oldalak.

### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdéseket tartalmazó oldalak

Ez a gyakori kérdéseket tartalmazó oldal, ahol a válaszok azonnal kövesse ugyanazon az oldalon szereplő kérdések leggyakoribb típusát. 

Az alábbi, egy egyszerű gyakori kérdéseket tartalmazó oldal egy példát:

![Egyszerű gyakori kérdéseket tartalmazó oldal](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Mutató hivatkozásokat tartalmaz a gyakori kérdéseket tartalmazó oldalak 

A gyakori kérdéseket tartalmazó oldal az ilyen típusú kérdéseket együtt vannak összesítve, és választ, amelyek különböző szakaszaiban azonos vagy eltérő lapjain van csatolva.

Alább egyik példája egy gyakori kérdéseket tartalmazó oldal mutató hivatkozásokat tartalmaz, amelyek ugyanazon az oldalon szakaszokban:

 ![A szakasz hivatkozás gyakori kérdéseket tartalmazó oldal](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>A témakörök kezdőlapja a gyakori kérdéseket tartalmazó oldalak

Az ilyen típusú – gyakori kérdések a témakörök honlappal rendelkezik, ahol az egyes témakörök az eltérő oldalán a megfelelő QnA-tudásbázisok mutató hivatkozást. Itt QnA Maker feltérképezi a társított oldalt a megfelelő kérdések és válaszok kinyerése.

Alább egyik példája egy gyakori kérdések lapra, ahol témakörök kezdőlapja már – gyakori kérdések szakaszokban eltérő lapjain hivatkozásokat. 

 ![Áruházra mutató mélyhivatkozás – gyakori kérdések lapján](../media/qnamaker-concepts-datasources/topics-faq.png) 


## <a name="pdf-doc-files"></a>PDF / DOC fájlok

A QnA Maker egy PDF- vagy DOC fájlban félig strukturált tartalmakat dolgoznak, és átalakíthatja a QnA-tudásbázisok. Egy jó fájl, amely jól kiolvasható olyan tartalom ahol strukturált valamilyen vannak rendezve, és jól definiált szakaszokban jelölt. A szakaszok további a struktúrát, vagy altémakörei kell megszakadt. Kibontási optimális dokumentumok hierarchikus fejlécekkel egyértelmű struktúrával rendelkezik.

A QnA Maker azonosítja szakaszok és struktúrát és a fájlban, például a betűméret betűstílust, vizuális jelek alapján kapcsolatok számozása színeket, stb. Részben strukturált PDF vagy DOC fájlokat lehet kézikönyvek – gyakori kérdések, irányelvek, szabályzatok, brosúrák, szórólapok és sok más típusú fájlok. Az alábbiakban néhány példa típusú ezeket a fájlokat.

### <a name="product-manuals"></a>Kézikönyvek

Manuális művelet rendszerint útmutatást anyagot, amely a termékek dokumentációiból tájékozódhat. Állítsa be, használja, karbantartása és a termék hibaelhárítása a felhasználó számára nyújt segítséget. A QnA Maker manuális dolgozza fel, ha a fejlécek és alszámok, kérdések és válaszok az azt követő tartalom bontja ki. Látható egy példa [Itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Alább egy példát egy index lapot, és a hierarchikus tartalom manuális van

 ![A termék manuális példa](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Kibontási optimális, amelyek a tábla tartalmát és/vagy a index lapot és a egy egyértelmű struktúra hierarchikus fejlécekkel kézikönyvek.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brosúrák, irányelvek, tanulmányok és egyéb fájlokat

Dokumentumok sok más típusú is lehet feldolgozni QA párok, létrehozásához megadott egy egyértelmű elrendezése és rendelkeznek. Ezek közé tartozik a: brosúrák, irányelvek, a jelentések fehér tanulmányok, tudományos dolgozatok, szabályzatok, könyveket, stb. Látható egy példa [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

 ![Strukturált QnA dokumentum](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *TXT*, *TSV* és *XLS* fájlok

QnA-tudásbázisok formájában strukturált *.txt*, *.tsv* vagy *.xls* fájlokat is is feltölthetők a QnA Maker létrehozásához vagy egy Tudásbázis bővítésével.  Ezek lehet egyszerű szöveges formátumban, vagy RTF vagy HTML-tartalmat is rendelkezhet. 

| Kérdés  | Válasz  | Metaadatok                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopokat a rendszer figyelmen kívül hagyja.

Az alábbi példában a strukturált QnA, *.xls* fájlt, HTML-tartalom:

 ![Excel-strukturált kérdések és válaszok](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Strukturált adatok formátumát importálása

Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Importálás a strukturált .tsv adatforrás adatait tartalmazó fájl szükséges. Ez az információ segít a QnA Maker, kérdés-válasz párt csoportot, és azokat egy adott adatforrást attribútumot.

| Kérdés  | Válasz  | Forrás| Metaadatok                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Tudásbázis tudásbázishoz hozzáadása

Ha nem rendelkezik már meglévő tartalom feltöltéséhez a Tudásbázis, hozzáadhat QnA-tudásbázisok besorolást a QnA Maker Knowledge base. Ismerje meg, hogyan frissíthető a Tudásbázis [Itt](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker szolgáltatás beállítása](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
