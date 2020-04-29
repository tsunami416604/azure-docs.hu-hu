---
title: Projekt keresése, szerkesztése és törlése – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Az egyéni Translator számos módszert kínál a projektek hatékony kezelésére. Több projektet is létrehozhat, a feltételek alapján kereshet, és szerkesztheti a projekteket. Egy projekt törlése az egyéni fordítóban is lehetséges.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 9432ac390b7677b3dea5aebc84cf23ef6e45db92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595719"
---
# <a name="search-edit-and-delete-projects"></a>Projektek keresése, szerkesztése és törlése

Az egyéni Translator számos módszert kínál a projektek hatékony kezelésére. Több projektet is létrehozhat, a feltételek alapján kereshet, és szerkesztheti a projekteket. Egy projekt törlése az egyéni fordítóban is lehetséges.  

## <a name="search-and-filter-projects"></a>Projektek keresése és szűrése

A szűrő eszköz lehetővé teszi a projektek különböző szűrési feltételek szerinti keresését. Az informatikai szűrők, például a projekt neve, állapota, a forrás és a cél nyelve, valamint a projekt kategóriája.

1.  Kattintson a szűrő gombra.

    ![Keresési projekt](media/how-to/how-to-search-project.png)

1.  A szűrést a következő mezők bármelyike (vagy mindegyike) alapján végezheti el: projekt neve, állapot, forrás nyelve, célnyelv és kategória.

2.  Kattintson az Alkalmaz gombra.

    ![Keresési projekt szűrési beállításai](media/how-to/how-to-search-project-filters.png)

3.  Törölje a szűrőt az összes projekt megtekintéséhez a "Törlés" gombra koppintva.


## <a name="edit-a-project"></a>Projekt szerkesztése

Az egyéni fordító lehetővé teszi a projekt nevének és leírásának szerkesztését. Más projekt-metaadatok, például a kategória, a forrás nyelv és a célnyelv nem érhetők el szerkesztésre. Az alábbi lépések a projektek szerkesztésének módját ismertetik.

1.  Kattintson a ceruza ikonra, amely akkor jelenik meg, amikor egy projekt fölé viszi a kurzort.

    ![Projekt szerkesztése](media/how-to/how-to-edit-project.png)

2.  A párbeszédpanelen módosíthatja a projekt nevét vagy a projekt leírását, de a projekt felirata, kategóriája vagy nyelvi párja nem módosítható.

    ![Projekt szerkesztése párbeszédpanel](media/how-to/how-to-edit-project-dialog.png)

3.  Kattintson a szűrő gombra.

## <a name="delete-a-project"></a>Projekt törlése

Ha már nincs szüksége rá, törölheti a projektet. Az alábbi lépések bemutatják a projektek törlésének módját.

1. Vigye a kurzort bármelyik projekt rekordra, és kattintson a Kuka ikonra.

   ![Projekt törlése](media/how-to/how-to-delete-project.png)

2. Erősítse meg a törlést. Egy projekt törlésével a projekten belül létrehozott összes modell törlődni fog. A projekt törlése nem befolyásolja a dokumentumokat.

   ![Megerősítő párbeszédpanel törlése](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>További lépések

- [Dokumentumok feltöltése](how-to-upload-document.md) az egyéni fordítási modell létrehozásához.
