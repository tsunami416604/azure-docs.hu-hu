---
title: Virtuális gép létrehozása az Azure-ban nem támogatott a VHD-ben | Microsoft Docs
description: Ez a cikk segít a VHD-hibák kijavításában a virtuális gépek Microsoft Azure-ben való futtatásakor.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: 23f31e8d36d6ba615b21a4043c7d39716fc6d89a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653494"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Virtuális gép Azure-beli létrehozásakor nem támogatott a VHD

Ez a cikk segít a VHD-hibák kijavításában a virtuális gépek Windows vagy Linux rendszeren való futtatásakor.

## <a name="symptoms"></a>Hibajelenségek

Amikor feltölt egy virtuális gépet Microsoft Azure egy feltöltött VHD használatával, a telepítés meghiúsul, és a következő hibaüzenetet adja vissza: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Ok

Ez a probléma a következő okok egyike miatt fordul elő:

- A VHD nem felel meg az 1 MB-os igazításnak (eltolásnak). A támogatott lemezméret 1 MB * N. A lemez mérete lehet például 102 401 MB.
- A VHD sérült vagy nem támogatott. 

## <a name="resolution"></a>Feloldás

> [!NOTE]
> A következő javítás végrehajtásához az ügyfélnek el kell végeznie ezeket a lépéseket, mielőtt feltölti a VHD-t az Azure-ba.

A probléma megoldásához méretezze át a lemezt úgy, hogy megfeleljen az 1 MB-os igazításnak:

- A Windowsban felmerülő probléma megoldásához használja a [Resize-VHD PowerShell-parancsmagot](/powershell/module/hyper-v/resize-vhd). Vegye figyelembe, hogy az **átméretezés – VHD** nem Azure PowerShell parancsmag.

  1. [A Hyper-V szerepkör telepítése a Windows Serveren](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [A virtuális lemez átalakítása rögzített méretű VHD-re](../windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)

- A Linux-probléma megoldásához használja a [QEMU-IMG parancsot](../linux/create-upload-generic.md).

Az Azure-beli virtuális gépek létrehozásával és feltöltésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Linux rendszerű virtuális gép feltöltése és létrehozása egyéni lemezképből az Azure CLI 1,0 használatával](../linux/upload-vhd.md)
- [Windows Server virtuális merevlemez létrehozása és feltöltése az Azure alkalmazásba](../windows/upload-generalized-managed.md)

A folyamatos problémák megsérült VHD-ket jelezhetnek. Ebben az esetben javasoljuk, hogy a virtuális merevlemezt teljesen újra létrehozza.

További információkat a következő cikkekben talál:

- [Tudnivalók a VHD-ről](../managed-disks-overview.md)
