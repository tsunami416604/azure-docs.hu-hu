---
title: "Telepítse az ASP.NET Core Linux Docker-tároló egy távoli Docker gazdagépen |} Microsoft Docs"
description: "Útmutató: az ASP.NET Core webalkalmazás telepítése az az Azure Docker fogadó Linux virtuális gép futó Docker-tároló Docker Visual Studio eszközök segítségével"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 60efffd9313f6972ae46fd1925d999597d3c6ba2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Telepítse az ASP.NET-tároló egy távoli Docker gazdagépen
## <a name="overview"></a>Áttekintés
Docker olyan egyszerűsített tároló, virtuális géphez, amely állomás alkalmazások és szolgáltatások segítségével bizonyos értelemben hasonló.
Ez az oktatóanyag bemutatja, hogyan használja a [Docker Visual Studio eszközök](https://docs.microsoft.com/dotnet/articles/core/docker/visual-studio-tools-for-docker) bővítmény ASP.NET Core alkalmazás egy Docker-állomáshoz az Azure PowerShell használatával történő telepítése.

## <a name="prerequisites"></a>Előfeltételek
A következő szükséges az oktatóanyag elvégzéséhez:

* Hozzon létre egy Azure Docker állomás virtuális gép leírtak [docker-gép használata az Azure-ral](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Telepítse a legújabb verzióját [Visual Studio](https://www.visualstudio.com/downloads/)
* Töltse le a [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
* Telepítés [Windows Docker](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Az ASP.NET Core webalkalmazás létrehozása
A következő lépések végigvezetik egy egyszerű ASP.NET Core alkalmazást ebben az oktatóanyagban használt létrehozása.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Adja hozzá a Docker-támogatás
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. A DockerTask.ps1 PowerShell-parancsfájl
1. Nyisson meg egy PowerShell-parancssorba, hogy a projekt gyökérkönyvtárában. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Ellenőrizze a távoli gazdagépen fut-e. Megtekintheti az állapot fut = 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. Létrehozására az alkalmazás - Build paraméter
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Futtassa az alkalmazást, használja a - Futtatás paraméter
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Miután befejeződött a docker, az alábbihoz hasonló eredményeket kell megjelennie:
   
   ![Az alkalmazás megtekintéséhez][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
