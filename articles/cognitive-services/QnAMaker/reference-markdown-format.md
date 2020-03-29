---
title: Markdown formátum - QnA Maker
description: Az alábbiakban a QnA Maker válaszszövegében használható markdown-formátumok listája látható.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045391"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>A QnA Maker válaszszövege által támogatott Markdown formátum

A QnA Maker a válaszszöveget markdownként tárolja. Sok ízek markdown. Annak érdekében, hogy a válaszszöveg et megfelelően adja vissza és jelenítse meg, használja ezt a hivatkozást.

A **[CommonMark](https://commonmark.org/help/tutorial/index.html)** oktatóanyag segítségével érvényesítheti a Markdown.Use the CommonMark tutorial to validate your Markdown. A bemutató rendelkezik egy **Try it** funkcióval a gyors másolás / beillesztés érvényesítéséhez.

## <a name="supported-markdown-format"></a>Támogatott markdown formátum

Az alábbiakban a QnA Maker válaszszövegében használható markdown-formátumok listája látható.

|Cél|Formátum|Markdown-példa|Renderelés<br>a hogy megjelenik a Chat bot|
|--|--|--|--|
Egy új vonal két mondat között.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formázás két mondat között](./media/qnamaker-concepts-datasources/format-newline.png)|
|A fejlécek h1-től h6-ig, a fejléc számát `#` jelöli. 1 `#` a h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátum markdown fejlécekkel](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formátum H1–H5 jelfejlécekkel](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Dőlt |`*text*`|`How do I create a bot with *QnA Maker*?`|![formátum dőlt betűvel](./media/qnamaker-concepts-datasources/format-italics.png)|
|Erős (félkövér)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formátum erős jelöléssel a félkövér](./media/qnamaker-concepts-datasources/format-strong.png)|
|Hivatkozás URL-címe|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL formátuma (hivatkozás)](./media/qnamaker-concepts-datasources/format-url.png)|
|*A nyilvános kép URL-címe|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formátum a nyilvános kép URL-címéhez ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Áthúzott|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formátum áthúzáshoz](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Félkövér és dőlt|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formátum félkövér és dőlt](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Félkövér URL-cím a hivatkozáshoz|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![félkövér URL formátuma](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Dőlt URL a hivatkozáshoz|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![dőlt url formátuma](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape markdown szimbólumok|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![dőlt url formátuma](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Rendezett lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Az előző példa a markdownba épített automatikus számozást használja.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Az előző példa explicit számozást használ.|![formátum a rendezett listához](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Rendezetlen listák|`\n * item1 \n * item2`<br>vagy<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![rendezetlen lista formátuma](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Beágyazott listák|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>A rendezett és rendezetlen listákat egymásba ágyazhatja. A tabulátor , `\t`jelzi a gyermekelem behúzási szintjét.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![beágyazott rendezetlen lista formátuma](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![beágyazott rendezett lista formátuma](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*A QnA Maker semmilyen módon nem dolgozza fel a képet. Az ügyfélalkalmazás feladata a lemezkép megjelenítése.

Ha a tartalmat frissítési/cseretudásbázis API-k használatával szeretné hozzáadni, és a content/file html címkéket tartalmaz, megőrizheti a HTML-kódot a fájlban, biztosítva, hogy a címkék megnyitása és zárása kódolt formátumban konvertálódjon.

| HTML megőrzése  | Képviselet az API-kérelemben  | Képviselet KB-ban |
|-----------|---------|-------------------------|
| Igen | \&lt;br\&gt; | &lt;Br&gt; |
| Igen | \&lt;h3\&gt;csatlakozó\&lt;/h3\&gt; | &lt;h3&gt;&lt;fejléc /h3&gt; |

Ezenkívül a CR LF(\r\n) a kb.-ban \n értékre konvertálódik. Az LF(\n) a helyzet szerint marad. Ha meg szeretne szabadulni bármely menekülési sorozattól, például \t vagy\\\\\n, használhatja a fordított perjelet, például: '\\\\r n' és '\\\\t'

## <a name="next-steps"></a>További lépések

Tekintse át a kötegelt [tesztelésfájl-formátumokat.](reference-tsv-format-batch-testing.md)
