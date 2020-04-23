---
title: Windows AWS EC2-példány áthelyezése az Azure-ba
description: Amazon Web Services (AWS) EC2 Windows-példány áthelyezése egy Azure-beli virtuális gépre.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 59d1bf08c0680d222710b55c6d6bdb4d5745da56
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084515"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Windows virtuális gép áthelyezése az Amazon Web Servicesből (AWS) egy Azure virtuális gépre

Ha az Azure virtuális gépeit a számítási feladatok üzemeltetéséhez értékeli, exportálhat egy meglévő Amazon Web Services (AWS) EC2 Windows Virtuálisgép-példányt, majd feltöltheti a virtuális merevlemezt (VHD) az Azure-ba. A virtuális merevlemez feltöltése után létrehozhat egy új virtuális gép az Azure-ban a virtuális merevlemezről. 

Ez a cikk egyetlen virtuális gép áthelyezését ismerteti az AWS-ről az Azure-ba. Ha a virtuális gépeket az AWS-ből az Azure-ba szeretné nagy méretekben áthelyezni, olvassa el a [Virtuális gépek áttelepítése az Amazon Web Servicesben (AWS) az Azure Site Recovery szolgáltatással](../../site-recovery/site-recovery-migrate-aws-to-azure.md)című témakört.

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése 
 
Általános és speciális virtuális gépeket is feltölthet az Azure-ba. Minden típus megköveteli, hogy előkészíti a virtuális gépet, mielőtt exportálna az AWS-ből. 

- **Általános virtuális merevlemez** – egy általános ított virtuális merevlemez a Sysprep programmal eltávolította az összes személyes fiókadatot. Ha a virtuális merevlemezt szeretné lemezképként használni új virtuális gépek létrehozásához, a következőket kell tennie: 
 
    * [Windows virtuális gép előkészítése](prepare-for-upload-vhd-image.md).  
    * Általánosítsa a virtuális gépet a Sysprep használatával.  

 
- **Speciális virtuális merevlemez** – egy speciális virtuális merevlemez az eredeti virtuális gép felhasználói fiókjait, alkalmazásait és egyéb állapotadatait tartja karban. Ha a virtuális merevlemezt egy új virtuális gép létrehozása ként kívánja használni, győződjön meg arról, hogy a következő lépések befejeződtek.  
    * [Készítsen elő egy Windows vHD-t az Azure-ba való feltöltéshez.](prepare-for-upload-vhd-image.md) **Ne** általánosítsa a virtuális gép segítségével Sysprep. 
    * Távolítson el minden vendég virtualizációs eszközt és ügynököt, amely telepítve van a virtuális gépre (azaz VMware-eszközök). 
    * Győződjön meg arról, hogy a virtuális gép úgy van beállítva, hogy lekéri az IP-címét és a DNS-beállításait a DHCP-n keresztül. Ez biztosítja, hogy a kiszolgáló beszerez egy IP-címet a virtuális hálózaton belül, amikor elindul.  


## <a name="export-and-download-the-vhd"></a>A virtuális merevlemez exportálása és letöltése 

Exportálja az EC2-példányt egy Virtuális merevlemezre egy Amazon S3 gyűjtőben. Kövesse az Amazon dokumentációs cikkének [lépéseit: Példány exportálása virtuális gépként virtuális gép importálása/exportálása használatával,](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) és futtassa a [create-instance-export-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) parancsot az EC2-példány virtuális merevlemez-fájlba való exportálásához. 

Az exportált VHD-fájl a megadott Amazon S3 gyűjtőbe kerül. A virtuális merevlemez exportálásának alapvető szintaxisa az alábbi szintaxis alatt van, csak cserélje le a zárójelben lévő \<helyőrző szöveget,> az adatokkal.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Miután a virtuális merevlemez exportálása, kövesse az [utasításokat, hogyan lehet letölteni egy objektumot egy S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) letölteni a VHD fájlt az S3 vödör. 

> [!IMPORTANT]
> Az AWS a virtuális merevlemez letöltési díjait számítja fel az adatátviteli díjakért. További információ: [Amazon S3 Pricing.](https://aws.amazon.com/s3/pricing/)


## <a name="next-steps"></a>További lépések

Most feltöltheti a virtuális merevlemezt az Azure-ba, és létrehozhat egy új virtuális gép. 

- Ha a Sysprep programot futtatta a forráson, hogy az exportálás előtt **általánosítsa,** olvassa el [az Általános virtuális merevlemez feltöltése és új virtuális gépek létrehozásának használata az Azure-ban című témakört.](upload-generalized-managed.md)
- Ha nem futtatta a Sysprep programot az exportálás előtt, a virtuális merevlemez **speciálisnak**minősül, olvassa el [a Speciális virtuális merevlemez feltöltése az Azure-ba című témakört, és hozzon létre egy új virtuális gépet.](create-vm-specialized.md)

 
