---
title: Linux RDMA-fürt beállítása MPI-alkalmazások futtatására |} A Microsoft Docs
description: Méret H16r, H16mr, a8-as vagy a9-es virtuális gépek futtatásához az MPI-alkalmazások az Azure RDMA hálózati használata Linux-fürt létrehozása
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/11/2018
ms.author: danlep
ms.openlocfilehash: 471fd4095fe45e76f94df8c61a07eeb9bbc1c120
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990727"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Linuxos RDMA-fürt beállítása MPI-alkalmazások futtatására

Ismerje meg, hogyan állítható be az Azure-ban a Linux RDMA-fürt [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) párhuzamos Message Passing Interface (MPI) alkalmazások futtatásához. Ez a cikk lépéseit egy Linux-alapú HPC rendszerkép futtatása az Intel MPI-fürtön való előkészítéséhez. Előkészítés, miután a lemezkép és az egyik virtuális gépek-fürt üzembe a [RDMA-kompatibilis Azure Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances). A fürt használatával, amely a távoli közvetlen memória-hozzáféréses (RDMA) technológiát alapján alacsony késésű, nagy átviteli sebességű hálózati hatékonyan kommunikációhoz MPI-alkalmazások futtatására.

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="cluster-deployment-options"></a>Fürt üzembe helyezési beállítások
Az alábbiakban különböző módszerek Linux RDMA-fürt létrehozása vagy Feladatütemezőt nélkül használhatja.

* **Az Azure CLI-szkriptek**: a cikk későbbi részében látható, ahogy a [Azure parancssori felület](../../../cli-install-nodejs.md) (CLI) használatával az RDMA-kompatibilis virtuális gépek az üzembe helyezési parancsfájlt. Szolgáltatásfelügyelet módban a parancssori felület a fürtcsomópontok tárolókonfigurációhoz hoz létre a klasszikus üzemi modellben, így számos számítási csomópontok üzembe helyezése eltarthat néhány percig. Az RDMA hálózati kapcsolat engedélyezéséhez a klasszikus üzemi modell használatakor a ugyanazon a felhőszolgáltatáson a virtuális gépek üzembe helyezése.
* **Az Azure Resource Manager-sablonok**: a Resource Manager üzemi modell üzembe helyezéséhez, amely csatlakozik az RDMA hálózati RDMA-kompatibilis virtuális gépek fürtjeit is használhatja. Is [saját sablon létrehozása](../../../resource-group-authoring-templates.md), vagy ellenőrizze a [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) a kívánt megoldás üzembe helyezéséhez a Microsoft vagy a Közösség által biztosított sablonok. Resource Manager-sablonok a gyors és megbízható módot Linux-fürt üzembe helyezése is biztosítanak. A Resource Manager üzemi modell használatakor az RDMA hálózati kapcsolat engedélyezéséhez a virtuális gépek ugyanazon rendelkezésre állási vagy virtuálisgép-méretezési csoport üzembe.
* **HPC Pack**:-Microsoft HPC Pack-fürt létrehozása az Azure-ban, és adja hozzá az RDMA-kompatibilis számítási csomópontok, amely az RDMA hálózati hozzáféréshez támogatott Linux-disztribúció. További információkért lásd: [Linuxos számítási csomópontok HPC Pack-fürtökben, az Azure-ban – első lépések](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Minta üzembe helyezési lépéseket a klasszikus modellben
A következő lépések bemutatják, hogyan SUSE Linux Enterprise Server (SLES) 12 HPC virtuális gép üzembe helyezése az Azure Marketplace-ről, weblapokkal és egyéni Virtuálisgép-rendszerképek létrehozása az Azure CLI használatával. Ezután a rendszerképét is használhatják az RDMA-kompatibilis virtuális gépek az üzembe helyezési szkript.

> [!TIP]
> Hasonló lépéseket követve az Azure piactéren a CentOS-alapú HPC rendszerképen alapuló RDMA-kompatibilis virtuális gépek üzembe helyezése. Néhány lépésben feljegyzett némileg eltérőek.
>

### <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép**: Azure kommunikálni Mac, Linux vagy Windows ügyfél számítógépre van szüksége. A lépések feltételezik, hogy egy Linux-ügyfél használ.
* **Azure-előfizetés**: Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) mindössze néhány perc alatt. A nagyobb fürtök esetén fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket.
* **Virtuális gép mérete rendelkezésre állása**: Ellenőrizze [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/) H-sorozat vagy egyéb RDMA-kompatibilis Virtuálisgép-méretek az Azure-régiók rendelkezésre állását.
* **Magkvóta**: növelje a magok nagy számítási igényű virtuális gépek üzembe helyezéséhez szükség lehet. Például legalább 128 maggal kell, ha azt szeretné, 8 a9-es virtuális gépek üzembe helyezéséhez, ahogyan az ebben a cikkben. Az előfizetés is korlátozhatja az egyes virtuális gép többek között a H-sorozat családokban üzembe helyezhető magok számát. Kérje egy kvótájának növelését, [nyisson meg egy támogatási kérést online](../../../azure-supportability/how-to-create-azure-support-request.md) díjmentesen.
* **Az Azure CLI**: [telepítése](../../../cli-install-nodejs.md) az Azure CLI és [csatlakozhat az Azure-előfizetés](/cli/azure/authenticate-azure-cli) az ügyfélszámítógépről.

