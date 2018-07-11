---
title: Hozzon létre, és a egy Linux VHD feltöltése az Azure-bA |} A Microsoft Docs
description: Létrehozása és feltöltése az Azure virtuális merevlemez (VHD), amely tartalmazza a Linux operációs rendszer a klasszikus üzemi modell használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.author: cynthn
ms.openlocfilehash: cdbe6aa5683ecf9d8bdaf6bbf9503ddc455f03ee
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928267"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Linux operációs rendszert tartalmazó virtuális merevlemez létrehozása és feltöltése
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [feltöltése az Azure Resource Manager egyéni lemezkép](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ez a cikk bemutatja, hogyan hozhat létre és töltse fel a virtuális merevlemez (VHD), így használhatja azt a saját rendszerkép hozhat létre virtuális gépeket az Azure-ban. Ismerje meg, hogyan készíti elő az operációs rendszer, így használhatja a rendszerkép alapján több virtuális gép létrehozásához. 


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy rendelkezik-e a következő elemek:

* **Linux operációs rendszer telepítve van egy .vhd fájl** -telepítette egy [Azure által támogatott Linux-disztribúció](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (vagy tekintse meg a [kapcsolatos tudnivalók nem támogatott disztribúciókkal](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) egy virtuális lemezen, a Virtuális merevlemez formátuma. Több eszköz létezik, hozzon létre egy virtuális gép és a virtuális merevlemez:
  * Telepítse és konfigurálja [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy [KVM](http://www.linux-kvm.org/page/RunningKVM), ügyelve arra, hogy a VHD-t használja, mint a kép formátuma. Ha szükséges, [kép konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) használatával `qemu-img convert`.
  * Is használhatja a Hyper-V [a Windows 10-es](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) vagy [a Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális Gépet, adja meg VHD formátumban. Szükség esetén átválthat VHDX-lemezek VHD-t használó [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-parancsmagot. További Azure nem támogatja dinamikus virtuális merevlemezek, feltöltésével, ezért az ilyen lemezek konvertálása statikus VHD feltöltése előtt szüksége. Eszközöket használhatja például [GÓHOZ készült Azure VHD segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) feltöltése az Azure-ba, a folyamat során a dinamikus lemezek konvertálása.

* **Az Azure parancssori felület** – telepítse a legújabb [Azure parancssori felület](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) a virtuális merevlemez feltöltéséhez.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>1. lépés: Készítse elő a feltölteni kívánt kép
Az Azure támogatja különböző Linux-disztribúciók (lásd: [támogatott Disztribúciók](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A következő cikkek végigvezetik az Azure által támogatott különböző Linux-disztribúciók előkészítése. Miután elvégezte a lépést az alábbi útmutatókban, térjen vissza ide, ha már van egy VHD-fájl feltöltése az Azure-bA készítenek:

* **[CentOS-alapú Disztribúciókon](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Egyéb - nem által támogatott Disztribúciók](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Az Azure platform SLA vonatkozik a Linux operációs rendszert futtató, csak akkor, ha a támogatott disztribúciókról egyik használja a konfiguráció a virtuális gépek részleteit a támogatott verziók az [Linux-Azure-Endorsed Disztribúciók](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Minden, az Azure lemezkép-katalógusában található Linux-disztribúciók is támogatott disztribúciókról a szükséges konfigurációval.
> 
> 

Is megtekintheti a **[Linux telepítési jegyzetek](../create-upload-generic.md#general-linux-installation-notes)** kapcsolatos további általános tippek Linux-rendszerképek előkészítéséről az Azure-hoz.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>2. lépés: Készítse elő a kapcsolatot az Azure-bA
Ellenőrizze, hogy a klasszikus üzemi modellben az Azure CLI-vel használ (`azure config mode asm`), majd jelentkezzen be a fiók:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>3. lépés: A Rendszerkép feltöltése az Azure-bA
A VHD fájlt feltölteni egy tárfiókra van szükség. Vagy választhat egy meglévő tárfiókot vagy [hozzon létre egy új](../../../storage/common/storage-create-storage-account.md).

Az Azure CLI használatával töltse fel a rendszerképet a következő paranccsal:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Az előző példában:

* **BlobStorageURL** tervezi használni a storage-fiók URL-címe
* **YourImagesFolder** ahol szeretné a lemezképek tárolását a blob Storage tárolóban van
* **VHDName** a címke sem, amely megjelenik a portálon a virtuális merevlemez azonosításához.
* **PathToVHDFile** a teljes elérési útja és neve a .vhd fájlt a gépén.

A következő parancsot egy teljes példát mutat be:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>4. lépés: Virtuális gép létrehozása a rendszerképből
Létrehoz egy virtuális gépet `azure vm create` egy normál virtuális géppel megegyező módon. Adja meg a rendszerkép az előző lépésben megadott névnek. A következő példában használunk a **myImage** az előző lépésben megadott rendszerkép neve:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Hozhat létre a saját virtuális gépeket, a saját felhasználónév + a jelszó, a helyet, a DNS-név és a rendszerkép neve biztosítanak.

## <a name="next-steps"></a>További lépések
További információkért lásd: [a klasszikus Azure üzemi modell Azure CLI-referenciáját](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
