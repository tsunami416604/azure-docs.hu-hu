---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590754"
---
Amikor adatlemezeket ad hozzá egy Linux virtuális géphez, hibákat tapasztalhat, ha a lemez nem létezik a LUN 0-nál. Ha manuálisan ad hozzá `az vm disk attach -new` lemezt a paranccsal, és logikai és logikai értelemben megad egy logikai`--lun`és kiszolgálói platformot a megfelelő logikai érték meghatározásához, ügyeljen arra, hogy a lemez már létezzen / létezzen a LUN 0-nál. 

Tekintsük a következő példát, amely `lsscsi`a kimenet egy részletét mutatja be:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

A két adatlemez létezik a LUN 0 és a `lsscsi` LUN 1 (a kimeneti részletek `[host:channel:target:lun]`első oszlopa) értéken. Mindkét lemez nek elérhetőnek kell lennie a virtuális gépen belül. Ha manuálisan adta meg az első, a Logikai alkat 1-es értékben hozzáadandó lemezt, a másodiklemezt pedig a 2.

> [!NOTE]
> Az `host` Azure-érték 5 ezekben a példákban, de ez a kiválasztott tárhely típusától függően változhat.
> 
> 

Ez a lemezviselkedés nem Azure-probléma, hanem az, ahogyan a Linux kernel követi az SCSI-specifikációkat. Amikor a Linux kernel ellenőrzi az SCSI-buszt csatlakoztatott eszközök után, egy eszközt kell találni a LUN 0-nál ahhoz, hogy a rendszer további eszközöket kereshesse. Mint ilyen:

* Tekintse át `lsscsi` az adatlemez hozzáadása utáni kimenetet, és ellenőrizze, hogy van-e lemez e logikai részen:.
* Ha a lemez nem jelenik meg megfelelően a virtuális gépen belül, ellenőrizze, hogy létezik-e lemez a LUN 0-nál.

