---
title: Mi az QnA Maker szolgáltatás?
titleSuffix: Azure Cognitive Services
description: A QnA Maker egy felhőalapú NLP-szolgáltatás, amely könnyen létrehoz egy természetes társalgási réteget az adataiban. A felhasználó az egyéni Tudásbázis (KB) adatai alapján megkeresheti a legmegfelelőbb választ az adott természetes nyelvi bevitelhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 08/01/2019
ms.author: diberry
ms.openlocfilehash: f56798359cdc8739a363bed3bfddadd584617adf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815494"
---
# <a name="what-is-the-qna-maker-service"></a>Mi a QnA Maker szolgáltatás?

A QnA Maker egy felhőalapú természetes nyelvi feldolgozó (NLP) szolgáltatás, amely könnyen létrehoz egy természetes társalgási réteget az adataiban. A felhasználó az egyéni Tudásbázis (KB) adatai alapján megkeresheti a legmegfelelőbb választ az adott természetes nyelvi bevitelhez.

QnA Maker-ügyfélalkalmazás bármely olyan társalgási alkalmazás, amely természetes nyelven kommunikál a felhasználóval a kérdés megválaszolásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.

## <a name="when-to-use-qna-maker"></a>Mikor kell használni a QnA Maker

* **Ha statikus információval rendelkezik** – használja a QnA Maker, ha a válaszokat a Tudásbázisban statikus információkkal látja el. Ez a Tudásbázis az Ön igényeinek megfelelően egyedi, amelyet dokumentumok, például [PDF-EK és URL-címek](../concepts/data-sources-supported.md)használatával készített.
* **Ha ugyanazt a választ szeretné adni egy kérelemhez, kérdéshez vagy parancshoz** – ha a különböző felhasználók ugyanezt a kérdést nyújtják be, ugyanazt a választ is visszaadja a rendszer. 
* **Ha a metaadatok alapján szeretné szűrni a statikus adatokat** – a [metaadat](../how-to/metadata-generateanswer-usage.md) -Címkék hozzáadása lehetőséggel további szűrési lehetőségeket biztosíthat az ügyfélalkalmazás felhasználóival és az információkkal kapcsolatban. A metaadatok általános információi közé tartozik a [Chit-Chat](../how-to/chit-chat-knowledge-base.md), a tartalom típusa vagy a formátum, a tartalom célja és a tartalom frissessége.
* **Ha olyan robot-beszélgetést szeretne kezelni, amely statikus adatokat tartalmaz** – az Ön tudomásul veszi a felhasználó beszélgetési szövegét vagy parancsát, és megválaszolja azt. Ha a válasz egy előre meghatározott beszélgetési folyamat része, amely a Tudásbázisban a többfordulatos [kontextusban](../how-to/multiturn-conversation.md)szerepel, a robot egyszerűen megadhatja ezt a folyamatot.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>QnA Maker Tudásbázis használata egy csevegési robotban

QnA Maker Tudásbázis közzétételét követően az ügyfélalkalmazás egy kérdést küld a Tudásbázis-végpontnak, és JSON-válaszként fogadja az eredményeket. QnA Maker egy közös ügyfélalkalmazás egy csevegési robot.

