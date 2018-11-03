---
title: Docker gazdagépek létrehozása az Azure-ban a Docker Machine |} A Microsoft Docs
description: Docker gazdagépek létrehozása az Azure-ban a Docker Machine használatát ismerteti.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 98b2d76f4b8ba9ba8316f3a33fd2ff5ed5899833
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969530"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Docker gazdagépek létrehozása az Azure-ban a docker-machine segítségével
Futó [Docker](https://www.docker.com/) tárolók egy gazdagépet a docker-démont futtató virtuális Gépre van szükség.
Ez a témakör ismerteti, hogyan használható a [docker-machine](https://docs.docker.com/machine/) paranccsal hozzon létre új Linux rendszerű virtuális gépek, a Docker-démont, az Azure-ban futó konfigurálva. 

**Megjegyzés:** 

* *Ez a cikk attól függ, a docker-machine verzió 0.9.0-rc2 vagy újabb*
* *Windows-tárolók támogatják a docker-machine segítségével a közeljövőben*

## <a name="create-vms-with-docker-machine"></a>Virtuális gép létrehozása a Docker Machine-vel
Az Azure-ban gazdagép virtuális gépek létrehozása a docker a `docker-machine create` parancsának használatával a `azure` illesztőprogramot. 

Az Azure-illesztőprogramot igényel az előfizetés-azonosítójára. Használhatja a [Azure CLI-vel](cli-install-nodejs.md) vagy a [az Azure Portal](https://portal.azure.com) beolvasni az Azure-előfizetésében. 

**Az Azure Portal használatával**

* Válassza ki **előfizetések** , a bal oldali navigációs oldal, másolja az előfizetés-azonosító.

**Az Azure CLI-vel**

* Típus ```azure account list``` , és másolja az előfizetés-azonosító.

Típus `docker-machine create --driver azure` , tekintse meg a beállításokat és alapértelmezett értékeik.
Emellett megtekintheti a [Docker Azure illesztőprogram dokumentáció](https://docs.docker.com/machine/drivers/azure/) további információ. 

Az alábbi példa megbízhatóak a [alapértelmezett értékek](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), de igény szerint állítsa be ezeket az értékeket: 

* Azure-DNS-ben a nevet a nyilvános IP-cím és a létrehozott tanúsítványokat. Ez az a virtuális gép DNS-nevét. A virtuális gép ezután biztonságosan állítsa le, a dinamikus IP-cím felszabadítása, és lehetővé teszi, hogy a virtuális gép újra új IP-Címmel rendelkező elindulása után újra. A név előtagja UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com régió egyedinek kell lennie.
* Nyissa meg a 80-as porton a kimenő internet-hozzáférést a virtuális gépen
* gyorsabb a premium storage használatához a virtuális gép mérete
* a virtuálisgép-lemez esetében használt prémium szintű storage

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Válassza ki a docker-gazdagép, a docker-machine
Miután egy bejegyzést a docker-machine a gazdagép, az alapértelmezett gazdagép beállíthatja a docker-parancsokat futtatni.

## <a name="using-powershell"></a>A PowerShell használata
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>A Bash használatával
```bash
eval $(docker-machine env MyDockerHost)
```

A megadott gazdagépre vonatkozóan most már futtathat docker-parancsokat

```
docker ps
docker info
```

## <a name="run-a-container"></a>Egy tároló futtatása
Egy gazdagépen konfigurált most már futtathat egy egyszerű webkiszolgálót annak megállapítására, hogy helyesen lett-e konfigurálva a gazdagépen.
Itt azt egy standard nginx rendszerképet használja, adja meg, hogy kell-e figyelni a 80-as porton és, hogy a gazdagép virtuális gép újraindul, ha a tárolót fog indítani, valamint (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

A kimenet a következőhöz a következőhöz hasonlóan kell kinéznie:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>A tároló tesztelése
Vizsgálja meg a futó tárolók használatával `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

És a futó tároló megtekintéséhez írja be a `docker-machine ip <VM name>` található az IP-címet adja meg a böngészőben:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Futó ngnix tároló](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Összegzés
A docker-machine egyszerűen létrehozhat az Azure-ban a docker-gazdagépek esetében az egyéni docker-gazdagép ellenőrzések.
Éles környezetben a tárolók, a üzemeltetési lásd a [Azure Container Service-ben](http://aka.ms/AzureContainerService)

A Visual Studio .NET Core-alkalmazások fejlesztése, lásd: [Docker-eszközök a Visual Studio](http://aka.ms/DockerToolsForVS)

