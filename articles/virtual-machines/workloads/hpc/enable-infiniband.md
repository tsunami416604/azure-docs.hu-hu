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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858483"
---
# <a name="enable-infiniband-with-sr-iov"></a>InfiniBand engedélyezése SR-IOV

A HPC-hez készült IaaS virtuális gépek használatának legegyszerűbb és ajánlott módja a CentOS-HPC 7,6 VM OS-rendszerkép használata. Ha az egyéni virtuálisgép-rendszerképet használja, a legegyszerűbben úgy konfigurálhatja a InfiniBand (IB), hogy hozzáadja a InfiniBandDriverLinux vagy a InfiniBandDriverWindows virtuálisgép-bővítményt a központi telepítéshez.
Ismerje meg, hogyan használhatja ezeket a virtuálisgép-bővítményeket [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) és [Windows rendszereken](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Ha manuálisan szeretné konfigurálni az InfiniBand-t az SR-IOV-kompatibilis virtuális gépeken (jelenleg HB és HC sorozat), kövesse az alábbi lépéseket. Ezek a lépések csak a RHEL/CentOS esetében használhatók. Ubuntu (16,04 és 18,04) és SLES (12 SP4 és 15) esetében a beérkezett fájlok illesztőprogramjai jól működnek.

## <a name="manually-install-ofed"></a>A OFED manuális telepítése

Telepítse a ConnectX-5 legújabb MLNX_OFED-illesztőprogramjait a [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)-ről.

RHEL/CentOS esetén (például 7,6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Windows rendszeren töltse le és telepítse a ConnectX-5 WinOF-2 illesztőprogramjait a [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>IPoIB engedélyezése

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>IP-cím kiosztása

Rendeljen hozzá egy IP-címet az ib0 felülethez a következők használatával:

- Rendeljen hozzá manuálisan IP-címet a ib0 felületéhez (root-ként).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

VAGY

- A WALinuxAgent használatával rendeljen hozzá IP-címet, és gondoskodjon arról, hogy megmaradjon.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>További lépések

További információ az Azure-beli [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) -ről.
