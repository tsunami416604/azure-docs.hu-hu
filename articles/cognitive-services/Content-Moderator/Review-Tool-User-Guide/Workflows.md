---
title: Definiálja és tartalom-jóváhagyás munkafolyamatok - Content Moderator
titlesuffix: Azure Cognitive Services
description: Az Azure Content Moderator a munkafolyamat-tervezővel és API-k segítségével egyéni munkafolyamatok és a küszöbértékek a tartalom szabályzatok alapján.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 265d5a2bd6a256d287e6ae164e883ade783f960f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262404"
---
# <a name="define-test-and-use-workflows"></a>Adja meg, tesztelése és munkafolyamatok

Az Azure Content Moderator a munkafolyamat-tervezővel és API-k segítségével egyéni munkafolyamatok és a küszöbértékek a tartalom szabályzatok alapján.

A munkafolyamatok "Csatlakozás", a Content Moderator API összekötők használatával. Más API-k is használhatja, ha egy API-összekötő érhető el. Itt az példában, amely alapértelmezés szerint a Content Moderator összekötőt használja.

## <a name="browse-to-the-workflows-section"></a>Keresse meg a munkafolyamatokkal

Az a **beállítások** lapon jelölje be **munkafolyamatok**.

  ![A munkafolyamatok beállítása](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Egy új munkafolyamat indításához

Válassza ki **munkafolyamat hozzáadása**.

  ![Egy munkafolyamat hozzáadása](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Rendeljen egy nevet és leírást

Nevezze el a munkafolyamatot, adjon meg egy leírást, és válassza ki a munkafolyamat kezeli az e képek vagy szöveges.

  ![A munkafolyamat nevét és leírását](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Az értékelési feltételek ("feltétel") megadása

Az alábbi képernyőfelvételen láthatja a mezők és az If-majd más beállításokat is, amely munkafolyamatok meghatározásához szükséges. Válasszon egy összekötőt. Ez a példa **a Content Moderator**. Függően az összekötő úgy dönt a kimeneti elérhető beállítások módosítása.

  ![Munkafolyamat-feltétel megadása](images/ocr-workflow-step-2-condition.PNG)

Miután kiválasztotta az összekötő és a kimenetét, amelyeket szeretne, válassza ki egy operátort és az értéket a feltétel.

## <a name="define-the-action-to-take"></a>Adja meg az elvégzendő műveletet

Válassza ki az elvégzendő műveletet, és megfelelnek a feltételt. A következő példa létrehoz egy kép áttekintése, egy címkét rendel `a`, és kiemeli a látható feltétel. A kívánt eredmények eléréséhez több feltételt is kombinálhatók. Igény szerint adjon hozzá egy másik (Else) elérési útja.

  ![A munkafolyamat-műveletek definiálása](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>A munkafolyamat mentése

Végül mentse a munkafolyamatot, és jegyezze fel a munkafolyamat nevét. Szüksége lesz a neve, moderálási feladat indításához a felülvizsgálati API-val.

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

Most, hogy a meghatározott egyéni munkafolyamat, tesztelje azt az minta tartalmat.

Válassza ki a megfelelő **munkafolyamat végrehajtása** gombra.

  ![A munkafolyamat tesztelése](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Fájl feltöltése

Mentse a [képet](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) helyi meghajtójára. A munkafolyamat tesztelése, jelölje be **válassza ki a fájl(ok)** , és töltse fel a rendszerképet.

  ![Lemezkép-fájl feltöltése](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>A munkafolyamat nyomon követése

A munkafolyamat nyomon követheti, ahogy végrehajtása.

  ![A munkafolyamat-végrehajtási nyomon követése](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Tekintse át az összes lemezképet, emberi moderálás kockázatosként megjelölt felhasználókról

Megtekintheti a rendszerképet, tekintse át, nyissa meg a **kép** lapjára **áttekintése**.

  ![Rendszerképek felülvizsgálata](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>További lépések 

Code-ból a munkafolyamat elindításához, használja az egyéni munkafolyamatokat a [ `Job` API a konzolhoz rövid](../try-review-api-job.md) és a [.NET SDK-t a rövid útmutató](../moderation-jobs-quickstart-dotnet.md).
