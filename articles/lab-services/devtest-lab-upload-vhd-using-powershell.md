---
title: VHD-fájl feltöltése a PowerShell-lel az Azure DevTest Labs |} A Microsoft Docs
description: VHD-fájl feltöltése PowerShell használatával tesztkörnyezet tárfiókba
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326528"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>VHD-fájl feltöltése PowerShell használatával tesztkörnyezet tárfiókba

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Az Azure DevTest Labs szolgáltatásban a VHD-fájlok létrehozása az egyéni lemezképek, amelyek segítségével a virtuális gépek üzembe helyezése használható. A következő lépések végigvezetik egy tesztkörnyezet egy storage-fiók egy VHD-fájl feltöltése a PowerShell használatával. Miután feltöltötte a VHD-fájl a [további lépések szakaszt](#next-steps) felsorolja az egyes cikkeket, amelyek bemutatják, hogyan hozhat létre egy egyéni rendszerképet a feltöltött VHD-fájlból. Lemezek és virtuális merevlemezek, az Azure-ban kapcsolatos további információkért lásd: [Bevezetés a managed Disks szolgáltatásba](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik egy VHD-fájl feltöltése a PowerShell-lel az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  

1. A labor paneljén válassza ki a **konfigurációs**. 

1. A laborgyakorlat **konfigurációs** panelen válassza ki **egyéni lemezképeket (VHD)**.

1. Az a **egyéni lemezképek** panelen válasszon ki **+ Hozzáadás**. 

1. Az a **egyéni lemezkép** panelen válassza ki **VHD**.

1. Az a **VHD** panelen válassza ki **Nahrát VHD pomocí PowerShell**.

    ![Töltse fel a VHD pomocí Powershellu](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Az a **Nahrát image pomocí PowerShell** panelen másolja ki a létrehozott PowerShell-szkript egy szövegszerkesztőbe.

1. Módosítsa a **LocalFilePath** paraméterében a **Add-AzureVhd** parancsmagot, hogy a feltölteni kívánt VHD-fájl helyére mutasson.

1. Futtassa a következő PowerShell-parancssorból, a **Add-AzureVhd** parancsmag (és a módosított **LocalFilePath** paraméter).

> [!WARNING] 
> 
> A VHD-fájlt, és az átviteli sebesség méretétől függően a folyamat egy VHD-fájl feltöltése megnőhet.

## <a name="next-steps"></a>További lépések

- [Egy egyéni lemezkép készítése VHD-fájlból az Azure portal segítségével Azure DevTest Labs szolgáltatásban](devtest-lab-create-template.md)
- [Egyéni rendszerkép létrehozása a PowerShell-lel VHD-fájlból az Azure DevTest Labs szolgáltatásban](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
