---
title: Mi az a QnA Maker szolgáltatás?
titleSuffix: Azure Cognitive Services
description: A QnA Maker egy felhőalapú NLP szolgáltatás, amely könnyedén természetes társalgási réteget hoz létre az adatok felett. Az egyéni tudásbázis (KB) információialapján megtalálhatja a legmegfelelőbb választ egy adott természetes nyelvi bevitelre.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 2863a086343b0a5d3bc69ae319dbe93d557bcb4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052929"
---
# <a name="what-is-the-qna-maker-service"></a>Mi a QnA Maker szolgáltatás?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

A QnA Maker egy felhőalapú természetes nyelvi feldolgozási (NLP) szolgáltatás, amely könnyedén létrehoz egy természetes társalgási réteget az adatok felett. Az egyéni tudásbázis (KB) információialapján megtalálhatja a legmegfelelőbb választ egy adott természetes nyelvi bevitelre.

A QnA Maker ügyfélalkalmazása minden olyan társalgási alkalmazás, amely természetes nyelven kommunikál a felhasználóval a kérdés megválaszolása érdekében. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.

## <a name="when-to-use-qna-maker"></a>Mikor kell használni a QnA Makert?

* **Ha statikus információkkal rendelkezik** – használja a QnA Makert, ha statikus információk vannak a válaszok tudásbázisában. Ez a tudásbázis az igényeinek megfelelően készült, amelyet olyan dokumentumokkal készített, mint a [PDF-fájlok és az URL-ek.](../concepts/content-types.md)
* **Ha ugyanazt a választ szeretné adni egy kérésre, kérdésre vagy parancsra** – ha különböző felhasználók ugyanazt a kérdést küldik el, akkor ugyanazt a választ adja vissza.
* **Ha a metaadatok alapján szeretné szűrni a statikus adatokat** – adja hozzá [a metaadat-címkéket,](../how-to/metadata-generateanswer-usage.md) hogy az ügyfélalkalmazás felhasználói és az információk szempontjából további szűrési lehetőségeket biztosítson. A gyakori metaadat-információk közé tartozik [a csevegés,](../how-to/chit-chat-knowledge-base.md)a tartalomtípusa vagy formátuma, a tartalom célja és a tartalom frissessége.
* **Ha statikus adatokat tartalmazó robotbeszélgetést szeretne kezelni** – a tudásbázis veszi a felhasználó beszélgetési szövegét vagy parancsát, és megválaszolja azt. Ha a válasz egy előre meghatározott beszélgetési folyamat része, amely a tudásbázisban [többfordulatos környezetben](../how-to/multiturn-conversation.md)jelenik meg, a robot könnyen biztosíthatja ezt a folyamatot.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>A QnA Maker tudásbázisának használata csevegőrobotban

A QnA Maker tudásbázis közzététele után egy ügyfélalkalmazás egy kérdést küld a tudásbázis végpontjára, és json-válaszként kapja meg az eredményeket. A QnA Maker közös ügyfélalkalmazása egy csevegőrobot.

