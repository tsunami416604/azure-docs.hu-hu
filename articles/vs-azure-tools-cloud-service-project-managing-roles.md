---
title: Az Azure felhőszolgáltatások Visual studióval szerepkörök kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá, és távolítsa el a szerepköröket az Azure felhőszolgáltatások Visual studióval.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 023d70fd1f1ae2f79483f44a84cfedd5d1e39f3f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054511"
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Az Azure felhőszolgáltatások Visual studióval szerepkörök kezelése
Miután létrehozta az Azure-felhőszolgáltatás, új szerepkörök hozzáadása, vagy távolítsa el a meglévő szerepköröket belőle. Egy meglévő projekt importálásához is, és alakítsa át a szerepkört. Például egy ASP.NET-alkalmazás importálása, és a webes szerepkörként kijelölje azt.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Szerepkör hozzáadása az Azure felhőszolgáltatás
A következő lépések végigvezetik egy webes vagy feldolgozói szerepkör hozzáadása az Azure felhőszolgáltatás-projekt a Visual Studióban.

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára

1. Kattintson a jobb gombbal a **szerepkörök** csomópontot a helyi menü megjelenítéséhez. A helyi menüből válassza ki a **Hozzáadás**, majd válasszon ki egy meglévő webes szerepkört vagy feldolgozói szerepkör az aktuális megoldáshoz, vagy hozzon létre egy webes vagy feldolgozói szerepkör projekt. Válassza ki egy megfelelő projektet, például egy ASP.NET webes alkalmazási projektet is, és társíthatja azt egy szerepkör projekt.

    ![A szerepkör hozzáadása az Azure felhőszolgáltatás-Projekt menü parancsai](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>A szerepkör eltávolítását az Azure-felhőszolgáltatás
A következő lépések végigvezetik egy webes vagy feldolgozói szerepkör eltávolítását az Azure felhőszolgáltatás-projekt a Visual Studióban.

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára

1. Bontsa ki a **szerepkörök** csomópont.

1. Kattintson a jobb gombbal a csomópontra, távolítsa el, és a helyi menüből válassza ki a kívánt **eltávolítása**. 

    ![A szerepkör hozzáadása egy Azure-felhőszolgáltatásban menü parancsai](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Újra hozzáadja egy szerepkör, egy Azure-felhőszolgáltatás-projekt
Ha a szerepkör eltávolítása a felhőszolgáltatási projektet, de később úgy dönt, hogy a szerepkör vissza hozzáadása a projekthez, csak a szerepkör nyilatkozatot és az alapszintű attribútumait, például a végpontok és diagnosztikai információk, kerülnek. További erőforrások vagy hivatkozások hozzáadódnak a `ServiceDefinition.csdef` fájl vagy a `ServiceConfiguration.cscfg` fájlt. Ha azt szeretné, adja meg az adatokat, manuálisan adja hozzá ezeket a fájlokat visszaimportálni szeretne.

Például előfordulhat, hogy eltávolítja a webszolgáltatási szerepkörét, és később úgy dönt, hogy ez a szerepkör hozzáadása vissza a megoldás. Ha így tesz, akkor hiba történik. Ez a hiba elkerülése érdekében fel kell vennie a `<LocalResources>` elem a következő XML formátumú be újra a `ServiceDefinition.csdef` fájlt. A webszolgáltatási szerepkörének hozzáadott vissza a projektbe a name attribútum részeként nevét használja a **<LocalStorage>** elemet. Ebben a példában a webes szolgáltatás szerepkör neve nem **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>További lépések
- [A szerepkörök az Azure cloud Services számára konfigurálása a Visual Studióval](vs-azure-tools-configure-roles-for-cloud-service.md)
