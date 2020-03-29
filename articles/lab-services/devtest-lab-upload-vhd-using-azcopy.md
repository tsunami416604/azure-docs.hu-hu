---
title: VHD-fájl feltöltése az Azure DevTest Labs alkalmazásba az AzCopy használatával | Microsoft dokumentumok
description: Ez a cikk egy forgatókönyvet az AzCopy parancssori segédprogram használatával egy VHD-fájl feltöltéséhez egy tesztkörnyezet tárfiókjába az Azure DevTest Labs.This article provides a walkthrough to use the AzCopy command-line utility to upload a VHD file to a lab's storage account in Azure DevTest Labs.
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
ms.openlocfilehash: 49dc70788bf2a44b6925c5f3f8226fdadab8768c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757422"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>VHD-fájl feltöltése a labor tárfiókjába az AzCopy használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Az Azure DevTest Labs, VHD-fájlok at lehet használni, hogy egyéni lemezképek, amelyek a virtuális gépek kiépítése. A következő lépések végigvezeti kutatazon az AzCopy parancssori segédprogrammal, hogy egy VHD-fájlt töltsön fel egy tesztkörnyezet tárfiókjába. Miután feltöltötte a vhd fájlt, a [Következő lépések szakasz](#next-steps) ban néhány cikket, amelyek bemutatják, hogyan lehet létrehozni egy egyéni képet a feltöltött VHD-fájlból. Az Azure-beli lemezekről és virtuális merevlemezekről a [Felügyelt lemezek – Bevezetés című témakörben](../virtual-machines/linux/managed-disks-overview.md) talál további információt.

> [!NOTE] 
>  
> AzAzCopy egy csak Windows rendszerű parancssori segédprogram.

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy Virtuális merevlemez-fájl feltöltésén az Azure DevTest Labs alkalmazásba az [AzCopy](https://aka.ms/downloadazcopy)használatával. 

1. A labor tárfiókjának nevének beszereznie az Azure Portalhasználatával:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor paneljén válassza a **Konfiguráció**lehetőséget. 

1. A labor **konfigurációpanelen** válassza **az Egyéni lemezképek (VD-k)** lehetőséget.

1. Az **Egyéni képek** panelen válassza a **+Hozzáadás**lehetőséget. 

1. Az **Egyéni kép** panelen válassza a **VHD**lehetőséget.

1. A **VHD** panelen válassza **a VHD feltöltése a PowerShell használatával lehetőséget.**

    ![Virtuális merevlemez feltöltése a PowerShell használatával](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. A **PowerShell-panel használatával egy kép feltöltése** megjeleníti az **Add-AzureVhd** parancsmag hívását. Az első paraméter (*Cél*) a blobtároló (*feltöltések*) URI-ját tartalmazza a következő formátumban:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Jegyezze fel a teljes URI-t, ahogy azt a későbbi lépésekben használja.

1. Töltse fel a VHD fájlt az AzCopy használatával:
 
1. [Töltse le és telepítse az AzCopy legújabb verzióját.](https://aka.ms/downloadazcopy)

1. Nyisson meg egy parancsablakot, és keresse meg az AzCopy telepítési könyvtárát. Szükség esetén hozzáadhatja az AzCopy telepítési helyét a rendszer elérési úthoz. Alapértelmezés szerint az AzCopy a következő könyvtárba van telepítve:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. A tárfiók kulcsés a blob tároló URI használatával futtassa a következő parancsot a parancssorban. A *vhdFileName* értéknek idézőjelek között kell lennie. A VHD-fájl feltöltésének folyamata a VHD fájl méretétől és a kapcsolat sebességétől függően hosszadalmas lehet.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>További lépések

- [Egyéni lemezkép létrehozása az Azure DevTest Labs ben egy virtuális merevlemez-fájlból az Azure Portalhasználatával](devtest-lab-create-template.md)
- [Egyéni lemezkép létrehozása az Azure DevTest Labs ben egy Virtuális merevlemez-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)