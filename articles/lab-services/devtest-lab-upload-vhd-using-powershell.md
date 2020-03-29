---
title: VHD-fájl feltöltése az Azure DevTest Labs alkalmazásba a PowerShell használatával | Microsoft dokumentumok
description: Ez a cikk egy forgatókönyvet, amely bemutatja, hogyan tölthet fel egy virtuális merevlemez-fájlt az Azure DevTest Labs a PowerShell használatával.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759755"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>VHD-fájl feltöltése a labor tárfiókjába a PowerShell használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Az Azure DevTest Labs, VHD-fájlok at lehet használni, hogy egyéni lemezképek, amelyek a virtuális gépek kiépítése. A következő lépések végigvezeti a PowerShell segítségével egy VHD-fájl feltöltése egy tesztkörnyezet tárfiókába. Miután feltöltötte a vhd fájlt, a [Következő lépések szakasz](#next-steps) ban néhány cikket, amelyek bemutatják, hogyan lehet létrehozni egy egyéni képet a feltöltött VHD-fájlból. Az Azure-beli lemezekről és virtuális merevlemezekről a [Felügyelt lemezek – Bevezetés című témakörben](../virtual-machines/linux/managed-disks-overview.md) talál további információt.

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy Virtuális merevlemez-fájl feltöltésén az Azure DevTest Labs-be a PowerShell használatával. 

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor paneljén válassza a **Konfiguráció**lehetőséget. 

1. A labor **konfigurációpanelen** válassza **az Egyéni lemezképek (VD-k)** lehetőséget.

1. Az **Egyéni képek** panelen válassza a **+Hozzáadás**lehetőséget. 

1. Az **Egyéni kép** panelen válassza a **VHD**lehetőséget.

1. A **VHD** panelen válassza **a VHD feltöltése a PowerShell használatával lehetőséget.**

    ![Virtuális merevlemez feltöltése a PowerShell használatával](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. A **PowerShell-panel használatával egy kép feltöltése,** másolja a létrehozott PowerShell-parancsfájlt egy szövegszerkesztőbe.

1. Módosítsa az **Add-AzureVhd** parancsmag **LocalFilePath** paraméterét, hogy a feltöltendő Virtuális merevlemez-fájl helyére mutasson.

1. Egy PowerShell-parancssorban futtassa az **Add-AzureVhd** parancsmast (a módosított **LocalFilePath-paraméterrel).**

> [!WARNING] 
> 
> A VHD-fájl feltöltésének folyamata a VHD fájl méretétől és a kapcsolat sebességétől függően hosszadalmas lehet.

## <a name="next-steps"></a>További lépések

- [Egyéni lemezkép létrehozása az Azure DevTest Labs ben egy virtuális merevlemez-fájlból az Azure Portalhasználatával](devtest-lab-create-template.md)
- [Egyéni lemezkép létrehozása az Azure DevTest Labs ben egy Virtuális merevlemez-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
