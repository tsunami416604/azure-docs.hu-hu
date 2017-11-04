---
title: "A Visual Studio Azure magánfelhőkben elérése |} Microsoft Docs"
description: "Útmutató a magánfelhő-alapú erőforrások eléréséhez a Visual Studio használatával."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: kraigb
ms.openlocfilehash: b2578c837732ab05d538e9b896ed3a3035075a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>A Visual Studio Azure magánfelhőkben elérése
Alapértelmezés szerint a Visual Studio támogatja a nyilvános Azure-felhőbe REST-végpontok. Ebben a témakörben elsajátíthatja, hogyan saját magánfelhő-alapú tanúsítvány használatára eléri - és a - használhatja a Visual Studio eszközből a magánfelhő.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>A saját Azure eléréséhez a Visual Studio felhő
1. Az a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885) megadása a magánfelhőhöz, töltse le a közzétételi-beállítások fájlt, vagy forduljon a rendszergazdához közzététele beállításfájl. A nyilvános Azure verzión a hivatkozásra kattintva töltse le a rendszer [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (A letöltött fájl kiterjesztése kell `.publishsettings`)

1. Nyissa meg a Visual Studio

1. A **Server Explorer**, kattintson a jobb gombbal a **Azure** csomópont, és válassza a helyi menüből **kezelése és a szűrő előfizetések**.
   
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
   
## <a name="next-steps"></a>Következő lépések
- [A Visual Studio eszközből közzétételéhez Azure-Felhőszolgáltatás](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Hogyan: letöltése és importálása beállítások és az előfizetési adatok közzététele](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)
