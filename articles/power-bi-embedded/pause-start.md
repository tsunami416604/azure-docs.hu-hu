---
title: "Felfüggesztése, és indítsa el a Power BI Embedded kapacitás és az Azure portálon |} Microsoft Docs"
description: "Ez a cikk végigvezeti a szüneteltetéséről, és indítsa el a Power BI Embedded kapacitás a Microsoft Azure-ban."
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
ms.openlocfilehash: ab5d184fde2cbcb517b325624c8405954b4d0972
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Felfüggesztése, és indítsa el a Power BI Embedded kapacitás és az Azure-portálon

Ez a cikk végigvezeti a szüneteltetéséről, és indítsa el a Power BI Embedded kapacitás a Microsoft Azure-ban. A parancs feltételezi, hogy a Power BI Embedded kapacitás hozott létre. Ha nem, olvassa el [Power BI Embedded létrehozása kapacitás az Azure portálon](create-capacity.md) a kezdéshez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="pause-your-capacity"></a>A kapacitás felfüggesztése

A kapacitás felfüggesztéséhez megakadályozza kiszámlázott. A kapacitás felfüggesztéséhez nagyszerű, ha nem szeretné használni, a kapacitás egy ideig. A következő lépésekkel felfüggeszti a kapacitást.

> [!NOTE]
> A kapacitás felfüggesztéséhez előfordulhat, hogy a tartalom rendelkezésre állása, a Power BI belül. Ügyeljen arra, hogy a teljes kapacitását munkaterületek megszakadásának megelőzése érdekében felfüggesztés előtt szüntesse meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki **további szolgáltatások** > **Power BI Embedded** a kapacitások megjelenítéséhez.

    ![További szolgáltatások az Azure portálon](media/pause-start/azure-portal-more-services.png)

3. Válassza ki a felfüggeszteni kívánt kapacitást.

    ![A Power BI Embedded kapacitás lista az Azure portálon](media/pause-start/azure-portal-capacity-list.png)

4. Válassza ki **szünet** belül a kapacitás részleteit.

    ![A kapacitás felfüggesztése](media/pause-start/azure-portal-pause-capacity.png)

5. Válassza ki **Igen**, amely megerősíti, hogy fel szeretné függeszteni a kapacitást.

    ![Erősítse meg a felfüggesztés](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Indítsa el a kapacitás

A kapacitás elindításával használatának folytatása A kapacitás indítása is folytatja a számlázási.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki **további szolgáltatások** > **Power BI Embedded** a kapacitások megjelenítéséhez.

    ![További szolgáltatások az Azure portálon](media/pause-start/azure-portal-more-services.png)

3. Válassza ki a kapacitás el szeretné indítani.

    ![A Power BI Embedded kapacitás lista az Azure portálon](media/pause-start/azure-portal-capacity-list.png)

4. Válassza ki **Start** belül a kapacitás részleteit.

    ![Indítsa el a kapacitás](media/pause-start/azure-portal-start-capacity.png)

5. Válassza ki **Igen**, amely megerősíti, hogy el szeretné indítani a kapacitást.

    ![Indítás megerősítése](media/pause-start/azure-portal-confirm-start.png)

Ha a tartalom hozzá van rendelve a kapacitás, érhető el egyszer elindult.

## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, a kapacitás méretezési felfelé vagy lefelé, lásd: [méretezni a Power BI Embedded kapacitás](scale-capacity.md).

A Power BI-tartalmakat az alkalmazáson belül beágyazás megkezdéséhez tekintse meg a [beágyazása a Power BI irányítópultok, jelentések és csempék](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

További kérdései vannak? [Próbálja a kérése a Power BI-Közösség](http://community.powerbi.com/)