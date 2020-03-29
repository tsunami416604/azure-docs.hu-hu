---
title: Tartalom-munkafolyamatok definiálása és használata a Véleményezés eszköz - Tartalommoderátor segítségével
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator munkafolyamat-tervezője segítségével egyéni munkafolyamatokat és küszöbértékeket határozhat meg a tartalomszabályzatai alapján.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754256"
---
# <a name="define-and-use-moderation-workflows"></a>Moderálási munkafolyamatok definiálása és használata

Ebből az útmutatóból megtudhatja, hogyan állíthatja be és használhatja a [munkafolyamatokat](../review-api.md#workflows) a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) webhelyén. A munkafolyamatok felhőalapú, testre szabott szűrők, amelyek segítségével hatékonyabban kezelheti a tartalmat. A munkafolyamatok különböző szolgáltatásokhoz kapcsolódhatnak, hogy különböző módokon szűrjék a tartalmat, majd megtegyékeljék a megfelelő lépéseket. Ez az útmutató bemutatja, hogyan használhatja a Tartalommoderátor-összekötőt (amely alapértelmezés szerint szerepel) a tartalom szűrésére és az emberi vélemények beállítására egy tipikus moderálási forgatókönyvben.

## <a name="create-a-new-workflow"></a>Új munkafolyamat létrehozása

Nyissa meg a [Tartalommoderátor-ellenőrző eszközt,](https://contentmoderator.cognitive.microsoft.com/) és jelentkezzen be. A **Beállítások** lapon válassza a **Munkafolyamatok**lehetőséget.

![Munkafolyamatok beállítása](images/2-workflows-0.png)

A következő képernyőn válassza a **Munkafolyamat hozzáadása**lehetőséget.

![Munkafolyamat hozzáadása](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Név és leírás hozzárendelése

Nevezze el a munkafolyamatot, adjon meg egy leírást, és válassza ki, hogy a munkafolyamat kezeli-e a képeket vagy a szöveget.

![Munkafolyamat neve és leírása](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Értékelési feltételek meghatározása

A következő képernyőn lépjen az **If** szakaszra. A felső legördülő menüben válassza a **Feltétel lehetőséget.** Ez lehetővé teszi, hogy konfigurálja azt a feltételt, amelyre a munkafolyamat végrehajtja a műveletet. Ha több feltételt szeretne használni, válassza a **Kombináció** lehetőséget. 

Ezután jelöljön ki egy összekötőt. Ez a példa **a Tartalommoderátort**használja. A választott összekötőtől függően különböző adatkimeneti lehetőségeket kap. Tekintse meg az [Összekötők](./configure.md#connectors) szakasza a Felülvizsgálati eszköz beállításai útmutató, hogyan kell beállítani más összekötők.

![Munkafolyamat-összekötő kijelölése](images/image-workflow-connect-to.PNG)

Válassza ki a használni kívánt kimenetet, és állítsa be a feltételeket, hogy ellenőrizze azt.

![Munkafolyamat-feltétel meghatározása](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>A művelet meghatározása

Nyissa meg az **Ezután** szakaszt, ahol kijelöl egy műveletet. A következő példa létrehoz egy képáttekintést, és hozzárendel egy címkét. Tetszés szerint hozzáadhat egy alternatív (Else) elérési utat, és ehhez is beállíthat egy műveletet.

![Munkafolyamat-művelet definiálása](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>A munkafolyamat mentése

Jegyezze fel a munkafolyamat nevét; a moderálási feladat munkafolyamat-API-val való indításához a névre van szükség (lásd alább). Végül mentse a munkafolyamatot a lap tetején található **Mentés** gombbal.

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

Most, hogy definiált egy egyéni munkafolyamatot, tesztelje azt mintatartalommal. Nyissa meg a **Munkafolyamatok** lehetőséget, és válassza a megfelelő **Munkafolyamat végrehajtása** gombot.

![Munkafolyamat-teszt](images/image-workflow-execute.PNG)

Mentse ezt [a mintaképet](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) a helyi meghajtóra. Ezután válassza **a Fájl(ok) kiválasztása lehetőséget,** és töltse fel a képet a munkafolyamatba.

![A futó egy idézet egymásra a kép](images/sample-text.jpg)

### <a name="track-progress"></a>Követés állapota

A munkafolyamat előrehaladását a következő előugró ablakban tekintheti meg.

![Munkafolyamat-végrehajtás nyomon követése](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Munkafolyamat-művelet ellenőrzése

Nyissa meg a **Kép** lap **Véleményezés csoportját,** és ellenőrizze, hogy van-e újonnan létrehozott képellenőrzés.

![Rendszerképek felülvizsgálata](images/image-workflow-review.PNG)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban a [Tartalommoderátor-ellenőrzési eszközből](https://contentmoderator.cognitive.microsoft.com)tanulta meg, hogyan állíthatja be és használhatja a moderálási munkafolyamatokat. Ezután tekintse meg a [REST API-útmutatót,](../try-review-api-workflow.md) amelyből megtudhatja, hogyan hozhat létre programozott munkafolyamatokat.
