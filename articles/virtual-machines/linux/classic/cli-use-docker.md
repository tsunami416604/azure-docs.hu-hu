---
title: "A Docker Virtuálisgép-bővítmény használatával Linux Azure rendszer számára"
description: "Ismerteti a Docker és az Azure virtuálisgép-bővítmények és mutatja be, amelyek az Azure parancssori felület használatával a parancssorból docker-gazdagépekből Azure virtuális gépek programozott módon létrehozásához."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: b276911ecbbf161cb6068c1af7a035850035b98d
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>A Docker VM-bővítmény használata az Azure parancssori felületével (Azure CLI)
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A Resource Manager modellt a Docker Virtuálisgép-bővítmény használatával kapcsolatos információkért lásd: [Itt](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ez a témakör ismerteti a Docker Virtuálisgép-bővítmény módról a kezelési (asm) az Azure CLI bármilyen platformon a virtuális gép létrehozása. [Docker](https://www.docker.com/) a legnépszerűbb virtualizálási módszerekkel által használt egyik [Linux tárolók](http://en.wikipedia.org/wiki/LXC) ahelyett, hogy a virtuális gépek adatforgalom elkülönítése, és a megosztott erőforrások számítástechnikai módja. Használhatja a Docker Virtuálisgép-bővítmény és a [Azure Linux ügynök](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) egy Docker tároló korlátlan számú tárolót tárolhat, az alkalmazások az Azure virtuális gép létrehozásához. Egy magas szintű leírását a tárolók és azok előnyeit, olvassa el a [Docker magas szintű Faliújság](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>A Docker Virtuálisgép-bővítmény használata az Azure-ral
Az Azure-ral a Docker Virtuálisgép-bővítmény használatához telepítenie kell egy verziója a [Azure parancssori felület](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) nagyobb, mint 0.8.6 (a írásának a jelenlegi verzió: 0.10.0-s). Telepítheti az Azure parancssori felület Mac, Linux és Windows rendszeren.

A teljes folyamat Docker használatához Azure felettébb egyszerű:

* Az Azure CLI és annak függőségeit telepítse a számítógépre, amelyről szeretne szabályozni Azure (Windows, ez lesz egy Linux-disztribúció virtuális gépként fut)
* Az Azure CLI Docker-parancsok segítségével hozzon létre egy virtuális gép Docker-állomás az Azure-ban
* A helyi Docker-parancsok segítségével kezelheti a Docker a tárolók a Docker virtuális Gépet az Azure-ban.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Telepítse az Azure parancssori felület (Azure CLI)
Telepítése és konfigurálása az Azure parancssori felület [az Azure parancssori felület telepítése](../../../cli-install-nodejs.md). A telepítés, írja be a `azure` parancsot a parancssorba, és megtekintheti az az Azure CLI ASCII ábrákat rövid rövid időn belül, amely a rendelkezésre álló alapvető parancsokat tartalmazza. Ha a telepítés megfelelően működött, kell beírni `azure help vm` és, hogy van-e a listában szereplő parancsok egyikét "docker".

> [!NOTE]
> Docker vannak a Windows-eszközök [Docker gép](https://docs.docker.com/installation/windows/), amely is segítségével automatizálhatja a docker-ügyfél, amelyekkel dolgozni az Azure virtuális gépeken, mint a docker-gazdagépekből.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Az Azure CLI csatlakozni az Azure-fiókjával
Az Azure parancssori felület használata előtt össze kell kapcsolnia a Azure-fiók hitelesítő adatait az Azure parancssori felület a platformon. A szakasz [csatlakoztatása az Azure-előfizetéshez](/cli/azure/authenticate-azure-cli) ismerteti, hogyan töltse le, és importálja a **.publishsettings** fájlt, vagy rendelje hozzá az Azure CLI szervezeti azonosítóval.

> [!NOTE]
> Néhány különbségek vannak viselkedés egyik vagy a hitelesítés, olvassa el a dokumentum fenti tudni, hogy a különböző funkcióit, a többi módszer használata esetén.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Docker telepítéséhez és a Docker Virtuálisgép-bővítmény használatához az Azure-bA
Kövesse a [Docker telepítési utasításokat](https://docs.docker.com/installation/#installation) Docker helyileg telepíteni a számítógépre.

Az Azure virtuális gép a Docker használatához rendelkeznie kell a virtuális gép használt Linux kép a [Azure Linux Virtuálisgép-ügynök](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) telepítve. Jelenleg nincsenek lemezképekről Ez csak két típusa:

* Az Azure kép katalógusából Ubuntu kép vagy
* A egyéni létrehozott Azure Linux ügynök a Linux-lemezkép telepítve és konfigurálva. Lásd: [Azure Linux Virtuálisgép-ügynök](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hogyan hozhat létre egyéni Linux virtuális gép és az Azure Virtuálisgép-ügynök további információt.

### <a name="using-the-azure-image-gallery"></a>Az Azure-lemezkép katalógus használata
Bash vagy terminál-munkamenetet a következő paranccsal Azure CLI keresse meg a legutóbbi Ubuntu képet használatához írja be a virtuális gép gyűjteménye

`azure vm image list | grep Ubuntu-14_04`

és válassza ki, a lemezkép nevét, például a `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`, és a lemezkép egy új virtuális gép létrehozása az alábbi parancs segítségével.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Ahol:

* *&lt;VM-cloudservice neve&gt;*  a virtuális gép lesz az Azure-ban a Docker tároló gazdaszámítógép neve
* *&lt;felhasználónév&gt;*  a virtuális gép alapértelmezett legfelső szintű felhasználó a felhasználónév
* *&lt;jelszó&gt;*  jelszava a *felhasználónév* , amely eleget tesz összetettségi az Azure-fiók

> [!NOTE]
> Jelenleg a jelszót kell legalább 8 karakterből álljon, egy kisbetű, és egy nagybetű, szám és egy speciális karaktert, például a következő karakterek egyikét tartalmazza: `!@#$%^&+=`. Nem, az előző mondatban végén időszak nincs egy speciális karaktert.
> 
> 

Ha a parancs sikeres volt, kell megjelennie, attól függően, hogy a pontos argumentumok és a használt beállításokat a következőhöz:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> A virtuális gépek létrehozására is igénybe vehet néhány percet, de után van kiépítve (állapot értéke `ReadyRole`) a Docker démon (a Docker-szolgáltatás) kezdődik, és csatlakozhat a Docker tároló-gazdagépen.
> 
> 

Írja be a Docker virtuális Gépet az Azure-ban létrehozott teszteléséhez

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

Ha  *&lt;vm-neve--használt&gt;*  neve, a virtuális gép a küldendő hívás használt `azure vm docker create`. Meg kell jelennie a következő, az azt jelzi, hogy a Docker állomás virtuális gép mentése és Azure-ban futó és a parancsok Várakozás hasonló. 

Most megpróbálhatja csatlakozni tájékoztatást kaphat a docker-ügyfél segítségével (az egyes Docker ügyfél beállítások, például a Mac, előfordulhat, hogy használandó `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Ugyanúgy kell lennie, hogy szerepel-e az összes működő, tekintse meg a virtuális Gépet a Docker-bővítmény:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker gazdagép VM hitelesítés
A Docker virtuális gép létrehozása mellett a `azure vm docker create` parancs is automatikusan engedélyezi a Docker ügyfélszámítógép, az Azure-tárolót állomás HTTPS-kapcsolaton keresztül csatlakozni a szükséges tanúsítványokat hoz létre, és a tanúsítványokat tárolja az ügyfél és a gazdagép gépeken, szükség szerint. Az ezt követő kísérletek a meglévő tanúsítványok fel újra és az új állomás megosztva.

Alapértelmezés szerint tanúsítványok kerülnek `~/.docker`, és a Docker fogja állítani a porton **2376**. Ha szeretné használni egy másik portot vagy a könyvtár, akkor előfordulhat, hogy használja a következő `azure vm docker create` parancssori kapcsolók konfigurálása a Docker-tároló egy másik portot vagy különböző tanúsítványok csatlakozó ügyfeleken használandó virtuális gép üzemeltetésére:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

A Docker démon a gazdagép be van állítva a figyelésére és által generált tanúsítványokat használ a megadott porton ügyfélkapcsolatok hitelesítése a `azure vm docker create` parancsot. Az ügyfél gépnek rendelkeznie kell ezeket a tanúsítványokat a Docker gazdagép eléréséhez.

> [!NOTE]
> Hálózati futtató gazdagépet, amelyen ezek a tanúsítványok nélkül lesz bárki, amelyek a számítógéphez való kapcsolódáshoz. Az alapértelmezett konfiguráció módosítása előtt győződjön meg arról, hogy tudomásul veszi a kockázatok a számítógép és az alkalmazások.
> 
> 

## <a name="next-steps"></a>Következő lépések
* Készen áll a [Docker felhasználói útmutató] és a Docker virtuális gép használja. Hozzon létre egy Docker-képes virtuális Gépet az új portálon, lásd: [a Docker Virtuálisgép-bővítmény használata a portál].
* Az Azure Docker Virtuálisgép-bővítmény is támogatja a Docker Compose, használja a deklaratív YAM-fájl egy fejlesztői modellezve alkalmazás teljes bármely környezet és egy egységes központi telepítés létrehozása. Lásd: [és Docker Compose határozza meg, és futtassa a több tároló alkalmazást egy Azure virtuális gépen a kezdéshez].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[a Docker Virtuálisgép-bővítmény használata a portál]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker felhasználói útmutató]:https://docs.docker.com/userguide/

[és Docker Compose határozza meg, és futtassa a több tároló alkalmazást egy Azure virtuális gépen a kezdéshez]:../docker-compose-quickstart.md
