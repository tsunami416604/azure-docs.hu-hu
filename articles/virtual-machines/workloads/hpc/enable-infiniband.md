---
title: InifinBand engedélyezése SR-IOV-Azure Virtual Machines | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a InfiniBand az SR-IOV használatával.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74196768"
---
# <a name="enable-infiniband-with-sr-iov"></a>InfiniBand engedélyezése SR-IOV

Az Azure NC, ND és H-sorozatú virtuális gépeket egy dedikált InfiniBand-hálózat támogatja. Minden RDMA-kompatibilis méret képes a hálózat Intel MPI-vel történő kihasználására. Egyes virtuálisgép-sorozatok kiterjesztett támogatást biztosítanak az összes MPI-implementációhoz és RDMA-műveletekhez az SR-IOV-n keresztül. A RDMA-kompatibilis virtuális gépek a GPU-ra [optimalizált](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) és [nagy teljesítményű számítási (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) virtuális gépeket tartalmaznak.

## <a name="choose-your-installation-path"></a>Telepítési útvonal kiválasztása

Első lépésként a legegyszerűbb lehetőség, ha a InfiniBand előre konfigurált platform-rendszerképet használ, ahol elérhető:

- **HPC IaaS virtuális gépek** – a IaaS virtuális gépek HPC-hez való használatának megkezdéséhez a legegyszerűbb megoldás a [CentOS-HPC 7,6 VM operációsrendszer-rendszerkép](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)használata, amely már konfigurálva van a InfiniBand. Mivel ez a rendszerkép már konfigurálva van a InfiniBand-mel, nem kell manuálisan konfigurálnia. A kompatibilis Windows-verziókkal kapcsolatban lásd: [Windows RDMA-kompatibilis példányok](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **GPU IaaS-alapú virtuális gépek** – a rendszer jelenleg nem konfigurálja a platform lemezképeit a GPU-ra optimalizált virtuális gépekhez, kivéve a [CentOS-HPC 7,6 VM operációsrendszer-lemezképet](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Ha egyéni rendszerképet szeretne konfigurálni a InfiniBand, olvassa el a [Mellanox-OFED manuális telepítése](#manually-install-mellanox-ofed)című témakört.

Ha egyéni virtuálisgép-rendszerképet vagy [GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) -t használó virtuális gépet használ, konfigurálja a InfiniBand a InfiniBandDriverLinux vagy a InfiniBandDriverWindows virtuálisgép-bővítménynek az üzembe helyezéshez való hozzáadásával. Megtudhatja, hogyan használhatja ezeket a virtuálisgép-bővítményeket [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) és [Windows rendszereken](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Mellanox-OFED manuális telepítése

A InfiniBand az SR-IOV használatával történő manuális konfigurálásához kövesse az alábbi lépéseket. A fenti lépésekben szereplő példa a RHEL/CentOS szintaxisát mutatja be, de a lépések általánosak, és használhatók bármilyen kompatibilis operációs rendszerhez, például Ubuntu (16,04, 18,04 19,04) és SLES (12 SP4 és 15). A beérkezett fájlok illesztőprogramjai is működnek, de a Mellanox OpenFabrics-illesztőprogramok további funkciókat biztosítanak.

A Mellanox-illesztőprogram támogatott terjesztésével kapcsolatos további információkért tekintse meg a legújabb [Mellanox OpenFabrics-illesztőprogramokat](https://www.mellanox.com/page/products_dyn?product_family=26). További információ a Mellanox OpenFabrics-illesztőprogramról: [Mellanox felhasználói útmutató](https://docs.mellanox.com/category/mlnxofedib).

Tekintse meg a következő példát a InfiniBand konfigurálásához Linux rendszeren:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Windows rendszeren töltse le és telepítse a [Windows-illesztőprogramok MELLANOX OFED](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>IP-InfiniBand engedélyezése

A következő parancsokkal engedélyezheti az IP-címek InfiniBand való használatát.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>További lépések

További információ az Azure-beli [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) -ről.
