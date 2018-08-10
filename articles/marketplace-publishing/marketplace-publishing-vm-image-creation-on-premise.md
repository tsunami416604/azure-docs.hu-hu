---
title: Egy a helyszíni virtuális gép rendszerképének létrehozása az Azure Marketplace-en |} A Microsoft Docs
description: Ismerje meg, és hajtsa végre a lépéseket egy a helyszíni virtuális gép rendszerképének létrehozása és üzembe helyezése az Azure piactéren, mások számára meg kell vásárolnia.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c1e6e67ba7a1321ce58fbd58c173fa63dfa385e0
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715811"
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Egy a helyszíni virtuális gép rendszerképének fejlesztése az Azure Marketplace-en
Javasoljuk, hogy az Azure virtuális merevlemezeket (VHD) közvetlenül a felhőben fejleszteni az RDP protokoll. Azonban meg kell, hogy lehetővé teszi a virtuális merevlemez letöltése és fejleszthetők a helyszíni infrastruktúra használatával.  

A helyszíni fejlesztési le kell töltenie az operációs rendszer a létrehozott virtuális gép virtuális Merevlemezét. Ezeket a lépéseket kellene kerül sor 3.3,. lépés részeként a rendszer fent.  

## <a name="download-a-vhd-image"></a>Töltse le a VHD-rendszerképet
### <a name="locate-a-blob-url"></a>Keresse meg a blob URL-címe
Annak érdekében, hogy töltse le a VHD-t, keresse meg az operációsrendszer-lemez blob URL-CÍMÉT.

Keresse meg a blob URL-címet az új [Microsoft Azure-portálon](https://portal.azure.com):

1. Lépjen a **Tallózás** > **virtuális gépek**, és válassza ki a központilag telepített virtuális Gépet.
2. A **konfigurálása**, jelölje be a **lemezek** csempét, amely megnyitja a lemezek panel.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Válassza ki a **operációsrendszer-lemez**, amely megnyílik egy újabb panel, amely megjeleníti a lemez tulajdonságai, például a virtuális merevlemez helye.
4. A blob URL-Címének másolása.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Most törölje az üzembe helyezett virtuális gép biztonsági lemezek törlése nélkül. A virtuális gép helyett törlését is leállíthatja. Ha a virtuális gép fut, ne töltse le az operációs rendszer VHD-t.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>VHD letöltése
Ha már tudja, hogy a blob URL-címe, a virtuális merevlemez használatával letöltheti a [az Azure portal](http://manage.windowsazure.com/) vagy a PowerShell használatával.  

> [!NOTE]
> Ez az útmutató a létrehozás időpontjában a virtuális merevlemez letöltésére funkciót még nem szerepel az új Microsoft Azure Portalon.  
> 
> 

**Töltse le az operációs rendszer VHD-n keresztül az aktuális [Azure Portalon](http://manage.windowsazure.com/)**

1. Ha még nem meg már, jelentkezzen be az Azure Portalon.
2. Kattintson a **tárolási** fülre.
3. Válassza ki a tárfiókot, amelyen belül a VHD-t tárolja.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Ez megjeleníti a tárfiók tulajdonságai. Válassza ki a **tárolók** fülre.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Válassza ki a tárolót, amely a VHD-t tárolja. Alapértelmezés szerint létrehozása a portálról, a virtuális Merevlemezt a virtuális merevlemezek tárolása tárolóban történik.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Válassza ki a megfelelő operációs rendszer virtuális Merevlemeze összehasonlítja az elmentett URL-CÍMÉT.
7. Kattintson a **Letöltés** gombra.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>VHD letöltése a PowerShell használatával
Az Azure portal használata mellett is használhatja a [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) parancsmaggal letöltheti az operációs rendszer VHD-t.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Például Save-AzureVhd-forrás "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" - LocalFilePath "C:\Users\Administrator\Desktop\baseimagevm.vhd" - StorageKey tulajdonságát <String>

> [!NOTE]
> **Mentés-AzureVhd** is rendelkezik egy **NumberOfThreads** , amelyek segítségével növelheti a párhuzamosságot a lehető legjobban kihasználják a rendelkezésre álló sávszélesség a letöltés lehetőséget.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>VHD feltöltése az Azure storage-fiókba
Ha felkészült a VHD-k a helyszíni, feltölti őket az Azure storage-fiókra van szükség. Ez a lépés után a virtuális merevlemez létrehozása a helyszínen, de a Virtuálisgép-lemezkép minősítés beszerzése előtt történik.

### <a name="create-a-storage-account-and-container"></a>Hozzon létre egy tárfiókot és tárolót
Azt javasoljuk, hogy virtuális merevlemezek tölthető fel az Egyesült államokbeli régióban storage-fiókra. Az összes virtuális merevlemezek egyetlen Termékváltozat egyetlen a tárfiókban található egyetlen tárolóban kell elhelyezni.

Hozzon létre egy tárfiókot, használhatja a [Microsoft Azure-portálon](https://portal.azure.com/), a PowerShell vagy a Linux parancssori eszköz.  

**Storage-fiók létrehozása a Microsoft Azure Portalon**

1. Kattintson az **Erőforrás létrehozása** gombra.
2. Válassza a **Storage** lehetőséget.
3. Töltse ki a tárfiók nevét, és válassza ki a helyet.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Kattintson a **Create** (Létrehozás) gombra.
5. A panel a létrehozott tárfiók nyitva kell lennie. Ha nem, válassza ki a **Tallózás** > **Tárfiókok**. A Storage-fiók panelen válassza ki a létrehozott tárfiókot.
6. Válassza ki **tárolók**.
   
   ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. A tárolók panelen válassza ki a **Hozzáadás**, majd adja meg a tároló neve és a tároló engedélyeit. Válassza ki **privát** a tároló engedélyeit.

> [!TIP]
> Azt javasoljuk, hogy hozzon létre egy tároló, amely szeretne közzétenni egy.
> 
> 

  ![rajz](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Storage-fiók létrehozása PowerShell használatával
A PowerShell-lel, hozzon létre egy tárfiókot a [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) parancsmagot.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Létrehozhat egy tárolót, hogy a tárfiókban található használatával a [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) parancsmagot.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Ezen parancsok feltételezik, hogy a jelenlegi tárfiók környezetét már be van állítva a PowerShellben.   Tekintse meg [beállítása az Azure PowerShell-lel](marketplace-publishing-powershell-setup.md) a PowerShell beállítása a további részletekért.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Hozzon létre egy tárfiókot a Macre és Linuxra készült parancssori eszközzel
> A [Linux parancssori eszköz](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), hozzon létre egy tárfiókot a következőképpen.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Hozzon létre egy tárolót a következőképpen.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>VHD feltöltése
A tárfiók és tároló létrehozása után feltöltheti az előkészített virtuális merevlemezeket. Használhatja a PowerShell, a Linux parancssori eszközzel vagy más Azure Storage felügyeleti eszközök.

### <a name="upload-a-vhd-via-powershell"></a>PowerShell-lel VHD feltöltése
Használja a [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) parancsmagot.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>VHD feltöltése a Macre és Linuxra készült parancssori eszközzel
Az a [Linux parancssori eszköz](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), használja a következő: az azure virtuálisgép-lemezkép létrehozása <image name> --hely <Location of the data center> – operációs rendszer Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Lásd még
* [Egy virtuálisgép-rendszerképet hoz létre a Marketplace-en](marketplace-publishing-vm-image-creation.md)
* [Azure PowerShell telepítése](marketplace-publishing-powershell-setup.md)

