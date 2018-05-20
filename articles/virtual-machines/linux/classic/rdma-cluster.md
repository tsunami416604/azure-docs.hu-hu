---
title: MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt |} Microsoft Docs
description: Méretű H16r, H16mr, A8 vagy A9 virtuális gépeket az Azure RDMA hálózati MPI alkalmazások futtatásához használandó Linux-fürt létrehozása
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
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: d53305aae3b12c0de983dced85a9626cf98c6309
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Linuxos RDMA-fürt beállítása MPI-alkalmazások futtatására
Ismerje meg, hogyan állíthat be az Azure-ban Linux RDMA fürt [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) párhuzamos Message Passing Interface (MPI) alkalmazások futtatásához. Ez a cikk lépéseit Intel MPI futhat egy fürt Linux HPC lemezkép előkészítése. Előkészítő, miután a virtuális gépek használata a lemezkép és az RDMA-kompatibilis Azure Virtuálisgép-méretek, (jelenleg H16r, H16mr, A8 és A9) egy fürt központi telepítése. A fürt használatával, amely a távoli közvetlen memória-hozzáférés (RDMA) technológián alapulnak, alacsony késésű, nagy átviteli hálózati hatékonyan kommunikációhoz MPI-alkalmazások futtatására.

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="cluster-deployment-options"></a>Fürt üzembe helyezési lehetőségei
Az alábbiakban módszert hozhat létre Linux RDMA-fürtöt, vagy a Feladatütemező nélkül használhat.

* **Az Azure CLI-parancsfájlok**: a cikk későbbi részében látható, használja a [Azure parancssori felület](../../../cli-install-nodejs.md) (CLI) egy fürt RDMA-kompatibilisek-e virtuális gépek telepítését parancsfájllal történő. A parancssori felület szolgáltatásfelügyelet módban a fürtcsomópontok Feladattervek hoz létre a klasszikus üzembe helyezési modellel, így sok számítási csomópont telepítése több percig is eltarthat. A klasszikus üzembe helyezési modellt használja az RDMA hálózati kapcsolat engedélyezéséhez telepítenie kell a virtuális gépek ugyanazt a felhőszolgáltatásban található.
* **Az Azure Resource Manager-sablonok**: egy fürt, amely összeköti az RDMA hálózati RDMA-kompatibilisek-e virtuális gépek telepítése a Resource Manager üzembe helyezési modellel is használhatja. Is [létrehozhat saját sablont](../../../resource-group-authoring-templates.md), vagy ellenőrizze a [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates/) Microsoft vagy a közösségi kívánt megoldás üzembe helyezéséhez által közzétett sablonokat. Resource Manager-sablonok is biztosít a Linux-fürt üzembe gyors és megbízható módot. Szeretné engedélyezni az RDMA hálózati kapcsolatot a Resource Manager üzembe helyezési modellel használatakor, központi telepítését az azonos rendelkezésre állási csoport a virtuális gépek.
* **HPC Pack**: Microsoft HPC Pack fürt létrehozása az Azure-ban, és adja hozzá az RDMA-kompatibilisek-e, a támogatott Linux-disztribúció az RDMA hálózati eléréséhez futtató számítási csomópontok. További információkért lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>A klasszikus modellben minta az üzembe helyezés lépései
A következő lépések bemutatják, hogyan telepítse a SUSE Linux Enterprise Server (SLES) 12 SP1 HPC virtuális Gépet az Azure piactérről, testre szabhatja, és hozzon létre egy egyéni Virtuálisgép-lemezkép az Azure parancssori felület használatával. A lemezkép használatával majd RDMA-kompatibilisek-e virtuális gépek a fürt a telepítési parancsfájl.

> [!TIP]
> Hasonló lépések segítségével az Azure piactéren CentOS alapú HPC képek alapján RDMA-kompatibilisek-e virtuális gépek fürt központi telepítése. Néhány lépést esetekben némileg eltérőek lehetnek. 
>
>

