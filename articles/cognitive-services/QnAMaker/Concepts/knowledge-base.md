---
title: Importálás adatforrásokból - QnA Maker
description: A QnA Maker tudásbázis a qna-párokhoz tartozó kérdés-válasz (QnA) készletekből és választható metaadatokból áll.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7772b2c76dfb92ced4d2c0d4b8dae97a59d1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457821"
---
# <a name="importing-from-data-sources"></a>Importálás adatforrásokból

A tudásbázis nyilvános URL-ek és fájlok által bevitt kérdés-válasz halmazokból áll.

## <a name="data-source-locations"></a>Adatforrás-helyek

A tartalom adatforrásból kerül a tudásbázisba. Az adatforrás-helyek **nyilvános URL-címek vagy fájlok,** amelyek nem igényelnek hitelesítést.

A hitelesítéssel védett [SharePoint-fájlok](../how-to/add-sharepoint-datasources.md)kivételt képeznek. A SharePoint-erőforrásoknak fájloknak kell lenniük, nem weblapoknak. Ha az URL-cím webkiterjesztéssel végződik, például . ASPX, nem importálja a QnA Maker-be a SharePointból.

## <a name="chit-chat-content"></a>Csevegés tartalma

A Chit chat QnA tartalomkészlet teljes tartalomadatforrásként érhető el több nyelven és társalgási stílusban. Ez lehet a kiindulási pont a bot személyiségét, és ez takarít meg az időt és költséget az írás őket a semmiből. További információ [arról, hogyan adhat hozzá](../how-to/chit-chat-knowledge-base.md) ezt a tartalomkészletet a tudásbázishoz.

## <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálás útján

A tudásbázis importálása lecseréli a meglévő tudásbázis tartalmát. Az importáláshoz `.tsv` strukturált fájlra van szükség, amely kérdéseket és választ tartalmaz. Ez az információ segít a QnA Maker csoportnak a kérdés-válasz készletek és attribútum őket egy adott adatforráshoz.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| 1. kérdés | Válasz1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 2. kérdés | 2. válasz | Szerkesztői|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturált többfordulatos formátum importálás útján

A többfordulatos beszélgetéseket `.tsv` fájlformátumban is létrehozhatja. A formátum lehetővé teszi a többfordulatos beszélgetések létrehozását a korábbi csevegési naplók elemzésével (más folyamatokkal, `.tsv` nem a QnA Maker használatával), majd a fájl automatizálással történő létrehozásához. Importálja a fájlt a meglévő tudásbázis lecseréléséhez.

> [!div class="mx-imgBorder"]
> ![Fogalmi modell 3 szint multi-turn kérdés](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A többfordulatos `.tsv`( multi-turn ) oszlopa a **Prompts**. Az `.tsv`Excelben látható példa a többfordulatos gyermekek meghatározásához szükséges információkat jeleníti meg:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

A **displayOrder** numerikus, a **displayText** pedig olyan szöveg, amely nem tartalmazhat markdownt.

> [!div class="mx-imgBorder"]
> ![Többfordulatos kérdés példa az Excelben látható módon](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportálás példaként

Ha nem biztos benne, hogyan kell képviselni `.tsv` a QnA meg a fájlban:
* Használja ezt a [letölthető példát a GitHubról](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Vagy hozza létre a készletet a QnA Maker portálon, mentse, majd exportálja a tudásbázist egy példa, hogyan kell képviselni a készlet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tudásbázisok fejlesztési életciklusa](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Lásd még

A QnA Maker [Markdown hivatkozássegítségével](../reference-markdown-format.md) formázhatja válaszait.

[A QnA Maker áttekintése](../Overview/overview.md)

Tudásbázis létrehozása és szerkesztése a következőkkel:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Válasz létrehozása a következőkkel:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
