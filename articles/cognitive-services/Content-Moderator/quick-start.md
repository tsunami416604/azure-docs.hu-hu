---
title: 'Gyors útmutató: Próbálja ki a Content Moderator a weben - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutatóban az online Content Moderator felülvizsgálati eszköz használandó kód írása nélkül a Content Moderator alapvető funkciói kipróbálásához.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: cfaf0bf20449262fcebb6cefc1b0a499c6190ae8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861170"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Gyors útmutató: Próbálja ki a Content Moderator a weben

Ez a rövid útmutatóban az online Content Moderator felülvizsgálati eszköz használandó kód írása nélkül a Content Moderator alapvető funkciói kipróbálásához. Ha szeretné ezt a szolgáltatást integrálja az alkalmazás időnél gyorsabban van szüksége, tekintse meg a lévő többi rövid útmutató a [további lépések](#next-steps) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Egy webes böngésző

## <a name="set-up-the-review-tool"></a>Állítsa be a felülvizsgálati eszköz
A Content Moderator felülvizsgálati eszköz egy webalapú eszköz, amely lehetővé teszi, hogy az emberi teszik a felülvizsgálók számára a cognitive Services-szolgáltatás támogatási döntések meghozatalában. Ebben az útmutatóban mindenképpen haladjon végig a rövid folyamat beállítása után a vizsgálóeszközt, így láthatja a Content Moderator szolgáltatás működésével. Nyissa meg a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyről, és regisztráljon.

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

Tekintse át a moderálás alkalmazott címkéket. Láthatja, hogy mely címkéket alkalmazta a tartalomhoz, és mi az pontszám az egyes kategóriák volt. Tekintse meg a [kép](image-moderation-api.md), [szöveg](text-moderation-api.md), és [videó](video-moderation-api.md) moderálás témakörökben tájékozódhat a különböző tartalmak címkék jelzi.

![Eredmények áttekintése](images/reviewresults_text.png)

A projektben akkor vagy a csapatától módosíthatja ezekkel a címkékkel vagy igény szerint további címkék hozzáadására. Fogjuk ezeket a módosításokat a elküldését a **tovább** gombra. Az üzleti alkalmazás meghívja a Moderator API-kat, mert a címkézett tartalom várólistára helyezését itt, készen áll arra, át kell néznie az emberi ellenőrző csapat. Nagy mennyiségű tartalmat, ezzel a megközelítéssel gyorsan áttekintheti.

A Content Moderator felülvizsgálati eszköz ezen a ponton rendelkezik használt példák a Content Moderator szolgáltatás teheti. Ezt követően akár többet is megtudhat a vizsgálóeszközt és integrálják őket, a szoftver projekt áttekintéséhez API-val kapcsolatban, vagy továbbléphet a a [további lépések](#next-steps) részből megtudhatja, hogyan használható a moderálási API-k magukat az alkalmazásban.

## <a name="learn-more-about-the-review-tool"></a>További információ a felülvizsgálati eszköz

A Content Moderator, tekintse át az eszközzel kapcsolatos további tudnivalókért tekintse meg a [vizsgálóeszköz](Review-Tool-User-Guide/human-in-the-loop.md) útmutatót, és tekintse meg a felülvizsgálati eszköz API-k segítségével megtudhatja, hogyan finomhangolása az emberi ellenőrző élmény:
- A [feladat API](try-review-api-job.md) a tartalmat a moderálási API-k használatával, és értékelések készít a felülvizsgálati eszközben. 
- A [felülvizsgálati API](try-review-api-review.md) közvetlenül hoz létre kép, szöveg vagy videót felülvizsgálatok emberi moderátorok a tartalom ellenőrzése nélkül. 
- A [munkafolyamat API](try-review-api-workflow.md) hoz létre, frissítését és az egyéni munkafolyamatokat, amely a csapat létrehozza részleteit.

Vagy folytassa a következő lépésekkel a kódban a moderálási API-k használatának első lépéseit.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható a moderálási API-k magukat az alkalmazásban.
- Képmoderálás megvalósításához. Használja a [API-konzol](try-image-api.md) vagy a [ C# rövid](image-moderation-quickstart-dotnet.md) kiszűrhető, és észlelheti a potenciális felnőtt és szexuális tartalom címkék, megbízhatósági pontszámokat és egyéb használatával a kinyert információkat.
- Szövegmoderálás megvalósításához. Használja a [API-konzol](try-text-api.md) vagy használja a [ C# rövid](text-moderation-quickstart-dotnet.md) tartalomszűrés, nem kívánatos szöveg gépi támogatású besorolás (előzetes verzió), a szöveges tartalom vizsgálata és a személyes azonosításra alkalmas adatokat (PII). 
- Videomoderálás megvalósításához. Kövesse a [videó moderálás útmutató útmutató a C# ](video-moderation-api.md) videók vizsgálata, és észlelheti a potenciális felnőtt és szexuális tartalom. 
