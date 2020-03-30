---
title: Tartalom szinkronizálása felhőalapú mappából
description: Megtudhatja, hogy miként telepítheti alkalmazását az Azure App Service szolgáltatásba egy felhőalapú mappából , például a OneDrive-ról vagy a Dropboxból származó tartalomszinkronizáláson keresztül.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482972"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Tartalom szinkronizálása felhőalapú mappából az Azure App Service szolgáltatásba
Ebből a cikkből megtudhatja, hogyan szinkronizálhatja a tartalmat az [Azure App Service-szolgáltatással](https://go.microsoft.com/fwlink/?LinkId=529714) a Dropboxból és a OneDrive-ról. 

Az igény szerinti tartalomszinkronizálás központi telepítését az App Service [Kudu központi telepítési motorja hajtja.](https://github.com/projectkudu/kudu/wiki) Az alkalmazáskóddal és a tartalommal egy kijelölt felhőmappában dolgozhat, majd egy gombnyomással szinkronizálhatja az App Service szolgáltatással. A tartalomszinkronizálás a Kudu buildkiszolgálót használja. 

## <a name="enable-content-sync-deployment"></a>Tartalomszinkronizálás telepítésének engedélyezése

A tartalomszinkronizálás engedélyezéséhez keresse meg az App Service-alkalmazás lapját az [Azure Portalon.](https://portal.azure.com)

A bal oldali menüben kattintson a **Deployment Center** > **OneDrive** vagy **a Dropbox** > Authorize**parancsra.** Kövesse az engedélyezési utasításokat. 

![](media/app-service-deploy-content-sync/choose-source.png)

Csak egyszer kell engedélyeznie a OneDrive-ot vagy a Dropboxot. Ha már jogosult, kattintson a **Folytatás**gombra. Az engedélyezett OneDrive- vagy Dropbox-fiókot a Fiók módosítása gombra kattintva **módosíthatja.**

![](media/app-service-deploy-content-sync/continue.png)

A **Konfigurálás** lapon jelölje ki a szinkronizálni kívánt mappát. Ez a mappa a OneDrive-on vagy a Dropboxban a következő kijelölt tartalomelérési út alatt jön létre. 
   
* **OneDrive:**`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Ha végzett, kattintson a **Folytatás gombra.**

Az **Összegzés** lapon ellenőrizze a beállításokat, és kattintson a **Befejezés gombra.**

## <a name="synchronize-content"></a>Tartalom szinkronizálása

Ha szinkronizálni szeretné a felhőmappában lévő tartalmat az App Service szolgáltatással, lépjen vissza a **Központi webhely** lapra, és kattintson a **Szinkronizálás gombra.**

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Az API-k alapvető eltérései miatt a **OneDrive Vállalati verzió** jelenleg nem támogatott. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Tartalomszinkronizálás telepítésének letiltása

A tartalomszinkronizálás letiltásához keresse meg az App Service-alkalmazás lapját az [Azure Portalon.](https://portal.azure.com)

A bal oldali menüben kattintson a **Telepítési központ** > **kapcsolatbontása parancsra.**

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Telepítés a helyi Git-tárlaton](deploy-local-git.md)
