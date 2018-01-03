---
title: "Egy helyi Docker-tároló alkalmazások hibakeresés |} Microsoft Docs"
description: "Megtudhatja, hogyan módosíthat egy alkalmazást, amely futtatja a helyi Docker-tároló, a tároló keresztül szerkesztési és frissítési frissítse és adja meg a töréspontokat hibakeresés"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: 7df8bb2dd61466fd9af6492277d54cf0ffdfeb0b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Alkalmazások hibakeresése a helyi Docker-tárolóban
## <a name="overview"></a>Áttekintés
A Visual Studio Tools for Docker a fejlesztésük és az alkalmazást helyileg egy Linux Docker-tároló ellenőrzése egységes módon biztosít.
Ne kelljen újraindítja a tárolót minden egyes módosítani egy kódot.
Ez a cikk bemutatja, hogyan a Szerkesztés és, a frissítés"szolgáltatás segítségével az ASP.NET Core webalkalmazás elindítása a helyi Docker-tároló, a szükséges módosításokat, és ezt követően frissítse a böngészőt, hogy ezeket a módosításokat.
Ez a cikk is bemutatja, hogyan állítson be töréspontokat a hibakereséshez.

> [!NOTE]
> Windows-tároló támogatása hamarosan egy későbbi kiadásban
>
>

## <a name="prerequisites"></a>Előfeltételek
A következő eszközök telepítése.

* [Visual Studio legújabb verziójának](https://www.visualstudio.com/downloads/)
* [A Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Docker-tároló futtatásához helyileg, szüksége lesz egy helyi docker-ügyfél.
Használhatja a [Docker eszközkészlet](https://www.docker.com/products/docker-toolbox), ehhez a Hyper-V letiltását, vagy használhatja [Docker for Windows](https://www.docker.com/get-docker), amely Hyper-V használ, és a Windows 10 szükséges.

Docker eszközkészlet használata esetén kell [a Docker-ügyfél konfigurálása](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Webalkalmazás létrehozása
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Adja hozzá a Docker-támogatás
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. A kód és a frissítési szerkesztése
Gyors ismétlésének módosításokat, indítsa el az alkalmazás olyan tárolóban, és folytatni a módosításokat, mint az IIS Express a Megtekintés.

1. A megoldás konfigurációs beállítása `Debug` nyomja le az ENTER  **&lt;CTRL + F5 >** a docker lemezkép, és futtassa helyileg.

    Miután a tároló kép készült, és futtatja a Docker-tároló, a Visual Studio indít, az alapértelmezett böngészőben a webalkalmazás.
    Ha a Microsoft Edge böngészőt használ, vagy ellenkező esetben a hibák rendelkezik, tekintse meg a [hibaelhárítás](vs-azure-tools-docker-troubleshooting-docker-errors.md) szakasz.
2. Ugrás a jogi tudnivalók megjelenítése Névjegy lapot, amely ahol fogjuk a módosításokat.
3. Térjen vissza a Visual Studio, és nyissa meg a `Views\Home\About.cshtml`.
4. Adja hozzá az alábbi HTML-tartalmakat, a fájl végén, és mentse a módosításokat.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. A kimeneti ablakban megtekintheti, ha a .NET-build befejeződött, és láthatja, hogy ezek a sorok, lépjen vissza a böngésző, és frissítse a jogi tudnivalók megjelenítése Névjegy lapot.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. A módosítások léptek érvénybe!

## <a name="4-debug-with-breakpoints"></a>4. Töréspontokat a hibakereséshez
Gyakran módosításokat kell további ellenőrzést, a Visual Studio hibakeresési szolgáltatásokat kihasználva.

1. Térjen vissza a Visual Studio, és nyissa meg a`Controllers\HomeController.cs`
2. Cserélje ki annak tartalmát a About() metódus a következő:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Állítson be egy töréspontot bal oldalán a `string message`... sor.
4. Találati  **&lt;F5 >** a hibakeresés.
5. Nyissa meg a jogi tudnivalók megjelenítése Névjegy lapot érni a töréspont megjelenését.
6. Visual Studio a töréspont megtekintéséhez váltson, és vizsgálja meg az üzenet értékét.

   ![][2]

## <a name="summary"></a>Összegzés
A [Docker Visual Studio 2015 eszközök](https://aka.ms/DockerToolsForVS), helyileg, a termelési létrehozásáról a belül egy Docker-tároló fejlődő termelékenységére kaphat.

## <a name="troubleshooting"></a>Hibaelhárítás
[Visual Studio Docker fejlesztői hibaelhárítása](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>További információ a Visual Studio, a Windows és az Azure Docker
* [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) -tároló a .NET Core kódot fejlesztése
* [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) - Build és a docker-tároló üzembe helyezése
* [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) -nyelv szolgáltatások docker-fájlok, szerkesztéséhez hamarosan további e2e esetén
* [Információ a Windows tároló](http://aka.ms/containers)-Windows Server és a Nano Server információk
* [Azure Tárolószolgáltatás](https://azure.microsoft.com/services/container-service/) - [Azure tároló szolgáltatás tartalom](http://aka.ms/AzureContainerService)
* További Docker használata című részben talál példákat [Docker használata](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) a a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [bemutató](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). A HealthClinic.biz bemutató további gyors útmutatóit lásd: [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Azure fejlesztői eszközök – gyors útmutatók).

## <a name="various-docker-tools"></a>Különböző Docker-eszközök
[Néhány nagy docker eszközök (Steve Lasker blog)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Jó cikkek
[A NGINX Mikroszolgáltatások bemutatása](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Bemutatók
* [Steve Lasker: A VS élő moszkvai 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Bevezetés az ASP.NET Core @ build 2016 – Ha Ön a bemutató](https://channel9.msdn.com/Events/Build/2016/B810)
* [.NET-alkalmazásfejlesztés tárolókban, a Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
