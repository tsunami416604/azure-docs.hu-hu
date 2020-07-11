---
title: Tartalom szinkronizálása egy Felhőbeli mappából
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást úgy, hogy a tartalom szinkronizálásával Azure App Service a Cloud mappában, például a OneDrive vagy a Dropbox használatával.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 693e552c8743b435fac6fda9d5ab023be5d9adeb
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221136"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Tartalom szinkronizálása egy Felhőbeli mappából a Azure App Serviceba
Ez a cikk bemutatja, hogyan szinkronizálhat tartalmat [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) a dropboxból és a OneDrive. 

Az igény szerinti tartalom-szinkronizálási telepítést a App Service [kudu üzembe helyezési motorja](https://github.com/projectkudu/kudu/wiki)látja el. Az alkalmazás kódjával és tartalmával egy kijelölt Felhőbeli mappában is dolgozhat, majd a gombra kattintva szinkronizálhatja App Service. A Content Sync a kudu Build-kiszolgálót használja. 

## <a name="enable-content-sync-deployment"></a>Tartalom-szinkronizálás telepítésének engedélyezése

A tartalom szinkronizálásának engedélyezéséhez navigáljon a [Azure Portal](https://portal.azure.com)app Service alkalmazás lapjára.

A bal oldali menüben kattintson a **Deployment Center**  >  **OneDrive** vagy a **Dropbox**  >  **engedélyezése**lehetőségre. Kövesse az engedélyezési utasításokat. 

![Bemutatja, hogyan engedélyezhető a OneDrive vagy a Dropbox a központi telepítési központban a Azure Portalban.](media/app-service-deploy-content-sync/choose-source.png)

Csak egyszer kell engedélyeznie a OneDrive vagy a Dropboxot. Ha már engedélyezte a jogosultságot, kattintson a **Continue (folytatás**) gombra. A **fiók módosítása**lehetőségre kattintva módosíthatja a jóváhagyott OneDrive vagy Dropbox-fiókot.

![Azt mutatja be, hogyan lehet módosítani a Azure Portal az üzembe helyezési központban az engedélyezve lévő OneDrive vagy Dropbox-fiókot.](media/app-service-deploy-content-sync/continue.png)

A **configure (Konfigurálás** ) lapon válassza ki a szinkronizálni kívánt mappát. Ez a mappa a OneDrive vagy a Dropbox következő kijelölt tartalom elérési útján jön létre. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Ha elkészült, kattintson a **Continue (folytatás**) gombra.

Az **Összefoglalás** lapon ellenőrizze a beállításokat, majd kattintson a **Befejezés**gombra.

## <a name="synchronize-content"></a>Tartalom szinkronizálása

Ha a felhőalapú mappában lévő tartalmat szinkronizálni szeretné a App Serviceval, lépjen vissza a **központi telepítési központ** lapra, és kattintson a **szinkronizálás**elemre.

![Bemutatja, hogyan lehet szinkronizálni a felhőalapú mappát App Servicekal.](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Az API-k alapvető eltérései miatt a **OneDrive for Business** jelenleg nem támogatott. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Tartalom-szinkronizálás központi telepítésének letiltása

A tartalom-szinkronizálás letiltásához navigáljon a [Azure Portal](https://portal.azure.com)app Service alkalmazás lapjára.

A bal oldali menüben kattintson a **központi telepítési központ**  >  **kapcsolat bontása**elemre.

![Bemutatja, hogyan lehet leválasztani a Cloud Folder syncet a App Service alkalmazással a Azure Portal.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Üzembe helyezés helyi git-tárházból](deploy-local-git.md)
