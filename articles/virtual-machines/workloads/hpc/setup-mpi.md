---
title: Üzenet-átadási felület beállítása HPC-Azure Virtual Machineshoz | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az MPI-t a HPC számára az Azure-ban.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77023990"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Üzenet küldési felületének beállítása HPC-hez

A Message Passing Interface (MPI) számítási feladatok a hagyományos HPC-munkaterhelések jelentős részét képezik. Az SR-IOV-kompatibilis VM-méretek az Azure-ban szinte bármilyen típusú MPI-t használhatnak. 

Az MPI-feladatok virtuális gépeken való futtatásához partíciós kulcsokat (p-Keys) kell beállítani a bérlők között. A p-Key értékek meghatározásával kapcsolatos részletekért kövesse a [partíciós kulcsok felderítése](#discover-partition-keys) című szakasz lépéseit.

## <a name="ucx"></a>UCX

A [UCX](https://github.com/openucx/ucx) a legjobb teljesítményt nyújtja az IB-ben, és EGYÜTTMŰKÖDIK a MPICH és a OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Telepítse a UCX az előzőekben leírtak szerint.

```bash
sudo yum install –y openmpi
```

OpenMPI létrehozása.

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

Tekintse meg a fentiekben említett partíciós kulcsot.

## <a name="mpich"></a>MPICH

Telepítse a UCX az előzőekben leírtak szerint.

MPICH létrehozása.

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

Tekintse meg a fentiekben említett partíciós kulcsot.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 létrehozása.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 futtatása.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Telepítse a szükséges csomagokat a platform MPI-hez.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Kövesse a telepítési folyamatot.

## <a name="intel-mpi"></a>Intel MPI

[Töltse le az Intel MPI](https://software.intel.com/mpi-library/choose-download)-t.

Módosítsa a I_MPI_FABRICS környezeti változót a verziótól függően. Az Intel MPI 2018 esetében használja a `I_MPI_FABRICS=shm:ofa` és a for 2019 használatát `I_MPI_FABRICS=shm:ofi` .

A folyamat kitűzése alapértelmezés szerint 15, 30 és 60 PPN megfelelően működik.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-referenciaértékek

[Töltse le a OSU MPI-referenciaértékeket](http://mvapich.cse.ohio-state.edu/benchmarks/) és a untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Teljesítménytesztek létrehozása egy adott MPI-könyvtár használatával:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Az MPI-referenciaértékek a `mpi/` mappában találhatók.


## <a name="discover-partition-keys"></a>Partíciós kulcsok felderítése

Az azonos bérlőn (rendelkezésre állási csoport vagy virtuálisgép-méretezési csoport) belüli más virtuális gépekkel való kommunikációhoz használható partíciós kulcsok (p-Keys) felderítése.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A kettő közül a nagyobb a bérlői kulcs, amelyet MPI-vel kell használni. Példa: Ha a következő a p-Keys, a 0x800b-t MPI-vel kell használni.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Használja az alapértelmezett (0x7fff) partíciós kulcstól eltérő partíciót. A UCX a p-Key MSB törlését igényli. Például állítsa be a UCX_IB_PKEY as 0x000b for 0x800b.

Azt is vegye figyelembe, hogy ha a bérlő (AVSet vagy VMSS) létezik, a PKEYs változatlan marad. Ez akkor is igaz, ha csomópontok lettek hozzáadva/törölve. Az új bérlők különböző PKEYs kapnak.


## <a name="set-up-user-limits-for-mpi"></a>Felhasználói korlátok beállítása MPI-re

Felhasználói korlátok beállítása MPI-re.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>SSH-kulcsok beállítása MPI-hez

Állítsa be az SSH-kulcsokat az azt igénylő MPI-típusokhoz.

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

A fenti szintaxis azt feltételezi, hogy egy megosztott kezdőkönyvtár, más. ssh könyvtárat kell átmásolni az egyes csomópontokra.

## <a name="next-steps"></a>További lépések

További információ az Azure-beli [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) -ről.
