---
title: A Visual Studio Azure magánfelhőkben elérése |} Microsoft Docs
description: Útmutató a magánfelhő-alapú erőforrások eléréséhez a Visual Studio használatával.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 0160ac6db2b92d5a30a19dd444d01a8558b3eed3
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>A Visual Studio Azure magánfelhőkben elérése

Alapértelmezés szerint a Visual Studio támogatja a REST-végpontok Azure felhőben. Ebből a cikkből megtanulhatja a saját magánfelhő-alapú tanúsítvány használatára a Visual Studio eszközből a magánfelhő felhasználói számára.

1. Az Azure portálon, a magánfelhő töltse le a közzétételi-beállítások fájlt, vagy forduljon a rendszergazdához közzététele beállításfájl. (A fájl kiterjesztése `.publishsettings`.)

1. A Visual Studio **Server Explorer**, kattintson a jobb gombbal a **Azure** csomópont, és válassza **kezelése és a szűrő előfizetések**.

    ![Előfizetések parancs kezelése](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Az a **kezelése a Microsoft Azure-előfizetések** párbeszédpanelen válassza a **tanúsítványok** lapra, majd válassza ki **importálási**.

    ![Az Azure tanúsítványok importálása](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Az a **importálása a Microsoft Azure-előfizetések** párbeszédablakban válassza **Tallózás**.

    ![Tallózás gombra a Microsoft Azure-előfizetések importálása párbeszédpanel](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. Az a **nyitott** párbeszédpanelen keresse meg a könyvtárat, ahová mentette az közzététele beállításfájl válassza ki a fájlt, és válassza **nyitott**.

    ![A publish-fájl kiválasztása](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Amikor visszatér a **importálása a Microsoft Azure-előfizetések** párbeszédablakban válassza **importálási**.

    ![Közzététele beállításfájl importálása](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    A tanúsítványok importálására a publish-fájl a Visual Studióhoz, és most kezelheti a magánfelhő-alapú erőforrásokhoz.

