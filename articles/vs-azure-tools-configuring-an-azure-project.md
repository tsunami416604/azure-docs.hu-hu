---
title: "A Visual Studio egy Azure-felhőszolgáltatás-projekt konfigurálása |} Microsoft Docs"
description: "További tudnivalók az Azure-felhőszolgáltatás-projekt konfigurálása a Visual Studio, a projekt követelményeitől függően."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/06/2017
ms.author: kraigb
ms.openlocfilehash: 799715093bd1a8c8e77e6cdb7168670dc263dfa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>A Visual Studio egy Azure-felhőszolgáltatás-projekt konfigurálása
Egy Azure-felhőszolgáltatás-projekt, beállíthatja a projekt követelményeitől függően. Beállítható a projekthez az alábbi kategóriákban:

- **Egy felhőalapú szolgáltatás közzététele az Azure-bA** -győződjön meg arról, hogy egy meglévő felhőalapú szolgáltatást, az Azure rendszerbe telepítendő nem véletlenül törli egy tulajdonságot.
- **Futtassa, vagy egy felhőalapú szolgáltatás a helyi számítógépen debug** -kiválaszthatja, hogy a szolgáltatás konfigurációját használja, és adja meg, hogy az Azure storage emulator elindításához.
- **Ellenőrizze a cloud service-csomag létrehozásakor** -úgy is dönt, hogy hibaként figyelmeztetéseket, így biztosítható, hogy a cloud service-csomag probléma nélkül telepíti. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Egy Azure-felhőszolgáltatás-projekt konfigurálásának lépései
1. Nyissa meg vagy egy felhőszolgáltatás-projekt létrehozása a Visual Studióban

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki a helyi menüből **tulajdonságok**.
   
1. A projekt Tulajdonságok lapján válassza a **fejlesztési** fülre.

    ![Projekt tulajdonságai menü](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Állítsa be **Rákérdezés a meglévő telepítés törlése előtt** való **igaz**. Ez a beállítás segít biztosítani, ne véletlenül törli egy meglévő Azure-telepítés

1. Válassza ki a kívánt **szolgáltatáskonfiguráció** mely szolgáltatáskonfiguráció jelzi a futtatásakor vagy a felhőalapú szolgáltatás helyileg debug használni kívánt. A szerepkör szolgáltatáskonfiguráció módosítása további információkért lásd: [a szerepkörök az Azure-felhőszolgáltatás konfigurálása a Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Állítsa be **Start Azure storage emulator** való **igaz** futtatásakor vagy a felhőalapú szolgáltatás helyi hibakeresése az Azure storage emulator indításához.

1. Állítsa be **figyelmeztetések hibaként** való **igaz** győződjön meg arról, nem tehető közzé, ha a csomag érvényesítési hibák vannak.

1. Állítsa be **webes projekt portok használatára** való **igaz** győződjön meg arról, hogy a webes szerepkör ugyanazt a portot használja minden egyes elindításakor azt a helyi IIS Express.

1. Válassza ki a Visual Studio eszköztár **mentése**.

## <a name="next-steps"></a>Következő lépések
- [Több szolgáltatáskonfiguráció használata Azure-projekt konfigurálása](vs-azure-tools-multiple-services-project-configurations.md)

