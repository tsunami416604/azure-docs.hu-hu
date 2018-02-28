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
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Linux-lemezképek hozzáadása Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A Linux-alapú lemezkép az Azure-verem piactéren hozzáadásával telepítheti Linux virtuális gépek (VM) Azure veremben. A Linux-lemezkép hozzáadása Azure verem legkönnyebben piactér felügyelet használatával. Ezeket a lemezképeket készíteni, és tesztelt Azure verem való kompatibilitás érdekében.

## <a name="marketplace-management"></a>Piactér-kezelés

Az Azure piactérről Linux képek letöltéséhez az eljárásokkal a következő cikkben található. Válassza ki a kívánt felhasználók számára az Azure Linux lemezképeket.

[Töltse le a Piactéri elemek az Azure-ból Azure verem](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>A saját lemezkép előkészítése

Előkészítheti a saját Linux kép a következő utasítások egyikének használatával:

   * [CentOS-alapú Disztribúciók](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Töltse le és telepítse a [Azure Linux ügynök](https://github.com/Azure/WALinuxAgent/).

    Az Azure Linux ügynök verziója 2.2.2 vagy kiépítését Azure verem és egyes verziói a Linux virtuális gép nem működik (2.2.12 és 2.2.13 példákat) szükséges. A felsorolt terjesztési számos korábban már tartalmazza az ügynök verzióját (általában nevezik `WALinuxAgent` vagy `walinuxagent`). Ha létrehoz egy egyéni lemezképet, telepítenie kell az ügynököt manuálisan. A telepített verzió is meg lehet határozni a csomag neve, vagy futtassa a `/usr/sbin/waagent -version` a virtuális Gépen.

    Kövesse az alábbi utasításokat a Azure Linux ügynök manuális - telepítése

   a. Első lépésként töltse le a legújabb Azure Linux ügynök a [GitHub](https://github.com/Azure/WALinuxAgent/releases), például:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. Bontsa ki az Azure-ügynököt:

            # tar -vzxf v2.2.21.tar.gz
   c. Python-setuptools telepítése

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Az Azure-ügynök telepítése:

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     Rendszerek a Python 2.x és a Python 3.x telepítve-mellé kell futtatni a következő parancsot:

         sudo python3 setup.py install --register-service
     További információkért lásd: az Azure Linux ügynök [információs](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Adja hozzá a piactér](azure-stack-add-vm-image.md). Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.
3. A lemezkép hozzáadása a piactéren, után a Piactéri elemet létrejön, és felhasználók telepítheti egy Linux virtuális gép.

## <a name="next-steps"></a>További lépések
[Az ajánlat Azure verem szolgáltatások áttekintése](azure-stack-offer-services-overview.md)
