---
title: A projekt keresése, szerkesztése és törlése – Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Egyéni fordító különböző módon kezelheti a projektek hatékony módon. Több projektet is létrehozhat, a feltételek alapján kereshet, szerkesztheti a projekteket. A projekt törlése az Egyéni fordítóban is lehetséges.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 9432ac390b7677b3dea5aebc84cf23ef6e45db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595719"
---
# <a name="search-edit-and-delete-projects"></a>Projektek keresése, szerkesztése és törlése

Az Egyéni fordító többféle lehetőséget kínál a projektek hatékony kezelésére. Számos projektet hozhat létre, kereshet a feltételek alapján, és szerkesztheti a projekteket. A projekt törlése az Egyéni fordítóban is lehetséges.  

## <a name="search-and-filter-projects"></a>Projektek keresése és szűrése

A szűrőeszköz lehetővé teszi a projektek különböző szűrési feltételek szerinti keresését. Szűri a projekt nevét, állapotát, forrás- és célnyelvét, valamint a projekt kategóriáját.

1.  Kattintson a szűrő gombra.

    ![Projekt keresése](media/how-to/how-to-search-project.png)

1.  A következő mezők bármelyike (vagy mindegyike) szerint szűrhet: projektnév, állapot, forrásnyelv, célnyelv és kategória.

2.  Kattintson az Alkalmaz gombra.

    ![Projektszűrő beállításainak keresése](media/how-to/how-to-search-project-filters.png)

3.  Törölje a szűrőt az összes projekt megtekintéséhez a "Törlés" gombra koppintva.


## <a name="edit-a-project"></a>Projekt szerkesztése

Az Egyéni fordító lehetővé teszi a projekt nevének és leírásának szerkesztését. Más projektmetaadatok, például a kategória, a forrásnyelv és a célnyelv nem szerkeszthetővé. Az alábbi lépések ismertetik a projektek szerkesztését.

1.  Kattintson a ceruza ikonra, amely akkor jelenik meg, amikor egy projekt fölé viszi az egérmutatót.

    ![Projekt szerkesztése](media/how-to/how-to-edit-project.png)

2.  A párbeszédpanelen módosíthatja a projekt nevét vagy leírását, de nem módosíthatja a projekt címkéjét, kategóriáját vagy nyelvi párját.

    ![Projekt szerkesztése párbeszédpanel](media/how-to/how-to-edit-project-dialog.png)

3.  Kattintson a szűrő gombra.

## <a name="delete-a-project"></a>Projekt törlése

Törölheti a projektet, ha már nincs rá szüksége. Az alábbi lépések ismertetik a projekt törlését.

1. Mutasson bármely projektrekordra, és kattintson a kuka ikonra.

   ![Projekt törlése](media/how-to/how-to-delete-project.png)

2. Megerősítem a törlést. A projekt törlésével a projekten belül létrehozott összes modell törlődik. A projekt törlése nincs hatással a dokumentumokra.

   ![Megerősítési párbeszédpanel törlése](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>További lépések

- [Dokumentumok feltöltésével](how-to-upload-document.md) megkezdheti az egyéni fordítási modell felépítését.
