---
title: "Töltse le a Piactéri elemek az Azure-ból |} Microsoft Docs"
description: "A saját Azure Alkalmazásveremben üzembe az Azure-ból letölthető Piactéri elemek."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Töltse le a Piactéri elemek az Azure-ból az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Hogy mely tartalmak ahhoz, hogy szerepeljen a verem Azure piactér mellett dönt, érdemes a tartalom érhető el az Azure piactérről. A tesztelt futtatásához Azure veremben volt az Azure piactéren elemek válogatott listáját is letölthető. Új elem gyakran felkerül a listára, ezért győződjön meg arról, hogy ellenőrizze az új tartalom.

Töltse le a Piactéri elemek, először [Azure verem regisztrálni Azure](azure-stack-register.md). 

## <a name="download"></a>Letöltés
1. Jelentkezzen be a verem Azure felügyeleti portálra (https://portal.local.azurestack.external).
2. Néhány Piactéri elemek különösen nagyok is lehetnek.  Győződjön meg arról, hogy elegendő lemezterület a számítógépen kattintva **erőforrás-szolgáltató** > **tárolási**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Kattintson a **további szolgáltatások** > **piactér felügyeleti**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Kattintson a **hozzáadása az Azure-ból** a letölthető elemek listájának megjelenítéséhez. Kattintson a leírásának megtekintéséhez és letöltési méret a lista minden eleme.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Válassza ki a elemet a listában, és kattintson a **letöltése**. Ezzel elindítja a kijelölt elemhez tartozó Virtuálisgép-lemezkép letöltése. Letöltési ideje eltérők lehetnek.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. A letöltés befejezése után telepítheti az új Piactéri elemet egy Azure verem operátor vagy a felhasználó. Kattintson a **+ új**, keresni az új piactér elem kategóriák közül, majd válassza ki az elemet.
7. Kattintson a **létrehozása** az létrehozása élményét, az újonnan letöltött elem megnyitása. Hajtsa végre a központi telepítése a cikk részletesen.

## <a name="next-steps"></a>Következő lépések

[Piactéri termék létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
