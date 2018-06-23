---
title: Ismerkedés az Azure Content moderátor |} Microsoft Docs
description: Ismerkedés az Azure Content moderátor módjáról.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347662"
---
# <a name="get-started-with-content-moderator"></a>Bevezetés a Tartalommoderátor használatába

Hozzáfogna a tartalom moderátor API-k és a felülvizsgálati segédprogram a következőképpen:

- [Indítsa el a felülvizsgálati eszközzel](#start-with-the-review-tool) az API-kulcsokat és a felülvizsgálati csoport létrehozásához. A felülvizsgálati eszköz vizsgálatát, és megtudhatja, hogyan integrálható a tartalom moderátor API-k használatával.
- [Fizessen elő a tartalom moderátor](#start-with-the-apis) az Azure portálon. Továbbra is szeretné iratkozzon fel az online tekintse át a csoport létrehozása.
- [A folyamat összekötő és a sablonok](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) integrációja egy könnyen használható tervezővel számos kivétele.

Függetlenül a választott lehetőség, tekintse meg a [hitelesítő adatok kezelése](review-tool-user-guide/credentials.md) cikkben található API hitelesítő adatait.

## <a name="start-with-the-review-tool"></a>Indítsa el a felülvizsgálati eszközzel
[Regisztráció](http://contentmoderator.cognitive.microsoft.com/) a tartalom moderátor felülvizsgálati eszköz webhelyen.

![Tartalom moderátor kezdőlap](images/homepage.PNG)

### <a name="create-a-review-team"></a>Tekintse át a csoport létrehozása
Adjon meg egy nevet a csoport. Ha azt szeretné, a kollégák, írja be az e-mail-címét is megteheti.

![Csoport egy tagja meghívása](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Képek feltöltése, vagy adja meg a szöveg
Kattintson a **próbálja > kép** vagy **próbálja > szöveg**. Legfeljebb öt minta képek feltöltése, vagy adja meg a minta szöveges moderálás.

![Próbálja meg a lemezkép vagy szöveges moderálás](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Az automatizált moderálás elküldése
Küldje el az automatikus moderálás a tartalmat. Belső a felülvizsgálati eszköz meghívja a moderálás API-k megvizsgálja a tartalmat. Ha az ellenőrzés befejeződött, egy üzenetet fog látni a vár a tekintse át az eredményekről láthatja.

![Mérsékelt fájlok](images/submitted.png)

### <a name="review-and-confirm-results"></a>Tekintse át és erősítse meg az eredmények
Tekintse át az automatikus moderált címkék, ha szükséges, módosítsa és használatával a **következő** gombra. Az üzleti alkalmazás meghívja a moderátor API-k, a sorba, címkézett tartalom kezdete, készen áll az emberi felülvizsgálati csapatok által felülvizsgálandó. Gyorsan áttekintheti nagy mennyiségű, ezzel a megközelítéssel tartalmat.

![Eredmények áttekintése](images/reviewresults.png)

Összes használata a [tekintse át az eszköz szolgáltatások](Review-Tool-User-Guide/human-in-the-loop.md) vagy folytassa a következő szakaszban olvashat az API-kat. A regisztrációs lépés kihagyása, mert az API-kulcsot, ahogy az a felülvizsgálati eszközben az Ön kiépített rendelkezik a [hitelesítő adatok kezelése](review-tool-user-guide/credentials.md) cikk.

### <a name="use-the-apis"></a>Az API-k

Most, hogy már megismerkedett a tartalom moderálás, és tekintse át az eszköz élmény, ismerje meg a tartalom moderátor integrálása az üzleti alkalmazások. Az alábbi szakasz segítségével további és gyorsított az SDK-k és minták megértését.

## <a name="start-with-the-apis"></a>Indítsa el az API-khoz

[Fizessen elő a tartalom moderátor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) az Azure portálon. Indítsa el a következő API-k egyike:

### <a name="image-moderation"></a>Képmoderálás

Indítsa el a [API konzol](try-image-api.md) , vagy használja a [.NET gyors üzembe helyezés](image-moderation-quickstart-dotnet.md) vizsgálati lemezképeket, és lehetséges felnőtt és ellopható tartalom észlelése címkék, várható, pontszámokat és egyéb használatával kiolvasott adatokat.

### <a name="text-moderation"></a>Szövegmoderálás

Indítsa el a [API konzol](try-text-api.md) , vagy használja a [.NET gyors üzembe helyezés](text-moderation-quickstart-dotnet.md) megvizsgálja a potenciális profán kifejezéseket, nem kívánt szöveg gép támogatású besorolás (előzetes verzió), a szöveges tartalom és a személyes azonosításra alkalmas adatokat (PII). 


### <a name="video-moderation"></a>Videomoderálás

Indítsa el a [.NET gyors üzembe helyezés](video-moderation-api.md) videók vizsgálata, és lehetséges felnőtt és ellopható tartalomtípusok. 


### <a name="review-apis"></a>API-k áttekintése

Válassza ki a feladatot, tekintse át és munkafolyamat API-k, kezdje itt.

- A [feladat API](try-review-api-job.md) a tartalmat a moderálás API-k használatával, és értékelést készít a felülvizsgálati eszközben. 
- A [felülvizsgálati API](try-review-api-review.md) közvetlenül hoz létre kép, text vagy videó értékelést az emberi moderátorok nélkül első vizsgálatát a tartalmat. 
- A [munkafolyamat API](try-review-api-workflow.md) hoz létre, frissíti, és lekérdezi a csapat hoz létre egyéni munkafolyamatokat részleteit.

## <a name="next-steps"></a>További lépések

További információ a tartalom moderálás kezdve a [moderálás API kép](image-moderation-api.md).
