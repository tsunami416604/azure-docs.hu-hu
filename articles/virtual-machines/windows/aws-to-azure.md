---
title: A Windows az AWS-beli virtuális gépek áthelyezése az Azure-bA |} A Microsoft Docs
description: Az Amazon Web Services (AWS) EC2 Windows-példány áthelyezése az Azure virtuális gépként.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f66101d9847c57c5e078c3484a243e7b38823f53
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001785"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Windows virtuális gép az Amazon Web Services (AWS) Azure virtuális gép áthelyezése

Ha éppen kipróbálja az Azure virtual machines, a számítási feladatok futtatásához, meglévő Amazon Web Services (AWS) EC2 Windows VM-példány exportálása, majd töltse fel a virtuális merevlemez (VHD) az Azure-bA. Miután feltöltötte a VHD-t, létrehozhat egy új virtuális Gépet az Azure-ban a VHD-ből. 

Ez a cikk ismerteti, hogy egyetlen virtuális gép áthelyezése az AWS-től az Azure-bA. Ha az áthelyezni kívánt virtuális gépeket az AWS-től az Azure-bA ipari méretekben, [az Amazon Web Services (AWS) virtuális gépek áttelepítése az Azure-bA az Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése 
 
Általános és a specializált virtuális merevlemezek tölthet fel az Azure-bA. Minden egyes szükséges, hogy az AWS-től exportálása előtt készítse elő a virtuális gép. 

- **Általános virtuális Merevlemezből** -általánosított virtuális merevlemez esetében minden személyes fiókadatot a Sysprep segítségével távolítja el. Ha szeretne használni a virtuális merevlemez képként hozhat létre az új virtuális gépeket, a következőket kell elvégeznie: 
 
    * [Egy Windows virtuális gép előkészítése](prepare-for-upload-vhd-image.md).  
    * A Sysprep használata virtuális gép általánosítása.  

 
- **Specializált virtuális merevlemez** – egyéni VHD kezeli a felhasználói fiókok, alkalmazások és más állapot adatait az eredeti virtuális gépről. Ha szeretne használni, mint a VHD-t hozzon létre egy új virtuális Gépet, győződjön meg arról, a következő lépéseket.  
    * [Töltse fel az Azure Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md). **Ne** a Sysprep használata virtuális gép általánosítása. 
    * A Vendég virtualizációs eszközök és a virtuális gépen (azaz a VMware-eszközök) telepített ügynökök eltávolítása. 
    * Győződjön meg arról, kérje le az IP-cím és DNS-beállítások a DHCP-n keresztül a virtuális gép van konfigurálva. Ez biztosítja, hogy a kiszolgáló a virtuális hálózaton belül egy IP-címet kap, indításkor.  


## <a name="export-and-download-the-vhd"></a>Exportálás, és töltse le a VHD-t 

Exportálja az EC2-példánynak az Amazon S3 gyűjtőt a virtuális merevlemez. Az Amazon dokumentációs cikk lépéseit követve [exportálása egy példányt, mint egy virtuális gép használata virtuális gép importálási/exportálási](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) , és futtassa a [-példány-export-feladat létrehozása](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) EC2-példány egy VHD-fájl exportálása parancsot. 

A megadott Amazon S3 gyűjtő rendszer menti az exportált VHD-fájl. Az alapszintű szintaxistól exportálása a VHD-t az alábbi érték csak cserélje le a helyőrző szöveget <brackets> adataival.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Miután exportálta a VHD-t, kövesse a [Hogyan tölthetem le egy objektumot, egy S3 gyűjtőt?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) töltheti le a VHD-fájlt az S3 gyűjtőt. 

> [!IMPORTANT]
> Az AWS díjak adatátviteli díjak letöltéséhez a VHD-t. Lásd: [Amazon S3 díjszabás](https://aws.amazon.com/s3/pricing/) további információt.


## <a name="next-steps"></a>További lépések

Most töltse fel a VHD-t az Azure-ba, és hozzon létre egy új virtuális Gépet. 

- Ha a forrás a Sysprep **generalize** azt exportálásakor, lásd: [általános VHD feltöltése és ezzel hozzon létre egy új virtuális gépeket az Azure-ban](upload-generalized-managed.md)
- Ha nem futtatta a Sysprep exportálása előtt, a VHD-t számít **speciális**, lásd: [egyéni VHD feltöltése az Azure-ba, és a egy új virtuális gép létrehozása](create-vm-specialized.md)

 
