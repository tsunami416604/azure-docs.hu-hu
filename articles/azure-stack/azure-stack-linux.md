---
title: "Linux-lemezképek hozzáadása Azure verem"
description: "Megtudhatja, hogyan Linux lemezképek hozzáadása Azure verem."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: anajod
ms.openlocfilehash: 29e5443de4dc43efe6d536b0f8b9cfc6ad37a669
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Linux-lemezképek hozzáadása Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet* 

Linux virtuális gépek Azure-veremben telepítését Linux-alapú lemezkép hozzáadása a verem Azure piactéren. A Linux-lemezkép hozzáadása Azure verem legkönnyebben piactér felügyelet használatával.

## <a name="marketplace-management"></a>Piactér-kezelés

Az Azure piactérről Linux képek letöltéséhez az eljárásokkal a következő cikkben található. Válassza ki a kívánt felhasználók számára az Azure Linux lemezképeket.

[Töltse le a Piactéri elemek az Azure-ból Azure verem](azure-stack-download-azure-marketplace-item.md).

## <a name="download-an-image"></a>Kép letöltése

Töltse le, és bontsa ki az Azure verem-kompatibilis Linux képek a következő hivatkozásokkal:


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. Ha szükséges, bontsa ki a kép VHD és [adja hozzá a piactér](azure-stack-add-vm-image.md). Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.
2. A lemezkép hozzáadása a piactéren, után a Piactéri elemet létrejön, és felhasználók telepítheti egy Linux virtuális gép.

## <a name="prepare-your-own-image"></a>A saját lemezkép előkészítése

Előkészítheti a saját Linux kép a következő utasítások egyikének használatával:
   
   * [CentOS-alapú Disztribúciók](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Töltse le és telepítse a [Azure Linux ügynök](https://github.com/Azure/WALinuxAgent/).
   
    Az Azure Linux ügynök verziója 2.1.3 vagy a Linux virtuális gép Azure veremben telepítéséhez szükséges. A felsorolt terjesztési számos korábban már szerepeljenek a verziója, az ügynök vagy csomag újabb saját adattárak (általában nevezik `WALinuxAgent` vagy `walinuxagent`). Azonban ha az Azure-ügynököt csomag verziószáma kisebb, mint 2.1.3 (például 2.0.18 vagy alsó), akkor az ügynököt manuálisan kell telepítenie. A telepített verzió is meg lehet határozni futtatásával vagy a csomag neve `/usr/sbin/waagent -version` a virtuális Gépen.
   
    Kövesse az alábbi utasításokat a Azure Linux ügynök manuális - telepítése
   
   a. Első lépésként töltse le a legújabb Azure Linux ügynök a [GitHub](https://github.com/Azure/WALinuxAgent/releases), például:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Bontsa ki az Azure-ügynököt:
     
            # tar -vzxf v2.2.16.tar.gz
   c. Python-setuptools telepítése
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Az Azure-ügynök telepítése:
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     Rendszerek a Python 2.x és a Python 3.x telepítve-mellé kell futtatni a következő parancsot:
     
         sudo python3 setup.py install --register-service
     További információkért lásd: az Azure Linux ügynök [információs](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Adja hozzá a piactér](azure-stack-add-vm-image.md). Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.
3. A lemezkép hozzáadása a piactéren, után a Piactéri elemet létrejön, és felhasználók telepítheti egy Linux virtuális gép.

## <a name="next-steps"></a>További lépések
[Az ajánlat Azure verem szolgáltatások áttekintése](azure-stack-offer-services-overview.md)

