---
title: Windows fejlesztési környezet kialakítása Service Fabric Mesh-alkalmazások létrehozásához | Microsoft Docs
description: Kialakíthatja úgy a Windows fejlesztési környezetet, hogy Service Fabric Mesh-alkalmazást hozhasson létre, és üzembe helyezhesse azt az Azure Service Fabric Mesh-ben.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 12/12/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 3493b94e93a03faaf60aaa64bf8b80cefb754657
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309059"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>A Windows fejlesztési környezet kialakítása Service Fabric Mesh-alkalmazások létrehozásához

Alkalmazások létrehozását és futtatását az Azure Service Fabric-háló a Windows fejlesztői gépen, lesz szüksége:

* Docker
* Visual Studio 2017
* Service Fabric-háló modul
* Service Fabric-háló SDK és eszközök.

És a következő Windows-verziók valamelyikét:

* A Windows 10 1709-es (Fall Creators update) vagy 1803 (Enterprise, Professional vagy oktatási) verziója (Windows 2018 április 10-frissítés)
* A Windows Server 1709-es verzió
* A Windows Server verzió 1803-as verzióban

Az alábbi utasítások lesz mindaz telepített súgó futtat Windows verziója alapján.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

A Service Fabric Mesh-alkalmazások üzembe helyezéséhez a Visual Studio 2017 szükséges. [Telepítse a 15.6.0-s][download-visual-studio] vagy újabb verziót, és engedélyezze a következő számítási feladatokat:

* ASP.NET és webfejlesztés
* Azure-fejlesztés

## <a name="install-docker"></a>A Docker telepítése

Ha már rendelkezik a Docker telepítve van, győződjön meg arról, hogy rendelkezik-e a legújabb verzióra. Docker a kérni, ha új verzió ki, de manuálisan ellenőrizni kell, hogy rendelkezik-e a legújabb verzióra.

#### <a name="install-docker-on-windows-10"></a>A Docker telepítése Windows 10 rendszeren

Töltse le, majd telepítse a [Docker Community Edition for Windows][download-docker] legújabb verzióját a Service Fabric Mesh által használt tárolóalapú Service Fabric-alkalmazások támogatásához.

Amikor a telepítés során a rendszer kéri, válassza a **Windows-tárolók használatát Linux-tárolók helyett**.

Ha a Hyper-V nincs engedélyezve a számítógépen, a Docker telepítője fel fogja ajánlani az engedélyezését. Ehhez kattintson az **OK** gombra, ha a rendszer arra kéri.

#### <a name="install-docker-on-windows-server-2016"></a>Telepítheti a Dockert a Windows Server 2016 rendszeren

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

> [!IMPORTANT]
> Egy fürt létrehozása előtt a Dockernek futnia **kell**.
> A Docker futásának ellenőrzéséhez nyisson meg egy terminálablakot, majd a `docker ps` parancs futtatásával ellenőrizze, hogy történik-e hiba. Ha a válasz nem jelez hibát, akkor a Docker fut, és készen áll a fürt létrehozására.

> [!Note]
> Ha esetében a Windows Fall Creators update (1709-es verzió) gép, csak használhat Windows 1709-es docker a docker-rendszerképek.
> Ha a Windows rendszeren fejleszt 2018 április 10. (verzió 1803) gép, vagy Windows 1709-es vagy 1803 verziójú docker-rendszerképeket is használhatja.

Ha a Visual Studiót használja, kihagyhatja ezt a szakaszt. Ha még nem rendelkezik helyi fürttel, a Visual Studio létre fog hozni egyet.

A legjobb teljesítmény hibakeresés létrehozása és futtatása a önálló Service Fabric-alkalmazáshoz egyszerre hozzon létre egy csomópontos helyi fejlesztési fürtöt. Ha egyszerre több alkalmazást futtatja, hozzon létre egy öt csomópontot számláló helyi fejlesztési fürtöt. A fürt üzembe helyezése, vagy a Service Fabric-háló projekt hibakeresési kell futnia.

Miután telepítette a futtatókörnyezet, az SDK-k, a Visual Studio tools, Docker, és Docker rendszert, hozzon létre egy fejlesztési fürtöt.

1. Zárja be a PowerShell-ablakot.
2. Nyisson meg egy új, emelt szintű PowerShell-ablakot rendszergazdaként. Ez a lépés a nemrégiben telepített Service Fabric-modulok betöltéséhez szükséges.
3. Futtassa az alábbi PowerShell-parancsot egy fejlesztési fürt létrehozásához:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. A helyi fürtkezelő eszköz elindításához futtassa a következő PowerShell-parancsot:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Ha a szolgáltatás cluster manager eszköz fut (megjelenik a tálcán), kattintson a jobb gombbal, és kattintson a **helyi fürt elindításához**.

![1. ábra – a helyi fürt indítása](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Készen áll a Service Fabric Mesh-alkalmazások létrehozására!

## <a name="next-steps"></a>További lépések

Olvassa át az [Azure Service Fabric-alkalmazás létrehozásáról](service-fabric-mesh-tutorial-create-dotnetcore.md) szóló szakaszt.

Választ találhat [gyakori kérdések és ismert problémák](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/