---
title: A Visual Studióval Azure magánfelhők elérése |} A Microsoft Docs
description: Ismerje meg, hogyan férhetnek hozzá a magánfelhő erőforrásai, a Visual Studio használatával.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 669d8fe2141016924432f8128256c3deb7c44db3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054703"
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>A Visual Studióval Azure magánfelhők elérése

Alapértelmezés szerint a Visual Studio támogatja az Azure-felhő REST-végpontokat. Ebből a cikkből elsajátíthatja a magánfelhő-tanúsítvány használatával elérheti és kezelheti a magánfelhő a Visual Studióból.

1. Az Azure Portalon, a magánfelhő töltse le a közzétételi-beállítások fájlt, vagy forduljon a rendszergazdához tartozó közzétételi beállításfájlt. (A fájl kiterjesztése `.publishsettings`.)

1. A Visual Studióban **Server Explorer**, kattintson a jobb gombbal a **Azure** csomópontra, és válassza **kezelése és a szűrő előfizetések**.

    ![Előfizetések parancs kezelése](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Az a **kezelése a Microsoft Azure-előfizetések** párbeszédpanelen válassza ki a **tanúsítványok** lapfülre, majd válassza ki **importálás**.

    ![Azure-tanúsítványok importálása](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Az a **importálása a Microsoft Azure-előfizetések** párbeszédablakban válassza **Tallózás**.

    ![Tallózás gomb importálása a Microsoft Azure-előfizetések párbeszédpanelen](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. Az a **nyílt** párbeszédpanelen tallózással keresse meg azt a könyvtárat, ahová mentette a publish-beállításfájl, válassza ki a fájlt, és válassza ki **nyílt**.

    ![A publish-fájl kiválasztása](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Amikor visszatér a **importálása a Microsoft Azure-előfizetések** párbeszédablakban válassza **importálás**.

    ![Közzététele beállításfájl importálása](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    A tanúsítványok importálására a közzétételi beállításfájlt Visual studióba, és most már használhatja a magánfelhő-erőforrásokkal.

