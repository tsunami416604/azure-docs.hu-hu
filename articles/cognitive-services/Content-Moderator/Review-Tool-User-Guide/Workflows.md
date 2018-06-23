---
title: Adja meg, és a munkafolyamatok Azure tartalom moderátor |} Microsoft Docs
description: 'Útmutató: a tartalom házirendek alapján egyéni munkafolyamatokat hozhat létre.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347015"
---
# <a name="define-test-and-use-workflows"></a>Adja meg, tesztelése és munkafolyamatok

A munkafolyamat-Tervező Azure tartalom moderátor és API-k segítségével egyéni munkafolyamatokat és a tartalom házirendek alapján küszöbértékeinek meghatározása.

Munkafolyamatok "Csatlakozás" a tartalom moderátor API összekötők használatával. Más API-k is használhatja, ha egy adott API-összekötő érhető el. Az itt példában a tartalom moderátor összekötő, amely alapértelmezés szerint.

## <a name="browse-to-the-workflows-section"></a>Keresse meg a munkafolyamatokkal

Az a **beállítások** lapon jelölje be **munkafolyamatok**.

  ![A munkafolyamatok beállítása](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Új munkafolyamat indítása

Válassza ki **munkafolyamat hozzáadása**.

  ![Egy munkafolyamat hozzáadása](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Rendelje hozzá a nevét és leírását.

A munkafolyamat nevet, adjon meg egy leírást, és válassza ki, hogy a munkafolyamat kezeli-e a lemezkép vagy a szöveg.

  ![A munkafolyamat nevét és leírását](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>A kiértékelési feltételek (a "feltétel") megadása

Az alábbi képernyőfelvételen látható a mezők és a Ha-akkor más beállításokat, amelyek a munkafolyamatokhoz definiálni kell. Válassza ki az összekötőt. Ez a példa **tartalom moderátor**. Attól függően, hogy az összekötő úgy dönt az elérhető lehetőségek a kimenet módosítása.

  ![Adja meg a munkafolyamat-feltétel](images/ocr-workflow-step-2-condition.PNG)

Az összekötő és a kívánt kimenetének kiválasztása után válassza ki egy operátort és a feltétel értéke.

## <a name="define-the-action-to-take"></a>Adja meg a műveletet

Válassza ki a végrehajtandó műveletet és a megfelelő feltétel. Az alábbi példa létrehoz egy kép áttekintése, egy címkét rendel `a`, és kiemeli a látható a feltételnek. A kívánt eredményt több feltétel is egyesíthet. Szükség esetén adjon hozzá egy másik (Else) elérési.

  ![Adja meg a munkafolyamat-művelet](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>A munkafolyamat mentése

Végezetül a munkafolyamat mentése, és jegyezze fel a munkafolyamat nevét. A név egy moderálás feladat indítása a felülvizsgálati API használatával van szüksége.

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

Most, hogy a meghatározott egyéni munkafolyamat, tesztelje tartalommal.

Válassza ki a megfelelő **munkafolyamat végrehajtása** gombra.

  ![A munkafolyamat tesztelése](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Fájl feltöltése

Mentse a [minta kép](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) helyi meghajtójára. Válassza ki a munkafolyamat teszteléséhez **válasszon (oka) t** és feltölti a lemezképet.

  ![Lemezkép-fájl feltöltése](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>A munkafolyamat nyomon követése

A munkafolyamat nyomon követi a, akkor hajt végre.

  ![Munkafolyamat-végrehajtási nyomon követése](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Bármely megjelölt emberi moderálás lemezképek áttekintése

Ellenőrizze, hogy a kép megtekintéséhez a **kép** lap **tekintse át**.

  ![Rendszerképek felülvizsgálata](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>További lépések 

A kód munkafolyamatot meghívni, használja az egyéni munkafolyamatokat a [ `Job` API konzol gyors üzembe helyezés](../try-review-api-job.md) és a [.NET SDK gyors üzembe helyezés](../moderation-jobs-quickstart-dotnet.md).
