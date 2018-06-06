---
title: A Windows AWS virtuális gépek áthelyezése az Azure-bA |} Microsoft Docs
description: Az Amazon Web Services (AWS) EC2 Windows példány az Azure virtuális gépek Azure PowerShell használatával.
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
ms.openlocfilehash: cb5b68e7bd0a1b247327e7147fe38eae19395f50
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726533"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Az Amazon Web Services (AWS) a Windows virtuális gépek áthelyezése az Azure PowerShell használatával

Ha éppen kipróbálja az Azure virtuális gépek a munkaterhelések, meglévő Amazon Web Services (AWS) EC2 Windows Virtuálisgép-példány exportálása, majd a virtuális merevlemez (VHD) feltöltése az Azure-bA. A VHD-t a feltöltést követően létrehozhat egy új virtuális gép az Azure virtuális merevlemezről. 

Ez a cikk ismerteti, egyetlen virtuális gép áthelyezése AWS az Azure-bA. Ha az áthelyezni kívánt virtuális gépeket az AWS Azure léptékű, lásd: [Amazon Web Services (AWS) virtuális gépek áttelepítése az Azure-bA az Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése 
 
Általános és a speciális virtuális merevlemezek feltöltheti az Azure-bA. Egyes elő kell készíteni a virtuális gép AWS exportálása előtt. 

- **Virtuális merevlemez általánosítva** -általánosított virtuális Merevlemezt volt-e az összes személyes fiókadatait a Sysprep segítségével távolítja el. Ha azt tervezi, a virtuális merevlemez használata képként az új virtuális gépek létrehozásához, a következőket: 
 
    * [A Windows virtuális gép előkészítése](prepare-for-upload-vhd-image.md).  
    * A virtuális gépet a Sysprep használatával generalize.  

 
- **Virtuális merevlemez kifejezetten** -speciális virtuális merevlemez kezeli a felhasználói fiókok, alkalmazások és az eredeti virtuális gép más állapotba adatait. Ha szeretne használni, mint a VHD-t hozzon létre egy új virtuális Gépet, győződjön meg arról, a következő lépéseket.  
    * [Az Azure-bA feltölteni egy Windows virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md). **Ne** generalize a virtuális Gépet a Sysprep használata. 
    * Távolítsa el a Vendég virtualizációs eszközök és a virtuális Gépre (azaz a VMware-eszközök) telepített ügynökök. 
    * Győződjön meg arról, a virtuális Géphez való lekérésére, az IP-cím és DNS-beállítások DHCP van konfigurálva. Ez biztosítja, hogy a kiszolgáló a Vneten belül egy IP-címet kap, indításkor.  


## <a name="export-and-download-the-vhd"></a>Exportálni, és töltse le a virtuális merevlemez 

Exportálja a EC2 példány az Amazon S3 gyűjtő a virtuális merevlemez. Kövesse az Amazon dokumentációcikkhez [exportálása egy példányát, a virtuális gép használatával virtuális gép importálási/exportálási](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) , és futtassa a [-példány-export-feladat létrehozása](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) parancs a EC2 példány exportálni egy VHD-fájlt. 

Az Amazon S3 gyűjtő megadja az exportált VHD-fájl kerül. Alapvető szintaxisa a VHD exportálása nem éri el, az imént cserélje le a helyőrző szöveget <brackets> a adatokkal.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

A VHD exportálása után kövesse az utasításokat [hogyan tegye le az objektum az az S3 gyűjtő?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) töltheti le a VHD-fájlt a S3 gyűjtő. 

> [!IMPORTANT]
> AWS díjak adatátvitel díjak letöltéséhez a VHD-t. Lásd: [Amazon S3 árképzési](https://aws.amazon.com/s3/pricing/) további információt.


## <a name="next-steps"></a>További lépések

Most a virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy új virtuális Gépet. 

- Ha a forrást a Sysprep futtatása **generalize** azt az exportálás előtt tekintse meg [egy általánosított virtuális merevlemez feltöltéséhez, és annak segítségével hozzon létre egy új virtuális gépek Azure-ban](upload-generalized-managed.md)
- A Sysprep futtatása exportálása előtt, ha a virtuális merevlemez tekinthető **speciális**, lásd: [speciális virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy új virtuális Gépet](create-vm-specialized.md)

 