![Kérdezz egy robottól, és kapj választ a tudásbázis tartalmából](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás elküldi a felhasználó _kérdését_ (a szöveget a saját szavaival), "Hogyan frissíthetem programozott módon a tudásbázisomat?" a tudásbázis végpontjához.|
|2|A QnA Maker a betanított tudásbázist használja a helyes válasz és a legjobb válasz keresésének finomítására használható nyomon követési utasítások megadására. A QnA Maker JSON-formátumú választ ad vissza.|
|3|Az ügyfélalkalmazás a JSON-válasz segítségével dönt a beszélgetés folytatásáról. Ezek a döntések magukban foglalhatják a legjobb válasz megjelenítését és a legjobb válasz keresésének finomítására vonatkozó további választási lehetőségek bemutatását. |
|||

## <a name="what-is-a-knowledge-base"></a>Mi az a tudásbázis?

A QnA Maker [a tartalmat](../concepts/knowledge-base.md) a kérdések és válaszkészletek tudásbázisába importálja. Az importálási folyamat információkat nyer ki a strukturált és félig strukturált tartalom részei közötti kapcsolatról, hogy a kérdés- és válaszhalmazok közötti kapcsolatot sugalljon. Ezeket a kérdés- és válaszkészleteket szerkesztheti, vagy új készleteket adhat hozzá.

A kérdés-válasz halmaz tartalma a következőket tartalmazza:
* A kérdés minden alternatív formája
* A válaszlehetőségek szűrésére használt metaadat-címkék a keresés során
* A keresés finomításának folytatására irányuló utasítások nyomon követése

![Példa kérdés rekedés metaadatokkal](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

A tudásbázis közzététele után az ügyfélalkalmazás elküldi a felhasználó kérdését a végpontnak. A QnA Maker szolgáltatás feldolgozza a kérdést, és a legjobb válasszal válaszol.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Robot létrehozása, kezelése és közzététele roboton kód nélkül

A QnA Maker portál biztosítja a teljes tudásbázis-szerzői élményt. A dokumentumokat jelenlegi formájukban importálhatja a tudásbázisba. Ezek a dokumentumok (például gyIK, termékkézikönyv, táblázatkezelő vagy weblap) megkérdőjelező- és válaszhalmazokká alakulnak át. A rendszer minden egyes készletet elemez a nyomon követési utasításokhoz, és más készletekhez csatlakozik. A végső _markdown_ formátum támogatja a gazdag bemutatót, beleértve a képeket és a linkeket.

A tudásbázis szerkesztése után tegye közzé a tudásbázist egy működő [Azure Web App-robotban](https://azure.microsoft.com/services/bot-service/) anélkül, hogy bármilyen kódot írna. Tesztelje robotját az [Azure Portalon,](https://portal.azure.com) vagy töltse le és folytassa a fejlesztést.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A keresés minősége és rangsorolása a lehető legjobb választ nyújtja

A QnA Maker rendszere egy réteges rangsorolási megközelítés. Az adatok az Azure search, amely egyben az első rangsorolási réteg ként is tárolja. Az Azure-keresés legjobb eredményeit ezután a QnA Maker NLP újrarangsorolási modelljén keresztül továbbítják a végső eredmények és a megbízhatósági pontszám elérése érdekében.

## <a name="qna-maker-improves-the-conversation-process"></a>A QnA Maker javítja a beszélgetési folyamatot

A QnA Maker többfordulatos utasításokat és aktív tanulást biztosít az alapvető kérdés- és válaszkészletek javításához.

**A többfordulós utasítások** lehetőséget adnak a kérdés- és válaszpárok összekapcsolására. Ez a kapcsolat lehetővé teszi, hogy az ügyfélalkalmazás a legjobb választ, és további kérdéseket, hogy finomítsa a keresést a végső választ.

Miután a tudásbázis kérdéseket kap a felhasználóktól a közzétett végponton, a QnA Maker **aktív tanulást** alkalmaz ezekre a valós kérdésekre, hogy a tudásbázis módosításait javasoljon a minőség javítása érdekében.

## <a name="development-lifecycle"></a>Fejlesztési életciklus

A QnA Maker szerzői, képzési és közzétételi lehetőségeket, valamint együttműködési engedélyeket biztosít a teljes fejlesztési életciklusba való integráláshoz.

> [!div class="mx-imgBorder"]
> ![A fejlesztési ciklus koncepcionális képe](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Hogyan kezdjem meg a használatát?

**1. lépés:** QnA Maker erőforrás létrehozása az [Azure Portalon.](https://portal.azure.com)

**2. lépés:** Hozzon létre egy tudásbázist a [QnA Maker](https://www.qnamaker.ai) portálon. [Fájlok és URL-címek](../concepts/content-types.md) hozzáadása a tudásbázis létrehozásához.

**3. lépés:** Tegye közzé tudásbázisát, és tesztelje az egyéni végpontról a [cURL vagy a Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)használatával.

**4. lépés:** Az ügyfélalkalmazásból programozott módon hívja meg a tudásbázis végpontját. Az ügyfélalkalmazás feldolgozza a JSON-választ, hogy a legjobb választ jelenítse meg a felhasználónak.

## <a name="next-steps"></a>További lépések
A QnA Maker mindent biztosít, amire szüksége lehet az egyéni tudásbázis létrehozásához, kezeléséhez és üzembe helyezéséhez.

> [!div class="nextstepaction"]
> [Tekintse át a legújabb módosításokat](../whats-new.md)
