---
title: Linux-lemezképek hozzáadása az Azure Stackhez
description: Ismerje meg, hogyan adja hozzá a Linux-rendszerképeket az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 8e30edcc7a600088693de57264665f5ffff3842a
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300737"
---
# <a name="add-linux-images-to-azure-stack"></a>Linux-lemezképek hozzáadása az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stacken Linux rendszerű virtuális gépek (VM) telepíthet egy linuxos rendszerképet az Azure Stack piactéren hozzáadásával. A Linux-rendszerképen hozzáadása az Azure Stackhez legegyszerűbben Marketplace Management szolgáltatáson keresztül. Ezeket a lemezképeket készíteni, és tesztelni az Azure Stackkel való kompatibilitás érdekében.

## <a name="marketplace-management"></a>Marketplace-en kezelése

Töltse le a Linux-rendszerképeket az Azure Marketplace-ről, kövesse a a [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](azure-stack-download-azure-marketplace-item.md) cikk. Válassza ki, hogy a felhasználók számára az Azure Stack kívánt Linux-lemezképekhez. 

Vegye figyelembe, hogy nincsenek-e képek gyakori frissítések, ezért érdemes ellenőrizni a Marketplace-en felügyeleti gyakran és naprakész állapotban tarthatja.

## <a name="prepare-your-own-image"></a>A saját lemezkép előkészítése

Amikor csak lehetséges, töltse le a lemezkép érhető el, amely készített, és tesztelni az Azure Stack piactéren Management szolgáltatáson keresztül.

Az Azure Linux-ügynök (általános nevén `WALinuxAgent` vagy `walinuxagent`) van az ügynök munka az Azure Stacken szükséges, és nem minden verzióit. Használjon 2.2.20 verzió vagy újabb, ha hoz létre a saját rendszerképét. Vegye figyelembe, hogy [a cloud-init](https://cloud-init.io/) jelenleg nem támogatott az Azure Stacken.

Saját Linux-rendszerképek, az alábbi utasítások segítségével készítheti elő:

* [CentOS-alapú Disztribúciókon](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>A lemezkép felvétele a Marketplace-en

Hajtsa végre a [adja hozzá a lemezképet a Marketplace-en](azure-stack-add-vm-image.md). Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.

A lemezképet a Marketplace-en való felvételét, Piactéri elem jön létre, és a felhasználók üzembe helyezhetnek egy Linux rendszerű virtuális gépet.

## <a name="next-steps"></a>További lépések

További információ a következő cikkekben talál:

- [Az Azure marketplace-elemek letöltése az Azure Stackhez](azure-stack-download-azure-marketplace-item.md)
- [Az Azure Stack piactéren – áttekintés](azure-stack-marketplace.md)
