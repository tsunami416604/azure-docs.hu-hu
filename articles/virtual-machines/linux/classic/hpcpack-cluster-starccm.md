---
title: Futtassa a csillag-CCM + HPC Pack Linux virtuális gépeken |} Microsoft Docs
description: Az Azure a Microsoft HPC Pack fürt központi telepítése és futtatása egy csillag-feladat CCM + több Linux a számítási csomópontok az RDMA-hálózaton.
services: virtual-machines-linux
documentationcenter: ''
author: xpillons
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: 8689d7abfd5ab45277df3b5672a1f6e7e874d88e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Futtassa a csillag-CCM + Microsoft HPC Pack egy Linux RDMA a fürt az Azure-ban
Ez a cikk bemutatja, hogyan Azure, és futtassa a Microsoft HPC Pack fürt központi telepítése egy [CD-adapco csillag-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) feladat több Linux számítási csomópontokra, amelyeket kötik össze, az InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

A Microsoft HPC Pack számos nagy méretű HPC és párhuzamos alkalmazások, beleértve a MPI alkalmazásokat, a Microsoft Azure virtuális gépek fürtön futtatandó szolgáltatásokat biztosítja. HPC Pack a telepített HPC Pack-fürtben lévő Linux számítási csomópont virtuális gépeken futó Linux HPC-alkalmazásokhoz is támogatja. Linux számítási csomópontokat használó HPC Pack bemutatása, lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Egy HPC Pack fürt beállítása
Töltse le a HPC Pack IaaS telepítési parancsfájlok a [letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=44949) és helyileg bontsa ki.

