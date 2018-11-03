---
title: Az Azure felhőszolgáltatás-projekt konfigurálása a Visual Studióval |} A Microsoft Docs
description: Ismerje meg az Azure felhőszolgáltatás-projekt konfigurálása a Visual Studio projekt követelményektől függően.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: f119f4c758fcc7d89e5790ba9e3a223dc3a726c5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969243"
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Az Azure felhőszolgáltatás-projekt konfigurálása a Visual Studióval
Konfigurálhatja egy Azure-felhőszolgáltatás-projekt projekt követelményektől függően. Az alábbi kategóriákban a projekt tulajdonságai állíthatók be:

- **Egy felhőalapú szolgáltatás közzététele az Azure-bA** – beállíthatja a tulajdonságot győződjön meg arról, hogy egy meglévő felhőszolgáltatásban üzembe helyezni az Azure nem véletlenül törlődik.
- **Spustit nebo ladit felhőszolgáltatás a helyi számítógépen** – kiválaszthatja, hogy egy szolgáltatás konfigurációját használja, és adja meg, hogy az Azure storage emulator elindításához.
- **Felhőszolgáltatás-csomagok érvényesítése létrehozásakor** – Ön dönt, hogy hibaként figyelmeztetéseket, így biztosíthatja, hogy a felhőszolgáltatás-csomagok probléma nélkül telepíti. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Az Azure felhőszolgáltatás-projekt konfigurálásának lépései
1. Nyissa meg, vagy egy felhőszolgáltatás-projekt létrehozása a Visual Studióban

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és a helyi menüből válassza ki a **tulajdonságok**.
   
1. A projekt tulajdonságai lapon válassza ki a **fejlesztési** fülre.

    ![Projekt tulajdonságai menü](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Állítsa be **kérése egy meglévő üzemelő példány törlése előtt** való **igaz**. Ez a beállítás lehetővé teszi a véletlenül ne törölje a meglévő környezet az Azure-ban

1. Válassza ki a kívánt **szolgáltatáskonfiguráció** melyik szolgáltatáskonfiguráció jelzi a futtatásakor, vagy helyileg a cloud service hibakeresése a használni kívánt. A szerepkör szolgáltatáskonfiguráció módosítása további információkért lásd: [konfigurálása az Azure cloud Services számára a szerepkörök a Visual Studióval](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Állítsa be **indítsa el az Azure storage emulator** való **igaz** futtatásakor, vagy helyileg a cloud service hibakeresése az Azure storage emulator indításához.

1. Állítsa be **figyelmeztetések hibaként** való **igaz** , hogy Ön nem tehető közzé, ha a csomag érvényesítési hibák léptek fel.

1. Állítsa be **web projektet portok használatára** való **igaz** , győződjön meg arról, hogy a webes szerepkör használja ugyanazt a portot, a rendszer minden alkalommal, elindul a helyi IIS Express.

1. A Visual Studio eszköztárában válassza **mentése**.

## <a name="next-steps"></a>További lépések
- [Több szolgáltatás-konfiguráció használata Azure-projekt konfigurálása](vs-azure-tools-multiple-services-project-configurations.md)

