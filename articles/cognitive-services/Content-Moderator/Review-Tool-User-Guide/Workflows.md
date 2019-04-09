---
title: Definiálja és a felülvizsgálati eszköz - Content Moderator tartalom munkafolyamatainak
titlesuffix: Azure Cognitive Services
description: Az Azure Content Moderator munkafolyamat-tervező segítségével egyéni munkafolyamatok és a küszöbértékek a tartalom szabályzatok alapján.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263077"
---
# <a name="define-and-use-moderation-workflows"></a>Definiálja és munkafolyamatok moderálása

Ebben az útmutatóban, megtudhatja, hogyan állíthatja be, és [munkafolyamatok](../review-api.md#workflows) a a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com) webhelyén. Munkafolyamatokat olyan felhőalapú testre szabott szűrőket, amelyek segítségével hatékonyabban kezelni a tartalom. A munkafolyamatok számos különböző módon tartalom szűrése és a megfelelő a megfelelő műveletet szolgáltatás képes csatlakozni. Ez az útmutató bemutatja, hogyan használhatja a Content Moderator connector (amely alapértelmezés szerint tartalmazza) tartalom szűrése és a egy tipikus moderálás forgatókönyvben emberi ellenőrzések beállítása.

## <a name="create-a-new-workflow"></a>Új munkafolyamat létrehozása

Nyissa meg a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) , és jelentkezzen be. Az a **beállítások** lapon jelölje be **munkafolyamatok**.

![A munkafolyamatok beállítása](images/2-workflows-0.png)

A következő képernyőn válassza ki a **munkafolyamat hozzáadása**.

![Egy munkafolyamat hozzáadása](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Rendeljen egy nevet és leírást

Nevezze el a munkafolyamatot, adjon meg egy leírást, és válassza ki, akár a munkafolyamat fogja kezelni, képeket vagy szöveget.

![A munkafolyamat nevét és leírását](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Értékelési feltételek megadása

A következő képernyőn, nyissa meg a **Ha** szakaszban. A felső legördülő menüből válassza ki a **feltétel**. Ez lehetővé teszi, hogy a feltétel, amelyen a munkafolyamat tart a művelet konfigurálása. Ha több feltétel használni szeretne, válassza a **kombináció** helyette. 

Ezután válasszon egy összekötőt. Ez a példa **a Content Moderator**. Az összekötő úgy dönt, attól függően különböző lehetőségeket a kimeneti adatokat fog kapni. Tekintse meg a [összekötők](./configure.md#connectors) megtudhatja, hogyan állítható be más összekötők felülvizsgálati eszköz beállítások útmutató szakasza.

![Válassza ki a munkafolyamat-összekötőt](images/image-workflow-connect-to.PNG)

Válassza ki a kívánt kimenetet használja, és állítsa be a feltételeket, hogy ellen.

![Munkafolyamat-feltétel megadása](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>A művelet megadása

Nyissa meg a **majd** szakaszt, itt választhatja ki egy műveletet. Az alábbi példa létrehoz egy kép tekintse át, és egy címkét rendel. Igény szerint adjon hozzá egy másik (Else) elérési és beállított egy műveletet, amely is.

![A munkafolyamat-műveletek definiálása](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>A munkafolyamat mentése

Jegyezze fel a munkafolyamat nevét; szüksége lesz a neve, moderálási feladat indításához a munkafolyamat API-val (lásd alább). Végül mentse a munkafolyamat használatával a **mentése** gombra a lap tetején.

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

Most, hogy egy egyéni munkafolyamat meghatározta, tesztelje azt az minta tartalmat. Lépjen a **munkafolyamatok** , és válassza ki a megfelelő **munkafolyamat végrehajtása** gombra.

![A munkafolyamat tesztelése](images/image-workflow-execute.PNG)

Mentse a fájlt [képet](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) helyi meghajtójára. Válassza ki **válassza ki a fájl(ok)** , és töltse fel a rendszerképet a munkafolyamatot.

![Az ajánlat észlelhetők a képen egy futtatója](images/sample-text.jpg)

### <a name="track-progress"></a>Követés állapota

A munkafolyamat állapotát megtekintheti a következő előugró ablak.

![A munkafolyamat-végrehajtási nyomon követése](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Ellenőrizze a munkafolyamat-művelet

Nyissa meg a **kép** lapjára **tekintse át** , és győződjön meg arról, hogy egy újonnan létrehozott rendszerképet áttekintése.

![Rendszerképek felülvizsgálata](images/image-workflow-review.PNG)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban útmutatóból megtudhatta, hogyan állíthatja be, és használja a Content Moderator moderálás munkafolyamatokat [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com). Ezután tekintse meg a [REST API-val útmutató](../try-review-api-workflow.md) megtudhatja, hogyan hozhat létre programozott módon a munkafolyamatokat.
