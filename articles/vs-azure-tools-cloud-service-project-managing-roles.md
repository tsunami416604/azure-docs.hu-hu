---
title: "Az Azure felhőszolgáltatások Visual Studio szerepkörök kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzáadása és eltávolítása a szerepkörök az Azure felhőszolgáltatások Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 6ed857b857cf8c14506ca39725c214a7fea4fc95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Az Azure felhőszolgáltatások Visual Studio szerepkörök kezelése
Miután létrehozta az Azure-felhőszolgáltatás, új szerepkörök hozzáadása, vagy meglévő szerepkörök eltávolításához. Egy létező projekt importálása is, majd átalakíthatja a szerepkör. Például egy ASP.NET-webalkalmazás importálhatja és kijelöl egy webes szerepkörben.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Szerepkör hozzáadása az Azure-felhőszolgáltatás
A következő lépések végigvezetik egy webes vagy feldolgozói szerepkör hozzáadása a Visual Studio egy Azure-felhőszolgáltatás-projekt.

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára

1. Kattintson a jobb gombbal a **szerepkörök** csomópontra, hogy megjelenjen a helyi menüből. Válassza ki a helyi menüből **Hozzáadás**, majd válassza ki a jelenlegi megoldást egy meglévő webes szerepkör vagy a feldolgozói szerepkör, vagy hozzon létre egy webes vagy feldolgozói szerepkör projekt. Is válassza ki a megfelelő projektben, például egy ASP.NET webalkalmazás projekthez, és társíthatja egy szerepkör-projektet.

    ![Egy Azure-felhőszolgáltatás-projekt szerepkör hozzáadása menüpont](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>A szerepkör eltávolítását Azure-felhőszolgáltatás
A következő lépések végigvezetik a Visual Studio egy Azure-felhőszolgáltatás-projekt egy webes vagy feldolgozói szerepkör eltávolítását.

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára

1. Bontsa ki a **szerepkörök** csomópont.

1. Kattintson a jobb gombbal a csomópontra, távolítsa el, és a helyi menüből válassza ki a kívánt **eltávolítása**. 

    ![A menüpont szerepkör hozzáadása az Azure-felhőszolgáltatás](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Olvasása a következő szerepkör egy Azure cloud service-projekthez
Ha a szerepkör eltávolítása a felhőszolgáltatás-projekt, de később úgy dönt, a szerepkör vissza hozzáadása a projekthez, csak a szerepkör nyilatkozat és alapvető attribútumait, például a végpontok és diagnosztikai információk, kerülnek. További erőforrások vagy hivatkozások hozzáadódnak a `ServiceDefinition.csdef` fájl vagy a `ServiceConfiguration.cscfg` fájlt. Ha hozzá szeretne adni ezt az információt, manuálisan adja hozzá ezeket a fájlokat újra üzembe szeretné.

Például előfordulhat, hogy távolítsa el a webes szerepkör-szolgáltatás, és később úgy dönt, hogy a szerepkör hozzáadása a megoldás programba. Ha így tesz, akkor hiba történik. Ez a hiba elkerüléséhez fel kell vennie a `<LocalResources>` be a következő XML-elem a `ServiceDefinition.csdef` fájlt. A webes szerepkör-szolgáltatás a name attribútum részeként hozzáadott újra üzembe a projekt nevét használja a  **<LocalStorage>**  elemet. Ebben a példában a webes szerepkör-szolgáltatás neve nem **WCFServiceWebRole1**.

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

## <a name="next-steps"></a>Következő lépések
- [A szerepkörök az Azure-felhőszolgáltatás konfigurálása a Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)
