---
title: Szinkronizálási tartalom egy felhőbeli mappa – az Azure App Service-ben
description: Megtudhatja, hogyan helyezze üzembe az alkalmazást az Azure App Service segítségével a tartalmak szinkronizálása egy felhőbeli mappa.
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
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: a818cf440fea939eb86271482e95aa813d678e06
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258627"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Az Azure App Service egy felhőbeli mappa szinkronizálási tartalom
Ez a cikk bemutatja, hogyan szinkronizálja a tartalmat [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) a Dropbox és a onedrive vállalati verzió. 

Az igény szerinti tartalom szinkronizálás üzembe helyezése az App Service-ben működteti [Kudu telepítési motor](https://github.com/projectkudu/kudu/wiki). A kód és a egy kijelölt felhőbeli mappa tartalmát is, és szinkronizálhatja az App Service egy gombra kattintva elküldjön. Tartalmak szinkronizálása a Kudu-buildelési kiszolgáló használ. 

## <a name="enable-content-sync-deployment"></a>Tartalmak szinkronizálása üzembe helyezés engedélyezése

Tartalmak szinkronizálása engedélyezéséhez nyissa meg az App Service-oldalra a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **onedrive vállalati verzió** vagy **Dropbox** > **engedélyezés**. Kövesse az engedélyezési utasításokat. 

![](media/app-service-deploy-content-sync/choose-source.png)

Ön csak engedélyezés szükséges a onedrive vállalati verzió vagy Dropbox egyszer. Ha a felhasználó már jogosult, egyszerűen kattintson **Folytatás**. Kattintva módosíthatja az arra jogosult onedrive vállalati verzió vagy Dropbox-fiókjában **fiók módosítása**.

![](media/app-service-deploy-content-sync/continue.png)

Az a **konfigurálása** lapon, válassza ki a szinkronizálni kívánt mappát. Ez a mappa alatt a következő kijelölt tartalom elérési útvonalát a onedrive vállalati verzió vagy Dropbox jön létre. 
   
* **Onedrive vállalati verzió**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Ha befejezte, kattintson a **Folytatás**.

Az a **összefoglalás** oldalon ellenőrizze a beállításokat, kattintson a **Befejezés**.

## <a name="synchronize-content"></a>Tartalom szinkronizálása

Ha meg szeretné szinkronizálni a felhőbeli mappa tartalmát az App Service szolgáltatással, lépjen vissza a **üzembe helyezési központ** lapot, és kattintson **szinkronizálási**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Az API-k, a mögöttes különbségek miatt **OneDrive vállalati verzióba** jelenleg nem támogatott. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Tartalmak szinkronizálása üzembe helyezés letiltása

Tartalmak szinkronizálása letiltásához nyissa meg az App Service-oldalra a [az Azure portal](https://portal.azure.com).

A bal oldali menüben kattintson a **üzembe helyezési központ** > **Disconnect**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Üzembe helyezés a helyi Git-adattár](app-service-deploy-local-git.md)
