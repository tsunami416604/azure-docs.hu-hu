---
title: 'Gyors útmutató: Ismerkedés a Content Moderator'
titlesuffix: Azure Cognitive Services
description: Ismerkedés a Content Moderator módja.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: f25434814a7fb3d0f49cab539b394970c9bcfb3b
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023440"
---
# <a name="quickstart-get-familiar-with-content-moderator"></a>Gyors útmutató: Ismerkedjen meg a Content Moderator

Ebben a rövid útmutatóban a használandó kód írása nélkül a Content Moderator alapvető funkciói kipróbálásához az online Content Moderator felülvizsgálati eszközben. Ha szeretné ezt a szolgáltatást integrálja az alkalmazás időnél gyorsabban van szüksége, tekintse meg a lévő többi rövid útmutató a [további lépések](#next-steps) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Egy webes böngésző

## <a name="set-up-the-review-tool"></a>Állítsa be a felülvizsgálati eszköz
A Content Moderator felülvizsgálati eszköz egy webalapú eszköz, amely lehetővé teszi, hogy az emberi teszik a felülvizsgálók számára a cognitive Services-szolgáltatás támogatási döntések meghozatalában. Ebben az útmutatóban mindenképpen haladjon végig a rövid folyamat beállítása után a vizsgálóeszközt, így láthatja a Content Moderator szolgáltatás működésével. Nyissa meg a [Content Moderator felülvizsgálati eszközben](http://contentmoderator.cognitive.microsoft.com/) webhelyről, és regisztráljon.

![Tartalom Moderator kezdőlapja](images/homepage.PNG)

## <a name="create-a-review-team"></a>Tekintse át a csoport létrehozása

Ezután hozzon létre egy csapatától. Egy működő esetben ez lesz a csoport, akik manuálisan áttekinti a szolgáltatás moderálás döntéseket hozhat. Egyelőre csak szeretne létrehozni a csoport nevét. Ha szeretne meghívhatja munkatársait a csapatának, írja be az e-mail-címüket itt megteheti.

![Csapattag meghívása](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Minta tartalom feltöltése

Most már készen áll a minta tartalom feltöltése. Válassza ki **próbálja > lemezkép**, **próbálja > szöveg**, vagy **próbálja > videó**.

![Próbálja ki a kép vagy szöveg moderálása](images/tryimagesortext.png)

Küldje el a tartalmat a moderálás. A felülvizsgálati eszköz belsőleg meghívja a moderálási API-k vizsgálata a tartalom. Vizsgálatát befejeződése után megjelenik egy üzenet arról értesíti, hogy nincsenek-e Várakozás a felülvizsgálati eredmények.

![Mérsékelt fájlok](images/submitted.png)

## <a name="review-moderation-tags"></a>Tekintse át a moderálás címkék

Tekintse át a moderálás alkalmazott címkéket. Láthatja, hogy mely címkéket alkalmazta a tartalomhoz, és mi az pontszám az egyes kategóriák volt.

![Eredmények áttekintése](images/reviewresults_text.png)

A projektben akkor vagy a csapatától módosíthatja ezekkel a címkékkel vagy igény szerint további címkék hozzáadására. Fogjuk ezeket a módosításokat a elküldését a **tovább** gombra. Az üzleti alkalmazás meghívja a Moderator API-kat, mert a címkézett tartalom várólistára helyezését itt, készen áll arra, át kell néznie az emberi ellenőrző csapat. Nagy mennyiségű tartalmat, ezzel a megközelítéssel gyorsan áttekintheti.

A Content Moderator felülvizsgálati eszközben használt ezen a ponton a Content Moderator szolgáltatás teheti példák. Ezt követően akár többet is megtudhat a vizsgálóeszközt és integrálják őket, a szoftver projekt áttekintéséhez API-val kapcsolatban, vagy továbbléphet a a [további lépések](#next-steps) részből megtudhatja, hogyan használható a moderálási API-k magukat az alkalmazásban.

## <a name="learn-more-about-the-review-tool"></a>További információ a felülvizsgálati eszköz

A Content Moderator tekintse át eszköz használatával kapcsolatos további tudnivalókért tekintse meg a [emberi hurok](Review-Tool-User-Guide/human-in-the-loop.md) útmutatót, és tekintse meg a tekintse át eszköz API-k megtudhatja, hogyan finomhangolása az emberi ellenőrző élmény:
- A [feladat API](try-review-api-job.md) a tartalmat a moderálási API-k használatával, és értékelések készít a felülvizsgálati eszközben. 
- A [felülvizsgálati API](try-review-api-review.md) közvetlenül hoz létre kép, szöveg vagy videót felülvizsgálatok emberi moderátorok a tartalom ellenőrzése nélkül. 
- A [munkafolyamat API](try-review-api-workflow.md) hoz létre, frissítését és az egyéni munkafolyamatokat, amely a csapat létrehozza részleteit.

Vagy folytassa a következő lépésekkel a kódban a moderálási API-k használatának első lépéseit.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható a moderálási API-k magukat az alkalmazásban.
- Képmoderálás megvalósításához. Használja a [API-konzol](try-image-api.md) vagy a [ C# rövid](image-moderation-quickstart-dotnet.md) kiszűrhető, és észlelheti a potenciális felnőtt és szexuális tartalom címkék, megbízhatósági pontszámokat és egyéb használatával a kinyert információkat.
- Szövegmoderálás megvalósításához. Használja a [API-konzol](try-text-api.md) vagy használja a [ C# rövid](text-moderation-quickstart-dotnet.md) tartalomszűrés, nem kívánatos szöveg gépi támogatású besorolás (előzetes verzió), a szöveges tartalom vizsgálata és a személyes azonosításra alkalmas adatokat (PII). 
- Videomoderálás megvalósításához. Használja a [ C# rövid](video-moderation-api.md) videók vizsgálata, és észlelheti a potenciális felnőtt és szexuális tartalom. 
