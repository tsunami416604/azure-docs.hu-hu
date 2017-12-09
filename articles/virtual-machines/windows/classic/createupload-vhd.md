---
title: "Létrehozása és feltöltése a Powershell használatával Virtuálisgép-lemezkép |} Microsoft Docs"
description: "Ismerje meg, hozzon létre, és töltse fel az általános Windows Server képet (VHD) a klasszikus üzembe helyezési modellel és az Azure PowerShell használatával."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: fa672fd7811e68368c311ef6a3f57eac4b240a4a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Windows Server-alapú VHD létrehozása és feltöltése az Azure-ba
Ez a cikk bemutatja, hogyan saját általánosított Virtuálisgép-lemezkép feltöltése a virtuális merevlemez (VHD), tehát a virtuális gépek létrehozásához használható. A lemezek és a Microsoft Azure virtuális merevlemezek kapcsolatos további tudnivalókért lásd: [kapcsolatos lemezek és a virtuális merevlemezek a virtuális gépek](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [feltöltése](../upload-generalized-managed.md) egy virtuális gép Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* **Azure-előfizetés** – Ha még nincs fiókja, akkor [szabad nyissa meg az Azure-fiók](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[A Microsoft Azure PowerShell](/powershell/azure/overview)**  -van a Microsoft Azure PowerShell-modul telepítése és konfigurálása az előfizetés használatára.
* **A. VHD-fájl** - támogatott Windows operációs rendszer egy .vhd fájl tárolja, és a virtuális géphez csatlakozik. Ellenőrizze, hogy ha a kiszolgálói szerepkörök fut a virtuális Merevlemezt a Sysprep által támogatott. További információkért lásd: [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > A VHDX formátum nem támogatott a Microsoft Azure-ban. A lemez VHD formátumú Hyper-V kezelőjével konvertálhatja vagy a [Convert-VHD parancsmag](http://technet.microsoft.com/library/hh848454.aspx). További információkért lásd: a [blogbejegyzés](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>1. lépés: A virtuális merevlemez előkészítése
A VHD-fájlt feltölti az Azure-ba, mielőtt kell a Sysprep eszközzel általánosítva. Ezzel előkészíti a VHD-képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx). Készítsen biztonsági másolatot a virtuális Gépet a Sysprep futtatása előtt.

A virtuális gépen, amely az operációs rendszer telepítve lett kövesse az alábbi lépéseket:

1. Jelentkezzen be az operációs rendszer.
2. Nyisson meg egy parancssori ablakot rendszergazdaként. Lépjen be **%windir%\system32\sysprep**, majd futtassa a `sysprep.exe`.

    ![Nyisson meg egy parancssori ablakot](./media/createupload-vhd/sysprep_commandprompt.png)
3. A **rendszer-előkészítő eszköz** párbeszédpanel jelenik meg.

   ![Indítsa el a Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. Az a **rendszer-előkészítő eszköz**, jelölje be **meg rendszer kívüli kezdőélmény (OOBE)** , és győződjön meg arról, hogy **Generalize** be van jelölve.
5. A **leállítási beállítások**, jelölje be **leállítási**.
6. Kattintson az **OK** gombra.

## <a name="step-2-create-a-storage-account-and-a-container"></a>2. lépés: A tárfiók és a tároló létrehozása
Egy Azure storage-fiókot kell, hogy jogosult a hely, a .vhd fájl feltöltéséhez. Ez a lépés bemutatja, hogyan hozzon létre egy fiókot, vagy kérjen a szükséges információ egy meglévő fiókkal. Cserélje le a változók &lsaquo; zárójeleket &rsaquo; a saját adataival.

1. Bejelentkezés

    ```powershell
    Add-AzureAccount
    ```

2. Állítsa be az Azure-előfizetéshez.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Hozzon létre egy új tárfiókot. A tárfiók neve egyedi, kell lennie 3-24 karakterből állhat. A név betűk és számok tetszőleges kombinációja lehet. Is meg kell adnia egy helyen, például az "Amerikai keleti"

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Az új tárfiók állítja be az alapértelmezett.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Hozzon létre egy új tároló.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>3. lépés: A .vhd fájl feltöltése
Használja a [Add-AzureVhd](https://docs.microsoft.com/powershell/module/azure/add-azurevhd) a virtuális merevlemez feltöltéséhez.

Az előző lépésben használt Azure PowerShell-ablakot, írja be a következő parancsot, és cserélje le a változók &lsaquo; zárójeleket &rsaquo; a saját adataival.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>4. lépés: Adja hozzá az egyéni lemezképek listája
Használja a [Add-AzureVMImage](https://docs.microsoft.com/powershell/module/azure/add-azurevmimage) parancsmag használatával adja hozzá az egyéni lemezképek listáját.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Következő lépések
Most [hozzon létre egy egyéni virtuális Gépet](createportal.md) a feltöltött lemezkép használata.