### <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép**: Azure kommunikálni Mac, Linux vagy a Windows ügyfél számítógépre van szüksége. Ezek a lépések feltételezik, hogy a Linux-ügyfelet kell használnia.
* **Azure-előfizetés**: Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) néhány percig. Nagyobb fürtök esetén fontolja meg a használatalapú előfizetés vagy egyéb beszerzési lehetőségek.
* **Virtuális gép mérete rendelkezésre állási**: A következő példány értékek RDMA-kompatibilis: H16r, H16mr, A8 és A9. Ellenőrizze [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/) a Azure-régiók rendelkezésre állás érdekében.
* **Magok kvóta**: szükség lehet a számítási igényű virtuális gépek fürt központi telepítése magszámra vonatkozó kvóta növeléséhez. Például legalább 128 magok kell, ha azt szeretné, 8 A9 virtuális gépek telepítése ebben a cikkben ismertetett módon. Az előfizetés is lehet, hogy korlátozza az egyes virtuális gép mérete családok, többek között a H-sorozat telepítheti magok száma. A kvóta növelését [nyissa meg az online támogatás ügyfélkérés](../../../azure-supportability/how-to-create-azure-support-request.md) díjmentesen.
* **Az Azure CLI**: [telepítése](../../../cli-install-nodejs.md) az Azure CLI és [csatlakozni az Azure-előfizetéshez](/cli/azure/authenticate-azure-cli) az ügyfélszámítógépről.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Az SLES 12 SP1 HPC virtuális gép kiépítése
Az Azure-bA az Azure parancssori felülettel történő bejelentkezés után futtassa `azure config list` annak ellenőrzéséhez, hogy a kimenet látható-e a szolgáltatásfelügyeleti módban. Ha nem, a mód beállítása a következő parancs futtatásával:

    azure config mode asm


Írja be az alábbi listában az összes olyan előfizetést, amelyet Ön jogosult-e használni:

    azure account list

Az aktuális aktív előfizetéssel, amelynél az `Current` beállítása `true`. Ha ez az előfizetés nem az a fürt létrehozásához használni kívánt, az aktív előfizetéssel állítsa be a megfelelő előfizetés-azonosító:

    azure account set <subscription-Id>

Tekintse meg az Azure-ban a nyilvánosan elérhető SLES 12 SP1 HPC-lemezképek, a következőhöz hasonló parancs futtatása, feltéve, hogy a rendszerhéj-környezet támogatja **grep**:

    azure vm image list | grep "suse.*hpc"

SLES 12 SP1 HPC képének az RDMA-kompatibilisek-e virtuális gép kiépítése a következőhöz hasonló parancs futtatásával:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Az elemek magyarázata:

