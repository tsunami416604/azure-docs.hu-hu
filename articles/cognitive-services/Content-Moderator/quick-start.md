---
title: 'Gyors útmutató: Content Moderator kipróbálása a Web-Content Moderator'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az online Content Moderator felülvizsgálati eszközt fogja használni a Content Moderator alapvető funkcióinak teszteléséhez anélkül, hogy kódot kellene írnia.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb4fc076d01c1108278cea0cebba958b4ea94660
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044055"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Gyors útmutató: Content Moderator kipróbálása a weben

Ebben a rövid útmutatóban az online Content Moderator felülvizsgálati eszközt fogja használni a Content Moderator alapvető funkcióinak teszteléséhez anélkül, hogy kódot kellene írnia. Ha gyorsabban szeretné integrálni ezt a szolgáltatást az alkalmazásba, tekintse meg a többi rövid útmutatót a [következő lépések](#next-steps) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Egy webböngésző

## <a name="set-up-the-review-tool"></a>A felülvizsgálati eszköz beállítása
A Content Moderator felülvizsgálati eszköz egy webalapú eszköz, amely lehetővé teszi, hogy az emberi felülvizsgálók segítsek a kognitív szolgáltatást a döntések meghozatalában. Ebben az útmutatóban elsajátíthatja a felülvizsgálati eszköz beállításának rövid folyamatát, hogy láthassa, hogyan működik a Content Moderator szolgáltatás. Lépjen a [Content moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyére, és regisztráljon.

![Content Moderator Kezdőlap](images/homepage.PNG)

## <a name="create-a-review-team"></a>Felülvizsgálati csapat létrehozása

Ezután hozzon létre egy felülvizsgálati csapatot. A munkahelyen ez azon személyek csoportja lesz, akik manuálisan áttekintik a szolgáltatás moderálási döntéseit. Egyelőre csak a csapat nevét kell létrehoznia. Ha meg szeretné hívni a munkatársakat a csapatnak, az e-mail-címei Itt megadhatók.

![Csapattag meghívása](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Minta tartalmának feltöltése

Most már készen áll a minta tartalmának feltöltésére. Válassza ki a **kipróbálás > a rendszerkép**lehetőséget, **próbálkozzon > szöveggel**, vagy **próbálja meg > videót**.

![Képek vagy szöveges moderálás kipróbálása](images/tryimagesortext.png)

A tartalom moderálás céljából történő elküldése. Belsőleg a felülvizsgálati eszköz meghívja a moderálási API-kat a tartalom vizsgálatához. Miután a vizsgálat befejeződött, megjelenik egy üzenet, amely tájékoztatja arról, hogy az eredmények várnak az ellenőrzésre.

![Közepes méretű fájlok](images/submitted.png)

## <a name="review-moderation-tags"></a>Moderációs címkék áttekintése

Tekintse át az alkalmazott moderálási címkéket. Láthatja, hogy mely címkék lettek alkalmazva a tartalomra, és hogy a pontszám milyen kategóriában volt. A [képekkel](image-moderation-api.md), [szövegekkel](text-moderation-api.md)és [videó](video-moderation-api.md) -moderálással kapcsolatos témakörökből megtudhatja, hogy mit jelez a különböző tartalmi címkék.

![Eredmények áttekintése](images/reviewresults_text.png)

Egy projektben Ön vagy a felülvizsgálati csapat módosíthatja ezeket a címkéket, vagy szükség szerint további címkéket is hozzáadhat. Ezeket a módosításokat a **Next (tovább** ) gombbal küldheti el. Ahogy az üzleti alkalmazás meghívja a moderátori API-kat, a címkézett tartalom itt fog megjelenni, és készen áll az emberi felülvizsgálati csapatok általi felülvizsgálatra. Ezt a módszert követve gyorsan áttekintheti a nagy mennyiségű tartalmat.

Ezen a ponton a Content Moderator felülvizsgálati eszköz használatával láthatta a Content Moderator szolgáltatás által elvégezhető példákat. Ezután megtudhatja, hogyan integrálhatja a felülvizsgálati eszközt, és hogyan integrálhatja azt egy szoftveres projektbe az API-k áttekintése segítségével, vagy kihagyhatja a [következő lépések](#next-steps) szakaszt, amelyből megtudhatja, hogyan használhatja saját maga a moderálási API-kat az alkalmazásban.

## <a name="learn-more-about-the-review-tool"></a>További információ a felülvizsgálati eszközről

Ha többet szeretne megtudni a Content Moderator felülvizsgálati eszköz használatáról, tekintse meg a [felülvizsgálati](Review-Tool-User-Guide/human-in-the-loop.md) eszköz útmutatását, és tekintse meg a felülvizsgálati eszköz API-kat, hogy megtudja, hogyan finomíthatja a humán felülvizsgálati élményt:
- A [feladatok API](try-review-api-job.md) a moderálási API-k használatával vizsgálja meg a tartalmat, és a felülvizsgálati eszközben hozza létre az értékeléseket. 
- A [felülvizsgálati API](try-review-api-review.md) közvetlenül a tartalom első vizsgálata nélkül hozza létre az emberi moderátorok képét, szövegét vagy videós felülvizsgálatát. 
- A [munkafolyamat API](try-review-api-workflow.md) hozza létre, frissíti és beolvassa a csoport által létrehozott egyéni munkafolyamatok részleteit.

Vagy folytassa a következő lépésekkel a kód moderálási API-jai használatának megkezdéséhez.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja saját maga a moderálási API-kat az alkalmazásban.
- Rendszerkép-moderálás implementálása. Az [API-konzollal](try-image-api.md) vagy a [.net SDK](dotnet-sdk-quickstart.md) gyors üzembe helyezésével ellenőrizheti a képeket, és felderítheti a potenciális felnőtteket és a zamatos tartalmakat címkék, megbízhatósági pontszámok és egyéb kinyert adatok használatával.
- Szöveges moderálás implementálása. Használja az [API-konzolt](try-text-api.md) , vagy használja a [.net SDK](dotnet-sdk-quickstart.md) gyors verzióját a szöveges tartalom vizsgálatához a lehetséges trágárság, a gépi támogatású nemkívánatos szöveg besorolása (előzetes verzió) és a személyes adatokat.
- Videó-moderálás implementálása. Kövesse a [videó moderálásának útmutatóját C# ](video-moderation-api.md) a videók vizsgálatához és a potenciális felnőtt és a zamatos tartalmak észleléséhez. 
