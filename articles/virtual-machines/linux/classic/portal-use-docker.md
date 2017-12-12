---
title: "Linux Docker Virtuálisgép-bővítmény használatával |} Microsoft Docs"
description: "Docker és az Azure virtuálisgép-bővítmények és létrehozása az Azure virtuális gépek, amelyek az Azure parancssori felület használatával klasszikus üzembe helyezési modellel docker-gazdagépekből ismerteti."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: f5d6bb066f9cee0d072ddb3129867cefefe3e66f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-docker-vm-extension-with-the-azure-portal"></a>A Docker Virtuálisgép-bővítmény használata az Azure-portálon
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[Docker](https://www.docker.com/) a legnépszerűbb virtualizálási módszerekkel által használt egyik [Linux tárolók](http://en.wikipedia.org/wiki/LXC) ahelyett, hogy a virtuális gépek adatforgalom elkülönítése, és a megosztott erőforrások számítástechnikai módja. Használhatja a Docker Virtuálisgép-bővítmény kezeli [Azure Linux ügynök] egy Docker tároló korlátlan számú tárolót tárolhat, az alkalmazások az Azure virtuális gép létrehozásához.

> [!NOTE]
> Ez a témakör ismerteti a Docker virtuális gép létrehozása az Azure-portálról. A Docker virtuális gép létrehozása a parancssorból, olvassa el [a Docker Virtuálisgép-bővítmény az Azure parancssori felület (CLI) használatával]. Egy magas szintű leírását a tárolók és azok előnyeit, olvassa el a [Docker magas szintű Faliújság](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Hozzon létre egy új virtuális Gépet a lemezkép-galériából
Az első lépés egy Azure virtuális Gépen, amely támogatja a Docker Virtuálisgép-bővítmény, az Ubuntu 14.04 LTS lemezkép használata a lemezkép-galériából egy példa kiszolgálói lemezképet és az Ubuntu 14.04 asztali ügyfélként a Linux-lemezkép szükséges. Kattintson a portál **+ új** hozzon létre új Virtuálisgép-példányt, és jelöljön ki egy Ubuntu 14.04 LTS képet a rendelkezésre álló lehetőségek közül, vagy a teljes kép gyűjteményből alább látható módon.

> [!NOTE]
> Jelenleg csak Ubuntu 14.04 LTS lemezképek újabb a 2014. július támogatják a Docker Virtuálisgép-bővítmény.
> 
> 

![Hozzon létre egy új Ubuntu kép](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Docker tanúsítványok létrehozása
A virtuális gép létrehozása után győződjön meg arról, hogy Docker telepítve van-e az ügyfélszámítógépen. (További információkért lásd: [Docker telepítési utasításokat](https://docs.docker.com/installation/#installation).)

A következők szerint Docker kommunikációs tanúsítványt és kulcsot-fájlok létrehozása [Docker fut, a https] és helyezze el őket a a  **`~/.docker`**  könyvtárhoz, az ügyfélszámítógépen.

> [!NOTE]
> A Docker Virtuálisgép-bővítmény a portál jelenleg szükséges hitelesítő adatokat, amelyek base64-kódolású.
> 
> 

A parancssorból használható  **`base64`**  vagy egy másik kedvenc kódolási base64-kódolású témakörök létrehozásához. Ezzel a tanúsítvány és kulcs fájlok egyszerű számú hasonló lehet:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>A Docker Virtuálisgép-bővítmény hozzáadása
A Docker Virtuálisgép-bővítmény hozzáadásához keresse meg a létrehozott Virtuálisgép-példány, és görgessen le a **bővítmények** , és kattintson rá elindítani a Virtuálisgép-bővítmények, a lent látható módon.

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Egy bővítmény hozzáadása
Kattintson a **+ Hozzáadás** adhat hozzá a virtuális gép lehetséges Virtuálisgép-bővítmények megjelenítéséhez.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Válassza ki a Docker Virtuálisgép-bővítmény
Válassza ki a Docker Virtuálisgép-bővítmény, amely megjelenik a Docker leírása és a fontos hivatkozásokat, és kattintson a **létrehozása** a lap alján a telepítési folyamat megkezdéséhez.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>A tanúsítvány és kulcs fájlok hozzáadása:
A mezőket adja meg a Hitelesítésszolgáltatói tanúsítvány, a kiszolgálói tanúsítvány és a kiszolgáló kulcs base64-kódolású verziói az alábbi ábrán látható módon.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Vegye figyelembe, hogy (ahogy a fenti kép) a 2376 ki van töltve alapértelmezés szerint. Azoknak a végpontoknak Itt adhat meg, de a következő lépéssel kattintva nyissa meg a megfelelő végpont. Ha megváltoztatja az alapértelmezett, mindenképpen nyissa meg a megfelelő végpont a következő lépésben.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>A Docker kommunikációs végpont hozzáadása
Ha jeleníti meg a korábban létrehozott erőforráscsoportot, válassza ki a virtuális Géphez társított hálózati biztonsági csoportot, majd kattintson **bejövő biztonsági szabályok** a szabályok megtekintése, ahogy az itt látható.

![](media/portal-use-docker/AddingEndpoint.png)

Kattintson a **+ Hozzáadás** egy másik szabály hozzáadásához, és az alapértelmezett esetben adja meg a végpont nevét (ebben a példában **Docker**), és 2376 "Célporttartomány". Állítsa be a protokoll értéket megjelenítő **TCP**, és kattintson a **OK** a szabály létrehozásához.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>A Docker-ügyfél és az Azure Docker állomás tesztelése
Keresse meg és másolja a virtuális gép tartomány nevét, típusát az ügyfélszámítógépen parancssori `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (ahol *dockerextension* az altartomány cseréli le a virtuális gép).

Az eredmény ehhez hasonlóan kell megjelennie:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Ha befejezte a fenti lépéseket, most már rendelkezik teljes körű működéséhez Docker futtató gazdagépet, amelyen egy Azure virtuális gépen, konfigurálni kell csatlakoztatni távolról azon egyéb ügyfelekről.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
Készen áll a [Docker felhasználói útmutató] és a Docker virtuális gép használja. Ha automatizálni szeretné a parancssori felületen keresztül létrehozása Docker-gazdagépekből Azure virtuális gépeken, lásd: [a Docker Virtuálisgép-bővítmény az Azure parancssori felület (CLI) használatával]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[a Docker Virtuálisgép-bővítmény az Azure parancssori felület (CLI) használatával]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux ügynök]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Docker fut, a https]:http://docs.docker.com/articles/https/
[Docker felhasználói útmutató]:https://docs.docker.com/userguide/
