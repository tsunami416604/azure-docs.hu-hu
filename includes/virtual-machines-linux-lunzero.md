---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 87dd3680aae3e87f78ab2dbe70c44b2008706747
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279674"
---
Adatlemezek hozzáadása egy Linux rendszerű virtuális géphez, ha hibákat tapasztalhat, ha a lemez nem létezik a LUN 0. Ha egy lemezt manuálisan ad hozzá a `azure vm disk attach-new` parancsot, és adja meg a LUN-t (`--lun`) ahelyett, hogy lehetővé teszi az Azure-platform határozza meg a megfelelő logikai Egységet, körültekintően járjon el, hogy a lemez már létezik, illetve akkor léteznek a LUN 0. 

Fontolja meg az alábbi példa kimenete egy kódrészletet megjelenítő `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

A két adatlemezt, a LUN 0 és a logikai egység 1 létezik (az első oszlop a `lsscsi` kimenetében `[host:channel:target:lun]`). A két lemez a virtuális gépen elérhetőnek kell lenniük. Ha az első lemez LUN-1-gyel hozzáadandó és a 2. logikai Egységben második lemez manuálisan kellett megadott, nem láthatók a lemezek megfelelően a virtuális gépen.

> [!NOTE]
> Az Azure `host` érték 5 ezekben a példákban, de ez, válassza ki a tároló típusától függően eltérőek lehetnek.
> 
> 

Ez a viselkedés lemez nincs egy Azure probléma, de a módja, amelyben a Linux kernel követi a SCSI-leírásokat. A Linux kernel megvizsgálja a SCSI-buszhoz csatlakoztatott eszközöket, amikor egy eszköz kell található LUN 0 ahhoz, hogy a rendszer folytatja a további eszközök keresése. Ilyen:

* Tekintse át a kimenetet a `lsscsi` ellenőrizze, hogy rendelkezik-e a lemez LUN 0, adatlemez hozzáadása után.
* Ha a lemez nem jelenik meg helyesen a virtuális gépen, a lemez LUN 0 meglétének ellenőrzése.

