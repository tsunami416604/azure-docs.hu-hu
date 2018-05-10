---
title: Telepítse az alkalmazást az Azure App Service FTP/S használatával |} Microsoft Docs
description: Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service FTP vagy ftps-t használ.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
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
2. Válassza ki **áttekintése** a bal oldali navigációs, majd jegyezze fel a értékeinek **FTP vagy üzembe helyező felhasználó**, **FTP-állomás neve**, és **állomásnév FTPS**. 

    ![FTP-kapcsolat információi](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Az FTP-kiszolgáló megfelelő segítséget nyújt a **FTP vagy üzembe helyező felhasználó** az Azure portál által megjelenített érték tartalmazza az alkalmazás nevére.
    > Ugyanazokat az információkat található kiválasztásakor **tulajdonságok** a bal oldali navigációs. 
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

## <a name="enforce-ftps"></a>FTPS kényszerítése

A fokozott biztonság érdekében engedélyezze FTP SSL-en keresztül csak. Ha nem adja meg FTP telepítési FTP és a ftps-t is letilthatók.

Az alkalmazás erőforrás lapján [Azure-portálon](https://portal.azure.com), jelölje be **Alkalmazásbeállítások** a bal oldali navigációs.

Titkosítatlan FTP letiltásához válassza **FTPS csak**. FTP és a ftps-t teljesen letiltásához válassza **letiltása**. Ha befejezte, kattintson a **mentése**.

![Tiltsa le az FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>FTP telepítési hibáinak elhárítása

- [Hogyan háríthatom FTP telepítési?](#how-can-i-troubleshoot-ftp-deployment)
- [I nem lehet FTP, és tegye közzé a saját kód. Hogyan lehet megoldani a problémát?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan lehet kapcsolódni az Azure App Service FTP passzív módra keresztül?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan háríthatom FTP telepítési?

Az első lépés FTP üzembe helyezés hibaelhárítása van elkülönítése a egy futásidejű alkalmazásproblémák egy központi telepítési probléma.

Egy központi telepítési probléma általában a fájlok vagy hibás fájlok, az alkalmazás központi telepítése nem eredményez. Az FTP-telepítés vizsgálja, vagy jelöljön ki egy alternatív telepítési elérési utat (például a verziókövetési rendszerrel) által korrigálható.

A futásidejű alkalmazásproblémák általában eredményez a az alkalmazás, de nem megfelelő alkalmazások viselkedése telepített fájlok megfelelő halmazát. Kód viselkedés futásidőben összpontosít, és vizsgálja a konkrét hiba elérési utak korrigálható.

A központi telepítés, illetve runtime jelöléssel probléma megállapításához lásd: [futásidejű problémák és telepítési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I nem lehet FTP, és tegye közzé a saját kód. Hogyan lehet megoldani a problémát?
Ellenőrizze, hogy helyesen adta a megfelelő állomásnév és [hitelesítő adatok](#step-1--set-deployment-credentials). Ellenőrizze azt is, hogy a számítógépen a következő FTP-portok nem blokkolja tűzfal:

- FTP-vezérlőkapcsolati port: 21
- FTP-adatok csatlakozási portja: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan lehet kapcsolódni az Azure App Service FTP passzív módra keresztül?
Az Azure App Service támogatja az aktív és passzív módú keresztül kapcsolódik. Passzív módú használata ajánlott, mivel a központi telepítés gépek általában (az operációs rendszer vagy üzleti hálózati vagy otthoni részeként) tűzfal mögött található. Lásd: egy [példa a WinSCP dokumentációs](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>További lépések

Összetettebb központi telepítési forgatókönyve esetén próbálja [üzembe helyezése az Azure git](app-service-deploy-local-git.md). Az Azure Git-alapú telepítést lehetővé teszi a verziókövetés, a csomag visszaállítás, az MSBuild és több.

## <a name="more-resources"></a>További források

* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deploy-ftp.md)
