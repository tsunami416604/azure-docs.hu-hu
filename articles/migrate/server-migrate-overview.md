---
title: VMware áttelepítési lehetőség kiválasztása Azure Migrate kiszolgáló áttelepítésével | Microsoft Docs
description: Áttekintést nyújt a VMware virtuális gépek Azure-ba való áttelepítésének lehetőségeiről Azure Migrate Server áttelepítéssel
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76028712"
---
# <a name="select-a-vmware-migration-option"></a>VMware áttelepítési lehetőség kiválasztása

A VMware virtuális gépeket áttelepítheti az Azure-ba az Azure Migrate Server áttelepítési eszköz használatával. Ez az eszköz néhány lehetőséget kínál a VMware virtuális gépek áttelepítésére:

- Áttelepítés ügynök nélküli replikálással. Telepítse át a virtuális gépeket anélkül, hogy bármit telepítenie kellene rájuk.
- Áttelepítés ügynökkel a replikáláshoz. Telepítsen egy ügynököt a virtuális gépre a replikáláshoz.




## <a name="compare-migration-methods"></a>Áttelepítési módszerek összehasonlítása

A kiválasztott összehasonlítások segítségével eldöntheti, hogy melyik módszert kell használni. Az [ügynök](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) nélküli és az [ügynök-alapú](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) áttelepítés teljes támogatási követelményeit is ellenőrizheti.

**Beállítás** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
**Azure-engedélyek** | Azure Migrate projekt létrehozásához, valamint a Azure Migrate berendezés telepítésekor létrehozott Azure AD-alkalmazások regisztrálásához engedélyekre van szükség. | Közreműködői engedélyekkel kell rendelkeznie az Azure-előfizetéshez. 
**Egyidejű replikáció** | Egy vCenter Server legfeljebb 100 virtuális gépet lehet replikálni egyszerre.<br/> Ha több mint 50 virtuális gépet szeretne áttelepíteni, hozzon létre több köteget a virtuális gépekről.<br/> Ha több replikálást végez, a szolgáltatás hatással lesz a teljesítményre. | NA
**Berendezések üzembe helyezése** | A [Azure Migrate berendezést](migrate-appliance.md) a helyszínen helyezik üzembe. | A [Azure Migrate replikációs berendezést](migrate-replication-appliance.md) a helyszínen helyezik üzembe.
**Site Recovery kompatibilitás** | Kompatibilis. | Nem lehet replikálni Azure Migrate-kiszolgáló áttelepítésével, ha Site Recovery használatával beállította egy gép replikálását.
**Céllemez** | Felügyelt lemezek | Felügyelt lemezek
**Lemezterület-korlátok** | OPERÁCIÓSRENDSZER-lemez: 2 TB<br/><br/> Adatlemez: 4 TB<br/><br/> Maximális lemezek: 60 | OPERÁCIÓSRENDSZER-lemez: 2 TB<br/><br/> Adatlemez: 8 TB<br/><br/> Maximális lemezek: 63
**Továbbító lemezek** | Nem támogatott | Támogatott
**UEFI-rendszerindítás** | Nem támogatott | Az Azure-ban áttelepített virtuális gép automatikusan BIOS rendszerindító virtuális gépre lesz konvertálva.<br/><br/> Az operációsrendszer-lemez legfeljebb négy partíciót tartalmazhat, és a köteteket NTFS fájlrendszerrel kell formázni.


## <a name="deployment-steps-comparison"></a>Telepítési lépések összehasonlítása

A korlátozások áttekintése után megismerheti, hogy az egyes megoldások üzembe helyezésével kapcsolatos lépések segítenek eldönteni, hogy melyik lehetőséget kell választania.

**Tevékenység** | **Részletek** |**Ügynök nélküli** | **Ügynök-alapú**
--- | --- | --- | ---
**Értékelés** | Az áttelepítés előtt mérje fel a kiszolgálókat.  Az értékelés nem kötelező. Javasoljuk, hogy az áttelepítés előtt mérje fel a gépeket, de nem kell. <br/><br/> Az értékeléshez Azure Migrate egy könnyű berendezést állít be a virtuális gépek felderítéséhez és értékeléséhez. | Ha az értékelés után ügynök nélküli áttelepítést futtat, akkor az ügynök nélküli Migrálás során az értékeléshez beállított Azure Migrate berendezés is használatos.  |  Ha az értékelés után ügynök-alapú áttelepítést futtat, az értékeléshez beállított berendezés nem használatos az ügynök nélküli áttelepítés során. A készüléket meghagyhatja, vagy eltávolíthatja, ha nem szeretné további felderítést és értékelést végezni.
**VMware-kiszolgálók és virtuális gépek előkészítése áttelepítésre** | Konfiguráljon számos beállítást a VMware-kiszolgálókon és a virtuális gépeken. | Kötelező | Kötelező
**A kiszolgáló áttelepítési eszközének hozzáadása** | Adja hozzá a Azure Migrate Server áttelepítési eszközt a Azure Migrate projektben. | Kötelező | Kötelező
**A Azure Migrate berendezés üzembe helyezése** | Hozzon létre egy egyszerűsített készüléket egy VMware virtuális gépen a virtuális gépek felderítéséhez és értékeléséhez. | Kötelező | Nem kötelező.
**A mobilitási szolgáltatás telepítése virtuális gépeken** | Telepítse a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre | Nem kötelező | Kötelező
**A Azure Migrate kiszolgáló áttelepítésének replikációs berendezésének üzembe helyezése** | Készülék beállítása VMware virtuális gépen a virtuális gépek felderítése és a virtuális gépeken futó mobilitási szolgáltatás és a Azure Migrate-kiszolgáló áttelepítése közötti híd létrehozásához | Nem kötelező | Kötelező
**Virtuális gépek replikálása**. Engedélyezze a virtuális gép replikálását. | Replikációs beállítások konfigurálása és a replikálni kívánt virtuális gépek kiválasztása | Kötelező | Kötelező
**Migrálási teszt futtatása** | Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e. | Kötelező | Kötelező
**Teljes áttelepítés futtatása** | Telepítse át a virtuális gépeket. | Kötelező | Kötelező




## <a name="next-steps"></a>További lépések

[VMWare virtuális gépek migrálása](tutorial-migrate-vmware.md) ügynök nélküli áttelepítéssel.



