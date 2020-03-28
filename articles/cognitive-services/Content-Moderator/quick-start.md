---
title: 'Rövid útmutató: Próbálja ki a tartalommoderátort a weben – Tartalommoderátor'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az online Tartalommoderátor-ellenőrző eszközt fogja használni a Tartalommoderátor alapvető funkcióinak tesztelésére anélkül, hogy kódot kellene írnia.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203563"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Rövid útmutató: Próbálja ki a tartalommoderátort az interneten

Ebben a rövid útmutatóban az online Tartalommoderátor-ellenőrző eszközzel anélkül tesztelheti a Tartalommoderátor alapvető funkcióit, hogy bármilyen kódot kellene írnia. Ha gyorsabban szeretné integrálni ezt a szolgáltatást az alkalmazásba, tekintse meg a további rövid útmutatókat a [Következő lépések](#next-steps) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

- Webböngésző

## <a name="set-up-the-review-tool"></a>Az ellenőrző eszköz beállítása
A Tartalommoderátor-ellenőrző eszköz egy webalapú eszköz, amely lehetővé teszi az emberi bírálók számára, hogy segítsék a kognitív szolgáltatást a döntéshozatalban. Ebben az útmutatóban a felülvizsgálati eszköz beállításának rövid folyamatán megy keresztül, hogy láthassa, hogyan működik a Tartalommoderátor szolgáltatás. Nyissa meg a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyét, és regisztráljon.

![Tartalommoderátor kezdőlapja](images/homepage.PNG)

## <a name="create-a-review-team"></a>Felülvizsgálati csoport létrehozása

Ezután hozzon létre egy felülvizsgálati csapatot. Egy működő forgatókönyv, ez lesz a csoport, akik manuálisan felülvizsgálja a szolgáltatás moderálási döntéseket. Csapat létrehozásához ki kell választania egy **régiót,** és meg kell adnia egy **csapatnevet** és egy **csapatazonosítót.** Ha meg szeretné hívni kollégáit a csapatba, ezt az e-mail címük megadásával teheti meg itt.

> [!NOTE]
> **A csapatnév** a felülvizsgálati csapat rövid neve. Ez a név jelenik meg az Azure Portalon. A **csapatazonosító** segítségével a programokkal azonosíthatja a felülvizsgálati csapatot.

> [!div class="mx-imgBorder"]
> ![Csapattag meghívása](images/create-team.png)

Ha úgy dönt, hogy az adatokat ügyfél által felügyelt kulccsal (CMK) titkosítja, a rendszer kéri a Tartalommoderátor erőforrás **erőforrásazonosítóját** az E0 tarifacsomagban. A megadott erőforrásnak újnak kell lennie. 

> [!div class="mx-imgBorder"]
> ![Csapattag meghívása CMK-vel](images/create-team-cmk.png)

Ha megpróbál újra felhasználni egy tartalommoderátor-erőforrást, a következő figyelmeztetés jelenik meg: 

> [!div class="mx-imgBorder"]
> ![CMK-hiba](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Mintatartalom feltöltése

Most már készen áll a mintatartalom feltöltésére. Válassza **> Kép kipróbálása**, **szöveg >** vagy próbálja ki **> videó**lehetőséget.

![Kép- vagy szövegmoderálás kipróbálása](images/tryimagesortext.png)

Küldje el a tartalmat moderálásra. Belsőleg a felülvizsgálati eszköz meghívja a moderálási API-kat a tartalom beszkafét. Miután a beolvasás befejeződött, megjelenik egy üzenet, amely tájékoztatja Önt arról, hogy az eredmények az értékelésre várnak.

![Közepes fájlok](images/submitted.png)

## <a name="review-moderation-tags"></a>Moderálási címkék áttekintése

Tekintse át az alkalmazott moderálási címkéket. Láthatja, hogy mely címkéket alkalmaztaa a tartalomra, és hogy mi volt a pontszám az egyes kategóriákban. A [kép,](image-moderation-api.md) [a szöveg](text-moderation-api.md)és a [videómoderálás](video-moderation-api.md) témakörében többet is megtudhat arról, hogy mit jeleznek a különböző tartalomcímkék.

![Eredmények áttekintése](images/reviewresults_text.png)

Egy projektben Ön vagy az ellenőrző csoport módosíthatja ezeket a címkéket, vagy szükség szerint további címkéket adhat hozzá. Ezeket a módosításokat a **Tovább** gombbal küldi el. Ahogy az üzleti alkalmazás meghívja a moderátor API-kat, a címkézett tartalom itt sorba áll, készen arra, hogy az emberi ellenőrző csoportok áttekintsék. Ezzel a módszerrel gyorsan áttekintheti a nagy mennyiségű tartalmat.

Ezen a ponton a Tartalommoderátor-ellenőrző eszközt használta a tartalommoderátor-szolgáltatás által megtehető példák megtekintéséhez. Ezután többet is megtudhat a felülvizsgálati eszközről, és arról, hogyan integrálhatja azt egy szoftverprojektbe az API-k áttekintése használatával, vagy átugorhatja a [Következő lépések](#next-steps) szakaszt, ahol megtudhatja, hogyan használhatja a Moderálás API-kat az alkalmazásban.

## <a name="learn-more-about-the-review-tool"></a>További információ a felülvizsgálati eszközről

Ha többet szeretne megtudni a Tartalommoderátor-ellenőrzési eszköz használatáról, tekintse meg a [Véleményezés eszköz](Review-Tool-User-Guide/human-in-the-loop.md) útmutatóját, és tekintse meg a Felülvizsgálati eszköz API-jait, amelyből megtudhatja, hogyan finomíthatja az emberi értékelés élményét:
- A [feladat API](try-review-api-job.md) ellenőrzi a tartalmat a moderálás API-k használatával, és értékeléseket hoz létre a felülvizsgálati eszközben. 
- A [Felülvizsgálati API](try-review-api-review.md) közvetlenül kép-, szöveg- vagy videoértékeléseket hoz létre az emberi moderátorok számára anélkül, hogy először beszkavalnák a tartalmat. 
- A [Munkafolyamat API](try-review-api-workflow.md) létrehozza, frissíti és leadja a csapat által létrehozott egyéni munkafolyamatok részleteit.

Vagy folytassa a következő lépésekkel a moderálási API-k használatának megkezdéséhez a kódban.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja a Moderálás API-kat az alkalmazásban.
- Képmoderálás megvalósítása. Használja az [API-konzolt,](try-image-api.md) vagy kövesse a [.NET SDK gyorsindítást](dotnet-sdk-quickstart.md) a képek bekéseléséhez és a potenciális felnőtt és pikáns tartalom észleléséhez címkék, megbízhatósági pontszámok és egyéb kinyert információk használatával.
- Szövegmoderálás megvalósítása. Használja az [API-konzolt,](try-text-api.md) vagy használja a [.NET SDK gyorsindítást](dotnet-sdk-quickstart.md) a szöveges tartalom lehetséges káromkodás, géppel támogatott nem kívánt szövegbesorolás (előzetes verzió) és személyes adatok vizsgálatához.
- Videómoderálás megvalósítása. Kövesse a [C# videómoderálási útmutatóját a](video-moderation-api.md) videók bekereséséhez és a potenciális felnőtt és pikáns tartalmak észleléséhez. 