Az Azure PowerShell feltétele. Ha a PowerShell nincs konfigurálva a helyi gépén, olvassa el a cikk [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

A írásának időpontjában a Linux-lemezképek, az Azure piactérről (amely tartalmazza a InfiniBand illesztőprogramokat az Azure-bA) a SLES 12 rendszert, a CentOS 6.5-ös és a CentOS 7.1 vannak. Ez a cikk az SLES 12 használatát alapul. Az összes Linux képek, amely támogatja a HPC a piactéren nevének beolvasására, futtathatja a következő PowerShell-parancsot:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

A kimenet tartalmazza a helyet, ahol ezek a lemezképek érhetők el, és a lemezkép neve (**ImageName**) használhatók a központi telepítési sablont később.

A fürt telepítése előtt kell HPC Pack telepítési sablon fájl. Mivel azt még célzó kis fürt, az átjárócsomópont a tartományvezérlőn lennie, és egy helyi SQL-adatbázist.

Az alábbi sablont fog ilyen átjárócsomópont, a rendszer nevű XML-fájl létrehozása **MyCluster.xml**, és cserélje le az értékeket **SubscriptionId**, **StorageAccount**,  **Hely**, **VMName**, és **szolgáltatásnév** legyen.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Indítsa el az átjárócsomópont létrehozása a PowerShell-parancs futtatásával egy rendszergazda jogú parancssort a:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20-30 perc elteltével az átjárócsomópont készen áll. Csatlakozhat az Azure portálon kattintson a **Connect** a virtuális gép ikonja.

Végül hárítsa el a DNS-továbbító előfordulhat. Ehhez indítsa el a DNS-kezelőben.

1. Kattintson a jobb gombbal a kiszolgáló nevét a DNS-kezelőben válassza **tulajdonságok**, majd kattintson a **továbbítók** lapon.
2. Kattintson a **szerkesztése** továbbítókat eltávolítása gombra, majd **OK**.
3. Győződjön meg arról, hogy a **gyökérmutató használatára, ha nincs továbbítók elérhető** jelölőnégyzet be van jelölve, és kattintson a **OK**.

## <a name="set-up-linux-compute-nodes"></a>Linux számítási csomópontok beállítása
A Linux számítási csomópontok a azonos központi telepítési sablont, amely az átjárócsomópont létrehozásához használt központi telepítését.

A fájl másolása **MyCluster.xml** az átjárócsomóponthoz, és a frissítés a helyi gép a **NodeCount** kód, amely számára telepíteni kívánja csomópontok száma (< = 20). Ügyeljen arra, hogy elegendő rendelkezésre álló magot rendelkezik a Azure kvótájának, mert A9 feltünteti az előfizetésében 16 mag fog használni. A9-es helyett A8 példányok (8 mag) használhatja, ha további virtuális gépek ugyanazt a költségvetést a használni kívánt.

Az átjárócsomópont másolja a HPC Pack IaaS telepítési parancsfájlokhoz.

Futtassa a következő Azure PowerShell-parancsokat egy rendszergazda jogú parancssorból:

1. Futtatás **Add-AzureAccount** csatlakozni az Azure-előfizetéshez.
2. Ha több előfizetéssel rendelkezik, akkor futtassa **Get-AzureSubscription** segítségével rendezze őket.
3. Állítsa az alapértelmezett előfizetés futtatásával a **válasszon-AzureSubscription - SubscriptionName xxxx-alapértelmezett** parancsot.
4. Futtatás **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** kell elkezdeni az telepítését Linux számítási csomópontjain.
   
   ![Átjárócsomópont telepítési művelet:][hndeploy]

A HPC Pack Cluster Manager eszköz megnyitásához. Néhány perc múlva a Linux számítási csomópontok rendszeresen megjelennek a listában számítási csomópont. A klasszikus üzembe helyezési mód IaaS virtuális gépek jönnek létre egymás után. Ezért fontos, a csomópontok számát, ha rávenni a felhasználókat az összes telepített is igénybe vehet jelentős mennyiségű időt.

![Linux-csomópontok HPC Pack-Fürtkezelőben][clustermanager]

Most, hogy minden csomópont működik, és a fürt, nincsenek további infrastruktúra-beállításokat, hogy.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Egy Azure fájlmegosztás Windows és Linux csomópontok beállítása
Az Azure File szolgáltatással parancsfájlok, alkalmazáscsomagok és adatfájlok tárolásához. Az Azure File állandó tároló Azure Blob storage-CIFS képességeket biztosít. Vegye figyelembe, hogy ez nem a leginkább méretezhető megoldás, de a legegyszerűbb egy és nem igényel dedikált virtuális gépek.

Azure-fájlmegosztás létrehozása a cikk utasításait követve [Ismerkedés az Azure File storage on Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Mint a tárfiók nevét **saname**, mint a fájlmegosztás neve **megosztásnév**, és a tárfiók hívóbetűjét, **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>A head csomóponton Azure fájlmegosztás csatlakoztatása
Nyisson meg egy rendszergazda jogú parancssort, és a hitelesítő adatok tárolását a helyi számítógép tárolójában a következő parancsot:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Ezután az Azure fájlmegosztás csatlakoztatásához futtassa:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>A számítási csomópontok Linux Azure fájlmegosztás csatlakoztatása
HPC Pack részeként elérhető egy hasznos eszköz a clusrun eszköz. Ez a parancssori eszköz segítségével ugyanaz a parancs futhat egyidőben számítási csomópontok készlete. Ebben az esetben segítségével Azure fájlmegosztás csatlakoztatása, és azt követően sem vesznek megmaradnak.
Egy rendszergazda jogú parancssort a head csomóponton futtassa a következő parancsokat.

A csatlakoztatási könyvtár létrehozásakor:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Az Azure fájlmegosztás csatlakoztatásához:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

A csatlakoztatási megosztás megőrzéséhez:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Telepítse a csillag-CCM +
Azure Virtuálisgép-példányok A8 és A9 InfiniBand támogatási és RDMA-képességeinek biztosítanak. A kernel-illesztőprogramokat, amelyek lehetővé teszik a szolgáltatásokat a Windows Server 2012 R2, a SUSE 12, a CentOS 6.5-ös és a CentOS 7.1 képekhez az Azure piactéren érhetők el. Microsoft MPI és Intel MPI (5.x) a két MPI-könyvtárak, amelyek támogatják a kívánt illesztőprogramokat az Azure-ban.

CD-adapco csillag-CCM + 11.x kiadási és újabb rendszer kötegelt Intel MPI verziójával 5.x, így Azure InfiniBand támogatása is megtalálható.

A Linux64 beolvasása csillag-CCM + csomagot a [CD-adapco portal](https://steve.cd-adapco.com). Ebben az esetben vegyes pontosság 11.02.010 verziót használtuk.

A head csomóponton a a **/hpcdata** Azure fájl megosztásához hozzon létre egy héjparancsfájlt nevű **setupstarccm.sh** az alábbi tartalommal. Ezt a csillag beállítása minden számítási csomóponton parancsfájlt-CCM + helyileg.

#### <a name="sample-setupstarcmsh-script"></a>Mintaparancsfájl setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Most, csillag beállítása-CCM + a Linux számítási csomópontok, nyisson meg egy rendszergazda jogú parancssort és futtassa a következő parancsot:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

A parancs futása közben, a CPU-használat a hőtérkép a fürtkezelő használatával figyelheti. Néhány perc múlva minden csomópontja megfelelően létre kell hozni.

## <a name="run-star-ccm-jobs"></a>Futtassa a csillag-CCM + feladatok
HPC Pack feladat ütemezési platformképességei csillag futtatásához használt-CCM + feladatok. Ehhez az szükséges, igazolnia kell a támogatása néhány olyan parancsfájlok, amelyek segítségével indítsa el a feladatot, és futtassa a csillag-CCM +. A bemeneti adatok tárhely az Azure fájlmegosztás első az egyszerűség érdekében.

A következő PowerShell-parancsfájl használata a várólistába helyezni a csillag-CCM + feladat. Három argumentummal van:

* A modell neve
* A használt csomópontok száma
* Az alkalmazandó minden egyes csomóponton magok száma

Mivel a csillag-CCM + töltheti memória sávszélesség célszerűbb általában kevesebb számítási csomópontok található magok használja, és adja hozzá az új csomópontok. Az egyes csomópontok magok pontos száma processzorcsaládját és a memóriamodulok közötti sebességétől függ.

A csomópontok kizárólag a feladathoz kiosztott, és nem osztható meg más feladatok. A feladat nem közvetlenül elindult MPI feladatként. A **runstarccm.sh** héjparancsfájlt a MPI indítója elindul.

A bemeneti modell és a **runstarccm.sh** parancsfájl tárolódnak a **/hpcdata** korábban csatlakoztatott megosztást.

Naplófájlok a feladatazonosító vannak nevezve, és tárolja a **/hpcdata megosztás**, együtt a csillag-CCM + kimeneti fájlok.

#### <a name="sample-submitstarccmjobps1-script"></a>Mintaparancsfájl SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Cserélje le **runner.java** az előnyben részesített csillag-CCM + Java modell indítója és naplózási kódot.

#### <a name="sample-runstarccmsh-script"></a>Mintaparancsfájl runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Ebben a tesztben használtuk egy igény szerinti Power licenc token. A jogkivonatot, meg kell beállítani a **$CDLMD_LICENSE_FILE** környezeti változót **1999@flex.cd-adapco.com** és a kulcsot a **- podkey** beállítást a parancssor.

Néhány inicializálás után a parancsfájl létrehoz – az a **$CCP_NODES_CORES** környezeti változókat, hogy HPC Pack beállítása – csomópontok hozhat létre egy hostfile, amely a MPI indítója használja listáját. A hostfile számítási csomópont a feladathoz, soronként egy név használt nevek listája fogja tartalmazni.

Formátuma **$CCP_NODES_CORES** ezt a mintát követi:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Az elemek magyarázata:

* `<Number of nodes>` Ez a feladat számára lefoglalt csomópontok száma van.
* `<Name of node_n_...>` Ez a feladat rendelt minden csomópont neve van.
* `<Cores of node_n_...>` van a csomópont, a feladat számára lefoglalt magok száma.

Magok száma (**$NBCORES**) is a csomópontok száma alapján számított (**$NBNODES**) és az egyes csomópontok magok száma (paraméterként megadott **$NBCORESPERNODE**).

MPI beállítások Intel MPI Azure használt megfelelően a következők:

* `-mpi intel` Adja meg az Intel MPI.
* `-fabric UDAPL` Azure InfiniBand műveletek használata.
* `-cpubind bandwidth,v` sávszélesség-optimalizálását a csillag MPI-CCM +.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` Intel MPI Azure InfiniBand dolgozni, és csomópontonként mag szükséges számának beállítása.
* `-batch` CSILLAG elindításához-CCM + kötegelt módban nincs felhasználói felületén.

Végezetül a feladat elindításához, győződjön meg arról, hogy a csomópontok megfelelően működik-e, és a kezelő online állapotban. A PowerShell-parancssorból futtassa ezt:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Csomópont leállítása
Meg Miután befejezte a teszteket, használhatja a következő HPC Pack PowerShell-parancsok leállítására és elindítására csomópontok:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>További lépések
Próbálja más Linux feladatokat futtatni. Például lásd:

* [A Microsoft HPC Pack NAMD futó Linux számítási csomópontok az Azure-ban](hpcpack-cluster-namd.md)
* [Microsoft HPC Pack OpenFOAM futtassa az Azure Linux RDMA fürtön](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
