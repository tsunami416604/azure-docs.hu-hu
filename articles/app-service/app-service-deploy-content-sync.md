---
title: "Az Azure App Service egy felhőalapú mappából a szinkronizálási tartalom"
description: "Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service tartalom szinkronizálási keresztül egy felhőalapú mappából."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Az Azure App Service egy felhőalapú mappából a szinkronizálási tartalom
Ez az oktatóanyag bemutatja, hogyan tartalom szinkronizálása [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) a népszerű tárolási felhőszolgáltatások, például a Dropbox és a onedrive vállalati verzió. 

## <a name="overview"></a>Tartalom sync telepítésének áttekintése
Az igény szerinti tartalom sync telepítési működteti a [Kudu telepítési motor](https://github.com/projectkudu/kudu/wiki) App Service szolgáltatással integrált. Az a [Azure-portálon](https://portal.azure.com), meghatározhat egy mappát a felhőbeli tárolóhelyen, az alkalmazás és a mappában található tartalom, és az App Service szinkronizálhat egy kattintással. Tartalom szinkronizálása a Kudu folyamata buildelés és üzembe helyezés használja. 

## <a name="contentsync"></a>Tartalom szinkronizálási központi telepítés engedélyezése
Tartalom szinkronizálás engedélyezése a [Azure-portálon](https://portal.azure.com), kövesse az alábbi lépéseket:

1. Az alkalmazás oldalon az Azure portálon kattintson **beállítások** > **központi telepítés forrásának**. Kattintson a **forrás választása**, majd jelölje be **OneDrive** vagy **Dropbox** telepítési forrásaként. 
   
    ![Tartalom szinkronizálása](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > Az API-k, a mögöttes különbségek miatt **onedrive vállalati verzió** jelenleg nem támogatott. 
   > 
   > 
2. Befejeződött a engedélyezési munkafolyamat ahhoz, hogy az App Service egy adott előre definiált megadott elérési úthoz való hozzáférésre OneDrive vagy a dropbox-ba, ahol az összes az App Service-tartalmat tárolja. Engedélyezést követően az App Service platform felkínálja a kijelölt tartalom elérési úton található tartalom mappa létrehozásához, vagy kiválaszthat egy meglévő tartalom mappát a kijelölt tartalom elérési útját. A kijelölt tartalom útvonalakat az App Service-szinkronizáláshoz használt cloud storage-fiókok a következők:  
   
   * **Onedrive vállalati verzió**:`Apps\Azure Web Apps` 
   * **Dropbox**:`Dropbox\Apps\Azure`
3. A tartalom a kezdeti szinkronizálás után az tartalom szinkronizálása az Azure-portálról igény szerinti kezdeményezhető. Telepítési előzmények érhető el a **központi telepítések** lap.
   
    ![Telepítési előzmények](./media/app-service-deploy-content-sync/onedrive_sync.png)

További információ a Dropbox telepítési érhető el a [telepítés a Dropbox] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

