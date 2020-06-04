---
title: Tudnivalók az Azure-beli virtuális gépek visszaállítási folyamatáról
description: Ismerje meg, hogyan állítja vissza az Azure Virtual Machines szolgáltatást a Azure Backup szolgáltatás
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325823"
---
# <a name="about-azure-vm-restore"></a>Tudnivalók az Azure-beli virtuális gépek visszaállításáról

Ez a cikk azt ismerteti, hogyan állítja vissza az Azure Virtual Machines (VM) [szolgáltatást a Azure Backup szolgáltatás](https://docs.microsoft.com/azure/backup/backup-overview) . Számos visszaállítási lehetőség van. Megbeszéljük az általuk támogatott különböző forgatókönyveket.

## <a name="concepts"></a>Alapelvek

- **Helyreállítási pont** (más néven **visszaállítási pont**): a helyreállítási pont az eredeti, biztonsági mentés alatt álló adatmennyiség másolata.

- **Szint (pillanatkép**és tár): az Azure virtuális gép biztonsági mentése két fázisban történik:

  - Az 1. fázisban az elvégzett pillanatkép a lemezzel együtt tárolódik. Ezt a **Pillanatkép-szintet**nevezzük. A pillanatkép-csomagok visszaállítása gyorsabb (mint a tárolóból történő visszaállítás), mert a pillanatképek a visszaállítás elindítása előtt nem tudják megvárni a pillanatképek másolását a tárba. Így a pillanatképek szintjéről történő visszaállítást [azonnali visszaállításnak](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)is nevezik.
  - A 2. fázisban a pillanatkép átadása és tárolása a Azure Backup szolgáltatás által felügyelt tárolóban történik. Ezt a tárolói **szintet**nevezzük.

- **Eredeti helyre történő helyreállítás (OLR)**: a visszaállítási pontról a forrás Azure virtuális gépre, ahonnan a biztonsági másolatok készültek, a helyreállítási pontban tárolt állapotra cserélve. Ez váltja fel a forrás virtuális gép operációsrendszer-lemezét és adatlemezét.

- **Alternatív hely helyreállítása (ALR)**: a helyreállítási pontról egy olyan kiszolgálóra történt helyreállítás, amely nem az eredeti kiszolgáló, ahol a biztonsági másolatok készültek.

- **Elemszintű visszaállítás (ILR):** A virtuális gépen lévő egyes fájlok vagy mappák visszaállítása a helyreállítási pontról

- **Rendelkezésre állás (replikációs típusok)**: a Azure Backup kétféle replikációt biztosít a tárterület/az adatmennyiség nagyfokú elérhetőségének biztosításához:
  - A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) háromszor replikálja az adatait (az adatait három példányban hozza létre) egy adatközpont tárolási méretezési egységében. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
  - A [geo-redundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS több mint LRS, de a GRS magasabb szintű tartósságot biztosít az adataihoz, még akkor is, ha van regionális leállás.

- **Régiók közötti visszaállítás (CRR)**: a [visszaállítási lehetőségek](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)egyike a tartományok közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy [Azure párosított régió](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="restore-scenarios"></a>Visszaállítási forgatókönyvek

![Visszaállítási forgatókönyvek ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Forgatókönyv**                                                 | **Mi történik**                                             | **A következő esetekben használja**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Visszaállítás új virtuális gép létrehozásához](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | Visszaállítja a teljes virtuális gépet a OLR (ha a forrás virtuális gép még létezik) vagy ALR | <li> Ha a forrásként szolgáló virtuális gép elveszett vagy sérült, akkor teljes virtuális gépet állíthat vissza.  <li> Létrehozhat egy másolatot a virtuális gépről  <li> A naplózási és megfelelőségi műveletek visszaállítására is lehetőség van  <li> Ez a lehetőség nem működik a Piactéri rendszerképekből létrehozott Azure-beli virtuális gépek esetén (azaz ha nem érhetők el, mert a licenc lejárt). |
| [A virtuális gép lemezei visszaállítása](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | A virtuális géphez csatolt lemezek visszaállítása                             |  Összes lemez: Ez a beállítás létrehozza a sablont, és visszaállítja a lemezt. A sablont speciális konfigurációkkal (például a rendelkezésre állási csoportokkal) szerkesztheti, hogy megfeleljen a követelményeknek, majd a sablon használatával és a lemez visszaállításával hozza létre újra a virtuális gépet. |
| [A virtuális gépen lévő adott fájlok visszaállítása](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | Válassza a visszaállítási pont lehetőséget, tallózással válassza ki a fájlokat, majd állítsa vissza őket ugyanarra a (vagy kompatibilis) operációs rendszerre, mint a biztonsági másolattal elválasztott virtuális gép. |  Ha ismeri a visszaállítani kívánt fájlokat, akkor a teljes virtuális gép visszaállítása helyett használja ezt a lehetőséget. |
| [Titkosított virtuális gép visszaállítása](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | A portálról állítsa vissza a lemezeket, majd a PowerShell használatával hozza létre a virtuális gépet. | <li> [Titkosított virtuális gép Azure Active Directory (HRE)](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [Titkosított virtuális gép HRE nélkül](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [Titkosított virtuális gép *HRE* - *HRE nélkül* áttelepítve](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Régiók közötti visszaállítás](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | Új virtuális gép létrehozása vagy lemezek visszaállítása másodlagos régióba (Azure párosított régió) | <li> **Teljes leállás**: a régiók közötti visszaállítási szolgáltatással a másodlagos régióban nem lehet várni az adatok helyreállítására. A visszaállításokat a másodlagos régióban is kezdeményezheti, még az Azure előtt is. <li> **Részleges leállás**: az állásidő olyan tárolási fürtökön fordulhat elő, ahol a Azure Backup tárolja a biztonsági másolatok adatait, vagy akár hálózatban is, összekapcsolva a biztonsági másolatban szereplő adataihoz társított Azure Backup és Storage-fürtöket. A régiók közötti visszaállítással a másodlagos régióban visszaállíthat egy visszaállítást a másodlagos régióban. <li> **Nincs leállás**: az üzletmenet-folytonosságot és a vész-helyreállítási (BCDR) gyakorlatokat a másodlagos régió adataival történő auditálási és megfelelőségi célokra is elvégezheti. Ez lehetővé teszi a biztonsági másolatok adatainak visszaállítását a másodlagos régióban, még akkor is, ha az elsődleges régióban nincs teljes vagy részleges leállás az üzletmenet folytonosságának és a vész-helyreállítási gyakorlatoknak.  |

------





## <a name="next-steps"></a>Következő lépések

- [Gyakori kérdések a virtuális gépek visszaállításáról](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Támogatott visszaállítási módszerek](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [Visszaállítási problémák elhárítása](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
