---
title: Egy Azure-felhőszolgáltatás-projekt létrehozása a Visual Studio |} Microsoft Docs
description: Ismerje meg, ha folytatni szeretné az Azure-felhőszolgáltatás-projekt létrehozása a Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 41c2bd0dc1f609b6b7b9cfda43c2378feb1c3b6c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
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
