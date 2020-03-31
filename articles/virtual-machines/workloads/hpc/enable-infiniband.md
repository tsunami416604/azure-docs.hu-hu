---
title: Az InifinBand engedélyezése SR-IOV -val - Azure virtuális gépek | Microsoft dokumentumok
description: További információ az InfiniBand sr-iov-val való engedélyezéséről.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196768"
---
# <a name="enable-infiniband-with-sr-iov"></a>Az InfiniBand engedélyezése SR-IOV-val

Az Azure NC, ND és H sorozatú virtuális gépek egy dedikált InfiniBand-hálózat tal vannak támogatva. Minden RDMA-kompatibilis méret képes a hálózat intel MPI használatával történő kihasználására. Néhány Virtuálisgép-sorozat bővítette az összes MPI implementáció és RDMA-ige támogatását az SR-IOV-on keresztül. Az RDMA-képes virtuális gépek [GPU-ra optimalizált](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) és [nagy teljesítményű számítási (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) virtuális gépeket tartalmaznak.

## <a name="choose-your-installation-path"></a>Válassza ki a telepítési útvonalat

A kezdéshez a legegyszerűbb megoldás az InfiniBand számára előre konfigurált platformlemezkép használata, ahol elérhető:

- **HPC IaaS virtuális gépek** – Az IaaS virtuális gépek HPC-hez való használatának megkezdéséhez a legegyszerűbb megoldás a [CentOS-HPC 7.6 VM OS lemezkép](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)használata, amely már konfigurálva van az InfiniBand-el. Mivel ez a lemezkép már konfigurálva van az InfiniBand-tel, nem kell manuálisan konfigurálnia. A kompatibilis [Windows-verziókról a Windows RDMA-kompatibilis példányai című témakörben találhatók.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

- **GPU IaaS virtuális gépek** – Jelenleg nincsenek platformképek előre konfigurálva a GPU-ra optimalizált virtuális gépekhez, kivéve a [CentOS-HPC 7.6 VM OS lemezképet.](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) Ha egyéni lemezképet szeretne beállítani az InfiniBand segítségével, olvassa el a [Mellanox OFED kézi telepítése című témakört.](#manually-install-mellanox-ofed)

Ha egyéni virtuálisgép-lemezképet vagy [GPU-ra optimalizált](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) virtuális gépet használ, konfigurálja azt az InfiniBand-tel az InfiniBandDriverLinux vagy az InfiniBandDriverWindows VM bővítmény központi telepítéséhez való hozzáadásával. Ismerje meg, hogyan használhatja ezeket a virtuálisgép-bővítményeket [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) és [Windows rendszerben.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

## <a name="manually-install-mellanox-ofed"></a>A Mellanox OFED kézi telepítése

Az InfiniBand SR-IOV-val történő manuális konfigurálásához kövesse az alábbi lépéseket. Az ezekben a lépésekben látható példa az RHEL/CentOS szintaxisát mutatja, de a lépések általánosak, és bármely kompatibilis operációs rendszerhez, például az Ubuntuhoz (16.04, 18.04 19.04) és az SLES-hez (12 SP4 és 15) használhatók. A beérkező illesztőprogramok is működnek, de a Mellanox OpenFabrics illesztőprogramok további funkciókat biztosítanak.

A Mellanox illesztőprogram támogatott disztribúcióiról a legújabb [Mellanox OpenFabrics illesztőprogramokat](https://www.mellanox.com/page/products_dyn?product_family=26)talál. A Mellanox OpenFabrics illesztőprogramról további információt a [Mellanox felhasználói útmutatóban talál.](https://docs.mellanox.com/category/mlnxofedib)

Az InfiniBand linuxos konfigurálásának következő példáját az alábbi példában láthatja:

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

Windows esetén töltse le és telepítse a [Mellanox OFED for Windows-illesztőprogramokat.](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>IP engedélyezése infiniband-en keresztül

Az alábbi parancsokkal engedélyezheti az IP-címet az InfiniBand-en keresztül.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>További lépések

További információ az Azure-beli [HPC-ről.](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)
