---
title: Egy Azure-felhőszolgáltatás-projekt létrehozása a Visual Studio |} Microsoft Docs
description: Ismerje meg, ha folytatni szeretné az Azure-felhőszolgáltatás-projekt létrehozása a Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 06213ecabf3669bf3b8cf2b8d73a4e8def359536
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Egy Azure-felhőszolgáltatás-projekt létrehozása a Visual Studio
Az Azure Tools for Visual Studio biztosít a projekt sablont, amely lehetővé teszi, hogy hozzon létre egy Azure felhőszolgáltatást. A projekt létrehozása után a Visual Studio lehetővé teszi konfigurálásához, hibakeresését és a felhőalapú szolgáltatás üzembe helyezése az Azure-bA.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>A Visual Studio egy Azure-felhőszolgáltatás-projekt létrehozásának lépései
Ez a szakasz végigvezeti az Azure-felhőszolgáltatás-projekt létrehozása a Visual Studióban legalább egy webes szerepkörök.  

1. Indítsa el a Visual Studiót rendszergazdaként.

1. A főmenü, válassza ki a **fájl** > **új** > **projekt**.

1. Válassza ki **felhő** a Visual C# vagy a Visual Basic sablon csomópontok projektre, és válassza a **Azure Cloud Service** sablont a listából.

    ![Új Azure-felhőszolgáltatás](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Adja meg, melyik fejlesztéséhez a projekthez használni kívánt .NET-keretrendszer verzióját.

1. Adjon nevet és a projekt helyét és a megoldás nevét. 

1. Kattintson az **OK** gombra.

1. Az a **új Microsoft Azure Cloud Service** párbeszédpanelen válassza ki a hozzáadni kívánt szerepköröket, és kattintson a jobbra mutató nyílra kattintva adja hozzá a megoldáshoz.

    ![Új Azure cloud service szerepkörök kiválasztása](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Hozzáadott szerepkör átnevezése, vigye a mutatót a szerepkör a **új Microsoft Azure Cloud Service** párbeszédpanel, és a helyi menüből válassza ki a **átnevezése**. Egy szerepkör nevezze át a megoldás belül (a a **Megoldáskezelőben**) után már hozzá lett adva.

    ![Azure cloud service szerepkör átnevezése](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

A Visual Studio Azure projekt társítást a projektek már a megoldásban. A projekt is magában foglalja a *szolgáltatásdefiníciós fájl* és *szolgáltatás konfigurációs fájlja*:

- **Szolgáltatásdefiníciós fájl** -futtatókörnyezet beállításokat az alkalmazás, például milyen-szerepkörök szükségesek, a végpontok és a virtuális gép méretét határozza meg. 
- **Szolgáltatáskonfigurációs fájlt** -konfigurálja a szerepkör hány példányban vannak futtassa és egy szerepkörhöz definiált beállítások értékeit. 

További információ ezen fájlokkal kapcsolatban, tekintse meg [az Azure-felhőszolgáltatás szerepkörök konfigurálásához a Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>További lépések
- [Az Azure cloud service projektek a Visual Studio szerepkörök kezelése](./vs-azure-tools-cloud-service-project-managing-roles.md)