Emellett tekintse át a központi telepítési szempontokról a [RDMA-kompatibilis Azure Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances).

### <a name="provision-an-sles-12-hpc-vm"></a>Az SLES 12 HPC virtuális gép
Miután bejelentkezett az Azure-bA az Azure CLI-vel, futtassa `azure config list` annak ellenőrzéséhez, hogy a kimenet megjeleníti a szolgáltatásfelügyelet módban. Ha nem jelenik meg, állítsa a módot a következő parancs futtatásával:

    azure config mode asm


Írja be a következő használatára jogosult előfizetések listáját:

    azure account list

Az aktuális aktív előfizetést, amelynél az `Current` beállítása `true`. Ha ez az előfizetés nem az a fürt létrehozásához használni kívánt, az aktív előfizetést as állítsa be a megfelelő előfizetés-azonosító:

    azure account set <subscription-Id>

Tekintheti meg a nyilvánosan elérhető SLES 12 HPC-rendszerképeket az Azure-ban, az alábbihoz hasonló parancs futtatására, feltéve, hogy a rendszerhéj-környezetet támogatja **grep**:

    azure vm image list | grep "suse.*hpc"

Virtuális gép egy RDMA-kompatibilis SLES 12 SP3 HPC-lemezképpel az alábbihoz hasonló parancs futtatásával:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-v20170913

Az elemek magyarázata:

* A méret (ebben a példában A9) az RDMA-kompatibilis Virtuálisgép-méretek egyike.
* A külső SSH-port száma (22-es ebben a példában az SSH alapértelmezés szerinti megoldás) bármilyen érvényes portszámot. A belső SSH-portszám 22-es értékre van állítva.
* A hely által meghatározott Azure-régióban jön létre egy új felhőszolgáltatást. Adjon meg egy helyet, ahol a választott Virtuálisgép-méret érhető el, például "West US".
* A SUSE elsőbbségi támogatás (amely keletkeznek többletköltségek) a SLES 12 lemezképnév jelenleg is lehet a lehetőségek közül, hogy rendelkezik `priority` a nevében, például: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-priority-v20170913`

### <a name="customize-the-vm"></a>A virtuális gép testreszabása
Miután a virtuális gép befejezte a kiépítést, a virtuális gép külső IP-cím (vagy DNS-név) használatával a virtuális gép ssh-KAPCSOLATOT, és a külső port számát konfigurált, és testre szabni. Kapcsolat részletek: [bejelentkezés egy Linux rendszerű virtuális gép](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Hajtsa végre a parancsokat a felhasználó a virtuális gépen konfigurált, kivéve, ha egy lépés végrehajtásához szükséges rendszergazdai hozzáférés.

> [!IMPORTANT]
> A Microsoft Azure nem biztosít a Linux rendszerű virtuális gépek rendszergazdai hozzáférés. Rendszergazdai hozzáférést, ha a felhasználót, hogy a virtuális Géphez csatlakoztatott futtassa a parancsokat `sudo`.
>
>

* **Frissítések**: frissítések telepítése a zypper használatával. Érdemes azt is, NFS segédprogramok telepítése.

  > [!IMPORTANT]
  > A SLES 12 HPC virtuális gép azt javasoljuk, hogy a kernel-frissítéseket, amelyek akkor okozhat problémát és a Linux RDMA-illesztőprogramok nem alkalmazza.
  >
  >
* **Az Intel MPI**: a virtuális gép a Intel MPI telepítésének befejezéséhez az alábbi parancs futtatásával:

    ```bash
    sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
    ```

* **Memóriát zárolni**: az MPI-kódok zárolni a rendelkezésre álló memória az rdma-t, adja hozzá, vagy módosítsa a /etc/security/limits.conf fájlban a következő beállításokat. Ez a fájl szerkesztése a legfelső szintű hozzáférésre van szüksége.

    ```bash
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Tesztelési célokra is beállíthat memlock korlátlan. Például: `* hard memlock unlimited`. További információkért lásd: [beállítás ismert legjobb módszerei zárolt memória mérete](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **SLES virtuális gépek SSH-kulcsok**: hozzon létre SSH-kulcsok a felhasználói fiókjához a számítási csomópontok közötti megbízhatósági kapcsolat létrehozása a a SLES-fürt MPI-feladatok futtatásakor. Ha telepítette a CentOS-alapú HPC virtuális gép, ne kövesse ezt a lépést. Tekintse meg az utasításokat a cikk későbbi részében az SSH-beállításának megbízhatósági kapcsolat a fürt csomópontjai között a lemezképet, és a fürt üzembe helyezése után.

  Az SSH-kulcsok létrehozásához futtassa a `ssh-keygen` parancsot. Amikor a bemeneti kéri, válassza ki a **Enter** a kulcsok létrehozásához az alapértelmezett hely a jelszó beállítása nélkül.

  A nyilvános kulcsot a fájlhoz hozzáfűzni kívánt authorized_keys ismert nyilvános kulcsok.

  ```bash
  cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  ```

  A ~/.ssh címtárban, szerkeszteni vagy létrehozni a `config` fájlt. Az IP-címtartományt a magánhálózati, amely az Azure-ban (ebben a példában 10.32.0.0/16) használatát tervezi, adja meg:

  ```bash
  host 10.32.0.*
  StrictHostKeyChecking no
  ```

  Másik lehetőségként a következőképpen listázhatja a fürt minden virtuális gép magánhálózati IP-címe:

  ```bash
  host 10.32.0.1
  StrictHostKeyChecking no
  host 10.32.0.2
  StrictHostKeyChecking no
  host 10.32.0.3
  StrictHostKeyChecking no
  ...
  ```

  > [!NOTE]
  > Konfigurálás `StrictHostKeyChecking no` hozhat létre biztonsági kockázatot jelenthetnek, ha egy adott IP-cím vagy tartomány nincs megadva.
  >
  >
* **Alkalmazások**: olyan alkalmazásokat van szüksége, vagy más testreszabás végrehajtása előtt rögzíti a lemezképet.

### <a name="capture-the-image"></a>A lemezkép rögzítése
A lemezkép rögzítése, először futtassa a következő parancsot a Linux rendszerű virtuális gépen. Ez a parancs a virtuális gép deprovisions, de kezeli a felhasználói fiókok és az Ön által beállított SSH-kulcsokat.

```bash
sudo waagent -deprovision
```

Az ügyfélszámítógépen futtassa a következő Azure CLI-parancsok lemezképének rögzítéséhez. További információkért lásd: [képként klasszikus Linuxos virtuális gép rögzítése](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Ezek a parancsok futtatása után a virtuális gép lemezképének rögzítése a használatra, és a virtuális gép törlődik. Most már készen áll a fürt üzembe helyezése az egyéni rendszerkép.

### <a name="deploy-a-cluster-with-the-image"></a>A lemezkép-fürt üzembe helyezése
Módosítsa az alábbi Bash-szkript a környezetének megfelelő értékekkel, és futtassa az ügyfélszámítógépről. Azure üzembe helyezte a virtuális gépek a klasszikus üzemi modellben tárolókonfigurációhoz, mert ez a szkript a javasolt nyolc a9-es virtuális gépek üzembe helyezése néhány percet vesz igénybe.

```bash
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a classic VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>;
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>CentOS HPC-fürt szempontjai
Ha a fürt létrehozása HPC SLES 12 helyett az Azure piactéren a CentOS-alapú HPC-rendszerképeket alapján szeretné, kövesse az előző szakaszban általános lépéseket. Amikor először hozzon létre és konfigurálhatja a virtuális Gépet, vegye figyelembe a következő eltérésekkel:

- Az Intel MPI már telepítve van a CentOS-alapú HPC-lemezképből üzembe helyezett virtuális gépen.
- Zárolási memóriabeállítások már jelentek meg a virtuális gép /etc/security/limits.conf fájlt.
- SSH-kulcsokat a virtuális gép üzembe helyezésekor a rögzítés nem hoznak létre. Ehelyett azt javasoljuk, felhasználó-alapú hitelesítés beállítása a fürt üzembe helyezése után. További információkért tekintse meg a következő szakaszt.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>A fürt beállításának SSH bizalmi kapcsolat beállítása
A CentOS-alapú HPC-fürt, a számítási csomópontok közötti megbízhatósági kapcsolat létrehozása két módszer van: gazdagép-alapú hitelesítés és a felhasználó-alapú hitelesítés. Gazdagép-alapú hitelesítés ebben a cikkben hatókörén kívül esik, és általában keresztül kell elvégezni egy bővítmény parancsfájl központi telepítése során. Felhasználó-alapú hitelesítés egy kényelmes megoldás az üzembe helyezés után a megbízhatósági kapcsolat létrehozása, és szükség van a létrehozása és a fürt SSH-kulcsokat a számítási csomópontok közötti megosztását. Ezt a módszert gyakran nevezik beállításának SSH-bejelentkezéskor, és akkor szükséges, ha az MPI-feladatok.

Egy mintaszkriptet `user_authentication.sh` a CentOS-alapú HPC a felhasználó-alapú hitelesítés engedélyezése a következő fürtön:

```bash
#!/bin/bash
# For CentOS user must first install epel-release, sshpass, and nmap (sshpass and nmap are available from epel-release for CentOS)

# usage ./user_authentication.sh [username] [password] [internalIP prefix]
# ./user_authentication.sh azureuser Azure@123 10.32.0
USER=$1
PASS=$2
IPPRE=$3
HEADNODE=`hostname`

mkdir -p .ssh
echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''

echo 'Host *' >> .ssh/config
echo 'StrictHostKeyChecking no' >> .ssh/config
chmod 400 .ssh/config
chown azureuser:azureuser /home/azureuser/.ssh/config

nmap -sn $IPPRE.* | grep $IPPRE. | awk '{print $5}' > nodeips.txt
for NAME in `cat nodeips.txt`; do sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'hostname' >> nodenames.txt;done

NAMES=`cat nodenames.txt` #names from names.txt file
for NAME in $NAMES; do
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 /home/$USER/nodenames.txt $USER@$NAME:/home/$USER/
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "mkdir .ssh && chmod 700 .ssh"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'touch /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo "Host *" >  /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo StrictHostKeyChecking no >> /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 400 /home/'$USER'/.ssh/config'
    cat .ssh/id_rsa.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'cat >> .ssh/authorized_keys'
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 $USER@$NAME:/home/$USER/.ssh/id_rsa.pub .ssh/sub_node.pub

    for SUBNODE in `cat nodeips.txt`; do
         sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'mkdir -p .ssh'
         cat .ssh/sub_node.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'cat >> .ssh/authorized_keys'
    done
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 700 .ssh/'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 640 .ssh/authorized_keys'
done
```

Ez a szkript futtatásához az alábbi lépések segítségével. Ez a szkript módosíthatja vagy használjon másik módszert létesíteni beállításának SSH-hitelesítést a fürt számítási csomópontjai között is.

A parancsfájl futtatásához, akkor ismernie kell az előtag, az alhálózati IP-cím. Az előtag lekéréséhez futtassa a következő parancsot a fürtcsomópontok egyike. A kimeneti hasonlóan kell kinéznie 10.1.3.5, és az előtag a 10.1.3 részét.

```bash
ifconfig eth0 | grep -w inet | awk '{print $2}'
```

Most futtassa a szkriptet három paraméter használatával: a gyakori felhasználónevet, a számítási csomópontokon, a számítási csomópontokat, és az alhálózati előtag, az előző parancs által visszaadott felhasználóhoz tartozó gyakori jelszavát.

```bash
./user_authentication.sh <myusername> <mypassword> 10.1.3
```

A parancsfájl a következő műveleteket hajtja végre:

* A gazdacsomópont nevű .ssh, amelyre szükség az beállításának bejelentkezési hoz létre egy könyvtárat.
* Létrehoz egy konfigurációs fájlt, amely arra utasítja, hogy a bejelentkezés bármely olyan csomópontról a fürt beállításának bejelentkezési .ssh könyvtárában.
* A csomópont nevét és a csomópont IP-címeket a fürt összes csomópontja tartalmazó fájlokat hoz létre. Ezeket a fájlokat a későbbi felhasználás céljából a szkript futtatása után van hátra.
* Létrehoz egy nyilvános és titkos kulcspárt a fürt minden csomópontján (beleértve a gazdacsomópont), és bejegyzést hoz létre a authorized_keys fájlba.

> [!WARNING]
> A parancsfájl futtatása, biztonsági kockázatot is létrehozhat. Győződjön meg arról, hogy a nyilvánoskulcs-adatait a ~/.ssh nem elosztott.
>

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Alapszintű két csomópontos fürt MPI futtatása
Ha ezt még nem tette meg, először állítsa be a környezetet az Intel MPI-t.

```bash
# For a SLES 12 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Az MPI-parancs futtatása
Az egyik a számítási csomópontokat, hogy MPI megfelelően van telepítve, és kommunikálhat között legalább két számítási csomópontok egy MPI parancsot tartalmazza. A következő **mpirun** futtatása paranccsal a **állomásnév** két csomópont parancsot. Az a `-hosts` paraméterben adja át az IP-címek két csomópontot az Azure virtuális hálózat (például 10.32.0.4,10.32.0.5).

```bash
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
A kimeneti felsorolásban szerepelnie kell a nevek az összes olyan csomópontot, amely bemenetként átadott `-hosts`. Ha például egy **mpirun** két csomópont parancs kimenete az alábbihoz hasonló:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Futtassa az MPI-teljesítményteszt
A következő Intel MPI-parancs futtatása egy pingpong teljesítményteszt, ellenőrizze a fürt konfigurációját, valamint az RDMA hálózati kapcsolat.

```bash
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Egy működő fürtöt két csomóponttal, a kimenetnek kell megjelennie a következőhöz hasonló. Az Azure RDMA hálózati vagy az üzenet 3 mikroszekundum alatti késést mérete legfeljebb 512 bájt várható.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 6 17:16:46 2018
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```
### <a name="sample-mpi-run-script"></a>Minta MPI-parancsfájl futtatása
Íme egy Bash-mintaszkript MPI-alkalmazások futtatásához szükséges változók konfigurálásához. Módosítsa az elérési útját `mpivars.sh` Intel MPI-példány igény szerint.

```bash
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the MPI job. Substitute with values appropriate for your application.

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

A Hosts fájl formátuma a következő. Adjon hozzá egy sor minden egyes csomópont esetében a fürtben. Adja meg a korábban, nem a DNS-nevek meghatározott privát IP-címeket a virtuális hálózatról. Ha például két gazdagépek 10.32.0.4 és 10.32.0.4 IP-címekkel rendelkező, a fájl a következőket tartalmazza:

```bash
10.32.0.4:16
10.32.0.5:16
```


## <a name="next-steps"></a>További lépések
* Üzembe helyezése, és a Linux MPI alkalmazások futtatása a Linux-fürtön.
* Tekintse meg a [Intel MPI Library dokumentációjában](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) útmutató az Intel MPI-t.
* Próbálja ki egy [gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) egy Intel Lustre fürt létrehozása HPC CentOS-alapú lemezkép használatával. 