![Kérdezzen egy robotot, és kapjon választ a Tudásbázis tartalmából](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás elküldi a felhasználó kérdését (a szöveg a saját szavaival), "hogyan programozott módon frissíti a tudásbázist?" a Tudásbázis-végponthoz.|
|2|A QnA Maker a betanított Tudásbázis használatával biztosítja a megfelelő választ, valamint azokat a követési kérdéseket, amelyek a legjobb válasz keresésének pontosítására használhatók. QnA Maker egy JSON-formátumú választ ad vissza.|
|3|Az ügyfélalkalmazás a JSON-választ használva döntéseket hoz a beszélgetés folytatásához. Ezek a döntések magukban foglalhatják a legjobb választ, vagy további választási lehetőségeket mutatnak be a legjobb válasz keresésének pontosításához. |
|||

## <a name="what-is-a-knowledge-base"></a>Mi az a Tudásbázis? 

QnA Maker [importálja a tartalmat](../concepts/data-sources-supported.md) a kérdés-és Levelesláda tudásbázisba. Az importálási folyamat kigyűjti a strukturált és részben strukturált tartalom részei közötti kapcsolat adatait, hogy a kérdés-és a válaszfájlok közötti kapcsolatra utaljon. Ezeket a kérdéseket és a válaszokat szerkesztheti, illetve újakat is hozzáadhat.  

A kérdés és a válaszfájl tartalma tartalmazza az adott válaszra vonatkozó összes alternatív kérdést, a keresés során a válaszok kiválasztásához használt metaadat-címkéket, valamint a keresési pontosítást a keresés folytatásához.

![Példa kérdésre és válaszra a metaadatokkal](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Miután közzétette a tudásbázist, egy ügyfélalkalmazás elküldi a felhasználó kérdését a végpontnak. A QnA Maker szolgáltatás feldolgozza a kérdést, és válaszol a legjobb válaszra. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Kód nélküli robot létrehozása, kezelése és közzététele

A QnA Maker portál a teljes Tudásbázis-létrehozási élményt biztosítja. Az aktuális formában importálhatja a dokumentumokat a tudásbázisba. Ezek a dokumentumok (például a gyakori kérdések, a manuális, a számolótáblák vagy a weblapok) át lesznek alakítva a kérdés-és a válasz-készletekbe. Az egyes készleteket a rendszer a követő kérések elemzésére és más készletekhez való csatlakozásra elemzi. A végső Markdown formátuma támogatja a gazdag bemutatót, beleértve a képeket és a hivatkozásokat. 

A Tudásbázis szerkesztése után tegye közzé a tudásbázist egy működő [Azure webalkalmazás](https://azure.microsoft.com/services/bot-service/) -roboton anélkül, hogy kódot kellene írnia. Tesztelje a robotot [](https://portal.azure.com) a Azure Portalban, vagy töltse le és folytassa a fejlesztést. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A keresési minőség és rangsorolás a lehető legjobb választ nyújtja

A QnA Maker rendszer egy rétegzett rangsorolási megközelítés. Az adattárolás az Azure Search szolgáltatásban történik, amely az első rangsorolási rétegként is szolgál. Az Azure Search legfontosabb eredményeit a rendszer átadja a QnA Maker NLP-re rangsoroló modelljének a végső eredmények és a megbízhatósági pontszám létrehozásához.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker javítja a beszélgetési folyamatot

A QnA Maker lehetővé teszi a több funkciós és az aktív tanulást, hogy segítsen az alapvető kérdés-és válaszfájlok tökéletesítésében. 

A **több fordulatos kérések** lehetővé teszi a kérdések és válaszok párok összekapcsolását. Ez a kapcsolódás lehetővé teszi az ügyfélalkalmazás számára a legjobb válasz megadását, és további kérdéseket biztosít a végső válasz keresésének pontosításához. 

Miután a Tudásbázis a közzétett végponton lévő felhasználóktól kapott kérdéseket, QnA Maker alkalmazza az **aktív tanulást** ezekre a valós kérdésekre, hogy a minőség javítása érdekében a Tudásbázisban módosításokat javasoljon a Tudásbázisban. 

## <a name="development-lifecycle"></a>Fejlesztési életciklus

A QnA Maker a szerzői műveletek, a képzés és a közzététel, valamint együttműködési engedélyek használatával integrálható a teljes fejlesztési életciklusba. 

## <a name="how-do-i-start"></a>Hogyan fogjak hozzá?

**1. lépés**: Hozzon létre egy QnA Maker erőforrást a [Azure Portalban](https://portal.azure.com). 

**2. lépés**: Hozzon létre egy tudásbázist a [QnA Maker](https://www.qnamaker.ai) portálon. [Fájlok és URL-címek](../concepts/data-sources-supported.md) hozzáadása a Tudásbázis létrehozásához.  

**3. lépés**: Közzéteheti a tudásbázist, és tesztelheti az egyéni végpontot [curl](../quickstarts/get-answer-from-kb-using-curl.md) vagy [Poster](../quickstarts/get-answer-from-kb-using-postman.md)használatával. 

**4. lépés**: Az ügyfélalkalmazás által programozott módon hívja meg a Tudásbázis végpontját, és olvassa el a JSON-választ, amely a legjobb választ jeleníti meg a felhasználónak.  

## <a name="news-and-updates"></a>Hírek és frissítések

Ismerje meg a QnA Maker újdonságait.

* Június 2019
    * Továbbfejlesztett ranker-modell francia, olasz, német, spanyol, portugál nyelven
* Április 2019
    * Támogatási webhely tartalmának kinyerése
    * SharePoint-dokumentum támogatása
* Március 2019
    * Aktív tanulás 
    * Továbbfejlesztett NLP-rangsorolási modell angol, 

## <a name="next-steps"></a>További lépések
QnA Maker az egyéni Tudásbázis létrehozásához, kezeléséhez és üzembe helyezéséhez szükséges összes információt tartalmazza. 

> [!div class="nextstepaction"]
> [QnA Maker-szolgáltatás létrehozása](../how-to/set-up-qnamaker-service-azure.md)
