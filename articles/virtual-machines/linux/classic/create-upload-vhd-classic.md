---
title: "Hozzon létre, és a Linux virtuális merevlemez feltöltéséhez az Azure-bA |} Microsoft Docs"
description: "Létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza a Linux operációs rendszert, a klasszikus telepítési modell segítségével"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
ms.openlocfilehash: 49cf4f1718e4dce1e86aa3c8921eaa8af5f16192
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Linux operációs rendszert tartalmazó virtuális merevlemez létrehozása és feltöltése
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [feltöltése az Azure Resource Manager használatával egyéni lemezképet](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ez a cikk bemutatja, hogyan hozhat létre, és töltse fel a virtuális merevlemez (VHD), használhatja a saját lemezképként az Azure virtuális gépek létrehozásához. Ismerje meg, hogyan készíti elő az operációs rendszer, így a lemezkép alapján több virtuális gép létrehozásához használhatja. 


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik-e a következő elemek:

* **Linux operációs rendszer van telepítve, a .vhd-fájllá** -telepítette egy [Azure által támogatott Linux-disztribúció](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy lásd: [nem támogatott disztribúciókkal kapcsolatos információi](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) a virtuális lemezre a VHD formátummal. Több különféle eszköz létezik a virtuális gép és a virtuális merevlemez létrehozásához:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a virtuális merevlemez használata a képformátum. Ha szükséges, akkor [lemezkép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával `qemu-img convert`.
  * Is használhatja a Hyper-V [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [Windows Server 2012 vagy 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg a VHD formátumban. Szükség esetén, VHDX-lemezek konvertálása virtuális merevlemez használatával [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltése, ezért ilyen lemezek konvertálása statikus virtuális merevlemezek feltöltés előtt meg kell. Használhatja például a [NYISSA meg az Azure virtuális merevlemez segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) átalakítani a dinamikus lemezek Azure feltöltése során.

* **Azure parancssori felület** – telepítse a legújabb [Azure parancssori felület](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) a virtuális merevlemez feltöltéséhez.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>1. lépés: Felkészülés a feltölteni kívánt kép
Azure támogatja a különböző Linux terjesztésekről (lásd: [támogatott Disztribúciókkal](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A következő cikkekben végigvezeti Önt a különböző Linux terjesztésekről Azure által támogatott előkészítése. A lépéseket az alábbi útmutatók befejezése után lépjen vissza ide, ha már van egy VHD-fájlt, amely az Azure-bA feltöltendő készen áll:

* **[CentOS-alapú Disztribúciók](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Egyéb - nem támogatott Disztribúciókkal](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Az Azure platformon SLA vonatkozik a virtuális gépek futnak a Linux operációs rendszert futtató, csak akkor, amikor a hitelesített terjesztéseket egyikét használják a konfigurációs információk a támogatott verziók a [Azure-Endorsed TerjesztéseketLinux](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A kép: Azure-katalógusban az összes Linux terjesztésekről a szükséges konfigurációval hitelesített terjesztéseket.
> 
> 

Lásd még: a  **[Linux telepítési jegyzetek](../create-upload-generic.md#general-linux-installation-notes)**  kapcsolatos további általános tippek Linux lemezképek előkészítése az Azure-bA.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>2. lépés: Felkészülés a kapcsolat az Azure-bA
Győződjön meg arról, hogy a klasszikus üzembe helyezési modellt használ az Azure parancssori felület (`azure config mode asm`), majd jelentkezzen be a fiókba:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>3. lépés: Töltse fel a lemezképet az Azure-bA
Töltse fel a VHD-fájlt a storage-fiók szükséges. Vagy válassza ki meglévő tárfiók vagy [hozzon létre egy újat](../../../storage/common/storage-create-storage-account.md).

Az Azure CLI segítségével feltölti a lemezképet a következő paranccsal:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Az előző példában:

* **BlobStorageURL** a tárfiók is használni kívánja az URL-címe
* **YourImagesFolder** a blob storage tárolóban van, hol szeretné tárolni a lemezképbe
* **VHDName** a címke sem, amely a virtuális merevlemez azonosításához portálon jelenik meg.
* **PathToVHDFile** a teljes elérési útja és neve a .vhd fájlt a számítógépre.

A teljes példát mutat be, a következő parancsot:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>4. lépés: Virtuális gép létrehozása lemezkép alapján
A virtuális gép használata `azure vm create` rendszeres virtuális azonos módon. Adja meg az előző lépésben nevet adott-e a lemezkép nevét. A következő példában használjuk a **myImage** az előző lépésben megadott lemezkép neve:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

A saját virtuális gépek létrehozásához adja meg saját felhasználónév + jelszó, hely, DNS-név és lemezkép neve.

## <a name="next-steps"></a>További lépések
További információkért lásd: [Azure CLI referencia az Azure klasszikus telepítési modell](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
