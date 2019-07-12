---
title: Válassza ki az Azure Migrate-kiszolgáló áttelepítése egy VMware-áttelepítési lehetőség |} A Microsoft Docs
description: Beállítások áttelepítése a VMware virtuális gépek áttekintést nyújt az Azure-bA az Azure Migrate-kiszolgáló áttelepítése
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811569"
---
# <a name="select-a-vmware-migration-option"></a>Válassza ki a VMware-áttelepítési lehetőség

VMware virtuális gépek áttelepíthetők az Azure-bA az Azure Migrate Server áttelepítési eszköz használata. Ez az eszköz egy néhány további lehetőség a VMware virtuális gépek migrálása kínálja:

- A replikációs ügynök nélküli kivételfigyelés áttelepítés. Telepítse át a virtuális gépek anélkül, hogy telepít semmit a őket.
- Áttelepítés a replikációs ügynök. Az ügynök telepítése a virtuális gép.


Bár a replikációs ügynök nélküli kivételfigyelés egyszerűbb üzembe helyezési szempontból, számos korlátozás jelenleg rendelkezik.

## <a name="agentless-migration-limitations"></a>Ügynök nélküli kivételfigyelés a migrálásra vonatkozó korlátozások

Korlátozások a következők:

- **Egyidejű replikáció**: Legfeljebb 50 virtuális gépeket egyidejűleg replikálható a vCenter-kiszolgáló.<br/> Ha több mint 50 virtuális gépek migrálásra, a virtuális gépek több kötegeinek létrehozása.<br/> Egyszerre több replikáló hatással a teljesítményre.
- **Virtuálisgép-lemezek**: Egy virtuális Gépet, amely az áttelepíteni kívánt 60 lemezzel kell rendelkeznie.
- **Virtuális gép operációs rendszerek**: Általában az Azure Migrate áttelepítheti Windows Server vagy Linux operációs rendszer, de a virtuális gépek módosításainak igényelhet, hogy az Azure rendszerben futtatnak. Az Azure Migrate szolgáltatással a módosítások automatikusan az ilyen operációs rendszerek:
    - Red Hat Enterprise Linux 6.5-ös + 7.0 +
    - CentOS 6.5-ös + 7.0 +
    - SUSE Linux Enterprise Server 12 SP1 +
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Más operációs rendszerek esetén hajtsa végre az esetlegesen manuálisan az áttelepítés előtt kell. A [oktatóanyagban áttelepítése](tutorial-migrate-vmware.md) azt ismerteti, hogyan teheti ezt.
- **Linux rendszerindítási**: Ha gyökérpartíció dedikált partíción, az operációsrendszer-lemez a kell tárolni, és nem oszlanak meg több lemezre kiterjedő.<br/> Gyökérpartíció része a gyökér (/) partíció, ha ezután a "/" partíció kell az operációsrendszer-lemez lehet, és nem terjed ki a többi lemezen.
- **UEFI-rendszerindítás**: Az UEFI-rendszerindítást virtuális gépek nem telepíthetők át.
- **Titkosított lemezek vagy kötetek (BitLocker, cryptfs)** : Titkosított lemezek vagy kötetek rendelkező virtuális gépek áttelepítése nem támogatott.
- **RDM és csatlakoztatott lemezekkel**: Ha a virtuális gép RDM vagy csatlakoztatott lemezzel rendelkezik, ezeket a lemezeket nem replikálhatók az Azure-bA
- **AZ NFS**: NFS-kötetek kötetek fürtkötetként a virtuális gépeken nem replikálható.
- **Adatméretét**: VMware virtuális gépek az Azure virtuális gépeire (Standard HDD, prémium szintű SSD) felügyelt lemezek csak áttelepítése.



## <a name="deployment-steps-comparison"></a>Üzembe helyezési lépéseket összehasonlítása

A korlátozások áttekintése, után mindegyik megoldás üzembe helyezése lépéseinek ismertetése előfordulhat, hogy segít eldönteni, hogy melyik lehetőséget válassza.

**Tevékenység** | **Részletek** |**Ügynök nélküli kivételfigyelés** | **Az ügynök-alapú**
--- | --- | --- | ---
**VMware-kiszolgálók és virtuális gépek előkészítése migráláshoz** | Számos beállítását konfigurálhatja a VMware-kiszolgálók és virtuális gépeken. | Szükséges | Szükséges
**A kiszolgáló-áttelepítési eszköz hozzáadása** | Az Azure Migrate-projektben adja hozzá az Azure Migrate Server áttelepítési eszköz. | Szükséges | Szükséges
**Az Azure Migrate berendezés üzembe helyezése** | Állítsa be a virtuális gépek felderítése és értékelése a VMware virtuális gép egy egyszerűsített berendezés. | Szükséges | Nem kötelező.
**A mobilitási szolgáltatás telepítése virtuális gépeken** | Minden replikálni kívánt virtuális gépen a mobilitási szolgáltatás telepítése | Nem szükséges | Szükséges
**Az Azure Migrate-kiszolgáló áttelepítése replikációs berendezés üzembe helyezése** | Állítsa be a VMware virtuális gép telepíthetőek virtuális gépek felderítéséhez és hidat építhet a virtuális gépek és az Azure Migrate-kiszolgáló áttelepítése futó mobilitási szolgáltatás között | Nem szükséges | Szükséges
**Virtuális gépek replikálása**. Engedélyezheti a virtuális gép replikációját. | Replikációs beállítások konfigurálása, és válassza ki a virtuális gépek replikálásához | Szükséges | Szükséges
**Migrálási teszt futtatása** | Győződjön meg arról, hogy minden a várt módon működik a migrálási teszt futtatása. | Szükséges | Szükséges
**A teljes migrálás futtatása** | A virtuális gépek áttelepítése. | Szükséges | Szükséges




## <a name="next-steps"></a>További lépések

[VMware virtuális gépek áttelepítése](tutorial-migrate-vmware.md) ügynök nélküli kivételfigyelés áttelepítés használatával.



