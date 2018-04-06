---
title: A Linux virtuális gép adatlemezzel |} Microsoft Docs
description: A portál használatával Linux virtuális gép új vagy meglévő adatlemezt csatolni.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 4acfe53d68db3192c1f6c3c9e5f91b55bd5df7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Linux virtuális gép adatlemezt csatolni a portál használatával 
Ez a cikk bemutatja, hogyan új és meglévő lemez csatolása a Linux virtuális gép az Azure portálon keresztül. Emellett [adatlemezt csatolni egy Windows virtuális Gépet az Azure portálon](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mielőtt lemez csatolása a virtuális Gépre, tekintse át a ezek a tippek:

* A virtuális gép mérete csatolhat hány adatlemezek szabályozza. További információkért lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* A prémium szintű storage, kell a DS-méretek és GS sorozatnak virtuális gép. Ezek a virtuális gépek prémium és Standard lemezek is használható. Prémium szintű storage bizonyos régiókban érhető el. További információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Virtuális gépekhez csatlakoztatott lemezek az Azure-ban tárolt ténylegesen .vhd fájlokat. További információkért lásd: [lemezek és a virtuális merevlemezek a virtuális gépek](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>A virtuális gép található.
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a bal oldali menü **virtuális gépek**.
3. Válassza ki a virtuális gépet a listából.
4. Az a virtuális gépek lap, a **Essentials**, kattintson a **lemezek**.
   
    ![Nyissa meg a lemez beállításai](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Új lemez csatolása

1. Az a **lemezek** ablaktáblán kattintson a **+ Hozzáadás adatlemez**.
2. Kattintson a legördülő menü **neve** válassza **létrehozása lemez**:

    ![Hozzon létre Azure felügyelt lemezes](./media/attach-disk-portal/create-new-md.png)

3. Adjon meg egy nevet a felügyelt lemezes. Tekintse át az alapértelmezett beállításokat, szükség szerint frissítése, és kattintson a **létrehozása**.
   
   ![Lemez beállítások áttekintése](./media/attach-disk-portal/create-new-md-settings.png)

4. Kattintson a **mentése** létrehozása a felügyelt lemezes és a virtuális gép konfigurációjának frissítése:

   ![Új Azure kezelt lemezre mentése](./media/attach-disk-portal/confirm-create-new-md.png)

5. Után az Azure létrehozza a lemezt, és csatolja azt a virtuális gépet, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**. Felügyelt lemezek, a legfelső szintű erőforrás, a lemez az erőforráscsoport gyökerében jelenik meg:

   ![Felügyelt lemezt Azure erőforráscsoport](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
1. Az a **lemezek** ablaktáblán kattintson a **+ Hozzáadás adatlemez**.
2. Kattintson a legördülő menü **neve** meglévő Azure-előfizetése számára elérhető felügyelt lemezek listájának megtekintéséhez. Válassza ki a felügyelt lemezt csatolni:

   ![Meglévő Azure kezelt lemez csatolása](./media/attach-disk-portal/select-existing-md.png)

3. Kattintson a **mentése** csatolni a meglévő felügyelt lemezt, és a Virtuálisgép-konfiguráció frissítése:
   
   ![Azure kezelt lemez frissítések mentése](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure a lemez csatolása a virtuális gép, miután a virtuális gép lemezbeállításokat alatt felsorolt **adatlemezek**.



## <a name="next-steps"></a>További lépések
Emellett [adatlemezzel](add-disk.md) az Azure parancssori felület használatával.
