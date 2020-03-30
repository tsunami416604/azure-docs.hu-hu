---
title: Válassza ki a VMware áttelepítési beállítás áttelepülése az Azure Áttelepítési kiszolgáló áttelepítése | Microsoft dokumentumok
description: Áttekintés tésszel a VMware virtuális gépek Azure-ba való áttelepítésének lehetőségeiről az Azure Áttelepítési Kiszolgáló áttelepítése segítségével
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028712"
---
# <a name="select-a-vmware-migration-option"></a>VMware-áttelepítési beállítás kiválasztása

A VMware virtuális gépek az Azure-ba az Azure-áttelepítési kiszolgáló áttelepítése eszközzel. Ez az eszköz néhány lehetőséget kínál a VMware VM-áttelepítéshez:

- Áttelepítés ügynök nélküli replikációval. Telepítse a virtuális gépeket anélkül, hogy bármit is telepítenie kellene rájuk.
- Áttelepítés ügynökkel replikációra. Telepítsen egy ügynököt a virtuális gépre replikációra.




## <a name="compare-migration-methods"></a>Áttelepítési módszerek összehasonlítása

Ezekkel a kiválasztott összehasonlításokkal eldöntheti, hogy melyik módszert használja. Tekintse át az ügynök [nélküli](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) és [ügynökalapú](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) áttelepítés teljes támogatási követelményeit is.

**Beállítás** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
**Azure-engedélyek** | Az Azure Migrate-projekt létrehozásához és az Azure Migrate-berendezés üzembe helyezésekor létrehozott Azure AD-alkalmazások regisztrálásához engedélyekre van szüksége. | Az Azure-előfizetéshez közreműködői engedélyekre van szüksége. 
**Egyidejű replikáció** | Legfeljebb 100 virtuális gép replikálható egyszerre egy vCenter-kiszolgálóról.<br/> Ha több mint 50 virtuális gép áttelepítési, hozzon létre több kötegvirtuális gépek.<br/> Több replikálása egy időben hatással lesz a teljesítményre. | NA
**A berendezés üzembe helyezése** | Az [Azure Migrate készülék](migrate-appliance.md) a helyszínen van telepítve. | Az [Azure Migrate replikációs berendezés](migrate-replication-appliance.md) a helyszínen van telepítve.
**Webhely-helyreállítási kompatibilitás** | Kompatibilis. | Nem replikálhatja az Azure Áttelepítési kiszolgáló áttelepítése, ha beállította a replikáció t a számítógép site recovery használatával.
**Céllemez** | Felügyelt lemezek | Felügyelt lemezek
**Lemezkorlátok** | OPERÁCIÓS RENDSZER lemeze: 2 TB<br/><br/> Adatlemez: 4 TB<br/><br/> Maximális lemezek: 60 | OPERÁCIÓS RENDSZER lemeze: 2 TB<br/><br/> Adatlemez: 8 TB<br/><br/> Maximális lemezek: 63
**Áthaladási lemezek** | Nem támogatott | Támogatott
**UEFI rendszerindítás** | Nem támogatott | Az azure-ban áttelepített virtuális gép automatikusan bios-rendszerindítási virtuális gépté alakul át.<br/><br/> Az operációs rendszer lemezének legfeljebb négy partícióval kell rendelkeznie, és a köteteket NTFS-sel kell formázni.


## <a name="deployment-steps-comparison"></a>Telepítési lépések összehasonlítása

A korlátozások áttekintése után az egyes megoldások üzembe helyezésének lépései segíthetnek eldönteni, hogy melyik lehetőséget válassza.

**Tevékenység** | **Részletek** |**Ügynök nélküli** | **Ügynök-alapú**
--- | --- | --- | ---
**Értékelés** | Kiszolgálók felmérése áttelepítés előtt.  Az értékelés nem kötelező. Javasoljuk, hogy az áttelepítés előtt értékelje a gépeket, de nem kell. <br/><br/> Az értékeléshez az Azure Migrate egy könnyű berendezést állít be a virtuális gépek felderítéséhez és értékeléséhez. | Ha egy ügynök nélküli áttelepítést futtat az értékelés után, ugyanazt az Azure Migrate-készüléket, amely értékelésre van beállítva, az ügynök nélküli áttelepítéshez használja.  |  Ha egy ügynök-alapú áttelepítés imputálás után futtatja, a felmérésre beállított készülék nem használatos ügynök nélküli áttelepítés során. A készüléket a helyén hagyhatja, vagy eltávolíthatja, ha nem szeretne további felfedezést és értékelést végezni.
**VMware-kiszolgálók és virtuális gépek előkészítése áttelepítésre** | Konfiguráljon számos beállítást a VMware-kiszolgálókon és a virtuális gépeken. | Kötelező | Kötelező
**A Kiszolgálóáttelepítés eszköz hozzáadása** | Adja hozzá az Azure Áttelepítési kiszolgáló eszközt az Azure Áttelepítés projekthez. | Kötelező | Kötelező
**Az Azure Migrate-berendezés üzembe helyezése** | Hozzon létre egy könnyű készüléket egy VMware virtuális gép virtuális gép virtuális gép virtuális gép felderítéséhez és értékeléséhez. | Kötelező | Nem szükséges.
**A Mobilitás szolgáltatás telepítése virtuális gépekre** | Telepítse a Mobilitás szolgáltatást minden replikálni kívánt virtuális gépre | Nem kötelező | Kötelező
**Az Azure Áttelepítési kiszolgáló áttelepítése replikációs berendezés telepítése** | A virtuális gépeken futó virtuális gépek en lévő készülék beállítása a virtuális gépek felderítéséhez, valamint a virtuális gépeken futó mobilitási szolgáltatás és az Azure Migrate Server Migration közötti hídhoz | Nem kötelező | Kötelező
**Virtuális gépek replikálása**. Virtuálisgép-replikáció engedélyezése. | Konfigurálja a replikációs beállításokat, és válassza a replikálandó virtuális gépeket | Kötelező | Kötelező
**Migrálási teszt futtatása** | Futtasson egy tesztáttelepítést, hogy minden a várt módon működjön. | Kötelező | Kötelező
**Teljes áttelepítés futtatása** | Telepítse át a virtuális gépeket. | Kötelező | Kötelező




## <a name="next-steps"></a>További lépések

[Telepítse nitoniware virtuális gépek](tutorial-migrate-vmware.md) ügynök nélküli áttelepítés.