* A méret (ebben a példában A9) egyike az RDMA-kompatibilisek-e Virtuálisgép-méretek.
* A külső SSH-port száma (a példában az SSH alapértelmezés 22) bármilyen érvényes portszámot. A belső SSH-portszám 22 van beállítva.
* Új felhőalapú szolgáltatás az Azure-régió, a hely által meghatározott jön létre. Adjon meg egy helyet, ahol a Virtuálisgép-méretet választott érhető el.
* A SUSE rangsorolási támogatással (amely további költségeket terhel) a SLES 12 SP1 kép neve jelenleg lehet ezek két lehetőség közül: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>A virtuális gép testreszabása
A virtuális gép befejeződése kiépítés, a virtuális gép külső IP-cím (vagy DNS-név) a virtuális gép SSH, és a külső portra a számát, és majd testreszabása után. Kapcsolat részletekért lásd: [Linuxot futtató virtuális gép bejelentkezés](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Parancsok végrehajtása a felhasználó nevében, konfigurálta a virtuális Gépen, kivéve, ha a rendszergazdai hozzáférés szükséges egy lépés befejezéséhez.

> [!IMPORTANT]
> A Microsoft Azure nem legfelső szintű hozzáférés biztosítása a Linux virtuális gépek. Ahhoz, hogy a rendszergazdai hozzáférés esetén a virtuális gép felhasználóként, futtassa a parancsokat `sudo`.
>
>

* **Frissítések**: frissítések telepítése zypper használatával. Akkor is telepíteni szeretne NFS segédprogramok.

  > [!IMPORTANT]
  > SLES 12 SP1 HPC virtuális gépen azt javasoljuk, hogy a rendszermag-frissítéseket, amelyek problémákat okozhatnak a Linux RDMA illesztőprogramok nem alkalmazza.
  >
  >
* **Intel MPI**: Intel MPI a SLES 12 SP1 HPC virtuális Gépre telepítése után a következő parancs futtatásával:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Memóriát zárolni**: A MPI kódok zárolni a rendelkezésre álló memória az RDMA, adja hozzá, vagy módosítsa a /etc/security/limits.conf fájlban a következő beállításokat. Ez a fájl szerkesztése legfelső szintű hozzáférés szükséges.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > Tesztelési célokra is állíthatja memlock korlátlan. Például: `<User or group name>    hard    memlock unlimited`. További információkért lásd: [beállítás ismert legjobb módszerei zárolt memória mérete](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **SLES virtuális gépek SSH-kulcsok**: készítése SSH-kulcsok a felhasználói fiókhoz, a számítási csomópontok közötti megbízhatósági kapcsolat létrehozása az SLES a fürt MPI-feladatok futtatásakor. Ha telepítette a HPC CentOS-alapú virtuális gépek, ne ezt a lépést. Tekintse meg a passwordless SSH megbízhatóság kialakításához fürt csomópontjai között a lemezképet, és a fürt központi telepítése után a cikk útmutatást.

    SSH-kulcsok létrehozásához futtassa a következő parancsot. Amikor a bemeneti kéri, válassza ki **Enter** a kulcs létrehozásához az alapértelmezett helyen jelszó beállítása nélkül.

        ssh-keygen

    A nyilvános kulcs hozzáfűzése ismert nyilvános kulcsok authorized_keys fájlhoz.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    A ~/.ssh könyvtárban szerkesztheti, és a konfigurációs fájl létrehozása. Adja meg az IP-címtartományt, amely (ebben a példában 10.32.0.0/16) Azure-ban használni kívánt magánhálózat:

        host 10.32.0.*
        StrictHostKeyChecking no

    Alternatív megoldásként listában az alábbiak szerint a fürt egyes virtuális gépek magánhálózati IP-címe:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > Konfigurálás `StrictHostKeyChecking no` biztonsági kockázatot hozhat létre, ha egy adott IP-cím vagy a tartomány nincs megadva.
  >
  >
* **Alkalmazások**: telepítse a szükséges, vagy hajtsa végre a más testreszabás is szerepelt, mielőtt a lemezképet rögzítené alkalmazásokat.

### <a name="capture-the-image"></a>A lemezkép rögzítése
A lemezkép rögzítése, először futtassa a következő parancsot a Linux virtuális Gépet. Ezt a parancsot a virtuális gép deprovisions, de megőrzi a felhasználói fiókok és a beállított SSH-kulcsok.

```
sudo waagent -deprovision
```

Az ügyfélszámítógépről a következő parancsokat az Azure parancssori felület a lemezkép rögzítése. További információkért lásd: [rögzítése képként klasszikus Linuxos virtuális gép](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Ezek a parancsok futtatása után a virtuális gép lemezképének rögzítése a használatra, és a virtuális gép törlődik. Most már rendelkezik egy fürt üzembe helyezésére az egyéni lemezképet.

### <a name="deploy-a-cluster-with-the-image"></a>A lemezképpel fürt központi telepítése
Módosítsa a következő Bash parancsfájlt a környezetének megfelelő értékekkel, és futtassa az ügyfélszámítógépről. Azure telepíti a virtuális gépek Feladattervek a klasszikus üzembe helyezési modellel, mert a nyolc A9 virtuális gépeket, javasolt ezt a parancsfájlt a telepítendő néhány percet vesz igénybe.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
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
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>A CentOS HPC-fürt szempontjai
Ha be szeretné állítani a HPC egy SLES 12 helyett az Azure piactéren CentOS alapú HPC lemezképet alapján fürt, kövesse az előző szakaszban leírt általános lépéseket. Vegye figyelembe a következő eltérésekkel, telepítéséhez és a virtuális gép konfigurálása során:

- Intel MPI már telepítve van a virtuális gép kiépítése a CentOS-alapú HPC-lemezkép.
- Zárolási memóriabeállításait már kerülnek a virtuális gép /etc/security/limits.conf fájlban.
- SSH-kulcsok a virtuális gép kiépítése a rögzítési nem hoznak létre. Ajánlja felhasználói hitelesítés beállítása a fürt telepítése után. További információkért lásd a következő.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>A fürt passwordless SSH megbízhatóságának beállítása
Egy CentOS-alapú HPC-fürtre, a számítási csomópontok közötti megbízhatósági kapcsolat létrehozásához két módszer áll rendelkezésre: állomás alapú hitelesítés és a felhasználó-alapú hitelesítés. Gazdagép-alapú hitelesítés Ez a cikk hatókörén kívül esik, és általában kell elvégezni egy bővítmény parancsfájl központi telepítése során. Felhasználó-alapú hitelesítés kényelmes, a telepítés után a megbízható kapcsolat kialakítása és a generáció és a fürt az SSH-kulcsok a számítási csomópontok közötti megosztásának igényel. Ezt a módszert gyakran nevezik passwordless SSH-bejelentkezéskor, és futó MPI-feladatok esetén szükséges.

Egy minta parancsfájlt a Közösségtől hozzájárult érhető el a [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) CentOS-alapú HPC-fürt könnyen felhasználói hitelesítést. Töltse le és használja ezt a parancsfájlt a következő lépések segítségével. Módosítsa ezt a parancsfájlt vagy más módszerrel használatával szeretne létrehozni a számítási fürtcsomópontok közötti passwordless SSH hitelesítés is.

    wget https://raw.githubusercontent.com/tanewill/utils/master/user_authentication.sh

A parancsfájl futtatásához kell tudni, hogy az előtag, az alhálózati IP-címek számára. Az előtag lekérése a következő parancs futtatásával a fürtcsomópontok egyike. A kimeneti hasonlóan kell kinéznie 10.1.3.5, és az előtag a 10.1.3 részét.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Most futtassa a parancsfájl segítségével történő három paramétert: a közös felhasználónevet, a számítási csomópontokat, a számítási csomópontokat, és az alhálózati előtag, az előző parancs által visszaküldött közös jelszót.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

A parancsfájl a következő műveleteket hajtja végre:

* A gazdacsomópont nevű .ssh, ami azonban szükséges az passwordless bejelentkezési hoz létre egy könyvtárat.
* Létrehoz egy konfigurációs fájlt a .ssh könyvtárban, amely arra utasítja a fürt minden csomópontján bejelentkezési engedélyezése passwordless bejelentkezni.
* A csomópont nevét és a fürt összes csomópontjának csomópont IP-címet tartalmazó fájlokat hozza létre. Ezek a fájlok megmaradnak a későbbi felhasználás a parancsfájl futtatása után.
* A privát és nyilvános kulcsból álló kulcspárt a fürt minden csomópontján (beleértve a gazdacsomópont) hoz létre, és létrehozza a bejegyzéseket a authorized_keys fájlba.

> [!WARNING]
> A parancsfájl futtatása, biztonsági kockázatot is létrehozhat. Győződjön meg arról, hogy a nyilvános kulcs információja ~/.ssh nem terjesztése.
>
>

## <a name="configure-intel-mpi"></a>Intel MPI konfigurálása
MPI alkalmazások futtatásához Azure Linux RDMA szüksége konfigurálása bizonyos Intel MPI vonatkozó környezeti változókat. Íme egy minta Bash parancsfájl konfigurálása a változókat, az alkalmazás futtatásához szükséges. Intel MPI-példány igény szerint módosítsa az elérési utat mpivars.sh.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

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

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

A Hosts fájl formátuma a következő. Adja hozzá az egyes csomópontok egy sort a fürtön. Adja meg a korábban, nem a DNS-nevek meghatározott privát IP-címek a virtuális hálózati. Például a két gazdagépek 10.32.0.1 és 10.32.0.2 IP-címekkel rendelkező, a fájl tartalmazza a következő:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>MPI futtatnak egy alapszintű két csomópontot tartalmazó fürtben
Ha még nem tette meg, először állítsa be a környezetet az Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Egy MPI parancs futtatása
Egy MPI paranccsal jelenítse meg, hogy MPI megfelelően van telepítve, és képes kommunikálni a közötti legalább két számítási csomópontjain a számítási csomópontok egyikén. A következő **mpirun** parancs elindul a **állomásnév** két csomópont parancs.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
A kimenetében csomópontjaihoz bemenetként továbbított nevei `-hosts`. Például egy **mpirun** két csomóponttal rendelkező parancs kimenetét a következő adja vissza:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Egy MPI teljesítményteszt futtatása
A következő Intel MPI parancs fut egy pingpong javasolt fürtkonfiguráció és az RDMA hálózati kapcsolat ellenőrzése.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Működő rendelkező fürtön két csomópont a következőhöz hasonló kimenetnek kell megjelennie. A Azure RDMA hálózati késés, vagy az üzenet 3 ezredmásodperc alatt legfeljebb 512 bájt méretű várt.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
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



## <a name="next-steps"></a>További lépések
* Regisztrálhat és futtathat a Linux MPI alkalmazások a Linux-fürt.
* Tekintse meg a [Intel MPI Library dokumentációjában](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) Intel MPI útmutatót.
* Próbálja meg egy [gyorsindítási sablonon](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) HPC CentOS-alapú lemezkép használatával az Intel fényesség fürt létrehozásához. További információkért lásd: [Intel felhő Edition telepítését a Microsoft Azure fényesség](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
