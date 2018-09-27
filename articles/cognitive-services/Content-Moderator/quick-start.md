---
title: Bevezetés a Tartalommoderátor használatába
titlesuffix: Azure Cognitive Services
description: Ismerkedés a Content Moderator módja.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: c2ac0ccd89b5f1436a151e3d69c5d7423090f244
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225294"
---
# <a name="get-started-with-content-moderator"></a>Bevezetés a Tartalommoderátor használatába

A Content Moderator Ismerkedés a következő módon:

- [Kezdje a vizsgálóeszközt](#start-with-the-review-tool) az API-kulcsot, és tekintse át a csoport létrehozása. Az az előnye, hogy az API-kulcsot a moderálási API-k hívásához vizsgálatának tartalmat és a felülvizsgálati API-k létrehozása az értékelések, további lépések nélkül használhatja.
- [Fizessen elő a Content Moderator](#start-with-the-apis) az Azure-ban, az API-kulcs beszerzése. Tekintse meg a [API-referencia](api-reference.md) és a [SDK-k](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Továbbra is szeretné regisztráljon online, tekintse át a csoport létrehozása.
- [A Flow-összekötőjét, és a sablonok](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) tekintse meg egy könnyen használható Tervezőeszköz integrációkat széles skáláját.

Tekintse meg a beállítást választja, függetlenül a [hitelesítő adatok kezelése](review-tool-user-guide/credentials.md) cikkben találja az API hitelesítő adatait.

## <a name="start-with-the-review-tool"></a>Kezdje a felülvizsgálati eszköz
[Regisztráció](http://contentmoderator.cognitive.microsoft.com/) a Content Moderator felülvizsgálati eszköz webhelyen.

![Tartalom Moderator kezdőlapja](images/homepage.PNG)

### <a name="create-a-review-team"></a>Tekintse át a csoport létrehozása
Nevezze el a csapat. Ha azt szeretné, a kollégák, írja be az e-mail címüket megteheti.

![Csapattag meghívása](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Képek feltöltése, vagy adja meg a szöveg
Kattintson a **próbálja > lemezkép** vagy **próbálja > szöveg**. Legfeljebb öt minta lemezképeket tölthetnek fel, vagy adja meg szövegminta moderálás.

![Próbálja ki a kép vagy szöveg moderálása](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Küldje el az automatizált moderálás előnyeit
Küldje el a tartalmat az automatizált moderálás előnyeit. Belsőleg a felülvizsgálati eszköz meghívja a moderálási API-k vizsgálata a tartalmat. Ha a beolvasás befejeződött, arról értesíti, Várakozás a felülvizsgálati eredmények kapcsolatos üzenet jelenik meg.

![Mérsékelt fájlok](images/submitted.png)

### <a name="review-and-confirm-results"></a>Tekintse át és az eredmények ellenőrzése
Tekintse át az automatikus Metz címkéket, ha szükséges, módosítsa és keresztül küldje el a **tovább** gombra. Az üzleti alkalmazás meghívja a Moderator API-k, az üzenetsor-kezelési, címkézett tartalom elindításakor, készen áll arra, át kell néznie az emberi ellenőrző csapat. Nagy mennyiségű tartalmat, ezzel a megközelítéssel gyorsan tekintse át.

![Eredmények áttekintése](images/reviewresults.png)

Ismerje meg, hogyan használhatja az összes a [tekintse át az eszköz a szolgáltatások](Review-Tool-User-Guide/human-in-the-loop.md) vagy folytassa a következő szakaszban megismerheti az API-kat. Az előfizetési lépés kihagyása, mert rendelkezik az API-kulcs ki az Ön számára a felülvizsgálati eszközben, ahogyan a [hitelesítő adatok kezelése](review-tool-user-guide/credentials.md) cikk.

### <a name="use-the-apis"></a>Az API-k használata

Ismerje meg, hogyan integrálható a Content Moderator az üzleti alkalmazásait. Tekintse meg a [API-referencia](api-reference.md) és a [SDK-k](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Fizessen elő az Azure Portalon

[Fizessen elő a Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) az Azure Portalon. A kezdéshez válasszon egyet a következő API-kat:

### <a name="image-moderation"></a>Képmoderálás

Kezdje a [API-konzol](try-image-api.md) vagy használja a [.NET – rövid útmutató](image-moderation-quickstart-dotnet.md) kiszűrhető, és észlelheti a potenciális felnőtt és szexuális tartalom címkék, megbízhatósági pontszámokat és egyéb használatával a kinyert információkat.

### <a name="text-moderation"></a>Szövegmoderálás

Kezdje a [API-konzol](try-text-api.md) vagy használja a [.NET – rövid útmutató](text-moderation-quickstart-dotnet.md) tartalomszűrés, nem kívánatos szöveg gépi támogatású besorolás (előzetes verzió), a szöveges tartalom vizsgálata és a személyes azonosításra alkalmas adatokat (PII). 


### <a name="video-moderation"></a>Videomoderálás

Kezdje a [.NET – rövid útmutató](video-moderation-api.md) videók vizsgálata, és észlelheti a potenciális felnőtt és szexuális tartalom. 


### <a name="review-apis"></a>API-k áttekintése

Válassza ki a feladatot, tekintse át és a munkafolyamat API-k, kezdje itt.

- A [feladat API](try-review-api-job.md) a tartalmat a moderálási API-k használatával, és értékelések készít a felülvizsgálati eszközben. 
- A [felülvizsgálati API](try-review-api-review.md) közvetlenül hoz létre kép, szöveg vagy videót felülvizsgálatok emberi moderátorok a tartalom ellenőrzése nélkül. 
- A [munkafolyamat API](try-review-api-workflow.md) hoz létre, frissítését és az egyéni munkafolyamatokat, amely a csapat létrehozza részleteit.

## <a name="next-steps"></a>További lépések

Tekintse meg a [API-referencia](api-reference.md) és a [SDK-k](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Segítségével az integráció gyorsan Beindíthatja IOT-a [.NET SDK-minták](sdk-and-samples.md#net-sdk-samples), [a C# REST API-minták](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) és [oktatóanyagok](sdk-and-samples.md#tutorials).
