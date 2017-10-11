---
title: "Docker-gazdagépekből létrehozása az Azure-ban Docker gép |} Microsoft Docs"
description: "Docker gép docker-gazdagépekből létrehozása az Azure-ban való használatát ismerteti."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Docker gazdagépek létrehozása az Azure-ban a docker-machine segítségével
Futó [Docker](https://www.docker.com/) tárolók egy gazdagépet a docker démon futtató virtuális igényel.
Ez a témakör ismerteti, hogyan használható a [docker-gép](https://docs.docker.com/machine/) parancs futtatásával hozzon létre új Linux virtuális gépek a Docker démon, Azure-beli konfigurálva. 

**Megjegyzés:** 

* *Ez a cikk docker-gép verziójának 0.9.0-rc2, vagy nagyobb függ.*
* *Windows-tárolók keresztül támogatják docker-gép a közeljövőben*

## <a name="create-vms-with-docker-machine"></a>Hozzon létre virtuális gépek Docker gép
Létrehozott egy docker állomás virtuális gépet az Azure-ban a `docker-machine create` parancs használatával a `azure` illesztőprogram. 

Az Azure meghajtó csak az előfizetés-azonosító. Használhatja a [Azure CLI](cli-install-nodejs.md) vagy a [Azure Portal](https://portal.azure.com) beolvasása az Azure-előfizetéséhez. 

**Az Azure portál használatával**

* Válassza ki **előfizetések** a a bal oldali navigációs lapjáról, és másolja az előfizetés-azonosító.

**Az Azure CLI-vel**

* Típus ```azure account list``` , és másolja az előfizetés-azonosító.

Típus `docker-machine create --driver azure` , a beállításokat, és az alapértelmezett értékekre.
Azt is láthatja a [Docker Azure illesztőprogram dokumentáció](https://docs.docker.com/machine/drivers/azure/) vonatkozó információ. 

Az alábbi példa alapul a [alapértelmezett értékek](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), de igény szerint állítsa be ezeket az értékeket: 

* Azure-dns generált tanúsítványokat és a nyilvános IP-cím társított név. Ez az a virtuális gép DNS-nevét. A virtuális gép ezután biztonságosan állítsa le, az dinamikus IP-cím felszabadítása, és lehetővé teszik a virtuális gép újra kezdődik-e egy új IP-cím feldolgozása után. A név előtagja egyedinek kell lennie az adott régióban UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Nyissa meg a virtuális Gépet, a kimenő internet-hozzáférés 80-as port
* gyorsabb prémium szintű storage magukat, hogy a virtuális gép mérete
* prémium szintű storage, használja a virtuálisgép-lemez

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>A docker-gép docker állomás
Miután egy bejegyzést a docker-gépen ahhoz, hogy a gazdagép, az alapértelmezett állomás docker parancsok futtatásakor állíthatja be.

## <a name="using-powershell"></a>A PowerShell használata
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Bash használatával
```bash
eval $(docker-machine env MyDockerHost)
```

A megadott gazdagépcsoport alapján most futtathatók docker-parancsok

```
docker ps
docker info
```

## <a name="run-a-container"></a>Egy tároló futtatása
Egy konfigurált gazdagéppel most futtathatja egy egyszerű webkiszolgálót, és ellenőrizze, hogy helyesen lett-e konfigurálva a gazdagépen.
Itt azt a szabványos nginx-lemezkép használatához adja meg, hogy kell-e figyelni 80-as porton, és, hogy ha a gazdagép virtuális gép újraindul, a tároló fog újraindul, valamint (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

A kimeneti hasonlóan kell kinéznie a következő:

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

És a futó tároló parancsot kell beírnia `docker-machine ip <VM name>` az IP-címet adja meg a böngészőben kereséséhez:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Futó ngnix tároló](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Összefoglalás
Docker-számítógéppel könnyen megadhat docker-gazdagépekből az Azure-ban a az egyes docker állomás érvényesítést.
Éles üzemeltető tároló, tekintse meg a [Azure Tárolószolgáltatásban](http://aka.ms/AzureContainerService)

A Visual Studio .NET Core alkalmazások fejlesztéséhez, lásd: [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS)

