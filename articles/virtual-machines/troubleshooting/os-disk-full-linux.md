---
title: A Linux rendszerű virtuális gépek teljes operációsrendszer-lemezével kapcsolatos problémák
description: A teljes operációsrendszer-lemezzel kapcsolatos hibák elhárítása Linux rendszerű virtuális gépen
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523536"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>A Linux rendszerű virtuális gépek teljes operációsrendszer-lemezével kapcsolatos problémák

Ha a Linux rendszerű virtuális gép (VM) operációsrendszer-lemeze megtelik, akkor a virtuális gép megfelelő működésével problémák merülhetnek fel.

## <a name="symptom"></a>Hibajelenség

Új fájl létrehozásakor a következő üzenet jelenik meg:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Több démon is jelzi, hogy nem tudnak ideiglenes fájlokat létrehozni a rendszerindítási munkamenet során.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Ok

A hibaüzenet több okból is előfordulhat:

1. A lemez megtelt.
1. Lehetséges, hogy a fájlrendszer elfogyott a inode.
1. Előfordulhat, hogy egy adatlemezt egy meglévő címtárban lehet csatlakoztatni, ami a fájlok elrejtését okozza.
1. Egy folyamat által megnyitott fájlok, amelyeket aztán törölnek.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. Szabad terület a lemezen.
1. Hozza létre újra a virtuális gépet.

> [!NOTE]
> Ha ezt a hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma elhárítása offline módban a probléma megoldásához.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
1. Az SSH használatával csatlakozzon a javítási virtuális géphez.

### <a name="free-up-space-on-the-disk"></a>Szabadítson fel lemezterületet a lemezen

A probléma megoldásához:

- Méretezze át a lemezt legfeljebb 1 TB-ra, ha az még nem a maximális 1 TB-os méretnél.
- Szabadítson fel lemezterületet.

1. Ellenőrizze, hogy a lemez megtelt-e. Ha a lemez mérete 1 TB-nál kisebb, az [Azure CLI](../linux/expand-disks.md)-vel legfeljebb 1 TB-ra bontsa ki.
1. Ha a lemez már 1 TB, szabadítson fel lemezterületet.
1. Az átméretezés és a tisztítás befejezése után folytassa a virtuális gép újraépítésével.

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
