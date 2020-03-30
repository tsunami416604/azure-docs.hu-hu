---
title: Üzenetátadási felület beállítása HPC-hez – Azure virtuális gépek | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be az MPI-t a HPC-hez az Azure-ban.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023990"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Üzenetátadási felület beállítása a HPC-hez

Az MPI-számítási feladatok a hagyományos HPC-munkaterhelések jelentős részét képezik. Az SR-IOV lehetővé tette a virtuális gép méreteit az Azure-ban lehetővé teszi az MPI szinte bármilyen ízét használni. 

MpI-feladatok futtatásához a virtuális gépeken partíciókulcsok (p-kulcsok) a bérlőn keresztül. Kövesse a [partíciókulcsok felderítése](#discover-partition-keys) szakaszban a p-kulcs értékek meghatározásával kapcsolatos részleteket.

## <a name="ucx"></a>UCX

[Az UCX](https://github.com/openucx/ucx) a legjobb teljesítményt nyújtja az IB-n, és az MPICH-vel és az OpenMPI-vel működik.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Telepítse az UCX-et a korábban leírtak szerint.

```bash
sudo yum install –y openmpi
```

Build OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Futtasd le az OpenMPI-t.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Ellenőrizze a partíció kulcsot, mint már említettük.

## <a name="mpich"></a>MPICH KÖZÖTT

Telepítse az UCX-et a korábban leírtak szerint.

Build MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH futtatása.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Ellenőrizze a partíció kulcsot, mint már említettük.

## <a name="mvapich2"></a>MVAPICH2

Build MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Az MVAPICH2 futtatása.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI közösségi kiadás

Telepítse a platform MPI-hez szükséges csomagokat.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Kövesse a telepítési folyamatot.

## <a name="intel-mpi"></a>Intel MPI

[Letöltés Intel MPI](https://software.intel.com/mpi-library/choose-download).

A I_MPI_FABRICS-környezet változójának módosítása a verziótól függően. Az Intel MPI 2018,use `I_MPI_FABRICS=shm:ofa` és 2019 esetén használja a használatát. `I_MPI_FABRICS=shm:ofi`

A folyamatrögzítés alapértelmezés szerint megfelelően működik a 15, 30 és 60 PPN esetén.

## <a name="osu-mpi-benchmarks"></a>OSU MPI referenciaértékek

[Letöltés OSU MPI benchmarkok](http://mvapich.cse.ohio-state.edu/benchmarks/) és untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Teljesítménymutatók létrehozása egy adott MPI-könyvtár használatával:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Az MPI-referenciaértékek a mappák alatt `mpi/` találhatók.


## <a name="discover-partition-keys"></a>Partíciókulcsok felderítése

Partíciókulcsok (p-kulcsok) a kommunikáció más virtuális gépekkel ugyanazon a bérlőn belül (availability set vagy virtuális gép méretezési csoport).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A kettő közül a nagyobb a bérlői kulcs, amelyet mpi-vel kell használni. Példa: Ha a p-billentyűk a következők, akkor a 0x800b-t kell használni az MPI-vel.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Használja az alapértelmezett (0x7fff) partíciókulcstól eltérő partíciókulcsot. Az UCX megköveteli a p-kulcs MSB-jének törlését. Állítsa be például UCX_IB_PKEY 0x000b-ként a 0x800b-hez.

Azt is vegye figyelembe, hogy mindaddig, amíg a bérlő (AVSet vagy VMSS) létezik, a PKEYs ugyanaz marad. Ez akkor is igaz, ha csomópontokat adnak hozzá/törölnek. Az új bérlők különböző PKEY-ket kapnak.


## <a name="set-up-user-limits-for-mpi"></a>Felhasználói korlátok beállítása mpi-hez

Felhasználói korlátok beállítása az MPI-hez.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>SSH-kulcsok beállítása mpi-hez

SSH-kulcsok beállítása az azt igénylő MPI-típusokhoz.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

A fenti szintaxis megosztott kezdőkönyvtárat feltételez, különben az .ssh könyvtárat minden csomópontra át kell másolni.

## <a name="next-steps"></a>További lépések

További információ az Azure-beli [HPC-ről.](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)
