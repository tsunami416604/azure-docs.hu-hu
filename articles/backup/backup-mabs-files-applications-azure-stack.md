---
title: Készítsen biztonsági másolatot az Azure-verem fájlokhoz és alkalmazásokhoz |} Microsoft Docs
description: Azure Backup használatával biztonsági mentése és helyreállítása Azure verem fájlokat és alkalmazásokat az Azure-verem környezethez.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 99ac43efa5d3211bbe2d790f28532e682058313c
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Biztonsági másolatot a fájlokhoz és alkalmazásokhoz Azure verem
Azure Backup segítségével védeni (vagy készítsen biztonsági másolatot) fájlok és alkalmazások Azure veremben. Biztonsági másolatot készíteni a fájlokhoz és alkalmazásokhoz, telepítse a Microsoft Azure Backup Server Azure verem futó virtuális gépként. Az azonos virtuális hálózatban lévő bármely Azure verem kiszolgálón futó alkalmazások, védelmet biztosíthat. Egyszer telepítette az Azure Backup Server, adja hozzá az Azure-lemezeket a helyi tárterület érhető el, a rövid távú biztonsági másolatok növelése érdekében. Az Azure Backup Server hosszú távú megőrzési Azure tárolást használ.

> [!NOTE]
> Bár az Azure Backup Server és System Center Data Protection Manager (DPM) hasonló a DPM nem támogatott Azure verem való használatra.
>


## <a name="azure-backup-server-protection-matrix"></a>Az Azure Backup Server védelmi mátrixa
Az Azure Backup Server a következő Azure verem virtuálisgép-munkaterhelését védi.

| Védett adatforrás | Védelem és helyreállítás |
| --------------------- | ----------------------- |
| Windows Server félig éves csatorna - Datacenter vagy Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 – Datacenter/Entprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2008 R2 – Datacenter vagy Enterprise/Standard | Kötetek, fájlok, mappák |
| SQL Server 2016 | Adatbázis |
| SQL Server 2014 | Adatbázis |
| SQL Server 2012 SP1 | Adatbázis |
| SharePoint 2013 | Farm, adatbázis, előtér, webalkalmazás-kiszolgáló |
| A SharePoint 2010 | Farm, adatbázis, előtér, webalkalmazás-kiszolgáló |


## <a name="install-azure-backup-server"></a>Az Azure Backup Server telepítése
Azure Backup Server telepítése egy Azure verem virtuális gépen, tekintse meg a cikk [készül, hogy az Azure Backup Server használatával-munkaterhelések biztonsági mentése](backup-azure-microsoft-azure-backup.md). Kiszolgáló telepítése és konfigurálása Azure biztonsági mentés, mielőtt vegye figyelembe a következőket:

