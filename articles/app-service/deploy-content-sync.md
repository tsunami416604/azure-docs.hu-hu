---
title: Tartalom szinkronizálása egy Felhőbeli mappából
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást úgy, hogy a tartalom szinkronizálásával Azure App Service a Cloud mappában, például a OneDrive vagy a Dropbox használatával.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c55894bff9501d3ffb9aa843a9eaa240a213180e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671731"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Tartalom szinkronizálása egy Felhőbeli mappából a Azure App Serviceba
Ez a cikk bemutatja, hogyan szinkronizálhat tartalmat [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) a dropboxból és a OneDrive. 

Az igény szerinti tartalom-szinkronizálási telepítést a App Service [kudu üzembe helyezési motorja](https://github.com/projectkudu/kudu/wiki)látja el. Használhatja az alkalmazás kódját és tartalmát egy kijelölt Felhőbeli mappában, majd a gombra kattintva szinkronizálhatja App Service. A Content Sync a kudu Build-kiszolgálót használja. 

## <a name="enable-content-sync-deployment"></a>Tartalom-szinkronizálás telepítésének engedélyezése

A tartalom szinkronizálásának engedélyezéséhez navigáljon a [Azure Portal](https://portal.azure.com)app Service alkalmazás lapjára.

A bal oldali menüben kattintson a **Deployment Center** > **OneDrive** vagy a **Dropbox** > **Engedélyezés**elemre. Kövesse az engedélyezési utasításokat. 

![](media/app-service-deploy-content-sync/choose-source.png)

Csak egyszer kell engedélyeznie a OneDrive vagy a Dropboxot. Ha már engedélyezte a jogosultságot, kattintson a **Continue (folytatás**) gombra. A **fiók módosítása**lehetőségre kattintva módosíthatja a jóváhagyott OneDrive vagy Dropbox-fiókot.

![](media/app-service-deploy-content-sync/continue.png)

A **configure (Konfigurálás** ) lapon válassza ki a szinkronizálni kívánt mappát. Ez a mappa a OneDrive vagy a Dropbox következő kijelölt tartalom elérési útján jön létre. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Ha elkészült, kattintson a **Continue (folytatás**) gombra.

Az **Összefoglalás** lapon ellenőrizze a beállításokat, majd kattintson a **Befejezés**gombra.

## <a name="synchronize-content"></a>Tartalom szinkronizálása

Ha a felhőalapú mappában lévő tartalmat szinkronizálni szeretné a App Serviceval, lépjen vissza a **központi telepítési központ** lapra, és kattintson a **szinkronizálás**elemre.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Az API-k alapvető eltérései miatt a **OneDrive for Business** jelenleg nem támogatott. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Tartalom-szinkronizálás központi telepítésének letiltása

A tartalom-szinkronizálás letiltásához navigáljon a [Azure Portal](https://portal.azure.com)app Service alkalmazás lapjára.

A bal oldali menüben kattintson a **központi telepítési központ** > a **Leválasztás**elemre.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Üzembe helyezés helyi git-tárházból](deploy-local-git.md)
