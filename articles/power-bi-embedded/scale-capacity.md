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
ms.date: 01/19/2018
ms.author: asaxton
ms.openlocfilehash: 9b7d0bc31946d6022e9bfae463f4a22eb12c2a58
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="scale-your-power-bi-embedded-capacity"></a>A Power BI Embedded kapacitás méretezése

Ez a cikk végigvezeti a Power BI Embedded kapacitás méretezése a Microsoft Azure-ban. Skálázás teszi növeli vagy csökkenti a kapacitást méretét.

A parancs feltételezi, hogy a Power BI Embedded kapacitás hozott létre. Ha nem, olvassa el [Power BI Embedded létrehozása kapacitás az Azure portálon](create-capacity.md) a kezdéshez.

> [!NOTE]
> A méretezési művelet körülbelül egy perce is tarthat. Ebben az időszakban a kapacitás nem lesz elérhető. Beágyazott tartalom betöltése sikertelen lehet.

## <a name="scale-a-capacity"></a>A kapacitás méretezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki **minden szolgáltatás** > **Power BI Embedded** a kapacitások megjelenítéséhez.

    ![Azure-portálon belül minden szolgáltatás](media/scale-capacity/azure-portal-more-services.png)

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

## <a name="next-steps"></a>További lépések

Felfüggesztése, vagy indítsa el a kapacitás, lásd: [szüneteltetése és indítsa el a Power BI Embedded kapacitás és az Azure-portálon](pause-start.md).

A Power BI-tartalmakat az alkalmazáson belül beágyazás megkezdéséhez tekintse meg a [beágyazása a Power BI-irányítópultok, jelentések és csempék](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

További kérdései vannak? [Próbálja a kérése a Power BI-Közösség](http://community.powerbi.com/)
