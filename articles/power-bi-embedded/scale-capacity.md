---
title: "A Power BI Embedded kapacitás méretezése |} Microsoft Docs"
description: "Ez a cikk végigvezeti a Power BI Embedded kapacitás méretezése a Microsoft Azure-ban."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: e1ab6a2f52fa56f1e04c6c327796587daf43596e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-power-bi-embedded-capacity"></a>A Power BI Embedded kapacitás méretezése

Ez a cikk végigvezeti a Power BI Embedded kapacitás méretezése a Microsoft Azure-ban. Skálázás teszi növeli vagy csökkenti a kapacitást méretét.

A parancs feltételezi, hogy a Power BI Embedded kapacitás hozott létre. Ha nem, olvassa el [Power BI Embedded létrehozása kapacitás az Azure portálon](create-capacity.md) a kezdéshez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="scale-a-capacity"></a>A kapacitás méretezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki **további szolgáltatások** > **Power BI Embedded** a kapacitások megjelenítéséhez.

    ![További szolgáltatások az Azure portálon](media/scale-capacity/azure-portal-more-services.png)

3. Válassza ki a kapacitás méretezésére.

    ![A Power BI Embedded kapacitás lista az Azure portálon](media/scale-capacity/azure-portal-capacity-list.png)

4. Válassza ki **tarifacsomag** alatt **méretezési** belül a kapacitást.

    ![Méretezési csoportban árképzési szint](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    A jelenlegi árképzési kék meghatározott.

    ![A jelenlegi árképzési szint kék leírt](media/scale-capacity/azure-portal-current-tier.png)

5. Felfelé vagy lefelé méretezési, válassza ki az új réteget történő áthelyezéséhez. A kijelölés körül szaggatott kék körvonal kiválasztásával új réteget helyezi. Válassza ki **válasszon** az új réteghez méretezése.

    ![Jelölje be új réteg](media/scale-capacity/azure-portal-select-new-tier.png)

    A kapacitás skálázás is igénybe vehet egy perc múlva befejezéséhez.

6. Erősítse meg a réteg az Áttekintés lap megtekintésével. A jelenlegi árképzési szerepel.

    ![Erősítse meg a jelenlegi rétegtől](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Következő lépések

Felfüggesztése, vagy indítsa el a kapacitás, lásd: [szüneteltetése és indítsa el a Power BI Embedded kapacitás és az Azure-portálon](pause-start.md).

A Power BI-tartalmakat az alkalmazáson belül beágyazás megkezdéséhez tekintse meg a [beágyazása a Power BI-irányítópultok, jelentések és csempék](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

További kérdései vannak? [Próbálja a kérése a Power BI-Közösség](http://community.powerbi.com/)