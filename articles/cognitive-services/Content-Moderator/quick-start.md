---
title: 'Gyors útmutató: Content Moderator kipróbálása a weben'
titleSuffix: Azure Cognitive Services
description: Az online Content Moderator felülvizsgálati eszköz használatával tesztelheti a Content Moderator alapvető funkcióit anélkül, hogy kódot kellene írnia.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: tartalom moderátora, tartalom moderálása
ms.openlocfilehash: 025c8fcf98a31d7b3380ee2530428d08428493fb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91596812"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Gyors útmutató: Content Moderator kipróbálása a weben

Ebben a rövid útmutatóban az online Content Moderator felülvizsgálati eszköz használatával tesztelheti a Content Moderator alapvető funkcióit anélkül, hogy kódot kellene írnia. Ha gyorsabban szeretné integrálni ezt a szolgáltatást a Content moderációs alkalmazásba, tekintse meg a többi rövid útmutatót a [következő lépések](#next-steps) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Egy webböngésző

## <a name="set-up-the-review-tool"></a>A felülvizsgálati eszköz beállítása

A Content Moderator felülvizsgálati eszköz egy webalapú eszköz, amely lehetővé teszi, hogy az emberi felülvizsgálók segítsek a kognitív szolgáltatást a döntések meghozatalában. Ebben az útmutatóban elsajátíthatja a felülvizsgálati eszköz beállításának rövid folyamatát, hogy láthassa, hogyan működik a Content Moderator szolgáltatás. Lépjen a [Content moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyére, és regisztráljon.

![Content Moderator Kezdőlap](images/homepage.PNG)

## <a name="create-a-review-team"></a>Felülvizsgálati csapat létrehozása

Ezután hozzon létre egy felülvizsgálati csapatot. A munkahelyen ez azon személyek csoportja lesz, akik manuálisan áttekintik a szolgáltatás moderálási döntéseit. Csapat létrehozásához ki kell választania egy **régiót**, és meg kell adnia a **csoport nevét** és a **csoport azonosítóját**. Ha meg szeretné hívni a munkatársakat a csapatnak, az e-mail-címei Itt megadhatók.

> [!NOTE]
> A **csapat neve** a felülvizsgálati csapat rövid neve. Ez a név jelenik meg a Azure Portalban. A **csapat azonosítója** a felülvizsgálati csapat programozott módon azonosítására szolgál.

> [!div class="mx-imgBorder"]
> ![Csapattag meghívása](images/create-team.png)

Ha ügyfél által felügyelt kulcs (CMK) használatával titkosítja az adattitkosítást, a rendszer a E0-díjszabási szinten kéri a Content Moderator-erőforrás **erőforrás-azonosítójának** megadását. Az Ön által megadott erőforrásnak újnak kell lennie. 

> [!div class="mx-imgBorder"]
> ![Csapattag meghívása a CMK](images/create-team-cmk.png)

Ha egy Content Moderator erőforrás újrafelhasználását kísérli meg, akkor a következő figyelmeztetés jelenik meg: 

> [!div class="mx-imgBorder"]
> ![CMK hiba](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Minta tartalmának feltöltése

Most már készen áll a minta tartalmának feltöltésére. Válassza ki a **kipróbálás > a rendszerkép**lehetőséget, **próbálkozzon > szöveggel**, vagy **próbálja meg > videót**.

![Képek vagy szöveges moderálás kipróbálása](images/tryimagesortext.png)

A tartalom moderálás céljából történő elküldése. Belsőleg a felülvizsgálati eszköz meghívja a moderálási API-kat a tartalom vizsgálatához. Miután a vizsgálat befejeződött, megjelenik egy üzenet, amely tájékoztatja arról, hogy az eredmények várnak az ellenőrzésre.

![Közepes méretű fájlok](images/submitted.png)

## <a name="review-moderation-tags"></a>Moderációs címkék áttekintése

Tekintse át az alkalmazott moderálási címkéket. Láthatja, hogy mely címkék lettek alkalmazva a tartalomra, és hogy a pontszám milyen kategóriában volt. A [képekkel](image-moderation-api.md), [szövegekkel](text-moderation-api.md)és [videó](video-moderation-api.md) -moderálással kapcsolatos témakörökből megtudhatja, hogy mit jelez a különböző tartalmi címkék.

![Az eredmények áttekintése](images/reviewresults_text.png)

Egy projektben Ön vagy a felülvizsgálati csapat módosíthatja ezeket a címkéket, vagy szükség szerint további címkéket is hozzáadhat. Ezeket a módosításokat a **Next (tovább** ) gombbal küldheti el. Ahogy az üzleti alkalmazás meghívja a moderátori API-kat, a címkézett tartalom itt fog megjelenni, és készen áll az emberi felülvizsgálati csapatok általi felülvizsgálatra. Ezt a módszert követve gyorsan áttekintheti a nagy mennyiségű tartalmat.

Ezen a ponton a Content Moderator felülvizsgálati eszköz használatával láthatta a Content Moderator szolgáltatás által elvégezhető példákat. Ezután megtudhatja, hogyan integrálhatja a felülvizsgálati eszközt, és hogyan integrálhatja azt egy szoftveres projektbe az API-k áttekintése segítségével, vagy kihagyhatja a [következő lépések](#next-steps) szakaszt, amelyből megtudhatja, hogyan használhatja saját maga a moderálási API-kat az alkalmazásban.

## <a name="learn-more-about-the-review-tool"></a>További információ a felülvizsgálati eszközről

Ha többet szeretne megtudni a Content Moderator felülvizsgálati eszköz használatáról, tekintse meg a [felülvizsgálati](Review-Tool-User-Guide/human-in-the-loop.md) eszköz útmutatását, és tekintse meg a felülvizsgálati eszköz API-kat, hogy megtudja, hogyan finomíthatja a humán felülvizsgálati élményt:
- A [feladatok API](try-review-api-job.md) a moderálási API-k használatával vizsgálja meg a tartalmat, és a felülvizsgálati eszközben hozza létre az értékeléseket. 
- A [felülvizsgálati API](try-review-api-review.md) közvetlenül a tartalom első vizsgálata nélkül hozza létre az emberi moderátorok képét, szövegét vagy videós felülvizsgálatát. 
- A [munkafolyamat API](try-review-api-workflow.md) hozza létre, frissíti és beolvassa a csoport által létrehozott egyéni munkafolyamatok részleteit.

Vagy folytassa a következő lépésekkel a kód moderálási API-jai használatának megkezdéséhez.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja saját maga a moderálási API-kat az alkalmazásban.
- Rendszerkép-moderálás implementálása. Az [API-konzollal](try-image-api.md) vagy az [ügyféloldali kódtár](client-libraries.md) gyors üzembe helyezésével ellenőrizheti a képeket, és felderítheti a potenciális felnőtt és a zamatos tartalmakat címkék, megbízhatósági pontszámok és egyéb kinyert adatok használatával.
- Szöveges moderálás implementálása. Az [API-konzollal](try-text-api.md) vagy az [ügyféloldali kódtár](client-libraries.md) rövid útmutatójának használatával vizsgálja meg a szöveges tartalmat a lehetséges trágárság, a gépi támogatással nem rendelkező szöveges besorolás (előzetes verzió) és a személyes adatokat.
- Videó-moderálás implementálása. Kövesse a [videó moderálásának útmutatóját a C#-](video-moderation-api.md) hoz a videók vizsgálatához és a potenciális felnőtt és a zamatos tartalmak észleléséhez. 
