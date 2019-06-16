---
title: Message Passing Interface beállítása HPC - Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg, hogyan MPI beállítása HPC az Azure-ban.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 1717c0d95c00e13ae0d87ab920bc88cd4f0df978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809842"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC Message Passing Interface beállítása

Message Passing Interface (MPI) számítási feladatokat egy hagyományos HPC számítási feladatok jelentős részét képezik. Az SR-IOV engedélyezett Virtuálisgép-méretek az Azure-ban használandó MPI, szinte bármilyen íz engedélyezése. 

MPI-feladatok futtatása virtuális gépeken be kell állítani a partíciókulcsok (p-kulcsok) minden olyan bérlő. Kövesse a [partíciókulcsok felderítése](#discover-partition-keys) a p-kulcs-érték meghatározásához a részleteket a következő szakaszban.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) IB és MPICH és OpenMPI együttműködik a lehető legjobb teljesítményt kínál.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Telepítse a fentieknek UCX.

```bash
sudo yum install –y openmpi
```

OpenMPI hozhat létre.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Futtassa a OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Ahogy korábban említettük, ellenőrizze a partíciós kulcs.

## <a name="mpich"></a>MPICH

Telepítse a fentieknek UCX.

MPICH hozhat létre.

```bash
wget http://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH fut.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Ahogy korábban említettük, ellenőrizze a partíciós kulcs.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 hozhat létre.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 fut.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

A Platform MPI szükséges csomagok telepítéséhez.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Kövesse a telepítési folyamat.

## <a name="intel-mpi"></a>Az Intel MPI

[Töltse le az Intel MPI](https://software.intel.com/mpi-library/choose-download).

Módosítsa a I_MPI_FABRICS környezeti változó verziójától függően. Az Intel MPI 2018 használjon `I_MPI_FABRICS=shm:ofa` és a 2019, használja `I_MPI_FABRICS=shm:ofi`.

Rögzítés a folyamat megfelelően működik 15, 30 és 60 PPN alapértelmezés szerint.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-Referenciaalapok

[OSU MPI referenciaalapokhoz képest történő letöltése] [ http://mvapich.cse.ohio-state.edu/benchmarks/ ](http://mvapich.cse.ohio-state.edu/benchmarks/) és untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Egy adott MPI-kódtár használatával referenciaalapokhoz képest történő létrehozása:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI referenciaértékek alatt `mpi/` mappát.


## <a name="discover-partition-keys"></a>Fedezze fel a partíciókulcsok

Fedezze fel a partíciókulcsok (p-keys) a többi virtuális géphez való kommunikációhoz.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A kettő közül a nagyobb a bérlői kulcs, amely MPI kell használni. Példa: Az alábbiakban a p-kulcsokat, ha 0x800b MPI kell használható.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Ezen kívül (0x7fff) alapértelmezett partíciókulcs a partíció. UCX igényel az MSB p-kulcs törölve lesz. Például állítsa be, a 0x800b 0x000b UCX_IB_PKEY.


## <a name="set-up-user-limits-for-mpi"></a>MPI felhasználókra vonatkozó korlátozások beállítása

Felhasználókra vonatkozó korlátozások beállítása MPI.

```bash
cat << EOF >> /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>MPI SSH-kulcsok beállítása

MPI-típusokat, amelyek miatt az SSH-kulcsok beállítása.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

A fenti szintaxisban feltételezi, hogy a megosztott kezdőkönyvtár, minden egyes csomópont más .ssh könyvtárában kell másolni.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
