---
title: Windows fejlesztési környezet kialakítása Service Fabric Mesh-alkalmazások létrehozásához | Microsoft Docs
description: Kialakíthatja úgy a Windows fejlesztési környezetet, hogy Service Fabric Mesh-alkalmazást hozhasson létre, és üzembe helyezhesse azt az Azure Service Fabric Mesh-ben.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 08/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 094cab324c7a7c66f5fcb57d488661109fcdabea
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362670"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>A Windows fejlesztési környezet kialakítása Service Fabric Mesh-alkalmazások létrehozásához

Az Azure Service Fabric Mesh-alkalmazások Windows rendszerű fejlesztői gépen való létrehozásához és futtatásához telepítse a Service Fabric Mesh-futtatókörnyezetet, az SDK-t és az eszközöket.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók

A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Windows 10 (Enterprise, Professional vagy Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

A Service Fabric Mesh-alkalmazások üzembe helyezéséhez a Visual Studio 2017 szükséges. [Telepítse a 15.6.0-s][download-visual-studio] vagy újabb verziót, és engedélyezze a következő számítási feladatokat:

* ASP.NET és webfejlesztés
* Azure-fejlesztés

## <a name="install-docker"></a>A Docker telepítése

#### <a name="windows-10"></a>Windows 10

Töltse le, majd telepítse a [Docker Community Edition for Windows][download-docker] legújabb verzióját a Service Fabric Mesh által használt tárolóalapú Service Fabric-alkalmazások támogatásához.

Amikor a telepítés során a rendszer kéri, válassza a **Windows-tárolók használatát Linux-tárolók helyett**.

Ha a Hyper-V nincs engedélyezve a számítógépen, a Docker telepítője fel fogja ajánlani az engedélyezését. Ehhez kattintson az **OK** gombra, ha a rendszer arra kéri.

#### <a name="windows-server-2016"></a>Windows Server 2016

Ha nincs engedélyezve a Hyper-V szerepkör, nyissa meg a PowerShellt rendszergazdaként, futtassa a következő parancsot a Hyper-V engedélyezéséhez, majd indítsa újra a számítógépet. További információt a [Docker Enterprise Edition for Windows Server][download-docker-server] kiadást ismertető szakaszban talál.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Indítsa újra a gépet.

Nyissa meg a PowerShellt rendszergazdaként, majd futtassa a következő parancsokat a Docker telepítéséhez:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK és eszközök

Telepítse a Service Fabric Mesh-futtatókörnyezetet, az SDK-t és az eszközöket a következő sorrendben.

1. A [Service Fabric Mesh SDK][download-sdkmesh] telepítéséhez használjon webplatform-telepítőt. Ez a Microsoft Azure Service Fabric SDK-t és a futtatókörnyezetet is telepíti.
2. Telepítse a [Visual Studio Service Fabric Mesh Tools (előzetes verzió) bővítményt][download-tools] a Visual Studio piacteréről.

## <a name="build-a-cluster"></a>Fürt létrehozása

Ha a Visual Studiót használja, kihagyhatja ezt a szakaszt. Ha még nem rendelkezik helyi fürttel, a Visual Studio létre fog hozni egyet.

Service Fabric-alkalmazások létrehozásakor és futtatásakor a legjobb hibakeresési teljesítmény érdekében javasoljuk, hogy hozzon létre egy egycsomópontos helyi fejlesztői fürtöt. Ennek a fürtnek Service Fabric Mesh-projektek üzembe helyezésekor vagy hibakeresésekor mindig futnia kell.

Egy fürt létrehozása előtt a Dockernek futnia **kell**. A Docker futásának ellenőrzéséhez nyisson meg egy terminálablakot, majd a `docker ps` parancs futtatásával ellenőrizze, hogy történik-e hiba. Ha a válasz nem jelez hibát, akkor a Docker fut, és készen áll a fürt létrehozására.

A futtatókörnyezetet, az SDK-k és a Visual Studio-eszközök telepítése után hozzon létre egy fejlesztési fürtöt.

1. Zárja be a PowerShell-ablakot.
2. Nyisson meg egy új, emelt szintű PowerShell-ablakot rendszergazdaként. Ez a lépés a nemrégiben telepített Service Fabric-modulok betöltéséhez szükséges.
3. Futtassa az alábbi PowerShell-parancsot egy fejlesztési fürt létrehozásához:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. A helyi fürtkezelő eszköz elindításához futtassa a következő PowerShell-parancsot:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Készen áll a Service Fabric Mesh-alkalmazások létrehozására!

## <a name="next-steps"></a>További lépések

Olvassa át az [Azure Service Fabric-alkalmazás létrehozásáról](service-fabric-mesh-tutorial-create-dotnetcore.md) szóló szakaszt.

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/