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
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 04d1d226093f131a521f32f47c333ff9aefc6f3b
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Az Azure App Service egy felhőalapú mappából a szinkronizálási tartalom
Az oktatóanyag bemutatja, hogyan lehet telepíteni [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) által népszerű tárolási felhőszolgáltatások, például a Dropbox és a onedrive vállalati verzió származó tartalom szinkronizálása. 

## <a name="overview"></a>Tartalom sync telepítésének áttekintése
Az igény szerinti tartalom sync telepítési működteti a [Kudu telepítési motor](https://github.com/projectkudu/kudu/wiki) App Service szolgáltatással integrált. Az a [Azure Portal](https://portal.azure.com), meghatározhat egy mappát a felhőbeli tárolóhelyen, az alkalmazás és a mappában található tartalom, és az App Service szinkronizálhat egy kattintással. Tartalom szinkronizálása a Kudu folyamata buildelés és üzembe helyezés használja. 

## <a name="contentsync"></a>Tartalom szinkronizálási központi telepítés engedélyezése
Tartalom szinkronizálás engedélyezése a [Azure Portal](https://portal.azure.com), kövesse az alábbi lépéseket:

1. Az Azure portálon az alkalmazás paneljén kattintson **beállítások** > **központi telepítés forrásának**. Kattintson a **forrás választása**, majd jelölje be **OneDrive** vagy **Dropbox** telepítési forrásaként. 
   
    ![Tartalom szinkronizálása](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > Az API-k, a mögöttes különbségek miatt **onedrive vállalati verzió** jelenleg nem támogatott. 
   > 
   > 
2. Végezze el az engedélyezési munkafolyamat ahhoz, hogy az App Service egy adott előre definiált megadott elérési úthoz való hozzáférésre a onedrive-on vagy a Dropbox összes, az App Service tartalmának tárolására.  
    Az App Service engedélyezést követően platform Erre azért van szükség a beállítás a kijelölt tartalom elérési úton található tartalom mappa létrehozásához, vagy kiválaszthat egy meglévő tartalom mappát a kijelölt tartalom elérési útját. A kijelölt tartalom útvonalakat az App Service-szinkronizáláshoz használt cloud storage-fiókok a következők:  
   
   * **Onedrive vállalati verzió**:`Apps\Azure Web Apps` 
   * **Dropbox**:`Dropbox\Apps\Azure`
3. A kezdeti tartalom szinkronizálása után a tartalom szinkronizálása az Azure-portálról igény szerinti kezdeményezhető. Telepítési előzmények esetén érhető el a **központi telepítések** panelen.
   
    ![Telepítési előzmények](./media/app-service-deploy-content-sync/onedrive_sync.png)

További információ a Dropbox telepítési érhető el a [telepítés a Dropbox] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

