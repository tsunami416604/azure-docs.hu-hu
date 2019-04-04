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
ms.openlocfilehash: c957812271d2ddd6639672d862026b30cfd19661
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804513"
---
# <a name="add-linux-images-to-azure-stack"></a>Linux-lemezképek hozzáadása az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stacken Linux rendszerű virtuális gépek (VM) telepíthet egy linuxos rendszerképet az Azure Stack piactéren hozzáadásával. A Linux-rendszerképen hozzáadása az Azure Stackhez legegyszerűbben Marketplace Management szolgáltatáson keresztül. Ezeket a lemezképeket készíteni, és tesztelni az Azure Stackkel való kompatibilitás érdekében.

## <a name="marketplace-management"></a>Marketplace-en kezelése

Töltse le a Linux-rendszerképeket az Azure Marketplace-ről, kövesse a a [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](azure-stack-download-azure-marketplace-item.md) cikk. Válassza ki, hogy a felhasználók számára az Azure Stack kívánt Linux-lemezképekhez. 

Vegye figyelembe, hogy nincsenek-e képek gyakori frissítések, ezért érdemes ellenőrizni a Marketplace-en felügyeleti gyakran és naprakész állapotban tarthatja.

## <a name="prepare-your-own-image"></a>A saját lemezkép előkészítése

Amikor csak lehetséges, töltse le a lemezkép érhető el, amely készített, és tesztelni az Azure Stack piactéren Management szolgáltatáson keresztül.

Az Azure Linux-ügynök (általános nevén `WALinuxAgent` vagy `walinuxagent`) szükség, és az ügynök nem minden verzióinak fog működni az Azure Stacken. A legújabb WALA vagy verziója 2.2.20 saját rendszerkép létrehozásakor használjon. Vegye figyelembe, hogy 2.2.20 és 2.2.35.1 (exkluzív) verziói nem működnek az Azure Stacken. Használja az ügynökök verzióinak 2.2.20 és 2.2.35.1 között, alkalmazza a 1901 gyorsjavítás/1902, vagy frissítse az Azure Stack a 1903 (vagy újabb). Vegye figyelembe, hogy [a cloud-init](https://cloud-init.io/) jelenleg nem támogatott az Azure Stacken.

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
