---
title: Linux-lemezképek hozzáadása Azure verem
description: Megtudhatja, hogyan Linux lemezképek hozzáadása Azure verem.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Linux-lemezképek hozzáadása Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A Linux-alapú lemezkép az Azure-verem piactéren hozzáadásával telepítheti Linux virtuális gépek (VM) Azure veremben. A Linux-lemezkép hozzáadása Azure verem legkönnyebben piactér felügyelet használatával. Ezeket a lemezképeket készíteni, és tesztelt Azure verem való kompatibilitás érdekében.

## <a name="marketplace-management"></a>Piactér-kezelés

Az Azure piactérről Linux képek letöltéséhez az eljárásokkal a következő cikkben található. Válassza ki a kívánt felhasználók számára az Azure Linux lemezképeket. 

[Töltse le a Piactéri elemek az Azure-ból Azure verem](azure-stack-download-azure-marketplace-item.md).

Vegye figyelembe, hogy vannak-e ezek a lemezképek gyakori frissítéseit, ezért a piactér felügyeleti gyakran naprakész állapotban tartása érdekében ellenőrizze.

## <a name="prepare-your-own-image"></a>A saját lemezkép előkészítése

 Ahol lehetséges, töltse le a piactér kezelhetőségét, ami elő és tesztelt Azure verem keresztül elérhető lemezképeket. 
 
 Az Azure Linux ügynök (általában nevezik `WALinuxAgent` vagy `walinuxagent`) kell megadni, és az ügynök nem minden verziói működnek az Azure-veremben. Használjon 2.2.18 verzió vagy újabb, ha saját rendszerkép létrehozása. Vegye figyelembe, hogy [felhő inicializálás](https://cloud-init.io/) Azure veremben jelenleg nem támogatott.

 Előkészítheti a saját Linux kép a következő útmutatás szerint:

   * [CentOS-alapú Disztribúciók](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>A lemezkép hozzáadása a piactér
 
Hajtsa végre a [adja hozzá a piactér](azure-stack-add-vm-image.md). Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.

A lemezkép hozzáadása a piactéren, után a Piactéri elemet létrejön, és felhasználók telepítheti egy Linux virtuális gép.
