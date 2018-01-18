---
title: "Telepítse az alkalmazást az Azure App Service FTP/S használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service FTP vagy ftps-t használ."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: fcd079306a8968505349bb3f4a805f203a5c9999
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Telepítse az alkalmazást az Azure App Service segítségével FTP/S

Ez a cikk bemutatja, hogyan FTP vagy ftps-t használja a webalkalmazást, mobil-háttéralkalmazás és API-alkalmazás telepítési [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Az FTP/S-végpont az alkalmazás már aktív. FTP/S telepítési ahhoz szükséges, hogy nem igényel konfigurálást.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>1. lépés: Az üzembe helyezési hitelesítő adatok beállítása

Az FTP-kiszolgáló, az alkalmazás eléréséhez először üzembe helyezési hitelesítő adatokat. 

Állítsa be, vagy a központi telepítési hitelesítő adatok alaphelyzetbe állítása, lásd: [Azure App Service üzembe helyezési hitelesítő adatok](app-service-deployment-credentials.md). Ez az oktatóanyag bemutatja, hogy a felhasználói szintű hitelesítő adatokat.

## <a name="step-2-get-ftp-connection-information"></a>2. lépés: FTP-kiszolgáló kapcsolati adatainak lekérése

1. Az a [Azure-portálon](https://portal.azure.com), nyissa meg az alkalmazás [erőforrás oldala](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Válassza ki **áttekintése** a bal oldali menüben, majd jegyezze fel a értékeit **FTP vagy üzembe helyező felhasználó**, **FTP-állomás neve**, és **állomásnév FTPS**. 

    ![FTP-kapcsolat információi](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Az FTP-kiszolgáló megfelelő segítséget nyújt a **FTP vagy üzembe helyező felhasználó** az Azure portál által megjelenített érték tartalmazza az alkalmazás nevére.
    > Ugyanazokat az információkat található kiválasztásakor **tulajdonságok** a bal oldali menüben. 
    >
    > Emellett a telepítési jelszó soha nem jelenik meg. Ha elfelejti a központi telepítés jelszavát, lépjen vissza a [1. lépés](#step1) és a központi telepítés jelszó.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>3. lépés: Telepítse a fájlokat az Azure

1. Az FTP-ügyfél (például [Visual Studio](https://www.visualstudio.com/vs/community/) vagy [FileZilla](https://filezilla-project.org/download.php?type=client)), a kapcsolat az alkalmazás kapcsolódni összegyűjtött információkat felhasználva.
3. Másolja a fájlokat és a megfelelő könyvtárstruktúrát a [ **/hely/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) az Azure-ban (vagy a **/hely/wwwroot/App_Data/feladatok/** könyvtár a webjobs-feladatok).
4. Tallózással keresse meg az alkalmazás URL-CÍMÉT, ellenőrizze az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> Eltérően [Git-alapú telepítések](app-service-deploy-local-git.md), FTP-telepítés nem támogatja a következő központi telepítés automatizálása: 
>
> - a függőségi visszaállítja (például a NuGet, NPM, PIP és szerkesztő automatizálása)
> - a .NET bináris fájl fordítás
> - a Web.config fájl. generációs (itt egy [Node.js példa](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Létre manuálisan a helyi számítógépen a szükséges fájlokat, és telepítheti azokat az alkalmazás együtt.
>
>

## <a name="next-steps"></a>További lépések

Összetettebb központi telepítési forgatókönyve esetén próbálja [üzembe helyezése az Azure git](app-service-deploy-local-git.md). Az Azure Git-alapú telepítést lehetővé teszi a verziókövetés, a csomag visszaállítás, az MSBuild és több.

## <a name="more-resources"></a>További források

* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deploy-ftp.md)
