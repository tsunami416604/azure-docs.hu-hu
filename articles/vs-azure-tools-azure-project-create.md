---
title: Az Azure felhőszolgáltatás-projekt létrehozása Visual studióval |} A Microsoft Docs
description: Ismerje meg, hozhat létre egy Azure-felhőszolgáltatás-projekt a Visual Studióval
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 3e6ab2078c6b9233360d7cabdc49eddf557b9b57
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189240"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Az Azure felhőszolgáltatás-projekt létrehozása Visual studióval
Az Azure Tools for Visual Studio biztosít, amelyek segítségével hozhat létre projektsablon egy [Azure-felhőszolgáltatásban](/azure/cloud-services/cloud-services-choose-me), azaz egy egyszerű általános célú Azure-szolgáltatás. A projekt létrehozása után a Visual Studio lehetővé teszi konfigurálása, a Hibakeresés és a felhőalapú szolgáltatás üzembe helyezése az Azure-bA.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>A Visual Studióban egy Azure-felhőszolgáltatás-projekt létrehozásának lépései
Ez a szakasz végigvezeti egy Azure-felhőszolgáltatás-projekt létrehozása a Visual Studióban, egy vagy több webes szerepkörökhöz.  

1. Indítsa el a Visual Studiót rendszergazdaként.

1. A fő menüjéből válassza **fájl** > **új** > **projekt**.

1. Válassza ki **felhőalapú** Visual C# vagy Visual Basic sablon csomópontok projektre, és válassza ki **Azure Cloud Service** sablont a listából.

    ![Új Azure-felhőszolgáltatás](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Adja meg a fejleszthet a projekthez használni kívánt .NET-keretrendszer mely verzióját.

1. Adjon meg egy nevet és a projekt helyét, és a megoldás nevét. 

1. Kattintson az **OK** gombra.

1. Az a **új Microsoft Azure-Felhőszolgáltatás** párbeszédpanelen válassza ki a hozzáadni kívánt szerepköröket, és válassza a jobbra mutató nyíl gombra a megoldás való hozzáadásakor.

    ![Új Azure cloud service szerepkörök kiválasztása](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Hozzáadott szerepkör átnevezése, vigye a mutatót a szerepkör a **új Microsoft Azure-Felhőszolgáltatás** párbeszédpanelen, és a helyi menüből válassza az **átnevezése**. Egy szerepkör nevezze át a megoldáson belül (az a **Megoldáskezelőben**) miután azt hozzáadta.

    ![Az Azure felhőszolgáltatási szerepkör átnevezése](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

A Visual Studióhoz kapcsolódó Azure-projekt rendelkezik társítást a projektet a megoldásban. A projekt is magában foglalja a *szolgáltatásdefiníciós fájl* és *szolgáltatás konfigurációs fájlja*:

- **Szolgáltatásdefiníciós fájl** – meghatározza a futásidő beállításait, az alkalmazás, többek között, hogy milyen szerepkörök szükségesek, a végpontok és a virtuálisgép-méretet. 
- **Szolgáltatáskonfigurációs fájlt** -konfigurálja, hogy hány szerepkör példányai futtató és a egy szerepkörhöz definiált beállítások értékeit. 

További információ ezen fájlokkal kapcsolatban lásd: [konfigurálása az Azure cloud Services számára a szerepkörök a Visual Studióval](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>További lépések
- [Az Azure cloud service-projektek a Visual Studióval szerepkörök kezelése](./vs-azure-tools-cloud-service-project-managing-roles.md)