### <a name="determining-size-of-virtual-machine"></a>Virtuális gép mérete meghatározása
Egy Azure verem virtuális gépen futó Azure Backup Server, használja a A2 méret vagy nagyobb. Ha segítségre van szüksége a virtuálisgép-méret kiválasztása, töltse le a [Azure verem virtuális gép mérete Számológép](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuális hálózatok verem Azure virtuális gépeken
Egy Azure verem munkaterhelés használt összes virtuális gépet a azonos Azure virtuális hálózat és az Azure-előfizetéshez kell tartoznia.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Biztonsági mentési adatok tárolására, a helyi lemezen, és az Azure-ban
Az Azure Backup Server biztonsági mentési adatokat, a műveleti helyreállítási a virtuális géphez csatolt Azure lemezeken tárolja. Miután a virtuális gép csatolt a lemezek és a tárolóhely, Azure Backup Server kezeli tároló. A biztonsági mentési adatok tárolási mértékét száma és mérete a lemez nem csatolható az egyes [verem Azure virtuális gép](../azure-stack/user/azure-stack-storage-overview.md). Minden Azure verem virtuális gép méretétől rendelkezik a virtuális géphez csatolt lemezek maximális számát. A2 például négy lemezek. A3 méretű nyolc lemezek. A4 16 lemez. Ebben az esetben a lemezek száma és mérete határozza meg a teljes biztonsági mentési tárolókészlet méretét.

> [!IMPORTANT]
> Meg kell **nem** az Azure Backup Server-csatolású lemezeket (biztonsági mentés) műveleti helyreállítási adatok megőrzéséhez a több mint öt napig.
>

Biztonsági mentési adatok tárolására az Azure-ban csökkenti a biztonsági mentési infrastruktúra Azure veremben. Ha öt napnál régebbi adatok, akkor az Azure-ban kell tárolni.

Az Azure biztonsági mentési adatok tárolására, hozzon létre, vagy használja a Recovery Services-tároló. Az Azure Backup Server munkaterhelést másolatot előkészítésekor fogja [a Recovery Services-tároló konfigurálása](backup-azure-microsoft-azure-backup.md#recovery-services-vault). Konfigurálása után minden alkalommal, amikor egy biztonságimásolat-készítő feladat fut, a helyreállítási pont létrehozása a tárolóban lévő állapottal. Minden Recovery Services-tároló legfeljebb 9999 helyreállítási pontokat tárolja. Attól függően, hogy mennyi ideig maradnak, és létrehozott helyreállítási pontok száma a biztonsági mentési adatok továbbra is sok éve. Például akkor képes havi helyreállítási pontot, és öt évig megőrzi azokat.
 
### <a name="using-sql-server"></a>SQL Server használata
Ha az Azure Backup Server-adatbázis egy távoli SQL Server használatához, jelölje be a csak egy Azure verem virtuális Gépen futó SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Az Azure Backup Server VM teljesítmény
Ha más virtuális gépekkel megosztva használ, a tárolási fiók méretét és IOPS-korlátok vonatkoznak hatással lehet az Azure Backup Server virtuális gép teljesítményét. Ezért egy külön tárfiókot kell használnia, az Azure Backup Server virtuális gép. Az Azure biztonsági mentési kiszolgálón futó Azure Backup ügynöknek szüksége van, a ideiglenes tárhelyre:
    - saját használatra (gyorsítótárhely),
    - (helyi átmeneti terület) a felhőből helyreállított adatok
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure biztonsági mentési mennyiségű ideiglenes lemezes tárolás konfigurálása
Minden Azure verem virtuális gép rendelkezik mennyiségű ideiglenes lemezes tárhellyel, a felhasználó kötetként rendelkezésre álló `D:\`. Az Azure Backup által igényelt helyi átmeneti terület beállítható úgy, hogy találhatók `D:\`, és a gyorsítótár helye lehet tenni `C:\`. Ezzel a módszerrel nem kell az Azure Backup Server virtuális géphez csatolt adatlemezek el kell faragottnak.

### <a name="scaling-deployment"></a>Központi telepítés skálázás
Ha azt szeretné, a telepítés méretezésére, lehetősége van a következő:
  - Vertikális felskálázás – Azure Backup Server virtuális gépen a több D sorozat méretének növelése és a helyi tároló növelése [verem Azure virtuális gép utasítások](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Adatok továbbítása - régebbi adatokat küldeni az Azure Backup Server így csak a legújabb adatokat az Azure biztonsági mentés-kiszolgálóhoz csatolt tárhelyen tárolnia.
  - Horizontális felskálázás -, adjon hozzá további Azure biztonsági mentés kiszolgálókat a munkaterhelések védelme érdekében.

## <a name="see-also"></a>Lásd még
Információ az Azure Backup Server használatával egyéb munkaterhelések védelme érdekében tekintse meg a következő cikkeket:
- [Készítsen biztonsági másolatot a SharePoint-farm.](backup-azure-backup-sharepoint-mabs.md)
- [Készítsen biztonsági másolatot az SQL server](backup-azure-sql-mabs.md)
