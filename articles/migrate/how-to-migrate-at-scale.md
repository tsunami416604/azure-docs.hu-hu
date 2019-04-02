---
title: Nagy számú virtuális gép az Azure-ba való migrálásának automatizálása |} A Microsoft Docs
description: Ismerteti, hogyan lehet áttelepíteni a virtuális gépek Azure Site Recovery használatával nagy számú szkriptek használatával
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762650"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Méretezési csoport áttelepítése a virtuális gépek Azure Site Recovery használatával

Ez a cikk segít megérteni a parancsprogramokkal áttelepíteni a virtuális gépek Azure Site Recovery használatával nagy számú folyamatán. Ezek a szkriptek érhetők el a letölthető innen [Azure PowerShell-minták](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) adattárat a Githubon. A parancsfájlok segítségével VMware, AWS, GCP virtuális gépek és fizikai kiszolgálók áttelepítése az Azure és a támogatási migrálása felügyelt lemezekre. Ezek a parancsfájlok segítségével a Hyper-V virtuális gépek migrálása, ha telepít át, hogy a virtuális gépek, mint a fizikai kiszolgálókat. A parancsfájlok kihasználhatja az Azure Site Recovery PowerShell dokumentált [Itt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuális korlátozások:
- Támogatja a csak az a cél virtuális gép az elsődleges hálózati adapter statikus IP-címének megadása
- A parancsfájlok nem lép az Azure Hybrid Benefittel kapcsolatos bemenetekben, akkor manuálisan kell frissíteni a portálon a replikált virtuális gép tulajdonságait

## <a name="how-does-it-work"></a>Hogyan működik?

### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, tegye a következőket kell:
- Győződjön meg arról, hogy a Site Recovery-tároló létrehozása az Azure-előfizetésben
- Ellenőrizze, hogy a konfigurációs kiszolgáló és a Folyamatkiszolgáló telepítve van a forrás környezetében, és a tároló találják meg a környezet
- Győződjön meg arról, hogy egy replikációs házirend létrehozása és a konfigurációs kiszolgálóhoz társított
- Győződjön meg arról, hogy hozzáadta a virtuális gép rendszergazdai fiók a konfigurációs kiszolgáló (azaz a rendszer replikálja a helyszíni virtuális gépek)
- Győződjön meg arról, hogy jönnek létre a cél-összetevők az Azure-ban
    - Céloldali erőforráscsoport
    - Céloldali Tárfiók (és az erőforráscsoport) – hozzon létre egy prémium szintű storage-fiókot, ha a prémium szintű managed Disks szolgáltatásba áttelepíteni kívánt
    - A gyorsítótár Tárfiókja (és az erőforráscsoport) – standard storage-fiók létrehozása és a tárolónak ugyanabban a régióban
    - A feladatátvételi virtuális hálózat (és az erőforráscsoport)
    - Cél alhálózat
    - Virtuális hálózat feladatátvételi teszthez (és az erőforráscsoport)
    - Rendelkezésre állási csoport (ha szükséges)
    - Céloldali hálózati biztonsági csoport és az erőforráscsoport
- Győződjön meg arról, hogy a cél virtuális gép tulajdonságainak döntött
    - Céloldali virtuális gép neve
    - Cél virtuális gép méretét az Azure-ban (születhet értékelése az Azure Migrate használatával)
    - Az elsődleges hálózati Adaptert a virtuális gép magánhálózati IP-címe
- Töltse le a parancsfájlok [Azure PowerShell-minták](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) adattárat a Githubon

### <a name="csv-input-file"></a>Fürt megosztott kötetei szolgáltatás bemeneti fájl
Miután befejeződött az összes előfeltételeket, létre kell hozzon létre egy CSV-fájl, amely minden egyes forrásgép áttelepíteni kívánt adatokat tartalmaz. A fürt megosztott kötetei szolgáltatás bemeneti rendelkeznie kell egy fejlécsort a bemeneti adatokkal és a egy sort, át kell minden egyes gépek adataival. Az összes parancsfájl dolgozhat ugyanazon a CSV-fájl lettek kialakítva. Fürt megosztott kötetei szolgáltatás mintasablon, hogy a parancsfájlok mappában érhető el.

### <a name="script-execution"></a>Szkript végrehajtása
Ha a fürt megosztott kötetei szolgáltatás készen áll, a helyszíni virtuális gépek áttelepítése a következő lépéseket hajthat végre:

**# Lépés** | **Szkript neve** | **Leírás**
--- | --- | ---
1 | asr_startmigration.ps1 | A virtuális gépek replikációjának engedélyezése a fürt megosztott kötetei szolgáltatás szerepel, a szkriptet hoz létre egy CSV-kimenetben a feladat részleteit az egyes virtuális Gépekhez
2 | asr_replicationstatus.ps1 | Ellenőrizze a replikáció állapotát, a parancsfájl hoz létre a fürt megosztott kötetei szolgáltatás állapotát az egyes virtuális Gépekhez
3 | asr_updateproperties.ps1 | Miután a virtuális gépek replikálása/védett, a szkript használatával frissítse a célkiszolgáló tulajdonságait a virtuális gép (számítási és hálózati tulajdonságok)
4 | asr_propertiescheck.ps1 | Győződjön meg arról, ha megfelelően frissíti a tulajdonságai
5 | asr_testmigration.ps1 |  Indítsa el a feladatátvételi tesztet a virtuális gépek a fürt megosztott kötetei szolgáltatás szerepel, a szkriptet hoz létre egy CSV-kimenetben a feladat részleteit az egyes virtuális Gépekhez
6 | asr_cleanuptestmigration.ps1 | Után manuálisan ellenőrizze, hogy a virtuális gépet a feladatátvételi tesztelése, a szkript használatával virtuális gépek feladatátvételi teszt karbantartása
7 | asr_migration.ps1 | A virtuális gépek a fürt megosztott kötetei szolgáltatás szerepel egy nem tervezett feladatátvétel végrehajtásához, a szkriptet hoz létre egy CSV-kimenetben a feladat részleteit az egyes virtuális Gépekhez. A parancsfájl nem áll le a helyszíni virtuális gépek, a kérelem konzisztencia érdekében a feladatátvétel indítása előtt javasoljuk, hogy manuálisan leállítja a virtuális gépek előtt hajtsa végre a parancsprogramot.
8 | asr_completemigration.ps1 | A véglegesítési művelet, a virtuális gépeken, és az Azure Site Recovery-entitások törlése
9 | asr_postmigration.ps1 | Ha azt tervezi, a hálózati biztonsági csoportok hozzárendelése a hálózati adapterek feladatátvételen átesett, ezt a parancsfájlt használhatja valósítható meg. Egy NSG-t rendel minden, a cél virtuális Gépen több hálózati Adapterrel.

## <a name="how-to-migrate-to-managed-disks"></a>A felügyelt lemezek migrálása?
A parancsfájl alapértelmezés szerint áttelepíti a virtuális gépek felügyelt lemezeket az Azure-ban. Ha a megadott célként megadott tárfiók premium storage-fiók, prémium szintű felügyelt lemezek jönnek létre post áttelepítés. A gyorsítótárfiók továbbra is a standard szintű fiók is lehet. Ha a célként megadott tárfiók standard szintű tárfiókot, a standard szintű lemezek jönnek létre a migrálás után. 

## <a name="next-steps"></a>További lépések

[További](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) információ kiszolgálók áttelepítése az Azure-bA az Azure Site Recoveryvel
