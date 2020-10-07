---
title: Importálás adatforrásokból – QnA Maker
description: Megtudhatja, hogyan importálhat kérdés-és válasz-párokat adatforrásokból – QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5f609dc27aa1251cfad0249d26ef5140936bfe41
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776952"
---
# <a name="importing-from-data-sources"></a>Importálás adatforrásokból

A Tudásbázis a nyilvános URL-címek és fájlok által benyújtott kérdések és válaszok párokból áll.

## <a name="data-source-locations"></a>Adatforrás helyei

A tartalom egy adatforrásból származó tudásbázisba kerül. Az adatforrás helyei **nyilvános URL-címek vagy fájlok**, amelyek nem igényelnek hitelesítést.

A hitelesítéssel védett [SharePoint-fájlok](../how-to/add-sharepoint-datasources.md)kivételt képeznek. A SharePoint-erőforrásoknak fájlokat, nem weblapokat kell tartalmazniuk. Ha az URL-cím egy webes kiterjesztéssel végződik, pl. ASPX, a rendszer nem importálja azt a SharePointból a QnA Makerbe.

## <a name="chit-chat-content"></a>Chit-csevegési tartalom

A Chit Chat QnA teljes tartalom-adatforrásként van felkínálva több nyelven és társalgási stílusban. Ez lehet a bot személyiségének kiindulási pontja, és megtakarítja az időt és a költségeket a semmiből való írás során. Megtudhatja [, hogyan adhatja hozzá](../how-to/chit-chat-knowledge-base.md) ezt a tartalmat a tudásbázishoz.

## <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálással

A Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Az importáláshoz olyan strukturált `.tsv` fájl szükséges, amely kérdéseket és válaszokat tartalmaz. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturált többsoros formátum importálással

A többszörös kapcsolású beszélgetéseket fájlformátumban hozhatja létre `.tsv` . A formátum lehetővé teszi a többfordulatos beszélgetések létrehozását a korábbi csevegési naplók elemzésével (más folyamatokkal, nem a QnA Maker használatával), majd a fájl létrehozását az `.tsv` Automation segítségével. Importálja a fájlt a meglévő Tudásbázis cseréjéhez.

> [!div class="mx-imgBorder"]
> ![3 szintű többfunkciós kérdés fogalmi modellje](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A többszörös bekapcsolás oszlopa a többszörös `.tsv` bekapcsolásra vonatkozik. **Prompts** Az `.tsv` Excelben látható példa a több bekapcsolású gyermekeket definiáló információk megjelenítéséhez:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

A **displayOrder** numerikus, és a **szöveg** olyan szöveg, amely nem tartalmaz Markdown.

> [!div class="mx-imgBorder"]
> ![Példa az Excelben látható, többszörösen elérhető kérdésekre](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportálás példaként

Ha nem biztos abban, hogy az QnA-párt képviselje a `.tsv` fájlban:
* Ezt a [letölthető példát használhatja a githubról](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Vagy hozza létre a párt a QnA Maker portálon, mentse, majd exportálja a tudásbázist egy példaként, hogy miként reprezentálja a párt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A tudásbázisok fejlesztési életciklusa](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

A válaszok formázásához használja a QnA Maker [Markdown-referenciát](../reference-markdown-format.md) .

[A QnA Maker áttekintése](../Overview/overview.md)

Tudásbázis létrehozása és szerkesztése az alábbiakkal:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Válasz létrehozása az alábbiakkal:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
