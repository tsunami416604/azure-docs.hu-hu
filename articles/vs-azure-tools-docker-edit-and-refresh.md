---
title: Alkalmazások hibakeresése a helyi Docker-tárolóban |} A Microsoft Docs
description: Megtudhatja, hogyan módosíthat egy helyi Docker-tárolóban futó alkalmazást, szerkesztése és a frissítés a tároló frissítése, és állítsa be töréspontokat a kiválasztott hibakeresés
services: azure-container-service
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: 07a7c1e11d8ca20ff4f42abcb84961cb7cd9e0e1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298213"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Alkalmazások hibakeresése a helyi Docker-tárolóban
## <a name="overview"></a>Áttekintés
A Visual Studio 2017 Linux Docker-tárolóban fejlesztéséhez, és ellenőrizze az alkalmazás helyi egységes módon biztosít.
Indítsa újra a tárolót minden alkalommal, amikor a kód módosítása győződjön meg arról, nem kell.
Ez a cikk bemutatja, hogyan indítsa el az ASP.NET Core webes alkalmazás a helyi Docker-tárolóban, végezze el a szükséges módosításokat, és ezt követően frissítse a böngészőt ezeket a módosításokat a "Szerkesztés és frissítése" funkció használatával.
Ez a cikk is bemutatja, hogyan állítson be töréspontokat a hibakereséshez.

> [!NOTE]
> Egy későbbi kiadásban Windows tároló támogatása hamarosan elérhető
>
>

## <a name="prerequisites"></a>Előfeltételek
A következő eszközöket telepíteni kell.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [A Microsoft ASP.NET Core 1.0-s SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Docker-tárolók futtatásához helyi, szüksége lesz egy helyi docker-ügyfél.
Használhatja a [Docker eszközkészlet](https://www.docker.com/products/docker-toolbox), ami megköveteli a Hyper-V le kell tiltani, vagy használhatja [Docker for Windows](https://www.docker.com/get-docker), amely a Hyper-V használ, és megköveteli a Windows 10-es.

Docker-eszközkészlet használata esetén kell [a Docker-ügyfél konfigurálása](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Webalkalmazás létrehozása
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Docker-támogatás hozzáadása
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. A kód és a frissítés szerkesztése
Gyors ismétlésének módosításokat, indítsa el az alkalmazás egy tárolóból, és továbbra is hajtsa végre módosításokat, tekinti meg őket, mint az IIS Express.

1. A megoldás konfigurációs beállítása `Debug` nyomja le az ENTER  **&lt;CTRL + F5 >** a docker-rendszerkép létrehozásához, és helyileg futtatja az alkalmazást.

    Miután a tároló rendszerképét állították össze, és a Docker-tárolóban fut, a Visual Studio elindítja a Web app, az alapértelmezett böngészőben.
    Ha a Microsoft Edge böngészőt használ, vagy ellenkező esetben a hibák kell, lásd: [hibaelhárítás](vs-azure-tools-docker-troubleshooting-docker-errors.md) szakaszban.
2. Nyissa meg a névjegy oldal, amely van, ahol fogunk hajtsa végre a módosításokat.
3. Térjen vissza a Visual Studio, és nyissa meg a `Views\Home\About.cshtml`.
4. Adja hozzá az alábbi HTML-tartalmakat, a fájl végén, és mentse a módosításokat.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. A kimeneti ablak jelenik meg, amikor a .NET-fordítás befejeződött, és láthatja, hogy ezek a sorok, váltson vissza a böngészőben, és frissítse a névjegy lapra.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. A módosítások alkalmazása!

## <a name="4-debug-with-breakpoints"></a>4. Töréspontokat a hibakereséshez
Gyakran módosításokat kell további ellenőrzés, a Visual Studio hibakereső funkcióját kihasználva.

1. Térjen vissza a Visual Studio, és nyissa meg a `Controllers\HomeController.cs`
2. Cserélje ki annak tartalmát a About() módszer a következő:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Állítson be egy töréspontot bal oldalán a `string message`... sor.
4. Találati  **&lt;F5 >** hibakeresésének elkezdéséhez.
5. Keresse meg a Töréspont a névjegy lapra.
6. Visual Studio a töréspont megtekintéséhez váltson, és vizsgálja meg az üzenet értékét.

   ![][2]

## <a name="summary"></a>Összegzés
A Docker-támogatás a Visual Studio 2017 kérheti le a termelékenység helyileg, az éles létrehozásáról a fejlődő egy Docker-tárolón belül végzett munkát.

## <a name="troubleshooting"></a>Hibaelhárítás
[Hibaelhárítás a Visual Studio Docker fejlesztési](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>További tudnivalók a Visual Studio, a Windows és az Azure Docker
* [Docker Azure DevOps-eszközök](http://aka.ms/dockertoolsforvsts) – létrehozása és üzembe helyezése a docker-tárolók
* [Docker-eszközök Visual Studio Code](http://aka.ms/dockertoolsforvscode) – nyelvi szolgáltatásokat docker-fájlok szerkesztéséhez hamarosan további e2e forgatókönyvekkel
* [Windows-tároló információk](http://aka.ms/containers)– Windows Server és a Nano Server adatait
* [Az Azure Container Service](https://azure.microsoft.com/services/container-service/) - [az Azure Container Service-tartalom](http://aka.ms/AzureContainerService)
* További példák a docker használatával, lásd: [Dockerrel](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) származó a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [bemutató](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). A HealthClinic.biz bemutató további gyors útmutatóit lásd: [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Azure fejlesztői eszközök – gyors útmutatók).

## <a name="various-docker-tools"></a>Docker-eszközök
[Néhány nagyszerű docker-eszközök (Steve Lasker blog)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Jó cikkek
[Az NGINX Mikroszolgáltatások bemutatása](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Bemutatók
* [Steve Lasker: VS Live, Las Vegas 2016 – Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Build 2016 – Amennyiben Ön, bemutató @ ASP.NET Core bemutatása](https://channel9.msdn.com/Events/Build/2016/B810)
* [Tárolók, a Channel 9-on a .NET-alkalmazások fejlesztése](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
