---
title: Importálás adatforrásokból – QnA Maker
description: A QnA Maker Tudásbázis egy kérdés-válasz típusú (QnA) készletből és az egyes QnA-párokhoz tartozó opcionális metaadatokból áll.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219686"
---
# <a name="importing-from-data-sources"></a>Importálás adatforrásokból

A Tudásbázis a nyilvános URL-címek és fájlok által benyújtott kérdés-és answer-csoportokból áll.

## <a name="data-source-locations"></a>Adatforrás helye

A tartalom egy adatforrásból származó tudásbázisba kerül. Az adatforrás helyei **nyilvános URL-címek vagy fájlok**, amelyek nem igényelnek hitelesítést.

A hitelesítéssel védett [SharePoint-fájlok](../how-to/add-sharepoint-datasources.md)kivételt képeznek. A SharePoint-erőforrásoknak fájlokat, nem weblapokat kell tartalmazniuk. Ha az URL-cím egy webes bővítménnyel végződik, például:. ASPX, a rendszer nem importálja QnA Maker a SharePointból.

## <a name="chit-chat-content"></a>Chit-csevegési tartalom

A Chit Chat QnA teljes tartalom-adatforrásként van felkínálva több nyelven és társalgási stílusban. Ez a robot felhőszerepkör felel a kiindulási pont lehet, és az időt és rögzíti őket előzmények költséget takaríthat meg. Megtudhatja [, hogyan adhatja hozzá](../how-to/chit-chat-knowledge-base.md) ezt a tartalmat a tudásbázishoz.

## <a name="structured-data-format-through-import"></a>Strukturált adatok formátumát importálása

Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Az importáláshoz strukturált `.tsv` fájl szükséges, amely kérdéseket és válaszokat tartalmaz. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz készleteket, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturált többsoros formátum importálással

A többszörös kapcsolású beszélgetéseket `.tsv` fájlformátumban hozhatja létre. A formátum lehetővé teszi a többfordulatos beszélgetések létrehozását a korábbi csevegési naplók elemzésével (más folyamatokkal, nem a QnA Maker), majd az Automation használatával hozza létre a `.tsv` fájlt. Importálja a fájlt a meglévő Tudásbázis cseréjéhez.

> [!div class="mx-imgBorder"]
> a 3 szint ![fogalmi modellje többfordulatú kérdésekkel](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A többszörös kapcsolású `.tsv`oszlopa a következőre vonatkozik **:.** Az Excelben látható `.tsv`például megjeleníti a több bekapcsoló gyermekek definiálásához szükséges információkat:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

A **displayOrder** numerikus, és a **szöveg** olyan szöveg, amely nem tartalmaz Markdown.

> [!div class="mx-imgBorder"]
> ![többirányú kérdéses példa az Excel programban látható módon](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportálás példaként

Ha nem tudja, hogyan jelöli meg a QnA a `.tsv` fájlban, hozza létre a készletet a QnA Maker portálon, mentse, majd exportálja a tudásbázist, hogy példát mutasson a készlet megjelenítésére.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis fejlesztési életciklusa](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

A válaszok formázásához használja a QnA Maker [Markdown-referenciát](../reference-markdown-format.md) .

[A QnA Maker áttekintése](../Overview/overview.md)

Tudásbázis létrehozása és szerkesztése az alábbiakkal:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Válasz létrehozása az alábbiakkal:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
