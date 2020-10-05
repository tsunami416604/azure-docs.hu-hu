---
title: Mi az QnA Maker szolgáltatás?
description: A QnA Maker egy felhőalapú NLP-szolgáltatás, amely könnyen létrehoz egy természetes társalgási réteget az adataiban. A felhasználó az egyéni Tudásbázis (KB) adatai alapján megkeresheti a legmegfelelőbb választ az adott természetes nyelvi bevitelhez.
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: d2ff2d789f2ea1ae6018d95ef1d880da87b4ff74
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "83994867"
---
# <a name="what-is-the-qna-maker-service"></a>Mi a QnA Maker szolgáltatás?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

A QnA Maker egy felhőalapú természetes nyelvi feldolgozó (NLP) szolgáltatás, amely könnyen létrehoz egy természetes társalgási réteget az adataiban. A felhasználó az egyéni Tudásbázis (KB) adatai alapján megkeresheti a legmegfelelőbb választ az adott természetes nyelvi bevitelhez.

QnA Maker-ügyfélalkalmazás bármely olyan társalgási alkalmazás, amely természetes nyelven kommunikál a felhasználóval a kérdés megválaszolásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.

## <a name="when-to-use-qna-maker"></a>Mikor kell használni a QnA Maker

* **Ha statikus információval rendelkezik** – használja a QnA Maker, ha a válaszokat a Tudásbázisban statikus információkkal látja el. Ez a Tudásbázis az Ön igényeinek megfelelően egyedi, amelyet dokumentumok, például [PDF-EK és URL-címek](../concepts/content-types.md)használatával készített.
* **Ha ugyanazt a választ szeretné adni egy kérelemhez, kérdéshez vagy parancshoz** – ha a különböző felhasználók ugyanezt a kérdést nyújtják be, ugyanazt a választ adja vissza.
* **Ha a metaadatok alapján szeretné szűrni a statikus adatokat** – a [metaadat](../how-to/metadata-generateanswer-usage.md) -Címkék hozzáadása lehetőséggel további szűrési lehetőségeket biztosíthat az ügyfélalkalmazás felhasználóival és az információkkal kapcsolatban. A metaadatok általános információi közé tartozik a [Chit-Chat](../how-to/chit-chat-knowledge-base.md), a tartalom típusa vagy a formátum, a tartalom célja és a tartalom frissessége.
* **Ha olyan robot-beszélgetést szeretne kezelni, amely statikus adatokat tartalmaz** – a Tudásbázis egy felhasználó beszélgetési szövegét vagy parancsát veszi át, és válaszol rá. Ha a válasz egy előre meghatározott beszélgetési folyamat része, amely a Tudásbázisban a [többfordulatos kontextusban](../how-to/multiturn-conversation.md)szerepel, a robot egyszerűen megadhatja ezt a folyamatot.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>QnA Maker Tudásbázis használata egy csevegési robotban

QnA Maker Tudásbázis közzétételét követően az ügyfélalkalmazás egy kérdést küld a Tudásbázis-végpontnak, és JSON-válaszként fogadja az eredményeket. QnA Maker egy közös ügyfélalkalmazás egy csevegési robot.

