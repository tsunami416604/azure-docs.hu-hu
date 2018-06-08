---
title: Az Azure App Service egy felhőalapú mappából a szinkronizálási tartalom
description: Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service tartalom szinkronizálási keresztül egy felhőalapú mappából.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: d456ae2ffbd3745ef976ad94219a3f998838066b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850219"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Az Azure App Service egy felhőalapú mappából a szinkronizálási tartalom
Ez a cikk bemutatja, hogyan tartalom szinkronizálása [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) a Dropbox és a onedrive-on. 

Az igény szerinti tartalom sync telepítési van-e kapcsolva az App Service által [Kudu telepítési motor](https://github.com/projectkudu/kudu/wiki). A mintaalkalmazás kódját és a tartalom a kijelölt felhő mappában, és beállíthatja, majd szinkronizálja az App Service egy kattintással. Tartalom szinkronizálása a Kudu build kiszolgálót használja. 

## <a name="enable-content-sync-deployment"></a>Tartalom szinkronizálási központi telepítés engedélyezése

Ahhoz, hogy a tartalom szinkronizálása, az App Service alkalmazás oldalra léphet a [Azure-portálon](https://portal.azure.com).

A bal oldali menüben kattintson **Telepítőközpontot** > **OneDrive** vagy **Dropbox** > **engedélyezés**. Az engedélyezési útmutatást követve. 

![](media/app-service-deploy-content-sync/choose-source.png)

Csak kell ahhoz, hogy a OneDrive vagy a Dropbox egyszer. Ha a felhasználó már jogosult, kattintson **Folytatás**. A meghatalmazott OneDrive vagy a Dropbox-fiók kattintva módosíthat **fiók módosítása**.

![](media/app-service-deploy-content-sync/continue.png)

Az a **konfigurálása** lapon, válassza ki a szinkronizálni kívánt mappát. A mappa létrehozása a következő kijelölt tartalom elérési úton található a onedrive-on vagy a dropbox-bA. 
   
* **Onedrive vállalati verzió**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Ha befejezte, kattintson a **Folytatás**.

Az a **összegzés** lapon ellenőrizze a beállításokat, majd kattintson **Befejezés**.

## <a name="synchronize-content"></a>Tartalom szinkronizálása

Ha meg szeretné szinkronizálni a felhő mappa tartalma az App Service, lépjen vissza a **Telepítőközpontot** lapot, és kattintson **szinkronizálási**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Az API-k, a mögöttes különbségek miatt **onedrive vállalati verzió** jelenleg nem támogatott. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Tartalom sync telepítési letiltása

A tartalom-szinkronizálás letiltása, az App Service alkalmazás oldalra navigáljon a [Azure-portálon](https://portal.azure.com).

A bal oldali menüben kattintson **Telepítőközpontot** > **OneDrive** vagy **Dropbox** > **Disconnect**.

![](media/app-service-deploy-content-sync/disable.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyi Git-tárház telepítése](app-service-deploy-local-git.md)
