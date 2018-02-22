---
title: "A helyszíni virtuális gép lemezkép létrehozása a Azure piactérről |} Microsoft Docs"
description: "Ismerje meg, és hajtsa végre a lépéseket a helyszíni Virtuálisgép-lemezkép létrehozása és telepítése az Azure piactéren mások megvásárlásához."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 77771f1e690bdfb59d42989a34068634f35f845d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>A helyszíni virtuálisgép-lemezkép kialakított az Azure piactéren
Határozottan javasoljuk, hogy a távoli asztal protokoll használatával fejlesztése az Azure virtuális merevlemezeket (VHD) közvetlenül a felhőben. Azonban ha kell, akkor lehet töltse le a virtuális Merevlemezt és fejleszthetők a helyszíni infrastruktúra használatával.  

A helyi fejlesztési akkor le kell töltenie az operációs rendszer a létrehozott virtuális gép virtuális Merevlemezét. Ezeket a lépéseket akkor kerül sor lépésben 3.3-as, a rendszer fent.  

## <a name="download-a-vhd-image"></a>A Virtuálismerevlemez-kép letöltése
### <a name="locate-a-blob-url"></a>Keresse meg a blob URL-címe
Ahhoz, hogy töltse le a VHD-t, keresse meg az operációsrendszer-lemez blob URL-CÍMÉT.

Keresse meg a blob URL-CÍMÉT az új [Microsoft Azure-portálon](https://portal.azure.com):

1. Nyissa meg a **Tallózás** > **virtuális gépek**, majd válassza ki a központilag telepített virtuális gép.
2. A **konfigurálása**, jelölje be a **lemezek** csempe, a lemezek paneljének megnyitása, amelyen.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Válassza ki a **operációsrendszer-lemez**, amely megnyílik egy újabb panel, amely megjeleníti a lemez tulajdonságai, például a virtuális merevlemez helye.
4. A blob URL-Címének másolása.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Most törölje a központilag telepített virtuális gép biztonsági lemezek törlése nélkül. A virtuális gép törlése helyett is leállíthatja. Ne töltse le az operációs rendszer virtuális Merevlemezt a virtuális gép futása közben.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>VHD letöltése
Után tudja, hogy a blob URL-címet, a virtuális merevlemez segítségével letöltheti a [Azure-portálon](http://manage.windowsazure.com/) vagy a PowerShell használatával.  

> [!NOTE]
> Ez az útmutató létrehozásának időpontjában töltheti le a virtuális merevlemez funkció még nincs jelen az új Microsoft Azure-portálon.  
> 
> 

**Töltse le az operációs rendszer virtuális merevlemez a jelenlegi keresztül [Azure-portálon](http://manage.windowsazure.com/)**

1. Ha még nem meg már, jelentkezzen be az Azure-portálon.
2. Kattintson a **tárolási** fülre.
3. Válassza ki a tárfiók, amelyen belül a VHD-t tárolja.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Ez megjeleníti a tárfiók tulajdonságai. Válassza ki a **tárolók** fülre.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Válassza ki a tároló, amely a VHD-t tárolja. Alapértelmezés szerint a portálon létrehozott virtuális merevlemez tárolja a VHD-k tárolója.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Válassza ki a megfelelő operációs rendszer virtuális Merevlemeze összehasonlítva az Ön által mentett egy URL-CÍMÉT.
7. Kattintson a **Letöltés** gombra.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Töltse le a virtuális merevlemez PowerShell használatával
Az Azure portál használata mellett is használhatja a [mentés-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) parancsmag használatával töltheti le az operációs rendszer virtuális Merevlemezt.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
For example, Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> **Mentés-AzureVhd** is rendelkezik egy **NumberOfThreads** lehetőség, amely segítségével növelheti a párhuzamosságot a rendelkezésre álló sávszélesség ajánlott használhatják a letöltés.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Töltse fel a VHD-k a Azure-tárfiók
Felkészülés a VHD-k a helyszínen, ha szüksége feltöltésükhöz be egy Azure storage-fiókot. Ez a lépés után a virtuális merevlemez létrehozása a helyszínen, de a VM-lemezkép hitelesítő megszerzése előtt történik.

### <a name="create-a-storage-account-and-container"></a>A tárfiók és tároló létrehozása
Azt javasoljuk, hogy virtuális merevlemezek egy tárfiókot az Amerikai Egyesült Államokban régióban kell-e töltve. Minden virtuális merevlemez egyetlen termékváltozat egy tárfiókon belül egyetlen tárolót kell helyezni.

Hozzon létre egy tárfiókot, használhatja a [Microsoft Azure-portálon](https://portal.azure.com/), PowerShell vagy a Linux parancssori eszközt.  

**A storage-fiók létrehozása a Microsoft Azure-portálon**

1. Kattintson a **hozzon létre egy erőforrást**.
2. Válassza ki **tárolási**.
3. Töltse ki a tárfiók nevét, és válassza ki a helyet.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Kattintson a **Create** (Létrehozás) gombra.
5. A létrehozott tárfiók panel nyitva kell lennie. Ha nem, válassza ki a **Tallózás** > **Tárfiókok**. A Storage-fiók panelen válassza ki a létrehozott tárfiókban.
6. Válassza ki **tárolók**.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. A tárolók panelen válassza ki a **Hozzáadás**, majd adja meg a tároló neve és a tároló engedélyeit. Válassza ki **titkos** tároló engedélyek.

> [!TIP]
> Azt javasoljuk, hogy hozzon létre egy tároló, amely azt tervezi, hogy közzététele SKU /.
> 
> 

  ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>A storage-fiók létrehozása a PowerShell használatával
A PowerShell használatával hozzon létre egy tárfiókot a [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) parancsmag.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Ezután a tárfiókon belül tárolója használatával hozhat létre a [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) parancsmag.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Ezeknek a parancsoknak azt feltételezik, hogy az aktuális tárfiók környezetét már be van állítva a PowerShellben.   Tekintse meg [beállítása az Azure PowerShell](marketplace-publishing-powershell-setup.md) kapcsolatban további részleteket a PowerShell-telepítőt.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>A storage-fiók létrehozása a parancssori eszközzel a Mac és Linux
> A [Linux parancssori eszköz](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), az alábbiak szerint hozzon létre egy tárfiókot.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Az alábbiak szerint hozzon létre egy tárolót.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>VHD feltöltése
A tárfiók és tároló létrehozása után feltöltheti az előkészített virtuális merevlemezeket. Használhatja a PowerShell, a Linux parancssori eszköz vagy egyéb Azure Storage felügyeleti eszközöket.

### <a name="upload-a-vhd-via-powershell"></a>A PowerShell segítségével virtuális merevlemez feltöltéséhez
Használja a [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) parancsmag.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>A virtuális merevlemez feltöltéséhez a parancssori eszközzel a Mac és Linux
Az a [Linux parancssori eszköz](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), használja a következő: azure virtuálisgép-lemezkép létrehozása <image name> --hely <Location of the data center> – operációs rendszer Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Lásd még
* [Hozzon létre egy virtuálisgép-lemezkép a piactér](marketplace-publishing-vm-image-creation.md)
* [Azure PowerShell telepítése](marketplace-publishing-powershell-setup.md)