![Kérdezzen egy robotot, és kapjon választ a Tudásbázis tartalmából](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Lépés|Művelet|
|:--|:--|
|1|Az ügyfélalkalmazás elküldi a felhasználó _kérdését_ (a szöveg a saját szavaival), "hogyan programozott módon frissíti a tudásbázist?" a Tudásbázis-végponthoz.|
|2|A QnA Maker a betanított Tudásbázis használatával biztosítja a megfelelő választ, valamint azokat a követési kérdéseket, amelyek a legjobb válasz keresésének pontosítására használhatók. QnA Maker egy JSON-formátumú választ ad vissza.|
|3|Az ügyfélalkalmazás a JSON-választ használva döntéseket hoz a beszélgetés folytatásához. Ezek a döntések magukban foglalhatják a legfontosabb választ, és további lehetőségeket mutatnak be a legjobb válasz keresésének pontosítására. |
|||

## <a name="what-is-a-knowledge-base"></a>Mi az a Tudásbázis?

QnA Maker [importálja a tartalmat](../concepts/knowledge-base.md) a kérdések és válaszok pár tudásbázisba. Az importálási folyamat kigyűjti a strukturált és a félig strukturált tartalom részei közötti kapcsolat adatait, hogy az a kérdés és a válasz párok közötti kapcsolatokat is magában foglalja. A kérdések és válaszok párokat szerkesztheti, vagy új párokat adhat hozzá.

A kérdés-válasz pár tartalma a következőket tartalmazza:
* A kérdés összes alternatív formája
* A keresés során a válaszadási lehetőségek szűréséhez használt metaadatok címkéi
* Követési kérések a keresés finomításának folytatásához

![Példa kérdésre és válaszra a metaadatokkal](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Miután közzétette a tudásbázist, egy ügyfélalkalmazás elküldi a felhasználó kérdését a végpontnak. A QnA Maker szolgáltatás feldolgozza a kérdést, és válaszol a legjobb válaszra.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Kód nélküli robot létrehozása, kezelése és közzététele

A QnA Maker portál a teljes Tudásbázis-létrehozási élményt biztosítja. Az aktuális formában importálhatja a dokumentumokat a tudásbázisba. Ezek a dokumentumok (például a gyakori kérdések, a manuális, a számolótáblák vagy a weblapok) át lesznek alakítva kérdés-válasz párokba. Az egyes párok elemzése a követő kérések és a más párokhoz való csatlakozás alapján történik. A végső _Markdown_ formátuma támogatja a gazdag bemutatót, beleértve a képeket és a hivatkozásokat.

A Tudásbázis szerkesztése után tegye közzé a tudásbázist egy működő [Azure webalkalmazás-roboton](https://azure.microsoft.com/services/bot-service/) anélkül, hogy kódot kellene írnia. Tesztelje a robotot a [Azure Portalban](https://portal.azure.com) , vagy töltse le és folytassa a fejlesztést.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A keresési minőség és rangsorolás a lehető legjobb választ nyújtja

A QnA Maker rendszer egy rétegzett rangsorolási megközelítés. Az adattárolás az Azure Search szolgáltatásban történik, amely az első rangsorolási rétegként is szolgál. Az Azure Search legfontosabb eredményeit a rendszer átadja a QnA Maker NLP-re rangsoroló modelljének a végső eredmények és a megbízhatósági pontszám létrehozásához.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker javítja a beszélgetési folyamatot

A QnA Maker többfunkciós és aktív tanulást tesz lehetővé az alapvető kérdések és válaszok párosításának javítása érdekében.

A **több fordulatos kérések** lehetővé teszi a kérdések és válaszok párok összekapcsolását. Ez a kapcsolódás lehetővé teszi az ügyfélalkalmazás számára a legjobb válasz megadását, és további kérdéseket biztosít a végső válasz keresésének pontosításához.

Miután a Tudásbázis a közzétett végponton lévő felhasználóktól kapott kérdéseket, QnA Maker alkalmazza az **aktív tanulást** ezekre a valós kérdésekre, hogy a minőség javítása érdekében a Tudásbázisban módosításokat javasoljon a Tudásbázisban.

## <a name="development-lifecycle"></a>Fejlesztési életciklus

A QnA Maker a szerzői műveletek, a képzés és a közzététel, valamint együttműködési engedélyek használatával integrálható a teljes fejlesztési életciklusba.

> [!div class="mx-imgBorder"]
> ![A fejlesztési ciklus fogalmi képe](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Hogyan kezdjem meg a használatát?

**1. lépés**: hozzon létre egy QnA Maker erőforrást a [Azure Portalban](https://portal.azure.com).

**2. lépés**: Tudásbázis létrehozása a [QnA Maker](https://www.qnamaker.ai) portálon. [Fájlok és URL-címek](../concepts/content-types.md) hozzáadása a Tudásbázis létrehozásához.

**3. lépés**: a Tudásbázis közzététele és az egyéni végpont tesztelése a [curl vagy a Poster](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)használatával.

**4. lépés**: az ügyfélalkalmazás használatával programozott módon hívja meg a Tudásbázis végpontját. Az ügyfélalkalmazás dolgozza fel a JSON-választ, hogy a legjobb választ jelenítse meg a felhasználónak.

## <a name="next-steps"></a>További lépések
QnA Maker az egyéni Tudásbázis létrehozásához, kezeléséhez és üzembe helyezéséhez szükséges összes információt tartalmazza.

> [!div class="nextstepaction"]
> [A legutóbbi módosítások áttekintése](../whats-new.md)
